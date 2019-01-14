---
title: Azure Application Insights のカスタム メトリックと診断を使用した Live Metrics Stream | Microsoft Docs
description: カスタム メトリックを使用して Web アプリをリアルタイムで監視し、エラー、トレース、イベントのライブ フィードを使用して問題を診断します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/04/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 403906a60d16a478dffd313b45aa1ce24e42196a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119236"
---
# <a name="live-metrics-stream-monitor--diagnose-with-1-second-latency"></a>Live Metrics Stream:1 秒の待機時間での監視と診断

[Application Insights](../../azure-monitor/app/app-insights-overview.md) の Live Metrics Stream を使用して、実稼働中の Web アプリケーションの心臓部を調べます。 メトリックとパフォーマンス カウンターを選択してフィルタリングし、サービスに支障をきたすことなく、リアルタイムで監視します。 失敗した要求と例外のサンプルからスタック トレースを検査します。 Live Metrics Stream は、[プロファイラー](../../azure-monitor/app/profiler.md)、[スナップショット デバッガー](../../azure-monitor/app/snapshot-debugger.md)、[パフォーマンス テスト](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests)とともに、実稼働中の Web サイト向けの強力で非侵襲的な診断ツールを提供します。

Live Metrics Stream を使用すると、次のことが可能になります。

* 修正がリリースされている間に、パフォーマンスと失敗の数を確認して、修正を検証します。
* テスト負荷の影響を監視し、問題をライブで診断します。 
* 監視するメトリックを選択してフィルタリングすることにより、特定のテスト セッションに焦点を当てたり、既知の問題を除外したりできます。
* 発生時に例外トレースを取得します。
* フィルターを試して、最も関連性の高い KPI を検索します。
* すべての Windows パフォーマンス カウンターをライブで監視します。
* 問題が発生しているサーバーを簡単に特定し、すべての KPI/ライブ フィードをフィルター処理してそのサーバーだけに絞り込むことができます。

[![Live Metrics Stream 動画](./media/live-stream/youtube.png)](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

## <a name="get-started"></a>作業開始

1. ASP.NET Web アプリケーションまたは [Windows Server アプリケーション](../../azure-monitor/app/windows-services.md) に [Application Insights をインストール](../../azure-monitor/app/asp-net.md)していない場合は、今すぐインストールします。 
2. Application Insights パッケージの**最新バージョンに更新**します。 Visual Studio でプロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。 **[更新プログラム]** タブを開き、**[リリース前のパッケージを含める]** をオンにし、すべての Microsoft.ApplicationInsights.* パッケージを選択します。

    アプリケーションを再デプロイします。

3. [Azure ポータル](https://portal.azure.com)で、アプリの Application Insights リソースを開いてから、Live Stream を開きます。

4. フィルターに顧客名などの機密データを使用する場合は、[コントロール チャネルを保護](#secure-the-control-channel)します。


![概要ブレードで、[Live Stream (ライブ ストリーム)] をクリックします](./media/live-stream/live-stream-2.png)

### <a name="no-data-check-your-server-firewall"></a>データが表示されない場合 サーバーのファイアウォールを確認

サーバーのファイアウォールで、[Live Metrics Stream の発信ポート](../../azure-monitor/app/ip-addresses.md#outgoing-ports)が開いているか確認します。 


## <a name="how-does-live-metrics-stream-differ-from-metrics-explorer-and-analytics"></a>Live Metrics Stream が メトリックス エクスプローラーや Analytics と異なる点

| |Live Stream | メトリックス エクスプローラーと Analytics |
|---|---|---|
|待機時間|1 秒以内に表示されるデータ|数分間で集計|
|リテンション期間なし|データは、グラフに表示されている間は保持され、その後破棄されます|[データは 90 日間保持](../../azure-monitor/app/data-retention-privacy.md#how-long-is-the-data-kept)|
|オン デマンド|Live Metrics を開いている間、データはストリーミングされます|SDK がインストールされて有効になるたびに、データが送信されます|
|無料|Live Stream データ用の料金は発生しません|[価格](../../azure-monitor/app/pricing.md)設定の対象
|サンプリング|選択したすべてのメトリックとカウンターが送信されます。 失敗やスタック トレースがサンプリングされます。 TelemetryProcessors は適用されません。|イベントが[サンプリング](../../azure-monitor/app/api-filtering-sampling.md)されることがあります|
|コントロール チャネル|フィルターの制御シグナルが SDK に送信されます。 [このチャネルをセキュリティで保護する](#secure-channel)ことをお勧めします。|通信はポータルへの一方向です|


## <a name="select-and-filter-your-metrics"></a>メトリックの選択とフィルタリング

(最新の SDK を使用する従来の ASP.NET アプリで利用可能。)

ポータルで Application Insights Telemetry に任意のフィルターを適用して、カスタム ライブ KPI を監視できます。 グラフをマウスでポイントしたときに表示されるフィルター コントロールをクリックします。 次のグラフは、URL 属性と期間属性にフィルターを適用して、カスタム要求数 KPI をプロットしています。 [ストリームのプレビュー] セクションでフィルターを検証します。このセクションには、指定した条件といずれかの時点で一致するテレメトリのライブ フィードが表示されます。 

![カスタム要求 KPI](./media/live-stream/live-stream-filteredMetric.png)

Count 以外の値を監視できます。 オプションはストリームの種類によって異なります。ストリームの種類には、任意の Application Insights Telemetry (要求、依存関係、例外、トレース、イベント、またはメトリック) を指定できます。 オプションには、独自の[カスタム測定](../../azure-monitor/app/api-custom-events-metrics.md#properties)を指定できます。

![値のオプション](./media/live-stream/live-stream-valueoptions.png)

Application Insights Telemetry だけでなく、Windows パフォーマンス カウンターを監視することもできます。ストリーム オプションから Windows パフォーマンス カウンターを選択し、パフォーマンス カウンターの名前を指定します。

ライブ メトリックは 2 つのポイントで集計されます。具体的には、サーバーごとにローカルで集計された後、すべてのサーバーにわたって集計されます。 どちらについても、それぞれのドロップダウンで他のオプションを選択することで既定値を変更できます。

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>サンプル テレメトリ:カスタム ライブ診断イベント
既定では、イベントのライブ フィードには失敗した要求と依存関係呼び出し、例外、イベント、トレースのサンプルが表示されます。 フィルター アイコンをクリックすると、任意の時点で適用された条件が表示されます。 

![既定のライブ フィード](./media/live-stream/live-stream-eventsdefault.png)

メトリックと同様に、Application Insights Telemetry の種類のいずれかに任意の条件を指定できます。 この例では、特定の要求エラー、トレース、イベントを選択しています。 また、すべての例外と依存関係エラーも選択しています。

![カスタム ライブ フィード](./media/live-stream/live-stream-events.png)

注:現時点では、例外メッセージ ベースの条件には、最も外側の例外メッセージを使用します。 前の例では、"クライアントが切断されました。" という内部例外メッセージ ("<--" 区切り記号の後) が示されている害のない例外を除外するために、 "Error reading request content (要求内容の読み取りエラー)" が含まれていないメッセージという条件を使用します。

ライブ フィードの項目をクリックして詳細を表示します。 フィードを一時停止するには、**[一時停止]** をクリックするか、下にスクロールするか、または項目をクリックします。 スクロールして上部に戻るか、一時停止されている間に収集された項目のカウンターをクリックすると、ライブ フィードが再開されます。

![サンプリングされたライブ エラー](./media/live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>サーバー インスタンスによるフィルター処理

特定のサーバー ロール インスタンスを監視する場合は、サーバーでフィルター処理できます。

![サンプリングされたライブ エラー](./media/live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>SDK の要件
カスタムの Live Metrics Stream は、バージョン 2.4.0-beta2 以降の [Application Insights SDK for web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) で使用できます。 NuGet パッケージ マネージャーで [リリース前のパッケージを含める] を必ず選択してください。

## <a name="secure-the-control-channel"></a>コントロール チャネルの保護
指定したカスタム フィルター条件は、Application Insights SDK の Live Metrics コンポーネントに送信されます。 フィルターに顧客 ID などの機密情報が含まれている可能性があります。 インストルメンテーション キーに加え、シークレット API キーを使用してチャネルをセキュリティで保護できます。
### <a name="create-an-api-key"></a>API キーを作成する

![API キーの作成](./media/live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>API キーを構成に追加する

### <a name="classic-aspnet"></a>クラシック ASP.NET

applicationinsights.config ファイルで、AuthenticationApiKey を QuickPulseTelemetryModule に追加します。
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add>

```
または、コードで QuickPulseTelemetryModule に API キーを設定します。

```csharp
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Extensibility;

             TelemetryConfiguration configuration = new TelemetryConfiguration();
            configuration.InstrumentationKey = "YOUR-IKEY-HERE";

            QuickPulseTelemetryProcessor processor = null;

            configuration.TelemetryProcessorChainBuilder
                .Use((next) =>
                {
                    processor = new QuickPulseTelemetryProcessor(next);
                    return processor;
                })
                        .Build();

            var QuickPulse = new QuickPulseTelemetryModule()
            {

                AuthenticationApiKey = "YOUR-API-KEY"
            };
            QuickPulse.Initialize(configuration);
            QuickPulse.RegisterTelemetryProcessor(processor);
            foreach (var telemetryProcessor in configuration.TelemetryProcessors)
                {
                if (telemetryProcessor is ITelemetryModule telemetryModule)
                    {
                    telemetryModule.Initialize(configuration);
                    }
                }

```

### <a name="aspnet-core-requires-application-insights-aspnet-core-sdk-230-beta-or-greater"></a>ASP.NET Core (Application Insights ASP.NET Core SDK 2.3.0-beta 以降が必要)

startup.cs ファイルを次のように変更します。

最初に以下を追加します。

``` C#
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
```

その後、ConfigureServices メソッド内で以下を追加します。

``` C#
services.ConfigureTelemetryModule<QuickPulseTelemetryModule> ((module, o) => module.AuthenticationApiKey = "YOUR-API-KEY-HERE");
```


ただし、接続されているすべてのサーバーを認識し、信頼している場合は、認証済みチャネルなしにカスタム フィルターを試すことができます。 このオプションは 6 か月間使用できます。 このオーバーライドは、新しいセッションごとに 1 回、または新しいサーバーがオンラインになったときに必要になります。

![Live Metrics の認証オプション](./media/live-stream/live-stream-auth.png)

>[!NOTE]
>フィルター条件に CustomerID などの機密情報を入力する前に、認証済みチャネルを設定することを強くお勧めします。
>

## <a name="generating-a-performance-test-load"></a>パフォーマンス テスト負荷の生成

負荷増加の影響を確認するには、パフォーマンス テスト ブレードを使用します。 これは、複数の同時ユーザーからの要求をシミュレートします。 単一の URL の "手動テスト" (ping テスト) を実行することも、アップロードする[複数ステップの Web パフォーマンス テスト](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests)を (可用性テストと同じ方法で) 実行することもできます。

> [!TIP]
> パフォーマンス テストを作成したあと、テストと Live Stream ブレードを別々のウィンドウで開きます。 キューに登録済みのパフォーマンス テストの開始を確認でき、同時にライブ ストリームを見ることができます。
>


## <a name="troubleshooting"></a>トラブルシューティング

データが表示されない場合 保護されているネットワークにアプリケーションが存在する場合:Live Metrics Stream では他の Application Insights Telemetry とは異なる IP アドレスを使用します。 [これらの IP アドレス](../../azure-monitor/app/ip-addresses.md)がファイアウォールで開いていることを確認してください。



## <a name="next-steps"></a>次の手順
* [Application Insights による使用状況の監視](../../azure-monitor/app/usage-overview.md)
* [診断検索の使用](../../azure-monitor/app/diagnostic-search.md)
* [プロファイラー](../../azure-monitor/app/profiler.md)
* [スナップショット デバッガー](../../azure-monitor/app/snapshot-debugger.md)
