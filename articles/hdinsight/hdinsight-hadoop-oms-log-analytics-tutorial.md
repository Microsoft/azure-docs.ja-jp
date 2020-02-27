---
title: Azure Monitor ログを使用して Azure HDInsight クラスターを監視する
description: Azure Monitor ログを使って HDInsight クラスターで実行中のジョブを監視する方法を説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: e4b33e132e660fba7d06ff33c7db06c7727dd26c
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162788"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor ログを使用して HDInsight クラスターを監視する

Azure Monitor ログを有効にして HDInsight で Hadoop クラスターの操作を監視する方法と、HDInsight 監視ソリューションを追加する方法を説明します。

[Azure Monitor ログ](../log-analytics/log-analytics-overview.md)は、可用性やパフォーマンスの維持を目的としてクラウド環境とオンプレミス環境を監視する Azure Monitor のサービスです。 Log Analytics を使用すると、クラウドおよびオンプレミスの環境内にあるリソースによって生成されたデータや、他の監視ツールのデータを収集し、複数のソースにわたる分析を行えます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペース。 このワークスペースは、独自のデータ リポジトリ、データ ソース、およびソリューションを備えた一意の Azure Monitor ログ環境と考えることができます。 手順については、[Log Analytics ワークスペースの作成](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace)に関するページをご覧ください。

* Azure HDInsight クラスター。 現在、Azure Monitor ログは次の HDInsight クラスター タイプで使用できます。

  * Hadoop
  * hbase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  HDInsight クラスターの作成手順については、[Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。  

* Azure PowerShell Az モジュール。  「[新しい Azure PowerShell Az モジュールの概要](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)」を参照してください。 最新バージョンを使用していることを確認します。 必要であれば、`Update-Module -Name Az` を実行します。

> [!NOTE]  
> パフォーマンス向上のため、HDInsight クラスターと Log Analytics ワークスペースの両方を同じリージョンに配置することをお勧めします。 Azure Monitor ログは、すべての Azure リージョンで使用できるわけではありません。

## <a name="enable-azure-monitor-logs-by-using-the-portal"></a>ポータルを使用した Azure Monitor ログの有効化

ここでは、ジョブやデバッグ ログなどを監視するために、Azure Log Analytics ワークスペースを使用するよう既存の HDInsight Hadoop クラスターを構成します。

1. [Azure portal](https://portal.azure.com/) でご自身のクラスターを選択します。  手順については、「[クラスターの一覧と表示](./hdinsight-administer-use-portal-linux.md#showClusters)」を参照してください。 このクラスターは、新しいポータル ページで開かれます。

1. 左側の **[監視]** で **[Azure Monitor]** を選択します。

1. メイン ビューの **[Azure Monitor の統合]** で、 **[有効化]** を選択します。

1. **[ワークスペースを選択]** ドロップダウン リストから、既存の Log Analytics ワークスペースを選択します。

1. **[保存]** を選択します。  設定を保存するまでしばらく時間がかかります。

    ![HDInsight クラスターの監視を有効にする](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "HDInsight クラスターの監視を有効にする")

## <a name="enable-azure-monitor-logs-by-using-azure-powershell"></a>Azure PowerShell を使用した Azure Monitor ログの有効化

Azure PowerShell Az モジュールの [Enable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) コマンドレットを使用して Azure Monitor ログを有効にすることができます。

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

無効にする場合は、[Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) コマンドレットを使用します。

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight クラスター管理ソリューションをインストールする

HDInsight では、Azure Monitor ログに追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、Azure Monitor ログに機能を追加して、追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。

利用可能な HDInsight ソリューションは次のとおりです。

* HDInsight Hadoop Monitoring
* HDInsight HBase の監視
* HDInsight Interactive Query Monitoring
* HDInsight Kafka Monitoring
* HDInsight Spark Monitoring
* HDInsight Storm Monitoring

管理ソリューションをインストールする手順については、「[Azure の管理ソリューション](../azure-monitor/insights/solutions.md#install-a-monitoring-solution)」をご覧ください。 テスト用に HDInsight Hadoop Monitoring ソリューションをインストールします。 インストールが完了すると、 **[概要]** の下に **HDInsightHadoop** タイルが表示されます。 **HDInsightHadoop** タイルを選択します。 HDInsightHadoop ソリューションは次のようになります。

![HDInsight 監視ソリューションのビュー](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

クラスターは新規のクラスターであるため、レポートにはアクティビティが表示されません。

## <a name="configuring-performance-counters"></a>パフォーマンス カウンターの構成

Azure Monitor では、クラスター内のノードのパフォーマンス メトリックの収集と分析もサポートしています。 この機能の有効化および構成の詳細については、[Azure Monitor での Linux パフォーマンス データ ソース](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters#linux-performance-counters)に関するページを参照してください。

## <a name="cluster-auditing"></a>クラスターの監査

HDInsight では、次の種類のログをインポートすることによって、Azure Monitor ログを使用したクラスターの監査をサポートしています。

* `log_gateway_audit_CL` - このテーブルは、成功/失敗したログイン試行を示すクラスター ゲートウェイ ノードの監査ログを提供します。
* `log_auth_CL` - このテーブルは、成功/失敗したログイン試行が含まれる SSH のログを提供します。
* `log_ambari_audit_CL` - このテーブルは、Ambari からの監査ログを提供します。
* `log_ranger_audti_CL` - このテーブルは、ESP クラスター上の Apache Ranger からの監査ログを提供します。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログでクエリを実行して HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)
