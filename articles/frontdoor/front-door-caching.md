---
title: Azure Front Door - キャッシュ | Microsoft Docs
description: この記事は、バックエンドの正常性が Azure Front Door によってどのように監視されるかを理解するのに役立ちます
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 1cb24e4a959e7d32a3c3b5b69a39938df4efddfa
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399873"
---
# <a name="caching-with-azure-front-door"></a>Azure Front Door でのキャッシュ
次のドキュメントは、キャッシュを有効にしたルーティング規則を使用して Front Door の動作を指定します。 Front Door は最新の Content Delivery Network (CDN) であり、動的サイト アクセラレーションおよび負荷分散に加えて、他の CDN と同様にキャッシュの動作もサポートされています。

## <a name="delivery-of-large-files"></a>大きなファイルの配信
Azure Front Door では、ファイル サイズの制限なしで、大きなファイルが配信されます。 Front Door では、オブジェクト チャンクと呼ばれる方法を使用します。 大きなファイルが要求されると、Front Door はバックエンドからファイルを分割して取得します。 完全なファイル要求またはバイト範囲を指定したファイル要求を受け取った後、Front Door 環境は、8 MB のチャンク単位でバックエンドにファイルを要求します。

</br>Front Door 環境に届いたチャンクはキャッシュされ、即座にユーザーに提供されます。 その後、Front Door は並列処理で次のチャンクをプリフェッチします。 このプリフェッチにより、コンテンツはチャンク 1 つ分だけ常にユーザーより先行することになるため、待ち時間が短縮されます。 この処理は、ファイル全体がダウンロードされるか (要求があった場合)、すべてのバイト範囲が利用可能になるか (要求があった場合)、クライアントが接続を終了するまで続けられます。

</br>バイト範囲の要求の詳細については、[RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html) を参照してください。
Front Door は受け取ったチャンクをそのままキャッシュするため、ファイル全体を Front Door キャッシュにキャッシュする必要はありません。 ファイルまたはバイト範囲に対する後続の要求に対しては、キャッシュの内容が提供されます。 すべてのチャンクがキャッシュされていない場合は、プリフェッチを使用してバックエンドにチャンクが要求されます。 この最適化は、バイト範囲要求をサポートするバックエンドの機能に依存します。バックエンドがバイト範囲要求をサポートしていない場合、この最適化の効果はありません。

## <a name="file-compression"></a>ファイル圧縮
Front Door は、エッジのコンテンツを動的に圧縮するため、クライアントへの応答が小さく高速になります。 すべてのファイルが圧縮の対象になります。 ただし、ファイルは圧縮リストに含まれている MIME の種類である必要があります。 現時点では、Front Door のこのリストを変更することはできません。 現在のリストは、次のとおりです。</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- "application/javascript"
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js"、"text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

また、ファイルのサイズは 1 KB 以上 8 MB 以下である必要があります。

これらのプロファイルでは、次の圧縮エンコードがサポートされています。
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

要求で gzip 圧縮と Brotli 圧縮がサポートされている場合は、Brotli 圧縮が優先されます。</br>
アセットの要求で圧縮が指定され、要求がキャッシュ ミスになった場合、Front Door は POP サーバー上で直接アセットの圧縮を行います。 その後、圧縮ファイルがキャッシュから提供されます。 結果として得られる項目は、transfer-encoding: chunked 付きで返されます。

## <a name="query-string-behavior"></a>クエリ文字列の動作
Front Door を使用すると、クエリ文字列を含む Web 要求のためにファイルをキャッシュする方法を制御することができます。 クエリ文字列を含む Web 要求で、クエリ文字列は要求の疑問符 (?) の後に指定されます。 クエリ文字列には、フィールド名とその値を等号 (=) で区切って指定される、キーと値のペア (複数可) を含めることができます。 キーと値のペアはそれぞれ、アンパサンド (&) で区切られます。 たとえば、「 `http://www.contoso.com/content.mov?field1=value1&field2=value2` 」のように入力します。 要求のクエリ文字列にキーと値のペアを複数指定する場合、どのような順序で指定してもかまいません。
- **クエリ文字列を無視**: 既定モード。 このモードでは、Front Door は要求元からのクエリ文字列を最初の要求時にバックエンドに渡して、アセットをキャッシュします。 Front Door 環境から提供されるアセットに対する後続のすべての要求で、キャッシュされたアセットの有効期限が切れるまで、クエリ文字列が無視されます。

- **一意の URL をすべてキャッシュ**: このモードでは、クエリ文字列を含む一意の URL が指定された各要求は、独自のキャッシュがある一意の資産として扱われます。 たとえば、`www.example.ashx?q=test1` の要求に対するバックエンドからの応答は Front Door 環境にキャッシュされ、同じクエリ文字列を持つ後続のキャッシュに対して返されます。 `www.example.ashx?q=test2` の要求は、独自の有効期限設定を持つ個別の資産としてキャッシュされます。

## <a name="cache-purge"></a>キャッシュの消去
Front Door は、アセットの Time-to-Live (TTL) が期限切れになるまで、そのアセットをキャッシュします。 アセットの TTL の期限が切れた後に、クライアントがアセットを要求すると、Front Door 環境はアセットの更新された最新コピーを取得し、クライアント要求に対応して、更新されたキャッシュを格納します。
</br>ユーザーが常にアセットの最新コピーを取得するのを確実にするベスト プラクティスは、更新ごとにアセットにバージョンを付け、新しい URL として発行することです。 Front Door では、次のクライアント要求のための新しいアセットが直ちに取得されます。 必要に応じて、すべてのエッジ ノードのキャッシュされたコンテンツを消去し、すべてのエッジ ノードが新しい更新されたアセットを取得するように強制することもできます。 たとえば、Web アプリケーションの更新に対応する場合や、正しくない情報を含むアセットをすばやく更新する場合などです。

</br>エッジ ノードから消去するアセットを選択します。 すべてのアセットを消去する場合は、[すべて消去] チェック ボックスをオンにします。 それ以外の場合は、消去する各アセットのパスを [パス] テキスト ボックスに入力します。 パスには次の形式がサポートされています。
1. **1 つのパスの消去**: (プロトコルとドメインを除く) アセットの完全なパスをファイル拡張子と共に指定して、個々のアセットを消去します (例: /pictures/strasbourg.png)。
2. **ワイルドカードによる消去**: アスタリスク (\*) をワイルドカードとして使用できます。 パスに /\* を付けてエンドポイントの下のすべてのフォルダー、サブフォルダー、およびファイルを消去するか、またはフォルダーと /\* (たとえば /pictures/\*) を指定して特定のフォルダーの下のすべてのサブフォルダーおよびファイルを消去します。
3. **ルート ドメインの消去**: パスに "/" を付けてエンドポイントのルートを削除します。

Front Door でのキャッシュの消去では、大文字と小文字が区別されます。 また、クエリ文字列に依存しません。つまり、URL を消去すると、そのすべてのクエリ文字列バリエーションが消去されます。 

## <a name="cache-expiration"></a>キャッシュの有効期限
キャッシュに項目が格納される期間を判断するために、次のヘッダーの順序が使用されます。</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age=\<seconds>
3. 有効期限: \<http-date>

応答がキャッシュされないことを示す Cache-Control 応答ヘッダー (Cache-Control: private、Cache-Control: no-cache、Cache-Control: no-store など) は受け入れられます。 ただし、POP で処理中の同じ URL の複数の要求がある場合、それらは応答を共有する場合があります。 キャッシュ制御が存在しない場合、既定の動作として、AFD によってリソースが X 時間の間、キャッシュに入れられます。ここで、X は 1 日から 3 日までの範囲からランダムに選択されます。

## <a name="request-headers"></a>要求ヘッダー

キャッシュを使用している場合、次の要求ヘッダーはバックエンドに転送されません。
- Content-Length
- Transfer-Encoding

## <a name="cache-duration"></a>Cache duration (キャッシュ期間)

キャッシュ期間は、Front Door デザイナーとルール エンジンの両方で構成できます。 Frontdoor デザイナーで設定されるキャッシュ期間は、最小キャッシュ期間です。 転送元のキャッシュ制御ヘッダーの TTL がオーバーライド値よりも大きい場合、このオーバーライドは機能しません。 

ルール エンジンで設定されるキャッシュ期間が真のキャッシュ オーバーライドです。つまり、転送元の応答ヘッダーに関係なく、そのオーバーライド値が使用されます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
