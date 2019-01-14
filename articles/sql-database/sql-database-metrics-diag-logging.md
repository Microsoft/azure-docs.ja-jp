---
title: Azure SQL Database のメトリックと診断のロギング | Microsoft Docs
description: Azure SQL Database リソースを構成して、リソースの使用状況およびクエリ実行の統計情報を保存する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 49c411487a29a7faa5a6cec5087a85d472309a4b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54044571"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Azure SQL Database のメトリックと診断のロギング

Azure SQL Database、エラスティック プール、Managed Instance、および Managed Instance のデータベースは、パフォーマンス監視を容易にするためのメトリックと診断ログをストリーミングできます。 リソース使用率、ワーカーとセッション、および接続性を次の Azure リソースのいずれかに送信するようにデータベースを構成することができます。

- **Azure SQL Analytics**: パフォーマンス レポート、アラート、軽減策の推奨事項を含む Azure データベースのインテリジェントな監視を取得します。
- **Azure Event Hubs**: SQL Database の利用統計情報を、カスタム監視ソリューションまたはホット パイプラインと統合します。
- **Azure Storage**: 大量のテレメトリを低価格でアーカイブします。

    ![アーキテクチャ](./media/sql-database-metrics-diag-logging/architecture.png)

各種の Azure サービスでサポートされているメトリックとログ カテゴリの詳細については、次の資料を参照してください。

- [Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 診断ログの概要](../azure-monitor/platform/diagnostic-logs-overview.md)

この記事では、データベース、エラスティック プール、および Managed Instance の診断テレメトリを有効にするためのガイダンスを提供します。 また、データベース診断テレメトリを表示するための監視ツールとして Azure SQL Analytics を構成する方法の理解にも役立ちます。

## <a name="enable-logging-of-diagnostics-telemetry"></a>診断テレメトリのログを有効にする

次のいずれかの方法を使用してメトリックと診断テレメトリのロギングを有効にして管理できます。

- Azure ポータル
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager テンプレート

メトリックと診断ログを有効にする場合は、診断テレメトリを収集するための Azure リソースの宛先を指定する必要があります。 利用可能なオプションは、次のとおりです。

- Azure の SQL 分析
- Azure Event Hubs
- Azure Storage

新しい Azure リソースをプロビジョニングするか、既存のリソースを選択できます。 **[診断設定]** オプションを使用してリソースを選択した後で、収集するデータを指定します。

> [!NOTE]
> またエラスティック プールまたは Managed Instance を使用している場合は、これらのリソースにも診断テレメトリを有効にすることをお勧めします。 エラスティック プールと Managed Instance のデータベース コンテナーには、独自の独立した診断テレメトリがあります。

## <a name="enable-logging-for-azure-sql-database-or-databases-in-managed-instance"></a>Azure SQL Database または Managed Instance のデータベースのログ記録を有効にする

SQL Database と、Managed Instance のデータベースでメトリックおよび診断ログを有効にします。既定では有効になっていません。

次の診断テレメトリを収集するように、Azure SQL Database と、Managed Instance のデータベースを設定できます。

| データベースの監視テレメトリ | Azure SQL Database のサポート | Managed Instance のデータベースのサポート |
| :------------------- | ------------------- | ------------------- |
| [すべてのメトリック](sql-database-metrics-diag-logging.md#all-metrics):DTU/CPU の割合、DTU/CPU の上限、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合が含まれます。 | [はい] | いいえ  |
| [QueryStoreRuntimeStatistics](sql-database-metrics-diag-logging.md#query-store-runtime-statistics):CPU 使用率、クエリ実行時間統計など、クエリのランタイム統計に関する情報が含まれます。 | [はい] | [はい] |
| [QueryStoreWaitStatistics](sql-database-metrics-diag-logging.md#query-store-wait-statistics):CPU、LOG、LOCKING など、クエリ待機統計 (クエリが待機していたもの) に関する情報が含まれます。 | [はい] | [はい] |
| [エラー](sql-database-metrics-diag-logging.md#errors-dataset):データベースには SQL エラーに関する情報が含まれます。 | [はい] | いいえ  |
| [DatabaseWaitStatistics](sql-database-metrics-diag-logging.md#database-wait-statistics-dataset):データベースが各種の待機に費やした時間に関する情報が含まれます。 | [はい] | いいえ  |
| [Timeouts](sql-database-metrics-diag-logging.md#time-outs-dataset):データベースにはタイムアウトに関する情報が含まれます。 | [はい] | いいえ  |
| [Blocks](sql-database-metrics-diag-logging.md#blockings-dataset):データベースにはブロック イベントに関する情報が含まれます。 | [はい] | いいえ  |
| [SQLInsights](sql-database-metrics-diag-logging.md#intelligent-insights-dataset):パフォーマンスに対する Intelligent Insights が含まれます。 詳細については、[Intelligent Insights](sql-database-intelligent-insights.md) に関するページを参照してください。 | [はい] | [はい] |

### <a name="azure-portal"></a>Azure ポータル

Azure portal 内のデータベースそれぞれの **[診断設定]** メニューを使用して、Azure SQL Database と Managed Instance のデータベースの診断テレメトリのストリーミングを構成します。 Microsoft Azure Storage、Azure Event Hubs、および Azure Log Analytics を宛先に設定できます。

### <a name="configure-streaming-of-diagnostics-telemetry-for-azure-sql-database"></a>Azure SQL Database の診断テレメトリのストリーミングを構成する

   ![SQL Database アイコン](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Azure SQL Database の診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Azure SQL Database リソースに移動します。
1. **[診断設定]** を選択します。
1. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。
   - 診断テレメトリをストリーミングするため並列接続を最大 3 つ作成できます。
   - 複数のリソースへの診断データの並列ストリーミングを構成するには、**[+Add diagnostic setting] (+ 診断設定の追加)** を選択します。

   ![SQL Database の診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. 独自の参照の設定名を入力します。
1. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]**、**[イベント ハブへのストリーム]**、または **[Log Analytics への送信]** から選択します。
1. 標準的なイベント ベースの監視エクスペリエンスを得るには、データベース診断ログ テレメトリ **[SQLInsights]**、**[AutomaticTuning]**、**[QueryStoreRuntimeStatistics]**、**[QueryStoreWaitStatistics]**、**[Errors]**、**[DatabaseWaitStatistics]**、**[Timeouts]**、**[Blocks]**、および **[Deadlocks]** のチェックボックスをオンにします。
1. 1 分ベースの高度な監視エクスペリエンスを得るには、**[AllMetrics]** のチェックボックスをオンにします。
1. **[保存]** を選択します。

   ![SQL Database の診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)

> [!NOTE]
> セキュリティ監査ログは、データベースの診断設定から有効にすることはできません。 監査ログのストリーミングを有効にするには、「[データベースに対する監査を設定する](sql-database-auditing.md#subheading-2)」と「[SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://blogs.msdn.microsoft.com/sqlsecurity/2018/09/13/sql-audit-logs-in-azure-log-analytics-and-azure-event-hubs/)」 (Azure Log Analytics と Azure Event Hubs の SQL 監査) を参照してください。
> [!TIP]
> 監視する Azure SQL Database ごとにこれらの手順を繰り返します。

### <a name="configure-streaming-of-diagnostics-telemetry-for-databases-in-managed-instance"></a>Managed Instance のデータベースの診断テレメトリのストリーミングを構成する

   ![Managed Instance のデータベースのアイコン](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Managed Instance のデータベースの診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Managed Instance のデータベースに移動します。
2. **[診断設定]** を選択します。
3. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。
   - 診断テレメトリをストリーミングするため並列接続を最大 3 つ作成できます。
   - 複数のリソースへの診断データの並列ストリーミングを構成するには、**[+Add diagnostic setting] (+ 診断設定の追加)** を選択します。

   ![Managed Instance データベースに対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

4. 独自の参照の設定名を入力します。
5. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]**、**[イベント ハブへのストリーム]**、または **[Log Analytics への送信]** から選択します。
6. データベース診断テレメトリ **[SQLInsights]**、**[QueryStoreRuntimeStatistics]**、**[QueryStoreWaitStatistics]**、および **[Errors]** のチェックボックスをオンにします。
7. **[保存]** を選択します。

   ![Managed Instance データベースに対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)

> [!TIP]
> 監視する Managed Instance のデータベースごとにこれらの手順を繰り返します。

## <a name="enable-logging-for-elastic-pools-or-managed-instance"></a>エラスティック プールまたは Managed Instance のロギングを有効にする

データベース コンテナーとしてエラスティック プールと Managed Instance の診断テレメトリを有効にします。 それらには独自の診断テレメトリがありますが、既定では有効になっていません。

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>エラスティック プールの診断テレメトリのストリーミングを構成する

   ![エラスティック プール アイコン](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

次の診断テレメトリを収集するように、エラスティック プール リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **エラスティック プール** | [すべてのメトリック](sql-database-metrics-diag-logging.md#all-metrics)には、eDTU/CPU の割合、eDTU/CPU の制限、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの制限、および XTP ストレージの割合が含まれています。 |

エラスティック プール リソースの診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Microsoft Azure portal でエラスティック プール リソースに移動します。
1. **[診断設定]** を選択します。
1. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![エラスティック プールに対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. 独自の参照の設定名を入力します。
1. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]**、**[イベント ハブへのストリーム]**、または **[Log Analytics への送信]** から選択します。
1. Log Analytics の場合は、**[構成]** を選択し、**[+Create New Workspace] (+ 新しいワークスペースの作成)** を選択することによって新しいワークスペースを作成するか、または既存のワークスペースを選択します。
1. エラスティック プールの診断テレメトリ **[AllMetrics]** のチェックボックスをオンにします。
1. **[保存]** を選択します。

   ![エラスティック プールに対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)

> [!TIP]
> 監視するエラスティック プールごとにこれらの手順を繰り返します。

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instance"></a>Managed Instance の診断テレメトリのストリーミングを構成する

   ![Managed Instance アイコン](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

次の診断テレメトリを収集するように、Managed Instance リソースを設定できます。

| リソース | 監視テレメトリ |
| :------------------- | ------------------- |
| **Managed Instance** | [ResourceUsageStats](sql-database-metrics-diag-logging.md#logs-for-managed-instance) には、仮想コアの個数、平均の CPU の割合、IO 要求、読み取り/書き込みバイト数、予約済みストレージ領域、および使用済みストレージ領域が含まれています。 |

Managed Instance リソースの診断テレメトリのストリーミングを有効にするには、次の手順に従います。

1. Microsoft Azure portal で Managed Instance リソースに移動します。
1. **[診断設定]** を選択します。
1. 以前の設定が存在しない場合は **[診断を有効にする]** を選択します。または、以前の設定を編集するには **[設定の編集]** を選択します。

   ![Managed Instance に対して診断を有効にする](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. 独自の参照の設定名を入力します。
1. 診断データのストリーミング先のリソースを **[ストレージ アカウントへのアーカイブ]**、**[イベント ハブへのストリーム]**、または **[Log Analytics への送信]** から選択します。
1. Log Analytics の場合は、**[構成]** を選択し、**[+Create New Workspace] (+ 新しいワークスペースの作成)** を選択することによって新しいワークスペースを作成するか、または既存のワークスペースを使用します。
1. インスタンスの診断テレメトリ **[ResourceUsageStats]** のチェックボックスをオンにします。
1. **[保存]** を選択します。

   ![Managed Instance に対して診断を構成する](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)

> [!TIP]
> 監視する Managed Instance ごとにこれらの手順を繰り返します。

### <a name="powershell"></a>PowerShell

PowerShell を使用してメトリックと診断のロギングを有効にできます。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   ストレージ アカウント ID は、送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Azure Service Bus ルール ID は、次の形式の文字列です。

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- 次のコマンドを使用して、Log Analytics ワークスペースのリソース ID を取得できます。

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="to-configure-multiple-azure-resources"></a>複数の Azure リソースの構成方法

複数のサブスクリプションをサポートするためには、「[Enable Azure resource metrics logging using PowerShell (PowerShell を使用して Azure リソース メトリックのログ記録を有効にする)](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/)」の PowerShell スクリプトを使用します。

スクリプト `Enable-AzureRMDiagnostics.ps1` の実行時にパラメーターとしてワークスペース リソース ID \<$WSID\> を入力すると、複数のリソースからの診断データをワークスペースに送信できます。

- 診断データの送信先のワークスペース ID \<$WSID\> を取得するには、次のスクリプトを使用します。

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```
   \<subID\> をサブスクリプション ID に、\<RG_NAME\> をリソース グループ名に、\<WS_NAME\> をワークスペース名に置き換えます。

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してメトリックと診断のロギングを有効にできます。

- ストレージ アカウントへの診断ログの保存を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   ストレージ アカウント ID は、送信先となるストレージ アカウントのリソース ID です。

- Event Hubs への診断ログのストリーミングを有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   Service Bus ルール ID は、この形式の文字列です。

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Log Analytics ワークスペースへの診断ログの送信を有効にするには、次のコマンドを使用します。

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

このパラメーターを組み合わせて、複数の出力オプションを有効にできます。

### <a name="rest-api"></a>REST API

[Azure Monitor REST API を使用して診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)方法を参照してください。

### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレートを使用してリソースの作成時に診断設定を有効にする](../azure-monitor/platform/diagnostic-logs-stream-template.md)方法を参照してください。

## <a name="stream-into-azure-sql-analytics"></a>Azure SQL Analytics へのストリーム

Azure SQL Analytics は、複数のサブスクリプションにわたって大規模に Azure SQL データベース、エラスティック プール、Managed Instance のパフォーマンスを監視するクラウド ソリューションです。 これは、Azure SQL Database のパフォーマンス メトリックの収集および視覚化に役立ち、パフォーマンスのトラブルシューティング用インテリジェンスが組み込まれています。

![Azure SQL Analytics の概要](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

SQL Database のメトリックと診断ログは、ポータルの診断設定タブに組み込まれている **[Send to Log Analytics] (Log Analytics に送信)** オプションを使用して Azure SQL Analytics にストリームできます。 Log Analytics は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して診断設定をすることでも有効にできます。

### <a name="installation-overview"></a>インストールの概要

Azure SQL Analytics を使用すると、SQL Database フリートを監視できます。 次の手順に従います。

1. Azure Marketplace から Azure SQL Analytics ソリューションを作成します。
2. ソリューションで監視ワークスペースを作成します。
3. ワークスペースに診断テレメトリをストリーミングするようにデータベースを構成します。

エラスティック プールまたは Managed Instance を使用している場合は、これらのリソースからの診断テレメトリのストリーミングを構成する必要があります。

### <a name="create-azure-sql-analytics-resource"></a>Azure SQL Analytics リソースを作成する

1. Azure Marketplace で Azure SQL Analytics を探し、それを選択します。

   ![ポータルで Azure SQL Analytics を検索する](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. ソリューションの概要画面で **[作成]** を選択します。

3. Azure SQL Analytics フォームに必要な追加情報 (ワークスペースの名前、サブスクリプション、リソース グループ、場所、価格レベル) を入力します。

   ![ポータルで Azure SQL Analytics を構成する](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. **[OK]** を選択して確認し、**[作成]** を選択します。

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>メトリックと診断ログを記録するようデータベースを構成する

データベースがメトリックを記録する場所を構成する最も簡単な方法は、Microsoft Azure portal を使用する方法です。 前述のように、Microsoft Azure portal で Microsoft Azure SQL Database リソースに移動し、**[診断設定]** を選択します。

エラスティック プールまたは Managed Instances を使用している場合、ワークスペースに診断テレメトリをストリーミングできるようにするには、これらのリソースで診断設定を構成する必要もあります。

### <a name="use-the-sql-analytics-solution"></a>Azure SQL Analytics ソリューションを使用する

階層型のダッシュボードとして Azure SQL Analytics を使用して、SQL Database リソースを表示できます。 SQL Analytics ソリューションの使用方法については、「[Monitor SQL Database by using the SQL Analytics solution](../log-analytics/log-analytics-azure-sql.md)」(SQL Analytics ソリューションを使用して SQL データベースを監視する) を参照してください。

## <a name="stream-into-event-hubs"></a>Event Hubs へのストリーム

Azure SQL Database のメトリックと診断ログは、Microsoft Azure portal に組み込まれている **[Stream to an event hub] (イベント ハブへのストリーム)** オプションを使用してイベント ハブにストリーミングできます。 Service Bus ルール ID は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API を使用して診断設定をすることでも有効にできます。

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Event Hubs におけるメトリックと診断ログの活用方法

選択したデータが Event Hubs にストリーミングされると、高度な監視シナリオを有効にできます。 Event Hubs は、イベント パイプラインの玄関口として機能します。 イベント ハブに収集されたデータは、リアルタイム分析プロバイダーやストレージ アダプターを使用して、変換および保存できます。 Event Hubs は、イベントのストリームの運用と、イベントの消費を分離します。 これにより、イベントのコンシューマーは独自のスケジュールでイベントにアクセスできます。 Event Hubs の詳細については、以下を参照してください。

- [Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Azure Event Hubs でストリーミングされたメトリックは、次の目的に使用できます。

- **サービスの正常性を表示するには、Power BI にホット パス データをストリーミングします**。 Event Hubs、Stream Analytics および Power BI を使用することで、メトリクスと診断データを Azure サービスの近リアルタイム洞察に簡単に転換できます。 Event Hubs の設定、Stream Analytics を使用したデータ処理、および PowerBI を出力として使用する方法の概要については、「[Stream Analytics と Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)」をご覧ください。

- **サード パーティ製のロギングおよびテレメトリ ストリームにログをストリームします**。 Event Hubs ストリーミングを使用することで、さまざまなサードパーティのモニタリングおよびログ解析ソリューションにマトリクスと診断ログを送信できます。

- **カスタム テレメトリおよびロギング プラットフォームの構築**。 既にカスタマイズされたテレメトリ プラットフォームがありますか、それとも構築を検討していますか? 高い拡張性の公開サブスクライブを特長とする Event Hubs を使用することで診断ログを柔軟に取り込むことができます。 [グローバル規模のテレメトリ プラットフォームで Event Hubs を使用する方法に関する Dan Rosanova によるガイド](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)をご覧ください。

## <a name="stream-into-storage"></a>ストレージへのストリーム

Azure SQL Database のメトリックと診断ログは、Microsoft Azure portal に組み込まれている **[ストレージ アカウントへのアーカイブ]** オプションを使用して Azure Storage に保存できます。 Storage は、PowerShell コマンドレット、Azure CLI、または Azure Monitor REST API の診断設定からも有効にできます。

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>ストレージ アカウントにおけるメトリックおよび診断ログのスキーマ

メトリックおよび診断ログの収集を設定した後、データの先頭行が取得されたときに、選択したストレージ アカウントにストレージ コンテナーが作成されます。 BLOB の構造は次のとおりです。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

または、次のようによりシンプルな構造になります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

たとえば、すべてのメトリックの BLOB 名は次のようになります。

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

エラスティック プールからのデータを格納する BLOB 名は次のようになります。

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-storage"></a>Azure Storage からメトリックとログをダウンロードする

[メトリックと診断ログをストレージからダウンロードする](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)を参照してください。

## <a name="data-retention-policy-and-pricing"></a>データ リテンション期間ポリシーと価格

Event Hubs または Storage アカウントを選択した場合は、保持ポリシーを指定できます。 このポリシーは、選択した期間よりも古いデータを削除します。 Log Analytics を指定した場合、リテンション期間ポリシーは選択した価格レベルに依存します。 この場合、提供される無料のデータ インジェスト単位数により、毎月いくつかのデータベースの無料の監視が可能になります。 診断テレメトリの消費量が無料の単位数を超えると、有料になる可能性があります。 アクティブなデータベースが取り込むデータは、ワークロードが重くなるほど、アイドル状態のデータベースよりも多くなることに注意してください。 詳細については、「[Log Analytics の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

Azure SQL Analytics を使用している場合は、Azure SQL Analytics のナビゲーション メニューで **[OMS ワークスペース]** を選択してから **[使用量**と**推定コスト]** を選択することによって、ソリューションでのデータ インジェストの消費を監視できます。

## <a name="metrics-and-logs-available"></a>利用可能なメトリックとログ

収集された監視テレメトリは、[SQL Analytics 言語](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)による独自の_カスタム分析_と_アプリケーション開発_に利用できます。

## <a name="all-metrics"></a>すべてのメトリック

リソースごとのすべてのメトリックの詳細については、次の表を参照してください。

### <a name="all-metrics-for-elastic-pools"></a>エラスティック プールのすべてのメトリック

|**リソース**|**メトリック**|
|---|---|
|エラスティック プール|eDTU の割合、使用中の eDTU、eDTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、ストレージの上限、XTP ストレージの割合 |

### <a name="all-metrics-for-azure-sql-databases"></a>Azure SQL Database のすべてのメトリック

|**リソース**|**メトリック**|
|---|---|
|Azure SQL データベース|DTU の割合、使用中の DTU、DTU の上限、CPU の割合、物理データ読み取りの割合、ログ書き込みの割合、ファイアウォール接続による成功/失敗/ブロック、セッションの割合、ワーカーの割合、ストレージ、ストレージの割合、XTP ストレージの割合、デッドロック |

## <a name="logs-for-managed-instance"></a>Managed Instance のログ

Managed Instance のログの詳細については、次の表を参照してください。

### <a name="resource-usage-statistics"></a>リソース使用統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure|
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にResourceUsageStats |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にMANAGEDINSTANCES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|Managed Instance の名前 |
|resourceId|リソース URI |
|SKU_s|Managed Instance の製品 SKU |
|virtual_core_count_s|使用可能な仮想コアの数 |
|avg_cpu_percent_s|平均 CPU の割合 |
|reserved_storage_mb_s|Managed Instance 上の予約済みストレージ容量 |
|storage_space_used_mb_s|Managed Instance 上の使用済みストレージ |
|io_requests_s|IOPS 数 |
|io_bytes_read_s|読み取り IOPS バイト |
|io_bytes_written_s|書き込み IOPS バイト |

## <a name="logs-for-azure-sql-databases-and-managed-instance-databases"></a>Azure SQL Database と Managed Instance データベースのログ

Azure SQL および Managed Instance データベースのログの詳細については、次の表を参照してください。

### <a name="query-store-runtime-statistics"></a>クエリ ストアのランタイム統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にQueryStoreRuntimeStatistics |
|OperationName|操作の名前。 常にQueryStoreRuntimeStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|query_hash_s|クエリ ハッシュ |
|query_plan_hash_s|クエリ プラン ハッシュ |
|statement_sql_handle_s|ステートメント sql ハンドル |
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset |
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset |
|logical_io_writes_d|論理 IO 書き込みの合計数 |
|max_logical_io_writes_d|実行ごとの論理 IO 書き込みの最大数 |
|physical_io_reads_d|物理 IO 読み取りの合計数 |
|max_physical_io_reads_d|実行ごとの論理 IO 読み取りの最大数 |
|logical_io_reads_d|論理 IO 読み取りの合計数 |
|max_logical_io_reads_d|実行ごとの論理 IO 読み取りの最大数 |
|execution_type_d|実行の種類 |
|count_executions_d|クエリの実行回数 |
|cpu_time_d|クエリで使用された合計 CPU 時間 (マイクロ秒単位) |
|max_cpu_time_d|1 回の実行による最大 CPU 時間コンシューマー (マイクロ秒単位) |
|dop_d|並列処理の次数の合計 |
|max_dop_d|1 回の実行で使用された並列処理の最大次数 |
|rowcount_d|返された行数の合計 |
|max_rowcount_d|1 回の実行で返された行の最大数 |
|query_max_used_memory_d|サポート技術情報で使用されたメモリの合計量 (KB 単位) |
|max_query_max_used_memory_d|1 回の実行で使用されたメモリの最大量 (KB 単位) |
|duration_d|合計実行時間 (マイクロ秒単位) |
|max_duration_d|1 回の実行の最大実行時間 |
|num_physical_io_reads_d|物理読み取りの合計数 |
|max_num_physical_io_reads_d|実行ごとの物理読み取りの最大数 |
|log_bytes_used_d|使用されたログの合計量 (バイト数) |
|max_log_bytes_used_d|実行ごとに使用されたログの最大量 (バイト数) |
|query_id_d|クエリ データ ストアでのクエリの ID |
|plan_id_d|クエリ データ ストアでのプランの ID |

[クエリ ストアのランタイム統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql)の詳細。

### <a name="query-store-wait-statistics"></a>クエリ ストアの待機統計

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にQueryStoreWaitStatistics |
|OperationName|操作の名前。 常にQueryStoreWaitStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|wait_category_s|待機のカテゴリ |
|is_parameterizable_s|クエリがパラメーター化可能かどうか |
|statement_type_s|ステートメントの種類 |
|statement_key_hash_s|ステートメント キー ハッシュ |
|exec_type_d|実行の種類 |
|total_query_wait_time_ms_d|特定の待機カテゴリでのクエリの合計待機時間 |
|max_query_wait_time_ms_d|特定の待機のカテゴリの個々の実行でのクエリの最大待機時間 |
|query_param_type_d|0 |
|query_hash_s|クエリ データ ストア内のクエリ ハッシュ |
|query_plan_hash_s|クエリ ストア内のクエリ プラン ハッシュ |
|statement_sql_handle_s|クエリ ストア内のステートメント ハンドル |
|interval_start_time_d|1900-1-1 からのティック数での間隔の開始 datetimeoffset |
|interval_end_time_d|1900-1-1 からのティック数での間隔の終了 datetimeoffset |
|count_executions_d|クエリの実行回数 |
|query_id_d|クエリ データ ストアでのクエリの ID |
|plan_id_d|クエリ データ ストアでのプランの ID |

[クエリ ストアの待機統計データ](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)の詳細。

### <a name="errors-dataset"></a>エラー データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQ |
|Category|カテゴリの名前。 常にErrors |
|OperationName|操作の名前。 常にErrorEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|Message|プレーンテキストでのエラー メッセージ |
|user_defined_b|エラーがユーザー定義ビットかどうか |
|error_number_d|エラー コード |
|severity|エラーの重大度 |
|state_d|エラーの状態 |
|query_hash_s|使用可能な場合は、失敗したクエリのクエリ ハッシュ |
|query_plan_hash_s|使用可能な場合は、失敗したクエリのクエリ プラン ハッシュ |

[Azure SQL Server エラー メッセージ](https://msdn.microsoft.com/library/cc645603.aspx)の詳細。

### <a name="database-wait-statistics-dataset"></a>データベースの待機統計データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にDatabaseWaitStatistics |
|OperationName|操作の名前。 常にDatabaseWaitStatisticsEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|wait_type_s|待機の種類の名前 |
|start_utc_date_t [UTC]|測定期間の開始時刻 |
|end_utc_date_t [UTC]|測定期間の終了時刻 |
|delta_max_wait_time_ms_d|実行ごとの最大待機時間 |
|delta_signal_wait_time_ms_d|シグナルの合計待機時間 |
|delta_wait_time_ms_d|期間内の合計待機時間 |
|delta_waiting_tasks_count_d|待機中のタスク数 |

[データベース待機統計](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)の詳細。

### <a name="time-outs-dataset"></a>タイムアウトのデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にTimeouts |
|OperationName|操作の名前。 常にTimeoutEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|error_state_d|エラー状態コード |
|query_hash_s|クエリ ハッシュ (使用可能な場合) |
|query_plan_hash_s|クエリ プラン ハッシュ (使用可能な場合) |

### <a name="blockings-dataset"></a>ブロックしているデータセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にBlocks |
|OperationName|操作の名前。 常にBlockEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|lock_mode_s|クエリで使用されるロック モード |
|resource_owner_type_s|ロックの所有者 |
|blocked_process_filtered_s|ブロックされているプロセスのレポート XML |
|duration_d|ロック期間 (ミリ秒) |

### <a name="deadlocks-dataset"></a>デッドロック データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC] |ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にデッドロック |
|OperationName|操作の名前。 常にDeadlockEvent |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|deadlock_xml_s|デッドロック レポート XML |

### <a name="automatic-tuning-dataset"></a>自動チューニング データセット

|プロパティ|説明|
|---|---|
|TenantId|テナント ID |
|SourceSystem|常にAzure |
|TimeGenerated [UTC]|ログが記録されたときのタイムスタンプ |
|type|常にAzureDiagnostics |
|ResourceProvider|リソース プロバイダーの名前。 常にMICROSOFT.SQL |
|Category|カテゴリの名前。 常にAutomaticTuning |
|リソース|リソースの名前 |
|ResourceType|リソースの種類の名前。 常にSERVERS/DATABASES |
|SubscriptionId|データベースのサブスクリプション GUID |
|ResourceGroup|データベースのリソース グループの名前 |
|LogicalServerName_s|データベースのサーバーの名前 |
|LogicalDatabaseName_s|データベースの名前 |
|ElasticPoolName_s|データベースのエラスティック プールの名前 (存在する場合) |
|DatabaseName_s|データベースの名前 |
|resourceId|リソース URI |
|RecommendationHash_s|自動チューニング推奨設定の一意のハッシュ |
|OptionName_s|自動チューニング オプション |
|Schema_s|データベース スキーマ |
|Table_s|影響を受けるテーブル |
|IndexName_s|インデックス名 |
|IndexColumns_s|列名 |
|IncludedColumns_s|含まれる列 |
|EstimatedImpact_s|自動チューニング推奨設定 JSON の推定される影響 |
|Event_s|自動チューニング イベントの種類 |
|Timestamp_t|最終更新時のタイムスタンプ |

### <a name="intelligent-insights-dataset"></a>Intelligent Insights データセット

[Intelligent Insights ログ形式](sql-database-intelligent-insights-use-diagnostics-log.md)の詳細。

## <a name="next-steps"></a>次の手順

ログ記録を有効にする方法や、各種の Azure サービスでサポートされているメトリックとログのカテゴリについては、次の資料を参照してください。

- [Microsoft Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Azure 診断ログの概要](../azure-monitor/platform/diagnostic-logs-overview.md)

Event Hubs の詳細については、次の資料を参照してください。

- [Azure Event Hubs とは](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Azure Storage の詳細については[メトリックと診断ログをストレージからダウンロードする](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-the-sample-application)を参照してください。
