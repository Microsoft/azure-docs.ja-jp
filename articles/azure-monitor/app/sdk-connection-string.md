---
title: Azure Application Insights の接続文字列 | Microsoft Docs
description: 接続文字列の使用方法。
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 944257c93e00dca77507f26db15f7bf45fbb387e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936420"
---
# <a name="connection-strings"></a>Connection strings

## <a name="overview"></a>概要

接続文字列を使用すると、Application Insights ユーザーは 1 つの構成設定を使用でき、複数のプロキシ設定が不要になります。 監視サービスにデータを送信することを検討しているイントラネット Web サーバー、ソブリン クラウド環境またはハイブリッド クラウド環境で非常に便利です。

キーと値のペアを使用すると、ユーザーが各 Application Insights (AI) サービスまたは製品のプレフィックスとサフィックスの組み合わせを簡単に定義できます。

> [!IMPORTANT]
> 接続文字列とインストルメンテーション キーの両方を設定することはお勧めしません。 ユーザーが両方を設定した場合は、最後に設定した方が優先されます。 


## <a name="scenario-overview"></a>シナリオの概要 

これが最も影響があると考えられるお客様のシナリオ:

- ファイアウォールの例外またはプロキシのリダイレクト 

    イントラネット Web サーバーの監視が必要な場合、以前のソリューションでは、お使いの構成に個々のサービスエンド ポイントを追加するようにお客様に求めていました。 詳細については、[このページ](../faq.md#can-i-monitor-an-intranet-web-server)を参照してください。 
    接続文字列により、この作業の設定が 1 つですみ、より優れた代替方法が提供されます。 プレフィックスとサフィックスを修正するだけで、すべてのエンドポイントを適切なサービスに自動的に設定してリダイレクトすることができます。 

- ソブリン クラウド環境またはハイブリッド クラウド環境

    ユーザーは、定義された [Azure Government リージョン](../../azure-government/compare-azure-government-global-azure.md#application-insights)にデータを送信できます。
    接続文字列を使用すると、イントラネット サーバーまたはハイブリッド クラウド設定のエンドポイント設定を定義できます。 

## <a name="getting-started"></a>作業の開始

### <a name="finding-my-connection-string"></a>接続文字列を見つける

接続文字列は、Application Insights リソースの [概要] ブレードに表示されます。

![概要ブレードの接続文字列](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>スキーマ

#### <a name="max-length"></a>最大長

接続でサポートされている最大長は 4096 文字です。

#### <a name="key-value-pairs"></a>キー/値ペア

接続文字列は、キーと値のペアがセミコロンで区切られた設定のリストで構成されます: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>構文

- `InstrumentationKey` (例:00000000-0000-0000-0000-000000000000) 接続文字列は **必須**フィールドです。
- `Authorization` (例: ikey) (現時点では ikey 認証のみがサポートされているため、この設定は省略可能です)。
- `EndpointSuffix` (例: applicationinsights.azure.cn) エンドポイント サフィックスを設定すると、接続先の Azure クラウドが SDK に指示されます。 SDK により、個々のサービスに対してエンドポイントの残りの部分がアセンブルされます。
- 明示的なエンドポイント。
  どのサービスも、接続文字列内で明示的にオーバーライドできます。
   - `IngestionEndpoint` (例: `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint` (例: `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint` (例: `https://profiler.applicationinsights.azure.com`)
   - `SnapshotEndpoint` (例: `https://snapshot.applicationinsights.azure.com`)

#### <a name="endpoint-schema"></a>エンドポイント スキーマ

`<prefix>.<suffix>`
- プレフィックス:サービスを定義します。 
- サフィックス:共通ドメイン名を定義します。

##### <a name="valid-suffixes"></a>有効なサフィックス

有効なサフィックスの一覧を次に示します 
- applicationinsights.azure.cn
- applicationinsights.us


関連項目: https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification


##### <a name="valid-prefixes"></a>有効なプレフィックス

- [テレメトリ インジェスト](./app-insights-overview.md): `dc`
- [Live Metrics](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [スナップショット](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>接続文字列の例


### <a name="minimal-valid-connection-string"></a>最小の有効な接続文字列

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

この例では、インストルメンテーション キーだけが設定されています。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、[SDK の既定値 ](https://github.com/microsoft/ApplicationInsights-dotnet/blob/e50d569cebf485e72e98f4a08a0bc0e30cdf42bc/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs#L6)に基づき、パブリック グローバル Azure に接続されます。
   - インジェスト: `https://dc.services.visualstudio.com/`
   - Live Metrics: `https://rt.services.visualstudio.com/`
   - Profiler: `https://agent.azureserviceprofiler.net/`
   - デバッガー: `https://agent.azureserviceprofiler.net/`



### <a name="connection-string-with-endpoint-suffix"></a>エンドポイント サフィックスが付いた接続文字列

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

この例では、この接続文字列で、エンドポイント サフィックスを指定し、SDK によりサービス エンドポイントが構築されます。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、指定されたエンドポイント サフィックスに基づきます。 
   - インジェスト: `https://dc.ai.contoso.com`
   - Live Metrics: `https://live.ai.contoso.com`
   - Profiler: `https://profiler.ai.contoso.com`
   - デバッガー: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>明示的なエンドポイント オーバーライドを含む接続文字列 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

この例では、この接続文字列で、すべてのサービスに明示的なオーバーライドを指定しています。 SDK により、変更なしで提供される正確なエンドポイントが使用されます。

- 認証方式の既定値は "ikey" です 
- インストルメンテーション キー:00000000-0000-0000-0000-000000000000
- リージョン サービスの URI は、明示的なオーバーライド値に基づきます。 
   - インジェスト: `https://custom.com:111/`
   - Live Metrics: `https://custom.com:222/`
   - Profiler: `https://custom.com:333/`
   - デバッガー: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>接続文字列を追加する方法

接続文字列は、次の SDK バージョンでサポートされています。
- .NET および .NET Core v2.12.0
- Java v2.5.1 および Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

接続文字列は、コード、環境変数、または構成ファイルのいずれかで設定できます。



### <a name="environment-variable"></a>環境変数

- 接続文字列: `APPLICATIONINSIGHTS_CONNECTION_STRING`

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

TelemetryConfiguration.ConnectionString: https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274

.NET の明示的な設定:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET 構成ファイル:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore の明示的な設定:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)


Java (v2.5.x) の明示的な設定:
```java
TelemetryConfiguration.getActive().setConnectionString("InstrumentationKey=00000000-0000-0000-0000-000000000000");
```

ApplicationInsights.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000;</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

重要:JavaScript では、環境変数の使用はサポートされていません。

スニペットの使用:

```javascript
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```


手動セットアップ:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

ユーザーが環境変数を設定することをお勧めします。

接続文字列を明示的に設定するには:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```


## <a name="next-steps"></a>次のステップ

実行時に開始する:

* [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](./azure-vm-vmss-apps.md)
* [IIS サーバー](./monitor-performance-live-website-now.md)
* [Azure Web Apps](./azure-web-apps.md)

開発時に開始する:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

