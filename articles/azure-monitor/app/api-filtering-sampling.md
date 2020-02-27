---
title: Azure Application Insights SDK におけるフィルター処理および前処理 | Microsoft Docs
description: テレメトリが Application Insights ポータルに送信される前に、SDK でフィルター処理またはデータへのプロパティの追加を行うためのテレメトリ プロセッサおよびテレメトリ初期化子を記述します。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/23/2016
ms.openlocfilehash: dcc71739d859fb9cf4e03e5d3540d3cdbc69ac49
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031545"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK におけるテレメトリのフィルター処理および前処理

Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのエンリッチと処理の方法をカスタマイズできます。

* [サンプリング](sampling.md) は、統計に影響を与えることなくテレメトリの量を削減します。 関連するデータ ポイントが一緒に保持されるので、問題の診断時にそれらのデータ ポイント間を移動することができます。 ポータルでは、サンプリングを補正するために合計数が乗算されます。
* テレメトリ プロセッサを使ったフィルター処理により、サーバーに送信される前のテレメトリから、不要な情報を SDK で取り除くことができます。 たとえば、ロボットからの要求を除外することでテレメトリの量を削減することができます。 フィルター処理は、トラフィックを削減するうえでは、サンプリングよりも基本的な方法です。 フィルター処理を使用すると、送信する内容をより細かく制御することができます。ただし、統計に影響を及ぼすことになるので注意が必要です (たとえば、すべての成功した要求を除外する場合など)。
* アプリから送信されるテレメトリ (標準モジュールからのテレメトリも含む) に対し、[テレメトリ初期化子でプロパティを追加したりプロパティに変更を加えたり](#add-properties)することができます。 たとえば、算出値や、ポータルでデータをフィルター処理するのに使用できるバージョン番号を追加することが可能です。
* [SDK API](../../azure-monitor/app/api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

開始する前に次の操作を実行してください。

* アプリケーションに適した SDK をインストールします。[ASP.NET](asp-net.md)、[ASP.NET Core](asp-net-core.md)、[Non HTTP/Worker for .NET/.NET Core](worker-service.md)、[Java](../../azure-monitor/app/java-get-started.md)、または [JavaScript](javascript.md)

<a name="filtering"></a>

## <a name="filtering"></a>Filtering

この手法では、テレメトリ ストリームに含める内容またはテレメトリ ストリームから除外する内容を直接制御できます。 フィルター処理を使用すると、Application Insights への送信対象からテレメトリ項目を除外することができます。 フィルター処理はサンプリングと組み合わせて使用することも別々に使用することもできます。

テレメトリのフィルター処理を行うには、テレメトリ プロセッサを記述し、それを `TelemetryConfiguration` に登録します。 どのテレメトリもこのプロセッサを通過します。テレメトリをストリームから除外するように選択することも、チェーン内の次のプロセッサに与えることもできます。 これには、HTTP 要求コレクターや依存関係コレクターなどの標準的なモジュールのテレメトリに加えて、自身で追跡したテレメトリも含まれます。 たとえば、ロボットからの要求や成功した依存関係の呼び出しについてのテレメトリをフィルターで除外できます。

> [!WARNING]
> プロセッサを使用して SDK から送信されるテレメトリをフィルター処理すると、ポータルに表示される統計にゆがみが生じ、関連項目を追跡するのが困難になる可能性があります。
>
> 代わりに、 [サンプリング](../../azure-monitor/app/sampling.md)の使用を検討します。
>
>

### <a name="create-a-telemetry-processor-c"></a>テレメトリ プロセッサを作成する (C#)

1. フィルターを作成するには、`ITelemetryProcessor` を実装します。

    テレメトリ プロセッサが処理のチェーンを構築することに注意してください。 テレメトリ プロセッサをインスタンス化するときは、チェーン内の次のプロセッサへの参照が与えられます。 テレメトリ データ ポイントが Process メソッドに渡されると、作業が実行され、そのチェーンの次のテレメトリ プロセッサが呼び出されます (呼び出されないこともあります)。

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. プロセッサを追加します。

**ASP.NET アプリ**: 次のスニペットを ApplicationInsights.config に挿入します。

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

名前付きのパブリック プロパティをクラス内に指定することにより、.config ファイルから文字列値を渡すことができます。

> [!WARNING]
> .config ファイル内の型名とプロパティ名をコード内のクラスおよびプロパティ名と慎重に照合してください。 存在しない型またはプロパティが .config ファイルによって参照されていると、SDK は何も通知せずにテレメトリの送信に失敗する場合があります。
>

**あるいは** 、コード内でフィルターを初期化することもできます。 適切な初期化クラス (たとえば `Global.asax.cs` の AppStart) で、プロセッサをチェーンに挿入します。

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

この時点より後に作成された TelemetryClients はプロセッサを使用します。

**ASP.NET Core またはワーカー サービス アプリ**

> [!NOTE]
> ASP.NET Core アプリケーションの場合または Microsoft.ApplicationInsights.WorkerService SDK を使用する場合、`ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用したプロセッサの追加は無効です。

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) または [WorkerService](worker-service.md#adding-telemetry-processors) を使用して作成されたアプリの場合、新しい `TelemetryProcessor` の追加は、`IServiceCollection` の `AddApplicationInsightsTelemetryProcessor` 拡張メソッドを使用して行います (以下の例を参照)。 このメソッドは、`Startup.cs` クラスの `ConfigureServices` メソッドの中で呼び出されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>フィルターの例

#### <a name="synthetic-requests"></a>人工的な要求

ボットと Web テストを除外します。 メトリックス エクスプローラーで人工的なソースを除外することもできますが、SDK 自体でそのソースをフィルター処理することにより、トラフィックとインジェストのサイズが削減されます。

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>失敗した認証

"401" 応答が返された要求を除外します。

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>リモートの依存関係の高速呼び出しを除外する

低速な呼び出しの診断のみを実行する場合は、高速呼び出しを除外します。

> [!NOTE]
> これによって、ポータルに表示される統計にゆがみが生じます。
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>依存関係の問題の診断

[このブログ](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) では、依存関係に対して定期的な ping を自動送信することによって依存関係の問題を診断するプロジェクトについて説明します。

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript Web アプリケーション

**ITelemetryInitializer を使用したフィルター処理**

1. テレメトリ初期化子のコールバック関数を作成します。 コールバック関数は、パラメーターとして `ITelemetryItem` を受け取ります。これは、処理されるイベントを指します。 このコールバックから `false` が返されると、テレメトリ項目がフィルターで除外されます。  

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
        return false;
     }
  
     return true;
   };
   ```

2. 次のテレメトリ初期化子のコールバックを追加します。

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>プロパティの追加/変更:ITelemetryInitializer


テレメトリを追加情報でエンリッチしたり、標準のテレメトリ モジュールによって設定されたテレメトリのプロパティをオーバーライドしたりするには、テレメトリ初期化子を使用します。

たとえば、Web 向けの Application Insights パッケージでは HTTP 要求に関するテレメトリが収集されます。 既定では、応答コードが 400 以上の要求はすべて失敗としてフラグが設定されます。 これに対して 400 を成功として処理する場合は、"成功" プロパティを設定するテレメトリ初期化子を指定できます。

テレメトリ初期化子を指定すると、Track*() メソッドのいずれかが呼び出されるたびに、テレメトリ初期化子も呼び出されます。 これには、標準のテレメトリ モジュールによって呼び出される `Track()` メソッドも含まれます。 通常、これらのモジュールでは、初期化子によって既に設定されているプロパティは設定されません。 テレメトリ初期化子は、テレメトリ プロセッサを呼び出す前に呼び出されます。 そのため、初期化子によって実行されたエンリッチメントはすべて、プロセッサから参照することができます。

**初期化子を定義する**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

**ASP.NET アプリ: 初期化子を読み込む**

ApplicationInsights.config で:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

*または、* Global.aspx.cs などのコード内で初期化子をインスタンス化することもできます。

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[このトピックのその他のサンプルについては、こちらをご覧ください。](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

**ASP.NET Core またはワーカー サービス アプリ: 初期化子を読み込む**

> [!NOTE]
> ASP.NET Core アプリケーションの場合または Microsoft.ApplicationInsights.WorkerService SDK を使用する場合、`ApplicationInsights.config` または `TelemetryConfiguration.Active` を使用した初期化子の追加は無効です。

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) または [WorkerService](worker-service.md#adding-telemetryinitializers) を使用して作成されたアプリの場合、新しい `TelemetryInitializer` の追加は、それを依存関係挿入コンテナーに追加することによって行います (以下の例を参照)。 これは、`Startup.ConfigureServices` メソッドで実行します。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

### <a name="java-telemetry-initializers"></a>Java テレメトリの初期化子

[Java SDK ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

次に、applicationinsights.xml ファイルにカスタム初期化子を登録します。

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
    <Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript テレメトリ初期化子
*JavaScript*

ポータルから取得した初期化コードの直後にテレメトリ初期化子を挿入します。

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

telemetryItem で使用できる非カスタム プロパティの概要については、[Application Insights エクスポート データ モデル](../../azure-monitor/app/export-data-model.md)に関するページを参照してください。

初期化子はいくつでも追加でき、また追加した順序で呼び出されます。

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python テレメトリ プロセッサ

OpenCensus Python のテレメトリ プロセッサは、エクスポート前にテレメトリを処理するために呼び出される単なるコールバック関数です。 コールバック関数は、パラメーターとして [エンベロープ](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) データ型を受け入れる必要があります。 テレメトリをエクスポートから除外するには、コールバック関数が `False` を返すようにします。 エンベロープの Azure Monitor データ型のスキーマについては、[こちら](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)を参照してください。

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
プロセッサはいくつでも追加でき、また追加した順序で呼び出されます。 1 つのプロセッサが例外をスローしても、それ以降のプロセッサは影響を受けません。

### <a name="example-telemetryinitializers"></a>TelemetryInitializers の例

#### <a name="add-custom-property"></a>カスタム プロパティを追加する

次のサンプル初期化子は、追跡されたすべてのテレメトリにカスタム プロパティを追加します。

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-cloud-role-name"></a>クラウド ロール名を追加する

次のサンプル初期化子は、追跡されたすべてのテレメトリにクラウド ロール名を設定します。

```csharp
public void Initialize(ITelemetry telemetry)
{
    if(string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor と ITelemetryInitializer

テレメトリ プロセッサとテレメトリ初期化子は何が違うのでしょうか。

* 共通する機能もあり、どちらもテレメトリのプロパティを追加したり変更したりする目的で使用できます。ただし、その目的であれば、初期化子の使用が推奨されます。
* TelemetryInitializer は常に TelemetryProcessor の前に実行します。
* TelemetryInitializers は、複数回呼び出すことができます。 慣例的に、既に設定されているプロパティは設定の対象外となります。
* TelemetryProcessor を使用すると、テレメトリ項目を完全に置換または破棄できます。
* 登録されている TelemetryInitializers はすべて、テレメトリ項目ごとに呼び出されることが保証されます。 テレメトリ プロセッサの場合、SDK で呼び出しが保証されるのは、最初のテレメトリ プロセッサのみです。 その他のプロセッサが呼び出されるかどうかは、先行するテレメトリ プロセッサによって決まります。
* 追加のプロパティでテレメトリをエンリッチしたり、既存のプロパティをオーバーライドしたりするには、TelemetryInitializers を使用します。 テレメトリから不要な情報を取り除くには、TelemetryProcessor を使用します。

## <a name="troubleshooting-applicationinsightsconfig"></a>ApplicationInsights.config のトラブルシューティング

* 完全修飾された型名とアセンブリ名が正しいことを確認します。
* applicationinsights.config ファイルが出力ディレクトリ内に存在し、最近の変更がすべて含まれていることを確認します。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [API の概要](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET リファレンス](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK コード

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>次のステップ
* [イベントおよびログを検索する](../../azure-monitor/app/diagnostic-search.md)
* [サンプリング](../../azure-monitor/app/sampling.md)
* [トラブルシューティング](../../azure-monitor/app/troubleshoot-faq.md)
