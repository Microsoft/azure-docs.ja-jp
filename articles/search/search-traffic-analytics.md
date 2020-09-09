---
title: 検索トラフィック分析用のテレメトリ
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search の検索トラフィックの分析を有効にし、Application Insights を使用してテレメトリとユーザーが開始したイベントを収集し、Power BI レポート上で結果を分析します。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 1e7f832faffc09cb7bbbcca73763b09f58cbb412
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019795"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>検索トラフィック分析用のテレメトリ データを収集する

検索トラフィックの分析とは、ユーザーが開始したクリック イベントやキーボード入力など、Azure Cognitive Search アプリケーションのユーザー操作に関するテレメトリ収集用のパターンのことです。 この情報を使用すると、頻度の高い検索用語、クリックスルー率、および 0 件の結果を生成するクエリ入力など、検索ソリューションの有効性を判定できます。

このパターンでは、[Application Insights](../azure-monitor/app/app-insights-overview.md) ([Azure Monitor](../azure-monitor/index.yml) の 1 つの機能) 上で依存関係を取得して、ユーザー データを収集します。 それには、この記事で説明されているように、クライアント コードにインストルメンテーションを追加する必要があります。 最後に、データを分析するためのレポート メカニズムが必要になります。 Power BI が推奨されますが、アプリケーション ダッシュボードや、Application Insights に接続される任意のツールを使用できます。

> [!NOTE]
> この記事で説明するパターンは、高度なシナリオと、クライアントに追加するコードによって生成されるクリックストリーム データを対象としています。 その一方、サービス ログは設定が簡単で、さまざまなメトリックを提供できます。また、ポータルで実行可能で、コードは必要ありません。 すべてのシナリオでログ記録を有効にすることをお勧めします。 詳細については、[ログ データの収集と分析](search-monitor-logs.md)に関するページを参照してください。

## <a name="identify-relevant-search-data"></a>関連する検索データを識別する

検索トラフィックの分析に役立つメトリックを得るには、検索アプリケーションのユーザーからのいくつかのシグナルをログに記録する必要があります。 これらのシグナルは、ユーザーが興味を持っていたり関連があると考えているコンテンツを示しています。 検索トラフィックの分析の場合、それには以下のものが含まれます。

+ ユーザーによって生成された検索イベント:ユーザーによって開始された検索クエリだけが対象です。 ファセット、追加コンテンツ、または内部情報の設定に使用される検索要求は重要ではありません。これらの要求は結果を歪曲し、偏った結果を招きます。

+ ユーザーによって生成されたクリック イベント:検索結果ページでは、クリック イベントは一般に、ドキュメントが特定の検索クエリの適切な結果であることを意味します。

検索やクリックのイベントを関連付け ID とリンクすることで、アプリケーションの検索機能がどれほど適切に実行されているかをより深く理解できるようになります。

## <a name="add-search-traffic-analytics"></a>検索トラフィックの分析を追加する

Azure Cognitive Search サービスの[ポータル](https://portal.azure.com) ページ上の [検索トラフィックの分析] ページに、このテレメトリ パターンに従うためのチート シートが含まれています。 このページからは、Application Insights リソースの選択または作成、インストルメンテーション キーの取得、ソリューションに適合させることができるスニペットのコピー、パターンに反映されたスキーマに基づいて構築された Power BI レポートのダウンロードを行うことができます。

![ポータルの [検索トラフィックの分析] ページ](media/search-traffic-analytics/azuresearch-trafficanalytics.png "ポータルの [検索トラフィックの分析] ページ")

## <a name="1---set-up-application-insights"></a>1 - Application Insights を設定する

既存の Application Insights リソースを選択するか、まだない場合は[リソースを作成する](../azure-monitor/app/create-new-resource.md)必要があります。 [検索トラフィックの分析] ページを使用する場合は、アプリケーションが Application Insights に接続するために必要なインストルメンテーション キーをコピーできます。

Application Insights リソースがある場合は、[サポートされている言語とプラットフォームに関する指示](../azure-monitor/app/platforms.md)に従って、アプリを登録することができます。 登録では、単純に、インストルメンテーション キーを Application Insights からコードに追加します。これにより、関連付けが設定されます。 キーは、ポータルで、または既存のリソースを選択するときに [検索トラフィックの分析] ページから見つけることができます。

Visual Studio プロジェクトの一部の種類に対して機能するショートカットは、以下の手順で反映されます。 数回クリックするだけで、リソースを作成してアプリを登録します。

1. Visual Studio と ASP.NET での開発の場合、ソリューションを開き、 **[プロジェクト]**  >  **[Application Insights Telemetry の追加]** と選択します。

1. **[開始]** をクリックします。

1. Microsoft アカウント、Azure サブスクリプション、および Application Insights リソース (新しいリソースが既定値です) を指定することでアプリを登録します。 **[登録]** をクリックします。

この時点で、アプリケーションはアプリケーション監視用に設定されます。これは、既定のメトリックを使用してすべてのページ読み込みが追跡されることを意味します。 前の手順の詳細については、「[Application Insights のサーバー側テレメトリを有効にする](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)」を参照してください。

## <a name="2---add-instrumentation"></a>2 - インストルメンテーションを追加する

この手順では、前の手順で作成した Application Insights リソースを使用して、独自の検索アプリケーションをインストルメント化します。 このプロセスには 4 つの手順があり、テレメトリ クライアントの作成から始まりまる。

### <a name="step-1-create-a-telemetry-client"></a>手順 1:テレメトリ クライアントを作成する

Application Insights にイベントを送信するオブジェクトを作成します。 サーバー側のアプリケーション コードまたはブラウザーで実行されているクライアント側コードに、インストルメンテーションを追加できます。ここでは、C# および JavaScript のバリアントとして表現されています。その他の言語については、[サポートされているプラットフォームとフレームワーク](../azure-monitor/app/platforms.md)の完全な一覧を参照してください。 必要な深さの情報が得られる方法を選択します。

サーバー側のテレメトリでは、アプリケーション層 (クラウドで Web サービスとして実行されるアプリケーションや、企業ネットワーク上のオンプレミス アプリとして実行されるアプリケーションなど) でメトリックをキャプチャします。 サーバー側のテレメトリでは、検索とクリックのイベント、結果内のドキュメントの位置、およびクエリ情報がキャプチャされますが、データ コレクションのスコープは、そのレイヤーで入手可能なすべての情報となります。

クライアントでは、クエリ入力の操作、ナビゲーションの追加、コンテキスト (クエリがホーム ページから開始されたか、製品ページから開始されたか、など) の付加を行うコードを追加できます。 お使いのソリューションがこれに該当する場合は、テレメトリに追加の詳細が反映されるように、クライアント側のインストルメンテーションも選択できます。 この追加の詳細情報を収集する方法は、このパターンの範囲を超えていますが、詳細な手順については、「[Web ページ向けの Application Insights](../azure-monitor/app/javascript.md#explore-browserclient-side-data)」で確認できます。 

**C# を使用する**

C# の場合、ASP.NET のプロジェクトであれば、アプリケーション構成 (appsettings.json など) の中に **InstrumentationKey** があります。 キーの場所が不確かな場合は、登録の手順を参照し直してください。

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**JavaScript を使用する**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>手順 2:関連付けのための検索 ID を要求する

検索要求をクリックに関連付けるには、これら 2 つの異なるイベントを関連付ける関連付け ID を保持している必要があります。 HTTP ヘッダーを使って検索 ID を要求すると、Azure Cognitive Search から検索 ID が提供されます。

検索 ID があれば、Azure Cognitive Search によって要求自体に対して出力されるメトリックと、Application Insights でログに記録しているカスタム メトリックとの関連付けを行うことができます。  

**C# を使用する**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript を使用する (REST API の呼び出し)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>手順 3:検索イベントをログに記録する

ユーザーによって検索要求が発行されるたびに、Application Insights のカスタム イベントに関する次のスキーマを使用して、検索要求を検索イベントとしてログに記録する必要があります。 ユーザーが生成する検索クエリだけをログに記録してください。

+ **SearchServiceName**: (文字列) 検索サービス名
+ **SearchId**: (GUID) 検索クエリの一意識別子 (検索応答で提供されます)
+ **IndexName**: (文字列) クエリの実行対象となる検索サービス インデックス
+ **QueryTerms**: (文字列) ユーザーによって入力された検索語句
+ **ResultCount**: (整数) 返されたドキュメントの数 (検索応答で提供されます)
+ **ScoringProfile**: (文字列) 使用されるスコアリング プロファイルの名前 (ある場合)

> [!NOTE]
> 検索クエリに $count=true を追加して、ユーザーによって生成されたクエリの数を要求します。 詳細については、[ドキュメントの検索 (REST)](/rest/api/searchservice/search-documents#counttrue--false) に関するページを参照してください。
>

**C# を使用する**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript を使用する**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>手順 4:クリック イベントをログに記録する

ユーザーがドキュメントをクリックするたびに、それをシグナルとして、検索分析用にログに記録する必要があります。 Application Insights のカスタム イベントで次のスキーマを使用して、これらのイベントをログに記録します。

+ **ServiceName**: (文字列) 検索サービス名
+ **SearchId**: (GUID) 関連する検索クエリの一意識別子
+ **DocId**: (文字列) ドキュメント識別子
+ **Position**: (整数) 検索結果ページのドキュメントの順位

> [!NOTE]
> Position は、アプリケーションでの基本的な順序を指します。 比較できるように、この数値は常に同じであれば自由に設定できます。
>

**C# を使用する**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript を使用する**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Power BI で分析する

アプリをインストルメント化し、アプリケーションが Application Insights に正しく接続されていることを確認したら、Power BI Desktop でデータを分析するための定義済みのレポート テンプレートをダウンロードします。 このレポートには、検索トラフィックの分析のためにキャプチャされた追加データの分析に役立つ定義済みのグラフとテーブルが含まれています。

1. Azure Cognitive Search ダッシュボードの左側のナビゲーション ウィンドウで、 **[設定]** 下の **[検索トラフィックの分析]** をクリックします。

1. **[検索トラフィックの分析]** ページのステップ 3 で、 **[Get Power BI Desktop]\(Power BI Desktop の取得\)** をクリックして Power BI をインストールします。

   ![Power BI レポートを取得する](./media/search-traffic-analytics/get-use-power-bi.png "Power BI レポートを取得する")

1. 同じページで、 **[Power BI レポートのダウンロード]** をクリックします。

1. このレポートが Power BI Desktop で開かれ、Application Insights に接続して資格情報を指定するよう求められます。 接続情報は、Application Insights リソースの Azure portal ページで見つけることができます。 資格情報には、ポータルのサインインに使用するのと同じユーザー名とパスワードを指定します。

   ![Application Insights に接続する](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights に接続する")

1. **[読み込み]** をクリックします。

レポートには、検索のパフォーマンスと関連性を向上させるために、多くの情報に基づいた意思決定を行う上で役立つグラフとテーブルが含まれています。

メトリックには、次のものが含まれています。

+ 検索ボリュームおよび最も一般的な語句とドキュメントのペア: クリック数で並べ替えられた、結果的に同じドキュメントがクリックされた語句。
+ クリックされていない検索語句: クリックが登録されていない上位クエリの語句

次のスクリーンショットは、すべてのスキーマ要素を使用した場合に、組み込みのレポートがどのように表示されるかを示しています。

![Azure Cognitive Search の Power BI ダッシュボード](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure Cognitive Search の Power BI ダッシュボード")

## <a name="next-steps"></a>次のステップ

検索アプリケーションをインストルメント化して、検索サービスに関する有益で洞察に富んだデータを取得します。

[Application Insights](../azure-monitor/app/app-insights-overview.md) では詳しい情報が見つかります。また、さまざまなサービス レベルについて詳しくは、[価格に関するページ](https://azure.microsoft.com/pricing/details/application-insights/)をご覧ください。

レポートの作成についての詳細。 「[Power BI Desktop の概要](/power-bi/fundamentals/desktop-getting-started)」を参照してください。