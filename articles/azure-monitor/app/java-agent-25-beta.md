---
title: Azure Application Insights (2.5.0-BETA) での Java Web アプリのパフォーマンス監視 | Microsoft Docs
description: Application Insights (2.5.0-BETA) を使用した Java Web サイトのパフォーマンスおよび利用状況の監視拡張。
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: 0596cc78c464deb117def3178827ba4945f857ac
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298183"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Java Web アプリでの依存関係、キャッチされた例外、メソッド実行時間の監視


[Java Web アプリを Application Insights][java] でインストルメント化した場合、Java エージェントを使用して、コードを変更することなく、詳細な分析を行うことができます。

* **依存関係:** アプリケーションが他のコンポーネントに対して行った呼び出しについてのデータであり、次のものを含みます。
  * Apache HttpClient、OkHttp、`java.net.HttpURLConnection` 経由で**発信された HTTP 呼び出し**がキャプチャされます。
  * Jedis クライアント経由で行われた **Redis** 呼び出しがキャプチャされます。
  * **JDBC クエリ** - MySQL と PostgreSQL で呼び出しにかかる時間が 10 秒を超えた場合、エージェントはクエリ プランをレポートします。

* **アプリケーションのログ記録:** アプリケーション ログをキャプチャし、HTTP 要求やその他のテレメトリに関連付けます。
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **操作の名前付けの改善:** (ポータルでの要求の集計に使用)
  * **Spring** - `@RequestMapping` に基づく。
  * **JAX-RS** - `@Path` に基づく。 

Java エージェントを使用するには、これをサーバーにインストールします。 Web アプリを [Application Insights Java SDK][java] を使用してインストルメント化する必要があります。 

## <a name="install-the-application-insights-agent-for-java"></a>Jave 用の Application Insights エージェントのインストール
1. Java サーバーを実行しているコンピューターで [エージェントをダウンロード](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest)します。 Application Insights の Java SDK コアおよび Web パッケージと同じバージョンの Java エージェントをダウンロードするようにしてください。
2. アプリケーション サーバーのスタートアップ スクリプトを編集し、次の JVM 引数を追加します。
   
    `-javaagent:<full path to the agent JAR file>`
   
    たとえば、Linux マシンの Tomcat で以下を実行します。
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. アプリケーション サーバーを再起動します。

## <a name="configure-the-agent"></a>エージェントの構成
`AI-Agent.xml` という名前のファイルを作成し、エージェントの JAR ファイルの場所と同じフォルダーに配置します。

xml ファイルの内容を設定します。 次の例を編集して、必要に応じて、機能を含めるか省略します。

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>追加構成 (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Azure App Services については、次のようにします。

* [設定]、[アプリケーションの設定] の順に選択します
* [アプリ設定] で、新しいキー値ペアを追加します。

キー:`JAVA_OPTS`値:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0-BETA.jar`

Java の最新バージョンについては、[ここ](https://github.com/Microsoft/ApplicationInsights-Java/releases
)でリリースを確認してください。 

エージェントは、D:/home/site/wwwroot/ directory で終わるようにプロジェクト内でリソースとしてパッケージ化する必要があります。 **[開発ツール]**  >  **[高度なツール]**  >  **[デバッグ コンソール]** に進み、サイト ディレクトリの内容を調べることで、エージェントが正しい App Service ディレクトリにあることを確認できます。    

* 設定を [保存] し、アプリを [再起動] します。 (これらの手順は、Windows で実行している App Services にのみ適用されます)

> [!NOTE]
> AI-Agent.xml とエージェントの jar ファイルは同じフォルダーに含まれている必要があります。 多くの場合、これらはプロジェクトの `/resources` フォルダーに一緒に配置されます。  

#### <a name="enable-w3c-distributed-tracing"></a>W3C 分散トレースを有効にする

AI-Agent.xml に次のコードを追加します。

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> 既定では、下位互換性モードが有効になっています。また、enableW3CBackCompat パラメーターは、省略可能で、このモードをオフにするときにのみ使用する必要があります。 

すべてのサービスが、W3C プロトコルをサポートする新しいバージョンの SDK に更新されたときに、これを行うことが理想的です。 W3C をサポートする新しいバージョンの SDK にできるだけ早く移行することを強くお勧めします。

**受信と送信 (エージェント) の構成が**[両方とも](correlation.md#w3c-distributed-tracing)正確に同じであることを確認してください。

## <a name="view-the-data"></a>データの表示
Application Insights リソースでは、集計されたリモートの依存関係やメソッドの実行時間が [[パフォーマンス] タイル][metrics]に表示されます。

依存関係、例外、メソッドのレポートの個々のインスタンスを検索するには、[[検索]][diagnostic] を開きます。

「[依存関係の問題の診断](../../azure-monitor/app/asp-net-dependencies.md#diagnosis)」を参照してください。

## <a name="questions-problems"></a>疑問がある場合 問題が発生した場合
* データが表示されない場合 [ファイアウォール例外の設定](../../azure-monitor/app/ip-addresses.md)
* [Java のトラブルシューティング](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
