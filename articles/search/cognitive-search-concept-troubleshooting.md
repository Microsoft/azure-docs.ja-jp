---
title: AI エンリッチメントの設計に関するヒント
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search で AI エンリッチメント パイプラインをセットアップするためのヒントとトラブルシューティング。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 8347ca5a33790d0b35176be47a0fa4811a19e3f1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935468"
---
# <a name="tips-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search における AI エンリッチメントに関するヒント

この記事では、Azure Cognitive Search で AI エンリッチメント機能を使い始めてから利用を続けるためのヒントやコツの一覧を示します。 

まだ[AI エンリッチメント API を呼び出す方法を学習するチュートリアル](cognitive-search-quickstart-blob.md)を行っていない場合は、AI エンリッチメントを BLOB データ ソースに適用する練習のために、このチュートリアルを実行してください。

## <a name="tip-1-start-with-a-small-dataset"></a>ヒント 1: 小さなデータセットから開始する
問題をすばやく見つける最良の方法は、問題を修正する速度を上げることです。 インデックス付け時間を短縮する最善の方法は、インデックス付けするドキュメントの数を減らすことです。 

まずは、ほんの一握りのドキュメント/レコードでデータ ソースを作成します。 ドキュメント サンプルは、インデックス付けするドキュメントの多様性の適切な表現である必要があります。 

エンド ツー エンド パイプラインを通じてドキュメント サンプルを実行し、結果がニーズを満たしていることを確認します。 結果が満足できるものであれば、データ ソースにさらにファイルを追加することができます。

## <a name="tip-2-make-sure-your-data-source-credentials-are-correct"></a>ヒント 2: データ ソースの資格情報が正しいことを確認する
データ ソース接続は、それを使用するインデクサーを定義するまでは検証されません。 インデクサーがデータにアクセスできないというエラーが表示される場合は、次の点を確認してください。
- 接続文字列正しい。 特に、SAS トークンを作成している場合は、Azure Cognitive Search の所定の形式を使用するようにしてください。 サポートされているさまざまなフォーマットについては、「[資格情報を指定する方法](
https://docs.microsoft.com/azure/search/search-howto-indexing-azure-blob-storage#how-to-specify-credentials)」のセクションを参照してください。
- インデクサー内のコンテナー名が正しい。

## <a name="tip-3-see-what-works-even-if-there-are-some-failures"></a>ヒント 3: いくつかのエラーがある場合でも何が動作しているかを確認する
場合によっては、小さなエラーによって直ちにインデクサーが停止されることがあります。 問題を 1 つずつ修正する計画があれば、問題ありません。 ただし、特定の種類のエラーを無視して、どのフローが実際に動作しているかを確認できるように、インデクサーを続行したい場合もあります。

その場合は、エラーを無視するようにインデクサーに指示することができます。 そうするには、インデクサー定義の一部として、*maxFailedItems* と *maxFailedItemsPerBatch* を -1 に設定します。

```
{
  "// rest of your indexer definition
   "parameters":
   {
      "maxFailedItems":-1,
      "maxFailedItemsPerBatch":-1
   }
}
```
> [!NOTE]
> ベスト プラクティスとして、実稼働ワークロードに対して maxFailedItems、maxFailedItemsPerBatch を 0 に設定することをお勧めします。

## <a name="tip-4-use-debug-sessions-to-identify-and-resolve-issues-with-your-skillset"></a>ヒント 4: デバッグ セッションを使用して、スキルセットの問題を特定し、解決する 

デバッグ セッションは、Azure portal の既存のスキルセットと連動するビジュアル エディターです。 デバッグ セッション内では、エラーを特定して解決し、変更を確認し、AI エンリッチメント パイプラインで運用スキルセットに対して変更を行うことができます。 これは、プレビュー機能です。[こちらのドキュメントをお読みください](./cognitive-search-debug-session.md)。 概念の詳細と概要については、[デバッグ セッション](./cognitive-search-tutorial-debug-sessions.md)に関する記事を参照してください。

1 つのドキュメントでのデバッグ セッションの作業は、より複雑なエンリッチメント パイプラインを反復的にビルドするための優れた方法です。

## <a name="tip-5-looking-at-enriched-documents-under-the-hood"></a>ヒント 5: エンリッチされたドキュメントの内部を確認する 
エンリッチされたドキュメントは、エンリッチメント中に作成され、プロセスが完了すると削除される、一時的な構造体です。

インデックス作成時に作成されるエンリッチされたドキュメントのスナップショットをキャプチャするには、インデックスに ```enriched``` という名前のフィールドを追加します。 インデクサーは、そのドキュメントのすべてのエンリッチメントの文字列表現を、このフィールド内に自動的にダンプします。

```enriched``` フィールドには、メモリ内のエンリッチされたドキュメントの JSON での論理表現である文字列が含まれます。  ただし、フィールド値は有効な JSON ドキュメントです。 引用符はエスケープされるため、書式設定された JSON としてドキュメントを表示するためには、`\"` を `"` に置き換える必要があります。 

エンリッチされたフィールドはデバッグのためだけに使用するフィールドであり、式の評価の対象となるコンテンツの論理構造を理解するために役立ちます。 インデックス作成の目的では、このフィールドに依存しないでください。

デバッグの目的で、インデックス定義の一部として ```enriched``` フィールドを追加します。

#### <a name="request-body-syntax"></a>要求本文の構文
```json
{
  "fields": [
    // other fields go here.
    {
      "name": "enriched",
      "type": "Edm.String",
      "searchable": false,
      "sortable": false,
      "filterable": false,
      "facetable": false
    }
  ]
}
```

## <a name="tip-6-expected-content-fails-to-appear"></a>ヒント 6: 期待したコンテンツが表示されない

コンテンツの不足は、インデックス作成中にドキュメントが削除された結果である可能性があります。 Free および Basic レベルでは、ドキュメントのサイズの制限が低く設定されています。 制限を超えるファイルは、インデックス作成中に削除されます。 削除されたドキュメントは、Azure portal で確認できます。 検索サービス ダッシュボードで、インデクサーのタイルをダブルクリックします。 インデックス付けが成功したドキュメントの割合を確認します。 100% でない場合は、割合をクリックすると詳細が表示されます。 

問題がファイル サイズに関連している場合は、"The blob \<file-name> のサイズが \<file-size> バイトです。現在のサービス レベルでのドキュメント抽出の最大サイズを超えています。" のようなエラーが表示されることがあります。 インデクサーの制限の詳細については、[サービスの制限](search-limits-quotas-capacity.md)に関するページを参照してください。

コンテンツが表示されない 2 つ目の理由として考えられるのは、関連する入力/出力マッピング エラーです。 たとえば、出力ターゲットの名前が "People" で、インデックス フィールド名が小文字の "people" である場合です。 システムはパイプライン全体に対して 201 成功メッセージを返す場合があるため、インデックス作成が成功したと思っても、実際にはフィールドが空になっています。 

## <a name="tip-7-extend-processing-beyond-maximum-run-time-24-hour-window"></a>ヒント 7: 最大実行時間 (24 時間ウィンドウ) を超えて処理を拡張する

画像解析は単純な場合でも計算が集中するため、画像が特に大きかったり複雑だったりする場合、処理時間が最大許容時間を超えることがあります。 

最大実行時間は、レベルごとに異なります。Free レベルでは数分であり、請求対象のレベルでは 24 時間のインデックス作成です。 オンデマンド処理が 24 時間以内に完了しない場合は、スケジュールに切り替えて、インデクサーが処理を中断した箇所から開始するようにします。 

スケジュールされたインデクサーは、スケジュールされた時間に、前回正常に処理されたドキュメントからインデックス作成を再開します。 定期的なスケジュールを使用することにより、インデクサーは数時間または数日にわたって、未処理の画像がすべて処理されるまで、画像のバックログに対する作業を続けることができます。 スケジュールの構文の詳細については、「[手順 3: インデクサーの作成](search-howto-indexing-azure-blob-storage.md#step-3-create-an-indexer)」または [Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関するページを参照してください。

> [!NOTE]
> インデクサーが特定のスケジュールに設定されているが実行のたびに同じドキュメントに対して繰り返し失敗する場合、進捗が再び正常化するまでの間、インデクサーは (最大で 24 時間に 1 回に) 間隔を開けて実行頻度を下げます。  インデクサーが特定の箇所で停止する原因になっていた問題をすべて修正したと思われる場合、インデクサーをオンデマンドで実行できます。それによって進捗が正常になったら、インデクサーは設定されていたスケジュール間隔に復帰します。

ポータル ベースのインデックス作成では (クイック スタートで説明したように)、[一度だけ実行する] のインデクサー オプションを選択すると、処理が 1 時間 (`"maxRunTime": "PT1H"`) に制限されます。 処理ウィンドウをもっと長い時間に拡張したい場合があります。

## <a name="tip-8-increase-indexing-throughput"></a>ヒント 8: インデックス作成のスループットを向上させる

[並列インデックス作成](search-howto-large-index.md)の場合は、データを複数のコンテナーまたは同じコンテナー内の複数の仮想フォルダーに配置します。 次に、複数のデータソースとインデクサーのペアを作成します。 すべてのインデクサーは、同じスキルセットを使用して同じターゲット検索インデックスに書き込むことができるため、検索アプリはこのパーティション分割を意識する必要はありません。
詳細については、「[大規模なデータセットのインデックス作成](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)」を参照してください。

## <a name="see-also"></a>関連項目
+ [クイック スタート: ポータルで AI エンリッチメント パイプラインを作成する](cognitive-search-quickstart-blob.md)
+ [チュートリアル:AI エンリッチメント REST API について学習する](cognitive-search-tutorial-blob.md)
+ [データ ソースの資格情報の指定](search-howto-indexing-azure-blob-storage.md#how-to-specify-credentials)
+ [大規模なデータセットのインデックス作成](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [エンリッチされたフィールドをインデックスにマップする方法](cognitive-search-output-field-mapping.md)