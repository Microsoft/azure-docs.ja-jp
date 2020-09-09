---
title: Azure Traffic Manager - トラフィックのルーティング方法
description: この記事では、Traffic Manager で使用されるさまざまなトラフィック ルーティング方法について説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: 3cf493beab6dfe1767ae35ea36732dc364e29736
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401658"
---
# <a name="traffic-manager-routing-methods"></a>Traffic Manager のルーティング方法

Azure Traffic Manager では、さまざまなサービス エンドポイントにネットワーク トラフィックをルーティングする方法を決定するために、6 つのトラフィック ルーティング方法をサポートしています。 Traffic Manager は、受信した各 DNS クエリに対し、プロファイルに関連付けられているトラフィック ルーティング方法を適用します。 トラフィック ルーティング方法によって、DNS 応答で返されるエンドポイントが決まります。

Traffic Manager では、次のトラフィック ルーティング方法を使用できます。

* **[優先順位](#priority-traffic-routing-method):** すべてのトラフィックにプライマリ サービス エンドポイントを使用し、プライマリ エンドポイントまたはバックアップ エンドポイントが使用できない場合に備えてバックアップを用意する場合は、"**優先順位**" を選択します。
* **[重み付け](#weighted):** 一連のエンドポイントに、均等にまたは定義した重みに従ってトラフィックを分散させる場合は、"**重み付け**" を選択します。
* **[パフォーマンス](#performance):** 複数のエンドポイントが地理的に異なる場所にあり、エンド ユーザーが、ネットワーク待ち時間が最も短いという意味で "最も近い" エンドポイントを使用できるようにする場合は、"**パフォーマンス**" を選択します。
* **[地理的](#geographic):** DNS クエリの発信元の地理的な場所に基づいてユーザーを特定のエンドポイント (Azure、外部、または入れ子になっているエンドポイント) に割り当てる場合は、"**地理的**" を選択します。 こうすることで、Traffic Manager の利用者は、ユーザーのリージョンを把握し、そのリージョンに基づいてユーザーをルーティングすることが重要なシナリオを実現できます。 データ主権規制、コンテンツおよびユーザー エクスペリエンスのローカライズ、さまざまなリージョンからのトラフィックの測定がその例に挙げられます。
* **[複数値](#multivalue):** エンドポイントとして IPv4 および IPv6 アドレスしか割り当てることのできない Traffic Manager プロファイルには、 **[複数値]** を選択します。 このプロファイルに対するクエリが受信されると、正常なエンドポイントがすべて返されます。
* **[[サブネット]](#subnet):** Traffic Manager プロファイル内の特定のエンドポイントにエンドユーザーの一連の IP アドレス範囲をマップする場合は、トラフィック ルーティング方法として **[サブネット]** を選択します。 要求が受信されたときに返されるエンドポイントは、その要求の送信元 IP アドレスにマップされているものです。 


すべての Traffic Manager プロファイルには、エンドポイントの正常性とエンドポイントの自動フェールオーバーの監視が含まれます。 詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md)に関する記事をご覧ください。 1 つの Traffic Manager プロファイルで使用できるトラフィック ルーティング方法は 1 つに限られます。 プロファイルの別のトラフィック ルーティング方法をいつでも選択できます。 変更は 1 分以内に適用され、ダウンタイムは発生しません。 入れ子になった Traffic Manager プロファイルを使用することで、トラフィック ルーティング方法を組み合わせることができます。 入れ子にすることで、高度で柔軟性のあるトラフィック ルーティング構成が可能になり、より大規模で複雑なアプリケーションのニーズに対応できます。 詳細については、「 [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

## <a name="priority-traffic-routing-method"></a>優先順位トラフィック ルーティング方法

多くの場合、組織ではサービスの信頼性を提供したいと考えており、主要なサービスがダウンした場合に備えて 1 つ以上のバックアップ サービスをデプロイすることでこれを実行しています。 "優先順位" トラフィック ルーティング方法を使用すると、Azure ユーザーはこのフェールオーバー パターンを簡単に実装できます。

![Azure Traffic Manager の "優先順位" トラフィック ルーティング方法](media/traffic-manager-routing-methods/priority.png)

Traffic Manager プロファイルには、サービス エンドポイントの優先順位リストが含まれます。 Traffic Manager では、既定では、すべてのトラフィックがプライマリ (優先順位が一番高い) エンドポイントに送信されます。 プライマリ エンドポイントを使用できない場合、Traffic Manager は、2 番目のエンドポイントにトラフィックをルーティングします。 プライマリとセカンダリのどちらのエンドポイントも使用できない場合、トラフィックは 3 番目のエンドポイントに送信されます。以降も同様です。 エンドポイントの可用性は、構成済みのステータス (有効または無効) とエンドポイントの継続的な監視に基づきます。

### <a name="configuring-endpoints"></a>エンドポイントの構成

Azure Resource Manager では、エンドポイントごとに "priority" プロパティを使用して、エンドポイントの優先順位を明示的に構成します。 このプロパティの値の範囲は、1 ～ 1000 です。 値が小さいほど、優先順位が高くなります。 同じ優先順位の値を複数のエンドポイントに指定することはできません。 プロパティの設定は省略可能です。 省略した場合、エンドポイントの順序に基づく既定の優先順位が使用されます。

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>重み付けトラフィック ルーティング方法
"重み付け" トラフィック ルーティング方法を使用すると、トラフィックを均等に分散したり、定義済みの重み付けを使用したりできます。

![Azure Traffic Manager の "重み付け" トラフィック ルーティング方法](media/traffic-manager-routing-methods/weighted.png)

重み付けトラフィック ルーティング方法では、Traffic Manager プロファイル構成で各エンドポイントに重みを割り当てます。 重みは 1 から 1000 の整数です。 このパラメーターは省略可能です。 省略すると、Traffic Managers では、既定の重みの "1" が使用されます。 重みが大きくなると、優先順位がそれだけ上がります。

Traffic Manager では、受信する DNS クエリごとに、使用可能なエンドポイントをランダムに選択します。 あるエンドポイントが選択される確率は、すべての利用可能なエンドポイントに割り当てられている重みに基づきます。 すべてのエンドポイント間で同じ重みを使用すると、トラフィック分散は均一になります。 特定のエンドポイントの重みを大きくする (または小さくする) と、それらのエンドポイントは DNS からの応答の頻度が増えます (または減ります)。

重み付け方式を使用することで、有用なシナリオをいくつか実現できます。

* アプリケーションの段階的アップグレード: 新しいエンドポイントにルーティングするトラフィックのパーセンテージを割り当て、時間と共に 100% までトラフィックを徐々に増やします。
* Azure へのアプリケーション移行: Azure と外部エンドポイントの両方を含むプロファイルを作成します。 エンドポイントの重みを調整して新しいエンドポイントを優先します。
* 追加容量のクラウド バースト: Traffic Manager プロファイルに対応させることで、オンプレミスのデプロイメントをクラウドにすばやく拡張します。 クラウドに追加容量が必要なとき、エンドポイントを追加または有効にして、各エンドポイントに送るトラフィックの割り当てを指定します。

重みは、Azure portal を使用して構成できるほか、Azure PowerShell、CLI、REST API を使用して構成することもできます。

クライアントと、DNS 名を解決するためにクライアントが使用する再帰 DNS サーバーによって、DNS 応答がキャッシュされることに留意してください。 このキャッシュは、重み付けトラフィック分散に影響を与える可能性があります。 クライアントと再帰 DNS サーバーの数が多い場合は、トラフィック分散が期待どおりに機能します。 ただし、クライアントまたは再帰 DNS サーバーの数が少ない場合は、キャッシュによってトラフィック分散に大きな偏りが生じる可能性があります。

一般的なユース ケースは次のとおりです。

* 開発環境とテスト環境
* アプリケーション間の通信
* 共通の再帰 DNS インフラストラクチャを共有する、限られたユーザーを対象としたアプリケーション (たとえば、プロキシ経由で接続する会社の従業員など)

これらの DNS キャッシュの影響は、Azure Traffic Manager だけでなく、DNS ベースのすべてのトラフィック ルーティング システムに共通するものです。 DNS キャッシュの明示的なクリアが回避策になる場合もあれば、 別のトラフィック ルーティング方法の方が適している場合もあります。

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>パフォーマンス トラフィック ルーティング方法

世界中の複数の場所にエンドポイントをデプロイすると、ユーザーに "最も近い" 場所にトラフィックをルーティングすることで、多くのアプリケーションの応答性を向上させることができます。 "パフォーマンス" トラフィック ルーティング方法が、この機能を提供します。

![Azure Traffic Manager の "パフォーマンス" トラフィック ルーティング方法](media/traffic-manager-routing-methods/performance.png)

"最も近い" エンドポイントが必ずしも地理的な距離で最も近いとは限りません。 "パフォーマンス" トラフィック ルーティング方法では、代わりに、ネットワーク待ち時間を尺度として最も近いエンドポイントを特定します。 Traffic Manager は、インターネット待機時間テーブルを管理して、IP アドレス範囲と各 Azure データセンター間のラウンドトリップ時間を追跡します。

Traffic Manager は、受信 DNS 要求の送信元 IP アドレスをインターネット待機時間テーブルで見つけます。 Traffic Manager では、その IP アドレス範囲について待ち時間が最も短くなる Azure データセンター内の使用可能なエンドポイントが選択され、DNS 応答でそのエンドポイントが返されます。

[Traffic Manager の動作のしくみ](traffic-manager-how-it-works.md)で説明したように、Traffic Manager は、クライアントから直接には DNS クエリを受信しません。 代わりに、DNS クエリは、クライアントが使用するように構成された再帰 DNS サービスから受信します。 そのため、"最も近い" エンドポイントの特定に使用される IP アドレスは、クライアントの IP アドレスではなく、再帰 DNS サービスの IP アドレスになります。 実際には、この IP アドレスはクライアントにとって適切なプロキシとなります。


Traffic Manager は、インターネット待機時間テーブルを定期的に更新して、グローバル インターネットと新しい Azure リージョンの変化に対応しています。 ただし、アプリケーションのパフォーマンスは、インターネット全体におけるリアルタイムな負荷の変動によって変わります。 パフォーマンスによるトラフィック ルーティングでは、特定のサービス エンドポイントの負荷は監視されません。 ただし、エンドポイントを使用できなくなった場合は、Traffic Manager は DNS クエリの応答にそのエンドポイントを含みません。


注意する点:

* プロファイルに同一 Azure リージョン内の複数のエンドポイントが含まれている場合は、Traffic Manager では、そのリージョンで利用可能なエンドポイントにトラフィックが均等に分散されます。 リージョン内で別のトラフィック分散を使用する場合は、[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)を使用できます。
* 最も近い Azure リージョン内のすべての有効なエンドポイントの機能が低下している場合は、Traffic Manager によって次に近い Azure リージョン内のエンドポイントにトラフィックが移動されます。 希望するフェールオーバー シーケンスを定義する場合は、「[入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」を使用してください。
* 外部エンドポイントまたは入れ子になったエンドポイントでパフォーマンス トラフィック ルーティング方法を使用する場合は、エンドポイントの場所を指定する必要があります。 デプロイメントに最も近い Azure リージョンを選択してください。 これらの場所は、インターネット待機時間テーブルでサポートされている値です。
* エンドポイントを選択するアルゴリズムは確定的です。 同じクライアントからの反復 DNS クエリは、同じエンドポイントに送信されます。 通常、クライアントは、出張中に別の再帰 DNS サーバーを使用します。 このクライアントは、別のエンドポイントにルーティングされる可能性があります。 また、ルーティングは、インターネット待機時間テーブルの更新の影響を受ける場合もあります。 そのため、パフォーマンス トラフィック ルーティング方法では、クライアントが常に同じエンドポイントにルーティングされる保証はありません。
* インターネット待機時間テーブルが変更されたときに、一部のクライアントが別のエンドポイントにルーティングされていることに気付く場合があります。 このルーティングの変更は、最新の待機時間データに基づいてより厳密に行われます。 これらの更新は、インターネットが継続的に発展する中で、パフォーマンス トラフィック ルーティングの精度を維持するために不可欠となります。

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>地理的トラフィック ルーティング方法

Traffic Manager プロファイルで地理的ルーティング方法を使用するように構成できます。これにより、DNS クエリの発信元の地理的な場所に基づいて、ユーザーを特定のエンドポイント (Azure、外部、または入れ子) にルーティングされます。 こうすることで、Traffic Manager の利用者は、ユーザーのリージョンを把握し、そのリージョンに基づいてユーザーをルーティングすることが重要なシナリオを実現できます。 データ主権規制、コンテンツおよびユーザー エクスペリエンスのローカライズ、さまざまなリージョンからのトラフィックの測定がその例に挙げられます。
地理的ルーティング用にプロファイルを構成したら、そのプロファイルに関連付けられた各エンドポイントに地理的なリージョンのセットを割り当てる必要があります。 リージョンは、次のレベルの粒度で指定できます。 
- 世界 – 任意のリージョン
- リージョン グループ – アフリカ、中東、オーストラリア/太平洋など 
- 国/リージョン – アイルランド、ペルー、香港特別行政区など 
- 都道府県 – 米国カリフォルニア州、オーストラリア クイーンズランド州、カナダ アルバータ州など (注: 都道府県でこのレベルの粒度がサポートされているのは、オーストラリア、カナダ、米国のみです)。

エンドポイントにリージョンまたはリージョンのセットが割り当てられると、それらのリージョンからの要求は、そのエンドポイントにのみルーティングされます。 Traffic Manager は、DNS クエリの発信元 IP アドレスを使用して、ユーザーがクエリを送信しているリージョンを特定します。通常、これはユーザーの代わりにクエリを実行するローカル DNS リゾルバーの IP アドレスになります。  

![Azure Traffic Manager の "地理的" トラフィック ルーティング方法](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager は DNS クエリの発信元 IP アドレスを読み取り、送信元のリージョンを決定します。 次に、この地理的リージョンがマップされているエンドポイントがあるかどうかを確認します。 この検索は、最も低い粒度レベル (サポートされている場合は都道府県、それ以外の場合は国/リージョン レベル) で始まり、最も高いレベルである**世界**まで進みます。 このトラバーサルを使用して見つかった最初の一致が、クエリの応答で返されるエンドポイントとして指定されます。 入れ子になっている種類のエンドポイントと一致する場合、ルーティング方法に基づいて、その子プロファイル内のエンドポイントが返されます。 次の点がこの動作に関係します。

- ルーティングの種類が地理的ルーティングの場合、リージョンは Traffic Manager プロファイル内の 1 つのエンドポイントにのみマップできます。 これにより、ユーザーのルーティングが確定的になるため、明確な地理的境界を必要とするシナリオを実現できます。
- ユーザーのリージョンが 2 つの異なるエンドポイントの地理的マッピングに属する場合、Traffic Manager は、粒度が低いほうのエンドポイントを選択します。そのリージョンからもう 1 つのエンドポイントへのルーティング要求は考慮されません。 たとえば、Endpoint1 と Endpoint2 の 2 つのエンドポイントが含まれた、ルーティングの種類が地理的ルーティングのプロファイルを考えてみましょう。 Endpoint1 はアイルランドからのトラフィックを受信するように構成し、Endpoint2 はヨーロッパからのトラフィックを受信するように構成します。 要求がアイルランドから送信された場合、常に Endpoint1 にルーティングされます。
- リージョンをマップできるのは 1 つのエンドポイントに限られるため、Traffic Manager は、エンドポイントが正常かどうかに関係なくそのエンドポイントを返します。

    >[!IMPORTANT]
    >地理的ルーティング方法を使用する場合は、少なくとも 2 つのエンドポイントを含む子プロファイルを持つ入れ子になった種類のエンドポイントに関連付けることを強くお勧めします。
- エンドポイントの一致が見つかったときに、そのエンドポイントが**停止**状態の場合、Traffic Manager は NODATA 応答を返します。 この場合、地理的リージョンの上位階層に対する検索はそれ以上行われません。 この動作は、子プロファイルが**停止**または**無効**の状態のときの、入れ子になったエンドポイントの種類にも該当します。
- エンドポイントが**無効**状態の場合、リージョンの照合プロセスの対象には含まれません。 この動作は、エンドポイントが**無効**状態のときの、入れ子になったエンドポイントの種類にも該当します。
- クエリが、そのプロファイルでマッピングされていない地理的リージョンから送信されている場合、Traffic Manager は NODATA 応答を返します。 そのため、リージョン "**世界**" が割り当てられた 1 つのエンドポイント (子プロファイル内に少なくとも 2 つのエンドポイントを含む入れ子になった種類が理想的) で地理的ルーティングを使用することを強くお勧めします。 これにより、リージョンにマップされていない IP アドレスも確実に処理されるようになります。

[Traffic Manager の動作のしくみ](traffic-manager-how-it-works.md)で説明したように、Traffic Manager は、クライアントから直接には DNS クエリを受信しません。 代わりに、DNS クエリは、クライアントが使用するように構成された再帰 DNS サービスから受信します。 そのため、リージョンの特定に使用される IP アドレスは、クライアントの IP アドレスではなく、再帰 DNS サービスの IP アドレスになります。 実際には、この IP アドレスはクライアントにとって適切なプロキシとなります。

### <a name="faqs"></a>FAQ

* [地理的ルーティングが役立つ例を教えてください。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-geographic-routing-is-useful)

* [パフォーマンス ルーティング方法を使用するか、地理的ルーティング方法を使用するかを判断する方法を教えてください。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Traffic Manager の地理的ルーティングがサポートされる地域を教えてください。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [ユーザーがどこからクエリを実行しているのかを Traffic Manager はどのようにして判別しているのですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Traffic Manager では、どのような場合でもユーザーの正確な地理的場所を正しく特定できることが保証されていますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [地理的ルーティングでは、エンドポイントが、その構成に使用された地域と物理的に同じ地域に存在する必要があるのですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [地理的ルーティングを行うための構成がなされていないプロファイルのエンドポイントにリージョンを割り当てることはできますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [既存のプロファイルのルーティング方法を地理的ルーティングに変更しようとしたときにエラーが発生するのはなぜですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [地理的ルーティングを有効にしたプロファイルには、エンドポイントではなく、入れ子にしたプロファイルを作成することが強く推奨されているのはなぜですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [このルーティング タイプをサポートする API バージョンに制限はありますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>複数値トラフィック ルーティング方法
**複数値**トラフィック ルーティング方法では、1 回の DNS クエリ応答で複数の正常なエンドポイントを取得することができます。 これにより呼び出し元は、返されたエンドポイントが無応答であった場合でも、他のエンドポイントとの間で、クライアント側の再試行を実行することができます。 このパターンを使用すると、サービスの可用性を高めることができ、また、新しい DNS クエリで正常なエンドポイントを取得することによって生じる待ち時間を減らすことができます。 複数値ルーティング方法を使用するためには、すべてのエンドポイントが "外部" タイプで、かつ IPv4 または IPv6 アドレスとして指定されている必要があります。 このプロファイルに対するクエリが受信されると、構成可能な最大リターン数を上限として、正常なエンドポイントがすべて返されます。

### <a name="faqs"></a>FAQ

* [複数値ルーティングが役立つユース ケースを教えてください。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [複数値ルーティングを使用する場合、何個のエンドポイントが返されますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [複数値ルーティングを使用する場合、同じエンドポイントのセットが取得されますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>サブネット トラフィック ルーティング方法
**サブネット** トラフィック ルーティング方法では、特定のエンドポイントにエンド ユーザーの一連の IP アドレス範囲をマップすることができます。 以後、Traffic Manager は、該当するプロファイルの DNS クエリを受信した場合、その要求の送信元 IP アドレス (ほとんどの場合、呼び出し元によって使用されている DNS リゾルバの送信 IP アドレス) を調査して、マップ先のエンドポイントを決め、クエリの応答としてそのエンドポイントを返します。 

エンドポイントにマップされる IP アドレスは、CIDR 範囲 (例: 1.2.3.0/24) またはアドレスの範囲 (例: 1.2.3.4-5.6.7.8) として指定できます。 エンドポイントに関連付ける IP 範囲は、そのプロファイル内で一意であることが必要です。同じプロファイル内の別のエンドポイントの IP アドレス セットと重複した範囲は使用できません。
アドレス範囲を持たないエンドポイントを定義した場合、これはフォールバックとして機能し、任意の残りのサブネットからトラフィックを取得します。 フォールバック エンドポイントが含まれていない場合、Traffic Manager は任意の未定義の範囲に NODATA 応答を送信します。 そのため、フォールバック エンドポイントを定義するか、または複数のエンドポイントにまたがって想定されるすべての IP 範囲を確実に指定することを強くお勧めします。

サブネット ルーティングを使えば、特定の IP 空間から接続してきているユーザーに異なるエクスペリエンスを提供することができます。 たとえば、サブネット ルーティングを使用すると、自社のオフィスから送信されたすべての要求を異なるエンドポイントにルーティングし、そこで社内限定版のアプリをテストすることができます。 また、特定の ISP から接続してきているユーザー (たとえば、特定の ISP のブロック ユーザー) に対して異なるエクスペリエンスを提供するシナリオにも対応できます。

### <a name="faqs"></a>FAQ

* [サブネット ルーティングが役立つユース ケースを教えてください。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Traffic Manager はどのような方法でエンド ユーザーの IP アドレスを把握するのですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [サブネット ルーティングを使用する場合に IP アドレスを指定するにはどうすればよいですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [サブネット ルーティングを使用する場合にフォールバック エンドポイントを指定するにはどうすればよいですか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [サブネット ルーティング型のプロファイルでエンドポイントが無効になっている場合はどうなりますか。](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>次のステップ

[Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)




