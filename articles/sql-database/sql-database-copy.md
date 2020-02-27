---
title: データベースをコピーする
description: トランザクション上一貫性のある既存の Azure SQL データベースのコピーを、同じサーバーまたは別のサーバーのいずれかに作成します。
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sashan
ms.reviewer: carlrab
ms.date: 11/14/2019
ms.openlocfilehash: e1df345fb9a89972ad1857a937c22d6e10ad1fba
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289409"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>トランザクション上一貫性のある Azure SQL データベースのコピーを作成する

Azure SQL Database では、同じサーバーまたは別のサーバーのいずれかに、トランザクション上一貫性のある、既存の Azure SQL データベース ([単一データベース](sql-database-single-database.md)) のコピーを作成するための複数の方法が用意されています。 Azure Portal、PowerShell、または T-SQL を使って、SQL Database をコピーできます。

## <a name="overview"></a>概要

データベースのコピーとは、コピーが要求された時点でのソース データベースのスナップショットのことです。 同じサーバーまたは別のサーバーを選択できます。 また、そのサービス レベルとコンピューティング サイズを維持するか、同じサービス レベル (エディション) 内の別のコンピューティング サイズを使用することもできます。 コピーの完了後、コピーは完全に機能する独立したデータベースになります。 この時点では、任意のエディションにアップグレードまたはダウン グレードできます。 ログイン、ユーザー、アクセス許可は非依存で管理できます。 コピーは geo レプリケーション テクノロジを使用して作成され、シード処理が完了すると、geo レプリケーション リンクは自動的に終了します。 geo レプリケーションを使用するためのすべての要件が、データベースのコピー操作に適用されます。 詳細については、「[アクティブ geo レプリケーションの作成と使用](sql-database-active-geo-replication.md)」を参照してください。

> [!NOTE]
> データベースのコピーを作成するときは、[自動データベース バックアップ](sql-database-automated-backups.md)が使用されます。

## <a name="logins-in-the-database-copy"></a>データベースのコピーへのログイン

データベースを同じ SQL Database サーバーにコピーすると、両方のデータベースで同じログインを利用できます。 データベースをコピーするために使うセキュリティ プリンシパルが、新しいデータベースのデータベース所有者になります。 すべてのデータベース ユーザー、アクセス許可、セキュリティ識別子 (SID) がデータベースのコピーにコピーされます。  

別の SQL Database サーバーにデータベースをコピーする場合は、新しいサーバーのセキュリティ プリンシパルが新しいデータベースのデータベース所有者になります。 データのアクセスに[包含データベース ユーザー](sql-database-manage-logins.md)を使う場合は、プライマリとセカンダリの両方のデータベースが常に同じユーザー資格情報を持つようにします。そうすることで、コピーの完了後すぐに同じ資格情報でアクセスできるようになります。

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) を使用すると、コピーで資格情報を管理する必要がなくなります。 ただし、新しいサーバーにデータベースをコピーする場合、ログインに基づくアクセスは動作しないことがあります。これは、そのログインが新しいサーバーに存在しないためです。 別の SQL Database サーバーにデータベースをコピーする場合のログイン管理の詳細については、[ディザスター リカバリー後に Azure SQL データベース セキュリティを管理する方法](sql-database-geo-replication-security-config.md)に関するページをご覧ください。

コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者のみが新しいデータベースにログインできます。 コピー操作が完了した後でログインを解決するには、「[ログインの解決](#resolve-logins)」をご覧ください。

## <a name="copy-a-database-by-using-the-azure-portal"></a>Azure Portal を使ってデータベースをコピーする

Azure Portal を使ってデータベースをコピーするには、データベースのページを開き、 **[コピー]** をクリックします。

   ![データベースのコピー](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>PowerShell を使ってデータベースをコピーする

データベースをコピーするには、次の例を使用します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell の場合、[New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy) コマンドレットを使用します。

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。  Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

データベースのコピーは非同期操作ですが、要求が受け入れられた直後にターゲット データベースが作成されます。 まだ進行中のコピー操作を取り消す必要がある場合は、[Remove-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) コマンドレットを使用してターゲット データベースをドロップします。

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azure-cli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

データベースのコピーは非同期操作ですが、要求が受け入れられた直後にターゲット データベースが作成されます。 まだ進行中のコピー操作を取り消す必要がある場合は、[az sql db delete](/cli/azure/sql/db#az-sql-db-delete) コマンドを使用してターゲット データベースをドロップします。

* * *

完全なサンプル スクリプトについては、「[新しいサーバーにデータベースをコピーする](scripts/sql-database-copy-database-to-new-server-powershell.md)」をご覧ください。

## <a name="rbac-roles-to-manage-database-copy"></a>データベースのコピーを管理する RBAC ロール

データベースのコピーを作成するには、次のロールが必要です

- サブスクリプションの所有者または
- SQL Server 共同作成者ロールまたは
- 次のアクセス許可を持つ、ソース データベースとターゲット データベースのカスタム ロール:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

データベースのコピーを取り消すには、次のロールが必要です

- サブスクリプションの所有者または
- SQL Server 共同作成者ロールまたは
- 次のアクセス許可を持つ、ソース データベースとターゲット データベースのカスタム ロール:

   Microsoft.Sql/servers/databases/read  Microsoft.Sql/servers/databases/write

Azure portal を使用してデータベースのコピーを管理するには、以下のアクセス許可も必要です。

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

ポータルでリソース グループのデプロイの下の操作と、SQL 操作を含む、複数のリソースプロバイダーにまたがる操作を表示するには、以下の追加の RBAC ロールが必要です。

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="copy-a-database-by-using-transact-sql"></a>Transact-SQL を使ってデータベースをコピーする

サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使って、マスター データベースにログオンします。 データベースを正常にコピーするには、サーバーレベル プリンシパルではないログインが dbmanager ロールのメンバーである必要があります。 ログインおよびサーバーへの接続の詳細については、 [ログインの管理](sql-database-manage-logins.md)に関するページを参照してください。

[CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) ステートメントを使用して、コピー元データベースのコピーを開始します。 このステートメントを実行すると、データベースのコピー処理が始まります。 データベースのコピーは非同期的な処理であるため、CREATE DATABASE ステートメントからは、データベースのコピーが完了する前に戻ります。

### <a name="copy-a-sql-database-to-the-same-server"></a>同じサーバーへの SQL データベースのコピー

サーバーレベル プリンシパル ログインか、コピーするデータベースを作成したときのログインを使って、マスター データベースにログオンします。 データベースを正常にコピーするには、サーバーレベル プリンシパルではないログインが dbmanager ロールのメンバーである必要があります。

このコマンドは、Database1 を、同じサーバー上の Database2 という名前の新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

   ```sql
   -- execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-a-sql-database-to-a-different-server"></a>別のサーバーへの SQL データベースのコピー

コピー先サーバー (新しいデータベースを作成する SQL Database サーバー) のマスター データベースにログインします。 コピー元の SQL Database サーバー上のコピー元データベースのデータベース所有者と同じ名前とパスワードを持つログインを使います。 コピー先サーバーでのログインも、dbmanager ロールのメンバーであるか、サーバーレベル プリンシパル ログインである必要があります。

このコマンドは、server1 上の Database1 を、server2 上の Database2 という新しいデータベースにコピーします。 データベースのサイズに応じて、コピー操作の完了に時間がかかる場合があります。

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> T-SQL COPY コマンドを発行するクライアントの IP からのインバウンド接続を許可するように、両方のサーバーのファイアウォールを構成する必要があります。

### <a name="copy-a-sql-database-to-a-different-subscription"></a>別のサブスクリプションに SQL データベースをコピーする

前のセクションに示された手順を使用して、別のサブスクリプションの SQL Database サーバーにデータベースをコピーすることができます。 名前とパスワードがソース データベースのデータベース所有者と同じであるログインを使用しており、それが dbmanager ロールのメンバーであるか、サーバーレベルのプリンシパル ログインであることを確認してください。 

> [!NOTE]
> [Azure portal](https://portal.azure.com) では、別のサブスクリプションへのコピーはサポートされていません。ポータルでは ARM API を呼び出し、サブスクリプション資格情報を使用して、geo レプリケーションに関係する両方のサーバーにアクセスするためです。  

### <a name="monitor-the-progress-of-the-copying-operation"></a>コピー操作の進行状況を監視する

sys.databases ビューと sys.dm_database_copies ビューを照会して、コピー操作を監視します。 コピー操作の進行中は、新しいデータベースの sys.databases ビューの **state_desc** 列が **COPYING** に設定されます。

* コピー操作が失敗すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **SUSPECT** に設定されます。 新しいデータベースに対して DROP ステートメントを実行した後でもう一度やり直してください。
* コピー操作が成功すると、新しいデータベースの sys.databases ビューの **state_desc** 列が **ONLINE** に設定されます。 コピー操作は完了しています。新しいデータベースは通常のデータベースであり、コピー元データベースから独立して変更することができます。

> [!NOTE]
> 進行中のコピー操作を取り消すには、新しいデータベースに対して [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) ステートメントを実行します。 また、コピー元のデータベースに対して DROP DATABASE ステートメントを実行することによっても、コピー操作を取り消すことができます。

> [!IMPORTANT]
> ソースよりも SLO が非常に小さいコピーを作成する必要がある場合、ターゲット データベースには、シード処理を完了するための十分なリソースがない可能性があります。これにより、コピー操作が失敗する可能性があります。 このシナリオでは、geo リストア要求を使用して、別のサーバーや別のリージョンにコピーを作成します。 詳細については、「[データベースのバックアップを使用した Azure SQL データベースの復旧](sql-database-recovery-using-backups.md#geo-restore)」を参照してください。

## <a name="resolve-logins"></a>ログインの解決

新しいデータベースがコピー先サーバーでオンラインになった後、 [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) ステートメントを使用して、ユーザーを新しいデータベースからコピー先サーバーのログインに再マップします。 孤立したユーザーを解決するには、「 [孤立ユーザーのトラブルシューティング](https://msdn.microsoft.com/library/ms175475.aspx)」をご覧ください。 「 [障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」もご覧ください。

新しいデータベースのすべてのユーザーのアクセス許可は、コピー元データベースで保持していたものと同じです。 データベースのコピーを開始したユーザーが新しいデータベースのデータベース所有者になり、新しいセキュリティ識別子 (SID) が割り当てられます。 コピーが成功した後、他のユーザーが再マップされるまでは、コピーを開始したログイン、つまりデータベース所有者のみが新しいデータベースにログインできます。

別の SQL Database サーバーにデータベースをコピーする場合のユーザーとログインの管理の詳細については、[ディザスター リカバリー後に Azure SQL データベース セキュリティを管理する方法](sql-database-geo-replication-security-config.md)に関する方法をご覧ください。

## <a name="database-copy-errors"></a>データベース コピー エラー

次のエラーは、Azure SQL Database でデータベースをコピーしているときに発生する可能性があります。 詳細については、「 [Azure SQL Database のコピー](sql-database-copy.md)」を参照してください。

| エラー コード | 重大度 | 説明 |
| ---:| ---:|:--- |
| 40635 |16 |IP アドレス '%.&#x2a;ls' のクライアントは一時的に無効化されています。 |
| 40637 |16 |データベース コピーの作成は現在無効です。 |
| 40561 |16 |データベースのコピーに失敗しました。 ソースまたはターゲットのデータベースが存在しません。 |
| 40562 |16 |データベースのコピーに失敗しました。 ソース データベースは削除されました。 |
| 40563 |16 |データベースのコピーに失敗しました。 ターゲット データベースは削除されました。 |
| 40564 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40565 |16 |データベースのコピーに失敗しました。 同じソースから複数のデータベース コピーを同時に実行することはできません。 ターゲット データベースを削除してやり直してください。 |
| 40566 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40567 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40568 |16 |データベースのコピーに失敗しました。 ソース データベースが使用できなくなりました。 ターゲット データベースを削除してやり直してください。 |
| 40569 |16 |データベースのコピーに失敗しました。 ターゲット データベースが使用できなくなりました。 ターゲット データベースを削除してやり直してください。 |
| 40570 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |
| 40571 |16 |データベースのコピーに失敗しました。内部エラーが発生しました。 ターゲット データベースを削除してやり直してください。 |

## <a name="next-steps"></a>次のステップ

- ログインについては、「[ログインの管理](sql-database-manage-logins.md)」および「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。
- データベースをエクスポートするには、「[BACPAC へのデータベースのエクスポート](sql-database-export.md)」をご覧ください。
