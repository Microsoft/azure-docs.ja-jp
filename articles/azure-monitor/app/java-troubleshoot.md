---
title: Java Web プロジェクトでの Application Insights のトラブルシューティング
description: トラブルシューティング ガイド - Application Insights でライブ Java アプリケーションを監視します。
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 1b2e1300c200619f5d1177e9761ad948c62c1239
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54013810"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Java 用 Application Insights のトラブルシューティングおよび Q&A
[Java 用 Azure Application Insights][java] について疑問または問題はありませんか。 ここでは、いくつかのヒントを紹介します。

## <a name="build-errors"></a>ビルド エラー
**Eclipse または Intellij Idea で、Maven または Gradle を使用して Application Insights SDK を追加すると、ビルドまたはチェックサムの検証エラーが発生します。**

* 依存 `<version>` 要素にワイルドカード文字を含むパターン (例:(Maven) `<version>[2.0,)</version>`、または (Gradle) `version:'2.0.+'`) を使用している場合、代わりに特定のバージョン (`2.0.1` など) を指定してみてください。 最新のバージョンの [リリース ノート](https://github.com/Microsoft/ApplicationInsights-Java/releases) を参照してください。

## <a name="no-data"></a>データが表示されない
**Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません。**

* 少し待ってから、[最新の情報に更新] をクリックします。 グラフは周期的に自動で更新されますが、手動で更新することもできます。 更新間隔は、グラフの時間範囲によって異なります。
* インストルメンテーション キーが、(プロジェクトのリソース フォルダーにある) ApplicationInsights.xml ファイル内で定義されているか、環境変数として構成されていることをご確認ください。
* この xml ファイルに `<DisableTelemetry>true</DisableTelemetry>` ノードが存在しないことをご確認ください。
* ファイアウォールで、dc.services.visualstudio.com への送信トラフィック用に TCP ポート 80 と 443 を開くことが必要な場合があります。 最新のバージョンの [ファイアウォール例外の一覧に関する記事](../../azure-monitor/app/ip-addresses.md)
* Microsoft Azure のスタート ボードで、サービス状態マップをご確認ください。 アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
* プロジェクトのリソース フォルダーにある ApplicationInsights.xml ファイル内で、ルート ノードの下に `<SDKLogger />` 要素を追加して IDE コンソール ウィンドウへのログを有効にし、AI:INFO/WARN/ERROR から始まるエントリに疑わしいログがないかを調べます。
* 正しい ApplicationInsights.xml ファイルが Java SDK によって正常に読み込まれたことを確認します。そのためには、コンソールの出力メッセージに「構成ファイルが正常に検出されました」というメッセージがあるかどうかを確認します。
* 構成ファイルが見つからない場合、出力メッセージを確認して構成ファイルの検索範囲を確かめ、ApplicationInsights.xml がこれらの検索場所のいずれかに存在していることを確認します。 通例、構成ファイルは Application Insights SDK の JAR ファイルの近くに見つかります。 たとえば、Tomcat の場合は WEB-INF/classes フォルダーがこれに相当します。 開発時に、Web プロジェクトのリソース フォルダーに ApplicationInsights.xml を配置できます。
* SDK の既知の問題については、[GitHub の問題に関するページ](https://github.com/Microsoft/ApplicationInsights-Java/issues)を参照してください。
* バージョンの競合の問題を回避するには、同じバージョンの Application Insights のコア、Web、エージェント、およびログ アペンダーを使用していることを確認してください。

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>データが表示されていたのに停止しました。
* [状態ブログ](https://blogs.msdn.com/b/applicationinsights-status/)をご確認ください。
* データ ポイントの月間クォータに達していませんか? Open Settings/Quota and Pricing to find out.上限に達している場合は、プランをアップグレードするか、追加容量分を購入することができます。 「 [料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)」をご覧ください。
* 最近 SDK をアップグレードしましたか? プロジェクト ディレクトリ内に重複していない SDK jar ファイルのみがあることを確認してください。 2 種類のバージョンの SDK が存在することはできません。
* 正しい AI リソースを見ていますか? アプリケーションの iKey を、テレメトリが必要なリソースに一致させてください。 これらが同じである必要があります。

#### <a name="i-dont-see-all-the-data-im-expecting"></a>予期しているデータがすべて表示されません
* [使用量と推定コスト] ページを開き、[サンプリング](../../azure-monitor/app/sampling.md)が動作中かどうかを確認します  (転送率が 100% の場合、サンプリングは実行されていません)。Application Insights サービスは、アプリから到着したテレメトリの一部だけを受け入れるように設定できます。 これにより、テレメトリの月間クォータの上限を超えないようにすることができます。
* SDK サンプリングを有効にしていますか? 有効にしている場合、該当するすべての型について、指定したレートでデータがサンプリングされます。
* 古いバージョンの Java SDK を実行していませんか? バージョン 2.0.1 以降に、ローカル ドライブでのデータ永続化だけでなく、ネットワークやバックエンドの断続的障害に対処できるように、フォールト トレランスのメカニズムを導入しました。
* テレメトリが過度になるという理由から、調整を行っていますか? 情報のログ記録を有効にしている場合は、"アプリが調整されました" というログ メッセージが表示されます。 現在の制限は、32,000 テレメトリ項目/秒です。

### <a name="java-agent-cannot-capture-dependency-data"></a>Java エージェントが依存関係データを取得できない
* [Java エージェントの構成](java-agent.md)に関するページに従って、Java エージェントを構成しましたか?
* Java エージェント jar ファイルと AI Agent.xml ファイルの両方が同じフォルダーに配置されていることを確認してください。
* 自動収集しようとしている依存関係が、自動収集でサポートされていることを確認します。 現在、MySQL、MsSQL、Oracle DB、Azure Cache for Redis の依存関係の収集のみがサポートされています。
* JDK 1.7 または 1.8 を使用していませんか? 現在 JDK 9 の依存関係の収集はサポートされていません。

## <a name="no-usage-data"></a>使用状況データがない
**要求と応答時間についてのデータは表示されますが、ページ ビュー、ブラウザー、またはユーザーについてのデータが表示されません。**

サーバーからテレメトリを送信するためのアプリ設定は正常に行われています。 次の手順は、[Web ブラウザーからテレメトリを送信するように Web ページを設定する][usage]ことです。

または、使用するクライアントが[電話やその他のデバイス][platforms]内のアプリの場合、そのアプリからテレメトリを送信できます。

クライアントおよびサーバー テレメトリの両方の設定に、同じインストルメンテーション キーを使用します。 データは同じ Application Insights リソース内に表示され、クライアントとサーバーのイベントを関連付けることができるようになります。


## <a name="disabling-telemetry"></a>テレメトリの無効化
**テレメトリの収集を無効にする方法を教えてください。**

コード内で以下のように指定します。

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**または**

プロジェクトのリソース フォルダーにある ApplicationInsights.xml を更新します。 ルート ノードの下に次の内容を追加します。

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML メソッドを使用するうえで、値を変更した場合はアプリケーションを再起動する必要があります。

## <a name="changing-the-target"></a>ターゲットの変更
**自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?**

* [新しいリソースのインストルメンテーション キーを取得します。][java]
* Eclipse の Azure Toolkit を使用してプロジェクトに Application Insights を追加した場合、Web プロジェクトを右クリックし、**[Azure]**、**[Configure Application Insights]** の順に選択して、キーを変更します。
* インストルメンテーション キーを環境変数として構成していた場合は、新しい iKey で環境変数の値を更新してください。
* それ以外の場合は、プロジェクトのリソース フォルダーにある ApplicationInsights.xml 内のキーを更新します。

## <a name="debug-data-from-the-sdk"></a>SDK からのデータをデバッグする

**SDK の動作を確認する方法について教えてください。**

API の仕組みの詳細を取得するには、ApplicationInsights.xml 構成ファイルのルート ノードの下に `<SDKLogger/>` を追加します。

ファイルに出力するようにロガーに指示することもできます。

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Tomcat サーバーの場合、ファイルは `%temp%\javasdklogs` または `java.io.tmpdir` で見つかります。


## <a name="the-azure-start-screen"></a>Azure のスタート画面
**[Azure Portal](https://portal.azure.com) を表示しています。このマップから、自分のアプリについて何か情報が得られるのでしょうか?**

いいえ、そのマップは世界中の Azure サーバーの正常性を表しています。

*Azure のスタート画面 (ホーム画面) から、アプリに関するデータを見つける方法を教えてください。*

[Application Insights 用にアプリを設定][java]してあると仮定します。[参照] をクリックし、[Application Insights] を選択して、アプリ用に作成したアプリ リソースを選択します。 次からその場所にすばやくアクセスできるように、スタート画面にアプリをピン留めすることができます。

## <a name="intranet-servers"></a>イントラネット サーバー
**イントラネット上のサーバーを監視できますか?**

はい、お使いのサーバーがパブリック インターネットを介して Application Insights ポータルにテレメトリを送信できるなら、可能です。

ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。

## <a name="data-retention"></a>データの保持
**ポータルでのデータ保持期間はどのくらいですか?セキュリティで保護されていますか?**

[データの保持とプライバシー][data]に関するページを参照してください。

## <a name="debug-logging"></a>デバッグ ログ
Application Insights では `org.apache.http` が使用されます。 これは、名前空間 `com.microsoft.applicationinsights.core.dependencies.http` の下の Application Insights のコアの jar ファイル内に再配置されます。 これにより、Application Insights では、同じ `org.apache.http` のさまざまなバージョンが 1 つのコード ベースに存在するシナリオに対処できます。

>[!NOTE]
>アプリですべての名前空間についてデバッグ レベルのログ記録を有効にすると、名前が `com.microsoft.applicationinsights.core.dependencies.http` に変更された `org.apache.http` を含むすべての実行中のモジュールによってこれが適用されます。 ログ呼び出しは Apache のライブラリによって行われるため、Application Insights では、これらの呼び出しにフィルタリングを適用できません。 デバッグ レベルのログ記録では多量のログ データが生成されるため、実稼働インスタンスにはお勧めしません。


## <a name="next-steps"></a>次の手順
**Java サーバー アプリ用に Application Insights を設定しました。他には何ができるか教えてください。**

* [Web ページの可用性の監視][availability]
* [Web ページの使用状況の監視][usage]
* [デバイス アプリの使用状況を追跡し、問題を診断する][platforms]
* [アプリの使用状況を追跡するためのコードを記述する][track]
* [診断ログのキャプチャ][javalogs]

## <a name="get-help"></a>問い合わせ
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [GitHub に関する問題を報告する](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

