---
title: Azure Application Insights Telemetry の相関付け | Microsoft Docs
description: Application Insights におけるテレメトリの相関付け
services: application-insights
documentationcenter: .net
author: lgayhardt
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2018
ms.reviewer: sergkanz
ms.author: lagayhar
ms.openlocfilehash: a6937b5b6b3b85dd51d80a928de02a00c361cc0e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117607"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights におけるテレメトリの相関付け

マイクロ サービスの世界では、すべての論理操作は、さまざまなサービス コンポーネントで作業を実行する必要があります。 これらの各コンポーネントは、[Application Insights](../../azure-monitor/app/app-insights-overview.md) によって個別に監視できます。 Web アプリ コンポーネントは、ユーザーの資格情報を検証するためにプロバイダー コンポーネントと通信し、視覚化するためのデータを取得するために API コンポーネントと通信します。 API コンポーネントは、他のサービスからデータのクエリを行ってキャッシュ プロバイダー コンポーネントを使用でき、この呼び出しについて課金コンポーネントに通知できます。 Application Insights は、分散しているテレメトリの相関付けをサポートします。 これにより、エラーやパフォーマンス低下を発生させているコンポーネントを検出することができます。

この記事では、複数のコンポーネントから送信されるテレメトリを関連付けるために Application Insights によって使用されるデータ モデルについて説明します。 コンテキスト反映技法とプロトコルの説明があります。 また、さまざまな言語とプラットフォームにおける相関付けの概念の実装も扱います。

## <a name="telemetry-correlation-data-model"></a>テレメトリ相関付けデータ モデル

Application Insights は、分散しているテレメトリを相関付けるための[データ モデル](../../azure-monitor/app/data-model.md)を定義しています。 テレメトリを論理操作と関連付けるために、すべてのテレメトリ項目には `operation_Id` と呼ばれるコンテキスト フィールドがあります。 この ID は、分散トレース内のすべてのテレメトリ項目で共有されます。 このため、1 つのレイヤーからテレメトリが失われた場合でも、他のコンポーネントから報告されたテレメトリを関連付けることができます。

分散型論理操作は、通常は、一連の小さな操作 (いずれかのコンポーネントによって処理される要求) で構成されます。 これらの操作は、[要求テレメトリ](../../azure-monitor/app/data-model-request-telemetry.md)によって定義されます。 すべての要求テレメトリには、それをグローバルに一意に識別する独自の `id` があります。 この要求に関連付けられるすべてのテレメトリ (トレースや例外など) では、`operation_parentId` が要求の `id` に設定されます。

すべての発信操作 (別のコンポーネントへの HTTP 呼び出しなど) は[依存関係テレメトリ](../../azure-monitor/app/data-model-dependency-telemetry.md)で表されます。 依存関係テレメトリも、グローバルに一意である独自の `id` を定義します。 この依存関係呼び出しによって開始された要求テレメトリは、それを `operation_parentId` として使用します。

`operation_Id`、`operation_parentId`、および `request.id` を `dependency.id` と共に使用して、分散型論理操作のビューを構築できます。 これらのフィールドは、テレメトリ呼び出しの因果関係の順序も定義します。

マイクロ サービス環境では、コンポーネントのトレースが別の記憶域に移動する可能性があります。 すべてのコンポーネントが、Application Insights 内に独自のインストルメンテーション キーを持っている可能性があります。 論理操作のテレメトリを取得するには、すべての記憶域からデータのクエリを行う必要があります。 記憶域の数が膨大な場合は、次の検索場所に関するヒントを用意する必要があります。

Application Insights データ モデルでは、2 つのフィールド (`request.source` と `dependency.target`) を定義して、この問題を解決しています。 最初のフィールドは依存関係要求を開始したコンポーネントを識別し、2 つ目のフィールドは依存関係呼び出しの応答を返したコンポーネントを識別します。


## <a name="example"></a>例

STOCKS API という名前の外部 API を使用して株の現在の市場価格を表示する STOCK PRICES アプリケーションの例を挙げてみましょう。 STOCK PRICES アプリケーションのページ `Stock page` は、クライアント Web ブラウザーが `GET /Home/Stock` を使用して開きます。 アプリケーションは、HTTP 呼び出し `GET /api/stock/value` を使用して STOCK API にクエリを実行します。

クエリを実行したテレメトリの結果を分析することができます。

```
(requests | union dependencies | union pageViews) 
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

結果ビューでは、すべてのテレメトリ項目がルートの `operation_Id` を共有していることに注目してください。 このページから AJAX 呼び出しが行われると、新しい一意の ID `qJSXU` が依存関係テレメトリに割り当てられ、pageView の ID が `operation_ParentId` として使用されます。 サーバー要求では、AJAX の ID を `operation_ParentId` として使用します。

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

外部サービスに対して `GET /api/stock/value` 呼び出しを行うと、そのサーバーの ID がわかります。 それによって `dependency.target` フィールドを適切に設定できます。 外部サービスが監視をサポートしていない場合、`target` は、サービスのホスト名に設定されます (`stock-prices-api.com` など)。 ただし、そのサービスが定義済みの HTTP ヘッダーを返すことで自身を識別している場合、`target` には、Application Insights がそのサービスからテレメトリのクエリを行うことで分散トレースを構築できるサービス IDが含まれます。 

## <a name="correlation-headers"></a>相関付けヘッダー

Microsoft は、[相関付け HTTP プロトコル](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)の RFC 提案に取り組んでいます。 この提案では、2 つのヘッダーを定義しています。

- `Request-Id`: 呼び出しのグローバルに一意の ID を記述します。
- `Correlation-Context`: 分散トレースのプロパティの名前と値のペアのコレクションを記述します。

この標準は、`Request-Id` 生成の 2 つのスキーマ (フラットと階層) も定義します。 フラット スキーマには、`Correlation-Context` コレクションに対して定義された、よく知られた `Id` キーがあります。

Application Insights は、相関付け HTTP プロトコル用の[拡張機能](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md)を定義します。 それは、`Request-Context` の名前と値のペアを使用して、直前の呼び出し元または呼び出し先によって使用されたプロパティのコレクションを伝達します。 Application Insights SDK は、このヘッダーを使用して、`dependency.target` フィールドと `request.source` フィールドを設定します。

### <a name="w3c-distributed-tracing"></a>W3C 分散トレース

[W3C 分散トレース形式](https://w3c.github.io/trace-context/)への切り替えを実施しています。 次のように定義します。
- `traceparent` - 呼び出しのグローバルな一意操作 ID と一意識別子を伝送します。
- `tracestate` - トレース システム固有のコンテキストを伝送します。

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-classic-apps"></a>ASP.NET Classic アプリの W3C 分散トレース サポートを有効にする

この機能は、バージョン 2.8.0 ベータ1 以降の Microsoft.ApplicationInsights.Web および Microsoft.ApplicationInsights.DependencyCollector パッケージで利用可能です。
既定で **[オフ]** になっています。有効にするには、`ApplicationInsights.config` を次のように変更します。

* `RequestTrackingTelemetryModule` 下に、値が `true` に設定されている `EnableW3CHeadersExtraction` 要素を追加します。
* `DependencyTrackingTelemetryModule` 下に、値が `true` に設定されている `EnableW3CHeadersInjection` 要素を追加します。

#### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core アプリの W3C 分散トレース サポートを有効にする

この機能は、Microsoft.ApplicationInsights.AspNetCore バージョン 2.5.0 ベータ 1 および Microsoft.ApplicationInsights.DependencyCollector バージョン 2.8.0 ベータ 1 で利用可能です。
既定で **[オフ]** になっています。有効にするには、`ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing` を `true` に変更します。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

## <a name="open-tracing-and-application-insights"></a>Open Tracing と Application Insights

[Open Tracing データ モデルの仕様](https://opentracing.io/)と Application Insights データ モデルは、次のようにマッピングされます。

| Application Insights                  | Open Tracing                                      |
|------------------------------------   |-------------------------------------------------  |
| `Request`、`PageView`                 | `span.kind = server` を含む `Span`                  |
| `Dependency`                          | `span.kind = client` を含む `Span`                  |
| `Request` と `Dependency` の `Id`    | `SpanId`                                          |
| `Operation_Id`                        | `TraceId`                                         |
| `Operation_ParentId`                  | タイプ `ChildOf` の `Reference` (親スパン)   |

Application Insights データ モデルの詳細については、[データ モデル](../../azure-monitor/app/data-model.md)に関するページを参照してください。 

Open Tracing の概念の定義については、Open Tracing の[仕様](https://github.com/opentracing/specification/blob/master/specification.md)と [semantic_conventions](https://github.com/opentracing/specification/blob/master/semantic_conventions.md) に関するページを参照してください。

## <a name="telemetry-correlation-in-net"></a>.NET におけるテレメトリの相関付け

時間の経過と共に、.NET は、テレメトリと診断ログを相関付けるための多数の方法を定義してきました。 [LogicalOperationStack と ActivityId](https://msdn.microsoft.com/library/system.diagnostics.correlationmanager.aspx) を追跡できる `System.Diagnostics.CorrelationManager` があります。 `System.Diagnostics.Tracing.EventSource` と Windows ETW は、[SetCurrentThreadActivityId](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid.aspx) メソッドを定義しています。 `ILogger` は [ログ スコープ](https://docs.microsoft.com/aspnet/core/fundamentals/logging#log-scopes) を使用します。 WCF と HTTP は、"現在の" コンテキストの伝達をつないでいます。

ただし、これらのメソッドでは、自動分散トレースをサポートできませんでした。 `DiagnosticsSource` は、コンピューター間の自動的な相関付けをサポートする方法です。 .NET ライブラリは DiagnosticsSource をサポートして、HTTP などのトランスポート経由で相関付けコンテキストをコンピューター間で自動的に伝達できるようにしています。

アクティビティの追跡の基本については、DiagnosticsSource の「[アクティビティ ユーザー ガイド](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)」を参照してください。 

ASP.NET Core 2.0 は、HTTP ヘッダーの抽出と新しいアクティビティの開始をサポートします。 

バージョン `4.1.0` から開始される `System.Net.HttpClient`は、相関付け HTTP ヘッダーの自動挿入とアクティビティとしての HTTP 呼び出しの追跡をサポートします。

ASP.NET Classic 用の新しい HTTP モジュール [Microsoft.AspNet.TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/) があります。 このモジュールは、DiagnosticsSource を使用して、テレメトリの相関付けを実装します。 それは、受信要求ヘッダーに基づいてアクティビティを開始します。 さらに、要求処理のさまざまな段階のテレメトリを相関付けます。 IIS 処理のすべての段階が異なる管理スレッドで実行されている場合でも、これを行います。

Application Insights SDK は、バージョン `2.4.0-beta1` から DiagnosticsSource とアクティビティを使用してテレメトリを収集し、それを現在のアクティビティに関連付けます。 

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-the-java-sdk"></a>Java SDK におけるテレメトリの相関付け
[Application Insights Java SDK](../../azure-monitor/app/java-get-started.md) のバージョン `2.0.0` 以降では、テレメトリの自動相関付けをサポートしています。 要求の範囲内で発行されたすべてのテレメトリ (トレース、例外、カスタム イベントなど) の `operation_id` が自動的に設定されます。 また、[Java SDK エージェント](../../azure-monitor/app/java-agent.md)が構成されている場合は、HTTP を介して、サービスの相関付けヘッダー (前述) がサービス呼び出しに伝達されます。 相関付け機能では、Apache HTTP クライアントによる呼び出しだけがサポートされます。 Spring Rest Template または Feign を使用している場合、どちらも Apache HTTP クライアントで内部的に使用できます。

現時点では、メッセージング テクノロジ (Kafka、RabbitMQ、Azure Service Bus など) 間でのコンテキストの自動伝達はサポートされていません。 ただし、`trackDependency` および `trackRequest` API を使用して、このようなシナリオを手動でコーディングすることは可能です。依存関係テレメトリはプロデューサーによってエンキューされるメッセージを表し、要求はコンシューマーによって処理されるメッセージを表します。 この場合、`operation_id` と `operation_parentId` の両方を、メッセージのプロパティで伝達する必要があります。

<a name="java-role-name"></a>
## <a name="role-name"></a>ロール名

[アプリケーション マップ](../../azure-monitor/app/app-map.md)にコンポーネント名を表示する方法をカスタマイズすることが必要な場合があります。 そのためには、次のいずれかを実行して `cloud_RoleName` を手動で設定します。

Application Insights Spring Boot スターターで Spring Boot を使用する場合、必要な変更はapplication.properties ファイルにアプリケーションのカスタム名を設定することだけです。

`spring.application.name=<name-of-app>`

Spring Boot スターターが自動的に、cloudRoleName を、ユーザーが spring.application.name プロパティに入力した値に割り当てます。

`WebRequestTrackingFilter` を使用している場合、`WebAppNameContextInitializer` によってアプリケーション名が自動的に設定されます。 次を構成ファイル (ApplicationInsights.xml) に追加します。
```XML
<ContextInitializers>
  <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebAppNameContextInitializer" />
</ContextInitializers>
```

次のようにクラウドのコンテキスト クラスを使用します。

```Java
telemetryClient.getContext().getCloud().setRole("My Component Name");
```

## <a name="next-steps"></a>次の手順

- [カスタム テレメトリを記述します](../../azure-monitor/app/api-custom-events-metrics.md)。
- 他の SDK における cloud_RoleName の設定について、[詳細を確認](../../azure-monitor/app/app-map.md#set-cloudrolename)します。
- Application Insights でマイクロ サービスのすべてのコンポーネントの利用を開始します。 [サポートされているプラットフォームを調べます](../../azure-monitor/app/platforms.md)。
- Application Insights の型とデータ モデルについては、[データ モデル](../../azure-monitor/app/data-model.md)に関するページを参照してください。
- [テレメトリの拡張とフィルター処理](../../azure-monitor/app/api-filtering-sampling.md)を行う方法を確認します。
- [Application Insights 構成リファレンス](configuration-with-applicationinsights-config.md)

