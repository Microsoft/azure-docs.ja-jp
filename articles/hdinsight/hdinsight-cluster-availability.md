---
title: '監視: Apache Ambari と Azure Monitor のログ - Azure HDInsight'
description: Ambari と Azure Monitor ログを使用してクラスターのヘルスと可用性を監視する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/06/2020
ms.openlocfilehash: 383366fa3e436c79bed28a7c47f1e9daa5f0d9de
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77060178"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-and-azure-monitor-logs"></a>Apache Ambari と Azure Monitor ログを使用してクラスターの可用性を監視する方法

HDInsight クラスターには、一目で理解しやすいヘルス情報と事前定義されたアラートを提供する Apache Ambari と、クエリ可能なメトリックとログに加えて構成可能なアラートを提供する Azure Monitor ログの統合の両方が含まれています。

このドキュメントでは、これらのツールを使用してクラスターを監視する方法を示し、Ambari アラートの構成、ノードの可用性率の監視、および 5 時間以内に 1 つ以上のノードからハートビートが受信されなかった場合に発生する Azure Monitor アラートの作成に関して、いくつかの例を紹介します。

## <a name="ambari"></a>Ambari

### <a name="dashboard"></a>ダッシュボード

以下に示した Azure portal の [HDInsight Overview]\(HDInsight の概要\) の **[Cluster dashboards]\(クラスター ダッシュボード\)** セクションにある **[Ambari home]\(Ambari ホーム\)** リンクを選択して、Ambari ダッシュボードにアクセスできます。 または、ブラウザーで `https://CLUSTERNAME.azurehdinsight.net` に移動してアクセスすることもできます。ここで、CLUSTERNAME はクラスターの名前です。

![HDInsight リソース ポータルのビュー](media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png)

次に、クラスター ログインのユーザー名とパスワードの入力を求められます。 クラスターを作成した時に選んだ資格情報を入力します。

Ambari ダッシュボードが開かれ、その中にあるウィジェットには、HDInsight クラスターの正常性についての簡単な概要がわかる、いくつかのメトリックが表示されます。 これらのウィジェットには、ライブの DataNodes (ワーカー ノード) および JournalNodes (zookeeper ノード) の数、NameNodes (ヘッド ノード) の稼働時間などのメトリックに加えて、Spark および Hadoop クラスターに対する YARN の ResourceManager の稼働時間など、特定のクラスターの種類に固有のメトリックも表示されます。

![Apache Ambari の使用状況に関するダッシュボード表示](media/hdinsight-cluster-availability/apache-ambari-dashboard.png)

### <a name="hosts--view-individual-node-status"></a>ホスト – 個々のノードの状態を表示する

個々のノードの状態情報を表示することもできます。 **[ホスト]** タブを選択して、クラスター内のすべてのノードの一覧を表示し、各ノードに関する基本情報を確認します。 各ノード名の左にある緑のチェックは、すべてのコンポーネントがノード上で稼働していることを示します。 ノード上でコンポーネントがダウンしている場合、緑のチェックの代わりに赤いアラートの三角形が表示されます。

![HDInsight Apache Ambari の [ホスト] のビュー](media/hdinsight-cluster-availability/apache-ambari-hosts1.png)

次に、ノードの**名前**を選択して、その特定のノードに関するより詳細なホスト メトリックを表示します。 このビューには、個々のコンポーネントごとの状態/可用性が表示されます。

![Apache Ambari の [ホスト] にある単一ノードのビュー](media/hdinsight-cluster-availability/apache-ambari-hosts-node.png)

### <a name="ambari-alerts"></a>Ambari のアラート

Ambari では、特定のイベントの通知を提供できる構成可能なアラートもいくつか提供しています。 アラートがトリガーされると、アラート数を記した赤いバッジとして Ambari の左上隅に表示されます。 このバッジを選択すると、現在のアラートの一覧が表示されます。

![Apache Ambari の現在のアラート数](media/hdinsight-cluster-availability/apache-ambari-alerts.png)

アラートの定義と状態の一覧を表示するには、次に示すように **[アラート]** タブをクリックします。

![Ambari のアラート定義のビュー](media/hdinsight-cluster-availability/ambari-alerts-definitions.png)

Ambari では、次に示すように、可用性に関連する多数の定義済みのアラートを提供しています。

| アラート名                        | 説明   |
|---|---|
| DataNode ヘルスの概要           | 異常な DataNode がある場合に、このサービス レベルのアラートがトリガーされます|
| NameNode の高可用性のヘルス | アクティブな NameNode とスタンバイの NameNode のどちらも実行されていない場合に、このサービス レベルのアラートがトリガーされます。|
| 利用可能な JournalNode の割合    | クラスター内でダウンしている JournalNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、JournalNode プロセス チェックの結果が集計されます。 |
| 利用可能な DataNode の割合       | クラスター内でダウンしている DataNode の数が、設定された重大しきい値を超えている場合は、このアラートがトリガーされます。 これにより、DataNode プロセス チェックの結果が集計されます。|

クラスターの可用性を監視できる Ambari アラートの完全な一覧は、[こちら](https://docs.microsoft.com/azure/hdinsight/hdinsight-high-availability-linux#ambari-web-ui)で確認できます。

アラートの詳細を表示したり、条件を変更したりするには、アラートの**名前**を選択します。 例として **DataNode ヘルスの概要**を取り上げます。 アラートの説明と、'警告' または '重大' のアラートをトリガーする固有の条件、および条件のチェック間隔を確認できます。 構成を編集するには、[構成] ボックスの右上隅にある **[編集]** ボタンを選択します。

![Apache Ambari アラートの構成](media/hdinsight-cluster-availability/ambari-alert-configuration.png)

ここでは、説明に加えて、さらに重要な警告または重大アラートのチェック間隔としきい値を編集できます。

![Ambari アラートの構成の編集ビュー](media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png)

この例では、異常な DataNodes が 2 つの場合に重大アラートをトリガーし、異常な DataNode が 1 つのみの場合に警告をトリガーできます。 編集が終了したら、 **[保存]** を選択します。

### <a name="email-notifications"></a>メール通知

必要に応じて、Ambari アラートに対する電子メール通知を構成することも可能です。 これを行うには、 **[アラート]** タブ上で、左上にある **[Actions]\(アクション\)** ボタン、 **[Manage Notifications]\(通知の管理\)** の順にクリックします。

![Ambari 上で通知のアクションを管理する](media/hdinsight-cluster-availability/ambari-manage-notifications.png)

アラート通知を管理するためのダイアログが開きます。 ダイアログの下部にある **+** を選択し、必須フィールドを入力して、電子メールの送信元となる電子メール サーバーの詳細を Ambari に提供します。

> [!TIP]
> Ambari の電子メール通知の設定は、多数の HDInsight クラスターを管理する場合に 1 つの場所でアラートを受け取るのに適した方法でもあります。

## <a name="azure-monitor-logs-integration"></a>Azure Monitor ログの統合

Azure Monitor ログでは、HDInsight クラスターなどの複数のリソースによって生成されたデータを 1 つの場所に収集して集計し、統合された監視エクスペリエンスを実現できます。

前提条件として、収集されたデータを格納するための Log Analytics ワークスペースが必要になります。 まだ作成していない場合は、次の記事の手順に従います:「[Azure ポータルで Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)」

### <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight Azure Monitor ログの統合を有効にする

ポータルの HDInsight クラスター リソースのページから、 **[Azure Monitor]** を選択します。 次に、 **[有効]** を選択して、ドロップダウンから Log Analytics ワークスペースを選択します。

![[HDInsight Operations Management Suite]](media/hdinsight-cluster-availability/azure-portal-monitoring.png)

### <a name="query-metrics-and-logs-tables"></a>メトリックとログ テーブルにクエリを実行する

Azure Monitor ログの統合が有効になったら (これには数分かかる場合があります)、 **[Log Analytics ワークスペース]** リソースに移動して、 **[ログ]** を選択します。

![Log Analytics ワークスペースの [ログ]](media/hdinsight-cluster-availability/hdinsight-portal-logs.png)

[ログ] には、次のような多数のサンプル クエリが一覧表示されます。

| クエリ名                      | 説明                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| 今日のコンピューターの可用性    | 1 時間ごとに、ログを送信するコンピューター数をグラフにする                     |
| ハートビードの一覧表示                 | 過去 1 時間のすべてのコンピューターのハートビートを一覧表示する                           |
| 各コンピューターの最新のハートビート | 各コンピューターから送信された最新のハートビートを表示する                             |
| 利用できないコンピューター           | 過去 5 時間以内にハートビートを送信しなかったすべての既知のコンピューターを一覧表示する |
| 可用性率               | 接続されている各コンピューターの可用性率を計算する                |

例として、上記のスクリーンショットに示すように、**可用性率**のサンプル クエリ上の **[実行]** を選択して、そのクエリを実行します。 これにより、クラスター内にある各ノードの可用性率が割合として表示されます。 複数の HDInsight クラスターを有効にしてメトリックを同じ Log Analytics ワークスペースに送信した場合、表示されているそれらのクラスター内のすべてのノードに対する可用性率が表示されます。

![Log Analytics ワークスペースにある [ログ] の '可用性率' サンプル クエリ](media/hdinsight-cluster-availability/portal-availability-rate.png)

> [!NOTE]  
> 可用性率は 24 時間の期間に測定されるので、正確な可用性率を表示するには、それまでに少なくとも 24 時間、クラスターが実行される必要があります。

右上隅にある **[固定]** をクリックして、共有のダッシュボードにこのテーブルを固定表示することが可能です。 書き込み可能な共有のダッシュボードがない場合は、次の記事で作成方法を確認できます:「[Azure portal でのダッシュボードの作成と共有](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards#publish-and-share-a-dashboard)」

### <a name="azure-monitor-alerts"></a>Azure Monitor アラート

メトリックの値またはクエリの結果が特定の条件に合った場合にトリガーする Azure Monitor アラートを設定することも可能です。 例として、1 つまたは複数のノードが 5 時間以内にハートビートを送信しなかった (つまり、利用できないと推定される) 場合に電子メールを送信するアラートを作成しましょう。

以下に示すように、 **[ログ]** から、**利用できないコンピューター**のサンプル クエリ上の **[実行]** を選択して、そのクエリを実行します。

![Log Analytics ワークスペースの [ログ] の '利用できないコンピューター' サンプル](media/hdinsight-cluster-availability/portal-unavailable-computers.png)

すべてのノードが利用可能な場合、このクエリは今のところ、0 の結果を返すはずです。 **[新しいアラート ルール]** をクリックして、このクエリでのアラートの構成を開始します。

![Log Analytics ワークスペースの [新しいアラート ルール]](media/hdinsight-cluster-availability/portal-logs-new-alert-rule.png)

アラートに対しては、3 つのコンポーネントがあります。ルールの作成対象とする*リソース* (この場合、Log Analytics ワークスペース)、アラートをトリガーする*条件*、アラートがトリガーされたときに発生する動作を決定する*アクション グループ*です。
以下に示すように**条件のタイトル**をクリックして、シグナル ロジックの構成を完了します。

![ポータル アラートのルール条件の作成](media/hdinsight-cluster-availability/portal-condition-title.png)

これにより、 **[シグナル ロジックの構成]** が開かれます。

**[アラート ロジック]** セクションを次のように設定します。

"*基準:結果の数、条件:より大きい、しきい値:0*"

このクエリでは、利用できないノードだけを結果として返すので、結果の数が 0 より大きい場合、必ずアラートが発生します。

**[評価基準]** セクションで、利用できないノードについてチェックする頻度に基づいて、 **[期間]** および **[頻度]** を設定します。

このアラートの目的に対しては、 **[期間] = [頻度]** となることを確認すべきです。 期間、頻度、およびその他のアラート パラメーターに関する詳細は、[こちら](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log#log-search-alert-rule---definition-and-types)で確認できます。

シグナル ロジックの構成が終わったら、 **[完了]** を選択します。

![アラート ルールにおいてシグナル ロジックを構成する](media/hdinsight-cluster-availability/portal-configure-signal-logic.png)

既存のアクション グループがまだない場合は、 **[アクション グループ]** セクション下にある **[新規作成]** をクリックします。

![アラート ルールの新しいアクション グループの作成](media/hdinsight-cluster-availability/portal-create-new-action-group.png)

これにより、 **[アクション グループの追加]** が開きます。 **[アクション グループ名 ]** 、 **[短い名前]** 、 **[サブスクリプション]** 、および **[リソース グループ]** を選択します。 **[アクション]** セクション下で、 **[アクション名]** を選択して、 **[アクションの種類]** として **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** を選択します。

> [!NOTE]
> [Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\) 以外にも、Azure Function、LogicApp、Webhook、ITSM、および Automation Runbook など、アラートがトリガーできる他のアクションが複数あります。 [詳細を確認してください。](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#action-specific-information)

これにより、 **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** が開きます。 受信者の **[名前]** を選択し、 **[電子メール]** チェック ボックスを**オン**にして、アラートの送信先になる電子メール アドレスを入力します。 **[Email/SMS/Push/Voice]\(電子メール/SMS/プッシュ/音声\)** で **[OK]** を選択してから、 **[アクション グループの追加]** でアクション グループの構成を完了します。

![アラート ルールのアクション グループの追加](media/hdinsight-cluster-availability/portal-add-action-group.png)

これらのブレードを閉じた後、 **[アクション グループ]** セクション下にアクション グループが一覧表示されていることを確認できます。 最後に、 **[アラート ルール名]** と **[説明]** を入力して、 **[重大度]** を選択し、 **[アラートの詳細]** セクションを完成させます。 **[アラート ルールの作成]** をクリックして完了します。

![ポータルのアラート ルールの作成の完了](media/hdinsight-cluster-availability/portal-create-alert-rule-finish.png)

> [!TIP]
> **[重大度]** を指定する機能は、複数のアラートを作成する場合に使用できる優れたツールとなります。 たとえば、単一のヘッド ノードがダウンした場合には [警告 (重大度 1)] を挙げるアラートを 1 つと、両方のヘッド ノードがダウンするという想定外のイベントには [重大 (重大度 0)] を挙げるもう 1 つのアラートを作成することが可能です。

このアラートの条件と一致した場合、アラートが発生し、次のようなアラートの詳細を含む電子メールを受信します。

![Azure Monitor アラート メールの例](media/hdinsight-cluster-availability/portal-oms-alert-email.png)

また、**Log Analytics ワークスペース**の **[アラート]** に移動して、発生したすべてのアラートを重大度別にグループ化して表示することもできます。

![Log Analytics ワークスペースのアラート](media/hdinsight-cluster-availability/hdi-portal-oms-alerts.png)

重要度によるグループ (上記で言えば強調表示されている **[重大度 1]** ) を選択すると、発生したその重大度の全アラートに対するレコードが次のように表示されます。

![Log Analytics ワークスペースの [重大度 1] のアラート](media/hdinsight-cluster-availability/portal-oms-alerts-sev1.png)

## <a name="next-steps"></a>次のステップ

- [HDInsight における Apache Hadoop クラスターの可用性と信頼性](hdinsight-high-availability-linux.md)
