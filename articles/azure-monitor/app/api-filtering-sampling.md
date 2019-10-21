---
title: Azure Application Insights SDK におけるフィルター処理および前処理 | Microsoft Docs
description: テレメトリが Application Insights ポータルに送信される前に、SDK でフィルター処理またはデータへのプロパティの追加を行うためのテレメトリ プロセッサおよびテレメトリ初期化子を記述します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 095d539404412d34c66201646f6134ff740f86b7
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299274"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK におけるテレメトリのフィルター処理および前処理

Application Insights SDK のプラグインを作成および構成して、Application Insights サービスに送信される前のテレメトリのエンリッチと処理の方法をカスタマイズできます。

* [サンプリング](sampling.md) は、統計に影響を与えることなくテレメトリの量を削減します。 関連するデータ ポイントが一緒に保持されるので、問題の診断時にそれらのデータ ポイント間を移動することができます。 ポータルでは、サンプリングを補正するために合計数が乗算されます。
* テレメトリ プロセッサを使ったフィルター処理により、サーバーに送信される前のテレメトリから、不要な情報を SDK で取り除くことができます。 たとえば、ロボットからの要求を除外することでテレメトリの量を削減することができます。 フィルター処理は、トラフィックを削減するうえでは、サンプリングよりも基本的な方法です。 フィルター処理を使用すると、送信する内容をより細かく制御することができます。ただし、統計に影響を及ぼすことになるので注意が必要です (たとえば、すべての成功した要求を除外する場合など)。
* アプリから送信されるテレメトリ (標準モジュールからのテレメトリも含む) に対し、[テレメトリ初期化子でプロパティを追加したりプロパティに変更を加えたり](#add-properties)することができます。 たとえば、算出値や、ポータルでデータをフィルター処理するのに使用できるバージョン番号を追加することが可能です。
* [SDK API](../../azure-monitor/app/api-custom-events-metrics.md) は、カスタム イベントとメトリックの送信に使用します。

開始する前に次の操作を実行してください。

* アプリケーションに適した SDK をアプリにインストールします ( [ASP.NET](asp-net.md)、[ASP.NET Core](asp-net-core.md)、[Non HTTP/Worker for .NET/.NET Core](worker-service.md)、[Java](../../azure-monitor/app/java-get-started.md))。

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>フィルター:ITelemetryProcessor

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

## <a name="add-properties-itelemetryinitializer"></a>プロパティの追加:ITelemetryInitializer

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
