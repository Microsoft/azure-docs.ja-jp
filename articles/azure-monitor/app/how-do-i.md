---
title: Azure Application Insights での作業 | Microsoft Docs
description: Application Insights での FAQ。
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 134089f4df8f80147182835ca8746322c1de7e50
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319254"
---
# <a name="how-do-i--in-application-insights"></a>Application Insights での作業
## <a name="get-an-email-when-"></a>電子メールの受信
### <a name="email-if-my-site-goes-down"></a>サイトがダウンした場合の電子メール
[可用性 Web テスト](./monitor-web-app-availability.md)を設定します。

### <a name="email-if-my-site-is-overloaded"></a>サイトが過負荷になっている場合の電子メール
[サーバー応答時間](../platform/alerts-log.md) の **アラート**を設定します。 1 ～ 2 秒の間のしきい値で機能する必要があります。

![サーバーの応答時間にアラートを設定する方法を示すスクリーンショット。](./media/how-do-i/030-server.png)

アプリがエラー コードを返すことによって負荷の兆候を示す場合もあります。 **[失敗した要求]** でアラートを設定します。

**[サーバーの例外]** でアラートを設定する場合は、データを表示するために [追加セットアップ](./asp-net-exceptions.md) が必要になる場合があります。

### <a name="email-on-exceptions"></a>例外での電子メール
1. [例外の監視を設定します](./asp-net-exceptions.md)
2. [アラートを設定](../platform/alerts-log.md) します

### <a name="email-on-an-event-in-my-app"></a>アプリのイベントでの電子メール
特定のイベントが発生したときに電子メールを受け取りたいものとします。 Application Insights は直接この機能を提供しませんが、 [メトリックがしきい値を超えたときにアラートを送信](../platform/alerts-log.md)できます。

アラートは [カスタム メトリック](./api-custom-events-metrics.md#trackmetric)には設定できますが、カスタム イベントには設定できません。 イベントが発生したときにメトリックを増やすコードを記述します。

```csharp
telemetry.TrackMetric("Alarm", 10);
```

または

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

アラートには 2 つの状態があるため、アラートを終了するときは低い値を送信する必要があります。

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

アラームを表示するには [メトリック エクスプローラー](../platform/metrics-charts.md) でグラフを作成します。

![メトリック エクスプローラーでアラームを表示するためのグラフを作成する方法を示すスクリーンショット。](./media/how-do-i/010-alarm.png)

メトリックが短時間中間値を超えたら発生するようにアラートを設定します。

![メトリックが短時間、中間値を超えたら発生するようにアラートを設定する方法を示すスクリーンショット。](./media/how-do-i/020-threshold.png)

平均計算期間を最小に設定します。

メトリックがしきい値を上回ったときと下回ったときの両方で、電子メールを受け取ります。

考慮すべき点:

* アラートには、"警告" と "正常" の 2 つの状態があります。 状態はメトリックを受信した場合にのみ評価されます。
* 電子メールは状態が変化したときにのみ送信されます。 これは、高い値と低い値の両方のメトリックを送信する必要がある理由です。
* アラートを評価するため、前の期間に受け取った値の平均が計算されます。 これはメトリックを受信するたびに行われるので、設定した期間より頻繁に電子メールが送信される可能性があります。
* 電子メールは "警告" と "正常" の両方で送信されるので、1 回限りのイベントを 2 つの状態として考え直すことができます。 たとえば、"ジョブ完了" イベントの代わりに、"ジョブ進行中" という状態を考え、その場合はジョブの開始時と終了時に電子メールを受け取ります。

### <a name="set-up-alerts-automatically"></a>アラートの自動設定
[Use PowerShell to create new alerts (PowerShell を使用した新しいアラートの作成)](../platform/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>PowerShell を使用した Application Insights の管理
* [新しいリソースの作成に関するページ](./create-new-resource.md#creating-a-resource-automatically)
* [新しいアラートの作成に関するページ](../platform/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>異なるバージョンのテレメトリを分離する

* アプリの複数のロール: 単一の Application Insights リソースを使用し、[cloud_Rolename](./app-map.md) でフィルター処理します。
* 開発、テスト、およびリリースのバージョンの分離: Application Insights の異なるリソースを使用します。 web.config からインストルメンテーション キーを選択します。[詳細情報](./separate-resources.md)
* ビルド バージョンのレポート: テレメトリ初期化子を使用してプロパティを追加します。 [詳細情報](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>バックエンド サーバーとデスクトップ アプリを監視する
[Windows Server SDK モジュールを使用する](./windows-desktop.md)。

## <a name="visualize-data"></a>データの視覚化
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>複数のアプリケーションのメトリックを使用したダッシュボード
* [Metric エクスプローラー](../platform/metrics-charts.md)でグラフをカスタマイズし、お気に入りとして保存します。 Azure ダッシュボードにピン留めします。

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>他のソースや Application Insights からのデータのあるダッシュボード
* [テレメトリを Power BI にエクスポートします](./export-power-bi.md)。

または

* SharePoint をダッシュボードとして使用して、SharePoint Web パーツにデータを表示します。 [連続エクスポートと Stream Analytics を使用して SQL にエクスポートします](./code-sample-export-sql-stream-analytics.md)。  PowerView を使用してデータベースを確認し、PowerView の SharePoint Web パーツを作成します。

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>匿名ユーザーまたは認証済みユーザーのフィルター処理
ユーザーがサインインしたら、[認証されたユーザー ID](./api-custom-events-metrics.md#authenticated-users) を設定できます。 (自動的には設定されません。)

この場合は、次のいずれかの操作を行うことができます。

* 特定のユーザー ID で検索

![特定のユーザー ID で検索するオプションを示すスクリーンショット。](./media/how-do-i/110-search.png)

* 匿名ユーザーまたは認証済みユーザーに対するメトリックのフィルター処理

![匿名ユーザーまたは認証済みユーザーに対するメトリックのフィルター処理を示すスクリーンショット。](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>プロパティ名または値を変更する
[フィルター](./api-filtering-sampling.md#filtering)を作成します。 これにより、アプリから Application Insights にテレメトリが送信される前に、テレメトリの変更またはフィルター処理ができるようになります。

## <a name="list-specific-users-and-their-usage"></a>特定のユーザーとその使用状況を一覧表示する
[特定のユーザーだけを検索](#search-specific-users)する場合は、[認証されたユーザー ID](./api-custom-events-metrics.md#authenticated-users) を設定できます。

ユーザーが表示するページやログインの頻度についてユーザーを一覧表示するには、2 つのオプションがあります。

* [認証されたユーザー ID を設定](./api-custom-events-metrics.md#authenticated-users)し、[データベースにエクスポート](./code-sample-export-sql-stream-analytics.md)します。そこで適切なツールを使用して、ユーザー データを分析します。
* ユーザー数が少ない場合は、メトリック値またはイベント名として関心のあるデータを使用し、ユーザー ID をプロパティとして設定して、カスタム イベントまたはメトリックを送信します。 ページ ビューを分析するには、標準の JavaScript trackPageView 呼び出しを置き換えます。 サーバー側のテレメトリを分析するには、テレメトリ初期化子を使用して、ユーザー ID をすべてのサーバー テレメトリに追加します。 次に、ユーザー ID を基に、メトリックや検索結果をフィルター処理および細分化します。

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>アプリから Application Insights へのトラフィックの削減
* [ApplicationInsights.config](./configuration-with-applicationinsights-config.md)で不要なモジュール (パフォーマンス カウンター コレクターなど) を無効にします。
* SDK で、 [サンプリングとフィルター処理](./api-filtering-sampling.md) を使用します。
* Web ページで、各ページ ビューで報告される AJAX 呼び出しの数を制限します。 `instrumentationKey:...` の後のスクリプト スニペットに、`,maxAjaxCallsPerView:3` (または適切な数値) を挿入します。
* [TrackMetric](./api-custom-events-metrics.md#trackmetric)を使用する場合は、結果を送信する前にメトリック値のバッチの集計を計算します。 これに対して提供される TrackMetric() のオーバーロードを考慮します。

詳細については、 [価格とクォータ](./pricing.md)に関するページを参照してください。

## <a name="disable-telemetry"></a>遠隔測定を無効にする
サーバーからテレメトリの収集と送信を **動的に停止および開始** するには:

### <a name="aspnet-classic-applications"></a>ASP.NET Classic アプリケーション

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>他のアプリケーション
コンソールまたは ASP.NET Core アプリケーションで `TelemetryConfiguration.Active` シングルトンを使用することはお勧めしません。
ご自分で `TelemetryConfiguration` インスタンスを作成した場合は、`DisableTelemetry` を `true` に設定します。

ASP.NET Core アプリケーションの場合は、[ASP.NET Core の依存関係の挿入](/aspnet/core/fundamentals/dependency-injection/)を使用して `TelemetryConfiguration` インスタンスにアクセスできます。 詳細については、「[Application Insights for ASP.NET Core applications (ASP.NET Core アプリケーション用の Application Insights)](./asp-net-core.md)」を参照してください。

## <a name="disable-selected-standard-collectors"></a>選択されている標準のコレクターを無効にする
標準のコレクター (パフォーマンス カウンター、HTTP 要求、依存関係など) を無効にすることができます

* **ASP.NET アプリケーション** - [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 内の該当する行を無効にするか、コメント アウトします
* **ASP.NET Core アプリケーション** - [ASP.NET Core 用の Application Insights](./asp-net-core.md#configuring-or-removing-default-telemetrymodules) に関する記事のテレメトリ モジュールの構成オプションに従います

## <a name="view-system-performance-counters"></a>システム パフォーマンス カウンターの表示
メトリックス エクスプローラーに表示できるメトリックには、一連のシステム パフォーマンス カウンターがあります。 事前定義された **サーバー** というブレードに、それらのいくつかが表示されます。

![Application Insights リソースを開いて、[サーバー] をクリック](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>パフォーマンス カウンターのデータが表示されない場合
* **IIS サーバー** の場合。 [Status Monitor をインストール](./monitor-performance-live-website-now.md)します。
* **Azure Web サイト** - パフォーマンス カウンターはまだサポートしていません。 Azure Web サイトのコントロール パネルの標準パーツとして取得できるメトリックがいくつか用意されています。
* **Unix サーバー** - [collectd をインストール](./java-collectd.md)します。

### <a name="to-display-more-performance-counters"></a>表示するパフォーマンス カウンターの数を増やすには
* 最初に、 [新しいグラフを追加](../platform/metrics-charts.md) し、提供されている基本的なセットにカウンターが含まれているかどうかを確認します。
* 含まれていない場合は、[パフォーマンス カウンター モジュールによって収集されたセットにカウンターを追加](./performance-counters.md)します。

