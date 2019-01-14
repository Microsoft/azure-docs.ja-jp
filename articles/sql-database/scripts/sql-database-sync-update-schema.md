---
title: PowerShell の例 - SQL データ同期の同期スキーマを更新する | Microsoft Docs
description: SQL データ同期の同期スキーマを更新するための Azure PowerShell のサンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: allenwux
ms.author: xiwu
ms.reviewer: douglasl
manager: craigg
ms.date: 01/10/2018
ms.openlocfilehash: f0e23371bc615283c48e5ed14ac08edc93802efc
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53717861"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell を使用して、既存の同期グループの同期スキーマを更新する

この PowerShell の例では、既存の SQL データ同期の同期グループの同期スキーマを更新します。 複数のテーブルを同期しているときは、同期スキーマの効率的な更新にこのスクリプトが役立ちます。 この例では、**UpdateSyncSchema** スクリプトの使用方法を示します。このスクリプトは、GitHub で [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) として入手できます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

SQL データ同期の概要については、[Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](../sql-database-sync-data.md)に関する記事を参照してください。

> [!IMPORTANT]
> Azure SQL データ同期は、現時点では Azure SQL Database Managed Instance をサポート**していません**。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="example-1---add-all-tables-to-the-sync-schema"></a>例 1 - 同期スキーマにすべてのテーブルを追加する

次の例では、データベース スキーマを最新の情報に更新し、ハブ データベースのすべての有効なテーブルを同期スキーマに追加します。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="example-2---add-and-remove-tables-and-columns"></a>例 2 - テーブルおよび列を追加および削除する

次の例では、`[dbo].[Table1]` と `[dbo].[Table2].[Column1]` を同期スキーマに追加し、`[dbo].[Table3]` を削除します。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscription_id> -ResourceGroupName <resource_group_name> -ServerName <server_name> -DatabaseName <database_name> -SyncGroupName <sync_group_name> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>スクリプト パラメーター

**UpdateSyncSchema** スクリプトには、次のパラメーターがあります。

| パラメーター | メモ |
|---|---|
| $SubscriptionId | 同期グループが作成されるサブスクリプション。 |
| $ResourceGroupName | 同期グループが作成されるリソース グループ。|
| $ServerName | ハブ データベースのサーバー名。|
| $DatabaseName | ハブ データベース名。 |
| $SyncGroupName | 同期グループ名。 |
| $MemberName | ハブ データベースからではなく同期メンバーからデータベース スキーマを読み込む場合は、メンバー名を指定します。 ハブからデータベース スキーマを読み込む場合は、このパラメーターは空のままにします。 |
| $TimeoutInSeconds | スクリプトがデータベース スキーマを最新の情報に更新するときのタイムアウト。 既定値は 900 秒です。 |
| $RefreshDatabaseSchema | スクリプトがデータベース スキーマを最新の情報に更新する必要があるかどうかを指定します。 データベース スキーマが以前の構成から変化している場合 (たとえば、新しいテーブルまたは新しい列を追加した場合)、スキーマを再構成する前に最新の情報に更新する必要があります。 既定値は false です。 |
| $AddAllTables | この値を true にすると、すべての有効なテーブルと列が同期スキーマに追加されます。 $TablesAndColumnsToAdd と $TablesAndColumnsToRemove の値は無視されます。 |
| $TablesAndColumnsToAdd | 同期スキーマに追加するテーブルまたは列を指定します。 各テーブルまたは列の名前は、スキーマ名で完全に区切る必要があります。 例: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 複数のテーブルまたは列の名前をコンマ (,) で区切って指定できます。 |
| $TablesAndColumnsToRemove | 同期スキーマから削除するテーブルまたは列を指定します。 各テーブルまたは列の名前は、スキーマ名で完全に区切る必要があります。 例: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 複数のテーブルまたは列の名前をコンマ (,) で区切って指定できます。 |
|||

## <a name="script-explanation"></a>スクリプトの説明

**UpdateSyncSchema** スクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncgroup) | 同期グループについての情報を返します。 |
| [Update-AzureRmSqlSyncGroup](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncgroup) | 同期グループを更新します。 |
| [Get-AzureRmSqlSyncMember](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncmember) | 同期メンバーについての情報を返します。 |
| [Get-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlsyncschema) | 同期スキーマについての情報を返します。 |
| [Update-AzureRmSqlSyncSchema](https://docs.microsoft.com/powershell/module/azurerm.sql/update-azurermsqlsyncschema) | 同期スキーマを更新します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。

SQL データ同期の詳細については、以下を参照してください。

-   概要 - [Azure SQL データ同期を使用して複数のクラウドおよびオンプレミス データベース間でデータを同期する](../sql-database-sync-data.md)
-   データ同期の設定
    - ポータル - [チュートリアル:Azure SQL Database とオンプレミスの SQL Server の間でデータを同期するように SQL データ同期を設定する](../sql-database-get-started-sql-data-sync.md)
    - PowerShell の場合
        -  [PowerShell を使用した複数の Azure SQL データベース間の同期](sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell を使用した Azure SQL Database と SQL Server オンプレミス データベース間の同期](sql-database-sync-data-between-azure-onprem.md)
-   データ同期エージェント - [Azure SQL データ同期のデータ同期エージェント](../sql-database-data-sync-agent.md)
-   ベスト プラクティス - [Azure SQL データ同期のベスト プラクティス](../sql-database-best-practices-data-sync.md)
-   監視 - [Log Analytics による SQL データ同期の監視](../sql-database-sync-monitor-oms.md)
-   トラブルシューティング - [Azure SQL データ同期に関する問題のトラブルシューティング](../sql-database-troubleshoot-data-sync.md)
-   同期スキーマの更新
    -   Transact-SQL の場合 - [Azure SQL データ同期内でスキーマ変更のレプリケートを自動化する](../sql-database-update-sync-schema.md)

SQL Database の詳細については、以下を参照してください。

-   [SQL Database の概要](../sql-database-technical-overview.md)
-   [データベースのライフサイクル管理](https://msdn.microsoft.com/library/jj907294.aspx)
