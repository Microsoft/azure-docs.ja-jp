---
title: Azure SQL Database のバックアップ - 自動、geo 冗長 | Microsoft Docs
description: SQL Database は数分ごとにローカル データベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 12/10/2018
ms.openlocfilehash: 2d6df569a2b5b813bd832adf5ef2e1d193de9364
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187570"
---
# <a name="automated-backups"></a>自動バックアップ

SQL Database では 7 - 35 日間保存されるデータベース バックアップが自動的に作成され、データ センターを使用できない場合でも、Azure の読み取りアクセス geo 冗長ストレージ (RA-GRS) を使用して確実に保存されます。 バックアップは自動的に作成され、追加料金は発生しません。 この処理のために何もする必要はありません。[バックアップの保有期間は変更](#how-to-change-the-pitr-backup-retention-period)することができます。 データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 セキュリティ規則で、長期間バックアップ (最長 10 年間) を利用できる必要がある場合は、[長期保有](sql-database-long-term-retention.md)を構成できます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは

SQL Database は、ポイントインタイム リストア (PITR) の目的で、SQL Server 技術を利用して、[完全](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)バックアップ、[差分](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)バックアップ、[トランザクション ログ](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server) バックアップを作成します。 トランザクション ログ バックアップは一般的には 5 - 10 分ごとに発生し、差分バックアップは 12 時間ごとに行われます。頻度はコンピューティング サイズとデータベース アクティビティの量に基づきます。 完全、差分、およびトランザクション ログ バックアップにより、データベースをホストする同じサーバーに、データベースを特定の時点に復元できます。 バックアップは RA-GRS ストレージ BLOB に格納され、この BLOB はデータ センターの停止に対する保護のために[ペアのデータ センター](../best-practices-availability-paired-regions.md)にレプリケートされます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。

これらのバックアップを使用して、以下を行うことができます。

- リテンション期間内の特定の時点にデータベースを復元します。 この操作により、元のデータベースと同じサーバーに新しいデータベースが作成されます。
- 削除したデータベースを、削除された時点または保有期間内の任意の時点に復元します。 削除されたデータベースは、元のデータベースが作成されたサーバーと同じサーバーにのみ復元できます。
- 別の地理的リージョンにデータベースを復元します。 geo リストアにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。
- データベースが長期アイテム保持ポリシー (LTR) で構成されている場合、そのデータベースは、特定の長期バックアップから復元します。 LTR により、データベースの古いバージョンに復元でき、コンプライアンスの要求を満たし、またはアプリケーションの古いバージョンを実行できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。
- 復元を実行するには、[バックアップからのデータベースの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の *データベース レプリケーション* は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。

## <a name="how-long-are-backups-kept"></a>バックアップの保持期間

各 SQL Database には、7 日～ 35 日まで ([購入モデルとサービス レベル](#pitr-retention-period)によって異なります) の既定のバックアップの保持期間があります。 Azure Logical Server 上のデータベースについてバックアップのリテンション期間を更新することができます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-the-pitr-backup-retention-period)に関するセクションを参照してください。

データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、7 日間のリテンション期間のある基本的なデータベースを削除すると、4 日間保存されているバックアップはさらに 3 日間保存されます。

最大の保有期間より長くバックアップを保持する必要がある場合は、データベースに 1 つまたは複数のより長い保有期間を追加するようにバックアップのプロパティを変更できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

> [!IMPORTANT]
> SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのエラスティック プールとデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。 長期保有を構成した場合、LTR を使用したデータベースのバックアップは削除されず、これらのデータベースを復元することができます。

### <a name="default-backup-retention-period"></a>既定のバックアップの保有期間

#### <a name="dtu-based-purchasing-model"></a>DTU ベースの購入モデル

DTU ベースの購入モデルを使用して作成されたデータベースのリテンション期間は、サービス レベルに依存します。

- Basic サービスレベルの場合、1 週間です。
- Standard サービス レベルの場合、5 週間です。
- Premium サービス レベルの場合、5 週間です。

#### <a name="vcore-based-purchasing-model"></a>仮想コアベースの購入モデル

[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を使用している場合、既定のバックアップの保有期間は 7 日間です (単一、プール、およびマネージド インスタンス データベースの場合)。 すべての Azure SQL データベース (単一、プール、およびマネージド インスタンス データベース) の場合、[バックアップの保有期間を最大 35 日に変更](#how-to-change-the-pitr-backup-retention-period)できます。

> [!WARNING]
> 現在のリテンション期間を短縮した場合、新しいリテンション期間より古いすべての既存のバックアップは、使用できなくなります。 現在のリテンション期間を延長した場合、SQL Database は、より長いリテンション期間に達するまでに、既存のバックアップを保持します。

## <a name="how-often-do-backups-happen"></a>バックアップが行われる頻度

### <a name="backups-for-point-in-time-restore"></a>ポイントインタイム リストアのバックアップ

SQL Database では、完全バックアップ、差分バックアップ、トランザクション ログ バックアップを自動的に作成して、ポイントインタイム リストア (PITR) のセルフ サービスをサポートします。 完全データベース バックアップは毎週、差分データベース バックアップは一般的に 12 時間ごとに、トランザクション ログ バックアップは通常、5 - 10 分ごとに作成されます。頻度は、コンピューティング サイズとデータベース アクティビティの量に基づきます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら SQL Database サービスによって決定されます。

PITR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)」をご覧ください。

### <a name="backups-for-long-term-retention"></a>長期保有のためのバックアップ

論理サーバーでホストされる SQL Database には、Azure Blob Storage での最大 10 年の完全バックアップの長期保有 (LTR) を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、週次の完全バックアップは自動的に別の RA-GRS ストレージ コンテナーにコピーされます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。

PITR と同じように、LTR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[Long-term backup retention](sql-database-long-term-retention.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="are-backups-encrypted"></a>バックアップの暗号化

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 Azure SQL データベースに対して TDE が有効になっているとき、バックアップも暗号化されます。 新しい Azure SQL データベースはすべて、既定で TDE が有効になった状態で構成されます。 TDE に関する詳細については、「[Azure SQL Database での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)」をご覧ください。

## <a name="how-does-microsoft-ensure-backup-integrity"></a>Microsoft はどのようにバックアップの整合性を保証しているか

Azure SQL Database のエンジニアリング チームは、サービス全体でデータベースの自動データベース バックアップの復元の自動テストを継続的に行っています。 復元時に、データベースは DBCC CHECKDB を使用した整合性チェックも受けます。 整合性チェック中に問題が見つかると、エンジニアリング チームにアラートが送信されます。 Azure SQL Database におけるデータ整合性の詳細については、「[Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」 (Azure SQL Database でのデータ整合性) を参照してください。

## <a name="how-do-automated-backups-impact-compliance"></a>自動バックアップによるコンプライアンスへの影響

PITR リテンション期間が 35 日間である DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ リカバリ ポリシーが損なわれないように、PITR リテンション期間が保持されます。 既定のリテンション期間では、お客様のコンプライアンス要件を満たしていない場合は、PowerShell または REST API を使用して PITR リテンション期間を変更できます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-the-pitr-backup-retention-period)に関するセクションを参照してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-the-pitr-backup-retention-period"></a>PITR のバックアップの保有期間を変更する方法

Azure portal、PowerShell、または REST API を使用して、既定の PITR のバックアップの保有期間を変更できます。 サポートされる値は7、14、21、28、または 35 日間です。 次の例では、PITR リテンション期間を 28 日間に変更する方法を示します。

> [!NOTE]
> これらの API は PITR リテンション期間にのみ影響を与えます。 データベースの LTR を構成した場合、それには影響ありません。 LTR の保持期間を変更する方法については、[長期保有](sql-database-long-term-retention.md)に関するページを参照してください。

### <a name="change-pitr-backup-retention-period-using-the-azure-portal"></a>Azure portal を使用して PITR のバックアップの保有期間を変更する

Azure portal を使用して PITR のバックアップの保有期間を変更するには、保有期間を変更するデータベースに移動し、**[概要]** をクリックします。

![Azure portal の PITR の変更](./media/sql-database-automated-backup/configure-backup-retention.png)

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell を使用して PITR バックアップ リテンション期間を変更する

```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

> [!IMPORTANT]
> この API は、バージョン [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview) 以降の AzureRM.Sql PowerShell モジュールに含まれています。

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API を使用して PITR リテンション期間を変更する

#### <a name="sample-request"></a>要求のサンプル

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
  "properties":{  
      "retentionDays":28
   }
}
```

#### <a name="sample-response"></a>応答のサンプル

状態コード:200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

詳細については、[バックアップの保有期間の REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure Portal を使用して特定の時点に復元する方法については、[Azure Portal を使用したデータベースのポイントインタイム リストア](sql-database-recovery-using-backups.md)に関するページをご覧ください。
- PowerShell を使用して特定の時点に復元する方法については、[PowerShell を使用したデータベースのポイントインタイム リストア](scripts/sql-database-restore-database-powershell.md)に関するページをご覧ください。
- Azure Blob Storage に長期保存される自動バックアップを Azure portal を使用して構成、管理、復元する方法については、[Azure portal を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保存される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
