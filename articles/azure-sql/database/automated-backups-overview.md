---
title: 自動の geo 冗長バックアップ
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database および Azure SQL Managed Instance は数分ごとにローカル データベースを自動的にバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: backup-restore
ms.custom: references_regions
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
ms.date: 08/04/2020
ms.openlocfilehash: 485a137f552ca06fba366d261eb38268d821ccaf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853217"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自動バックアップ - Azure SQL Database および SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-database-backup"></a>データベースのバックアップとは

データの破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 これらのバックアップにより、構成された保有期間内の特定の時点にデータベースを復元できます。 データ保護規則で、バックアップを長期間 (最長 10 年間) 利用できるようにする必要がある場合は、単一データベースとプールされたデータベースの両方で[長期保有](long-term-retention-overview.md)を構成できます。

### <a name="backup-frequency"></a>バックアップ頻度

SQL Database と SQL Managed Instance は SQL Server 技術を利用して、[完全バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)を毎週、[差分バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)を 12 から 24 時間ごと、そして[トランザクション ログ バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)を 5 から 10 分ごとに作成します。 トランザクション ログ バックアップの頻度は、コンピューティング サイズとデータベース アクティビティの量に基づいて決まります。

データベースを復元するとき、復元する必要がある完全バックアップ、差分バックアップ、トランザクション ログ バックアップはどれであるかがサービスによって判定されます。

### <a name="backup-storage-redundancy"></a>バックアップ ストレージの冗長性

SQL Database と SQL Managed Instance では既定で、[ペアになっているリージョン](../../best-practices-availability-paired-regions.md)にレプリケートされる geo 冗長 (RA-GRS) [ストレージ BLOB](../../storage/common/storage-redundancy.md) にデータが格納されます。 それにより、プライマリ リージョンのバックアップ ストレージに影響する障害が起きないように保護され、万一障害が発生しても別のリージョンにサーバーを復元できます。 

SQL Managed Instance には、マネージド インスタンスがデプロイされる同じリージョン内にデータが確実に留まるように、ストレージ冗長性をローカル冗長 (LRS) またはゾーン冗長 (ZRS) のストレージ BLOB に変更する機能が導入されています。 ストレージの冗長性メカニズムでは、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止や停電、大規模な自然災害など) からデータを保護するため、データのコピーが複数格納されます。 

バックアップ ストレージの冗長性を構成するオプションによって、SQL Managed Instance には LRS、ZRS、または RA-GRS のストレージ BLOB から選択できる柔軟性が与えられます。 リソースがプロビジョニングされたら、マネージド インスタンス作成プロセス中にバックアップ ストレージ冗長性を構成します。ストレージ冗長性を変更することはできなくなりました。 ゾーン冗長ストレージ (ZRS) は現在、[特定のリージョン](../../storage/common/storage-redundancy.md#zone-redundant-storage)でのみ利用できます。


> [!IMPORTANT]
> SQL Managed Instance では、構成されたバックアップの冗長性が、ポイントインタイム リストア (PITR) に使用される短期保有バックアップ設定と長期的バックアップ (LTR) に使用される長期保有バックアップの両方に適用されます。


> [!NOTE]
> Azure SQL Database の構成可能なバックアップ ストレージ冗長性は現在、東南アジア Azure リージョンの一部のお客様が制限付きのプライベート プレビューとして利用できます。 このプライベート プレビューへの登録をご希望の場合は、[sqlbackuppreview@microsoft.com](mailto:sqlbackuppreview@microsoft.com) にお問い合わせください。 

データ保護規則で、バックアップを長期間 (最長 10 年間) 利用できるようにする必要がある場合は、単一データベースとプールされたデータベースの両方で[長期保有](long-term-retention-overview.md)を構成できます。

### <a name="backup-usage"></a>バックアップの用途


これらのバックアップを使用して、以下を行うことができます。

- **既存データベースのポイントインタイム リストア** - Azure portal、Azure PowerShell、Azure CLI、または REST API を使用して、保持期間内の[過去の特定の時点に既存のデータベースを復元します](recovery-using-backups.md#point-in-time-restore)。 SQL Database では、この操作により、元のデータベースと同じサーバーに新しいデータベースが作成されますが、元のデータベースが上書きされるのを防ぐため、別の名前が使用されます。 復元が完了したら、元のデータベースを削除することができます。 または、元のデータベースの[名前を変更](https://docs.microsoft.com/sql/relational-databases/databases/rename-a-database)したうえで、復元されたデータベースの名前を元のデータベース名に変更してもかまいません。 SQL Managed Instance でも同様に、この操作により、同じリージョン、同じサブスクリプションの同じ、または異なるマネージド インスタンスに、データベースのコピーが作成されます。
- **削除されたデータベースのポイントインタイム リストア** - [削除されたデータベースを削除の時点に復元します](recovery-using-backups.md#deleted-database-restore)。または、保持期間内の特定の時点に復元します。 削除されたデータベースは、元のデータベースが作成されていたのと同じサーバーまたはマネージド インスタンスにのみ復元できます。 データベースを削除すると、データが失われないように、削除前にサービスによって最後のトランザクション ログ バックアップが取得されます。
- **geo リストア** - [別の地理的リージョンにデータベースを復元します](recovery-using-backups.md#geo-restore)。 geo リストアを使用すると、プライマリ リージョンのデータベースまたはバックアップにアクセスできないときでも、地理的な災害から復旧できます。 任意の Azure リージョンの既存のサーバーまたはマネージド インスタンスに、新しいデータベースが作成されます。
   > [!IMPORTANT]
   > geo リストアは、geo 冗長 (RA-GRS) バックアップ ストレージが構成されたマネージド インスタンスでのみ利用できます。
- **長期的バックアップからの復元** - データベースの長期保有ポリシー (LTR) が構成されている場合、単一データベースまたはプールされたデータベースの[特定の長期バックアップからデータベースを復元します](long-term-retention-overview.md)。 LTR により、[Azure portal](long-term-backup-retention-configure.md#using-the-azure-portal) または [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) を使用して、コンプライアンスの要求を満たすため、またはアプリケーションの以前バージョンを実行するために、以前のバージョンのデータベースを復元できます。 詳細については、「[長期保存](long-term-retention-overview.md)」をご覧ください。

復元を実行するには、[バックアップからのデータベースの復元](recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage では、"*レプリケーション*" とは、ある場所から別の場所に BLOB をコピーすることを表します。 SQL では、"*データベース レプリケーション*" とは、複数のセカンダリ データベースをプライマリ データベースと同期しておくために使用されるさまざまなテクノロジのことです。

次の例を使用して、バックアップの構成と復元の操作を試すことができます。

| 操作 | Azure portal | Azure PowerShell |
|---|---|---|
| **バックアップ保有期間を変更する** | [SQL Database](automated-backups-overview.md?tabs=single-database#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [SQL Managed Instance](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [SQL Database](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| **長期的なバックアップ保有期間を変更する** | [SQL Database](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>SQL Managed Instance - N/A  | [SQL Database](long-term-backup-retention-configure.md)<br/>[SQL Managed Instance](../managed-instance/long-term-backup-retention-configure.md)  |
| **特定の時点からデータベースを復元する** | [SQL Database](recovery-using-backups.md#point-in-time-restore)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| **削除されたデータベースの復元** | [SQL Database](recovery-using-backups.md)<br>[SQL Managed Instance](../managed-instance/point-in-time-restore.md#restore-a-deleted-database) | [SQL Database](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [SQL Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| **Azure Blob Storage からデータベースを復元する** | SQL Database - N/A <br/>SQL Managed Instance - N/A  | SQL Database - N/A <br/>[SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-scheduling"></a>バックアップのスケジュール設定

初回の完全バックアップは、新しいデータベースの作成または復元の直後にスケジュールされます。 通常このバックアップは 30 分以内に終了しますが、データベースが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかる場合があり、通常は新しいデータベースより大きくなります。 初回の完全バックアップ以降のすべてのバックアップは、自動的にスケジュールおよび管理されます。 すべてのデータベースのバックアップの正確なタイミングは、システム全体のワークロードのバランスを取りながら、SQL Database サービスまたは SQL Managed Instance サービスによって決定されます。 バックアップ ジョブのスケジュールを変更したり、スケジュールを無効にしたりすることはできません。

> [!IMPORTANT]
> 新しいデータベース、復元されたデータベース、またはコピーされたデータベースの場合、特定の時点への復元機能は、最初の完全バックアップの後で最初のトランザクション ログ バックアップが作成された時点から、使用できるようになります。

## <a name="backup-storage-consumption"></a>バックアップ ストレージ消費量

SQL Server のバックアップと復元のテクノロジでは、特定の時点にデータベースを復元するには、1 回の完全バックアップ、必要に応じて 1 回の差分バックアップ、1 回以上のトランザクション ログ バックアップで構成される、中断のないバックアップ チェーンが必要です。 SQL Database および SQL Managed Instance のバックアップ スケジュールには、毎週、1 回の完全バックアップが含まれます。 したがって、保有期間全体で PITR を有効にするには、構成されている保有期間より最大で 1 週間長い期間の、完全バックアップ、差分バックアップ、トランザクション ログ バックアップが追加で保存されている必要があります。 

つまり、保有期間中の任意の時点において、保有期間の最も古い時点より古い完全バックアップと、その完全バックアップから次の完全バックアップまでの差分バックアップとトランザクション ログ バックアップの中断されていないチェーンが、存在する必要があります。

> [!NOTE]
> PITR を有効にするため、追加のバックアップは、構成されている保有期間より最大 1 週間長く格納されます。 バックアップ ストレージは、すべてのバックアップと同じ料金で課金されます。 

PITR 機能を提供するために必要なくなったバックアップは、自動的に削除されます。 差分バックアップとログ バックアップでは先行する完全バックアップが復元可能である必要なため、3 つのバックアップの種類すべてが毎週まとめて消去されます。

[TDE で暗号化された](transparent-data-encryption-tde-overview.md)データベースを含むすべてのデータベースでは、バックアップ ストレージの大きさとコストを削減するためバックアップが圧縮されます。 平均のバックアップ圧縮率は 3 から 4 倍ですが、データの性質や、データベースでデータ圧縮が使用されているかどうかにより、大幅に低くなったり高くなったりする可能性があります。

SQL Database と SQL Managed Instance では、使用されたバックアップ ストレージの合計が累積値として計算されます。 この累積値が 1 時間おきに Azure 課金パイプラインに報告されます。この時間あたり使用量がパイプラインによって集計されて、毎月末に消費量が計算されます。 データベースの削除後は、バックアップが古くなって削除されると共に消費量が減少します。 すべてのバックアップが削除されて、PITR が不可能になると、課金は停止します。
   
> [!IMPORTANT]
> データベースが削除された場合でも、データベースのバックアップは PITR を有効にするために保持されます。 データベースを削除して再作成すると、ストレージとコンピューティングのコストが削減される場合がありますが、削除されるたびに、削除された各データベースのバックアップがサービスによって保持されるため、バックアップ ストレージのコストが増加する可能性があります。 

### <a name="monitor-consumption"></a>消費量の監視

仮想コア データベースの場合、各種バックアップ (完全、差分、ログ) によって使用されるストレージは、データベース監視ブレード上で別個のメトリックとして報告されます。 次の図では、単一データベースのバックアップ ストレージ消費量の監視方法が示されています。 この機能は、マネージド インスタンスでは現在使用できません。

![Azure portal でのデータベース バックアップ消費量の監視](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>バックアップ ストレージ消費量を微調整する

データベースの最大データ サイズまでのバックアップ ストレージの使用量については、課金されません。 超過のバックアップ ストレージ消費量は、個々のデータベースのワークロードと最大サイズに依存します。 バックアップ ストレージ消費量を減らすには、次の調整手法のいくつかを検討してください。

- 必要最小限まで[バックアップの保持期間](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)を短縮します。
- インデックスの再構築などの大規模な書き込み操作を、必要以上に頻繁に行わないようにします。
- 大規模なデータ読み込み操作の場合、[クラスター化された列ストア インデックス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview)を使用して、関連する[ベスト プラクティス](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance)に従うことを検討し、クラスター化されていないインデックスの数を減らします。
- 汎用サービス レベルでは、プロビジョニングされたデータ ストレージの方が、バックアップ ストレージの価格よりも安価です。 超過のバックアップ ストレージのコストが継続的に増加している場合は、データ ストレージを増やしてバックアップ ストレージを節約することを検討してください。
- 一時的な結果やデータの保存には、アプリケーションのロジックでの永続的テーブルではなく TempDB を使用します。
- 可能な限り (Dev/Test 環境など) ローカル冗長バックアップ ストレージを使用します。

## <a name="backup-retention"></a>バックアップ保有期間

新しいデータベース、復元されたデータベース、コピーされたデータベースのすべてについて、Azure SQL Database と Azure SQL Managed Instance では、既定で、過去 7 日間の PITR が可能な十分なバックアップが保持されます。 Hyperscale データベースを除き、1 から 35 日の範囲で、アクティブなデータベースごとに[バックアップの保持期間を変更する](#change-the-pitr-backup-retention-period)ことができます。 「[バックアップ ストレージ消費量](#backup-storage-consumption)」で説明されているように、PITR を有効にするために保存されているバックアップは、保有期間より古い場合があります。 Azure SQL Managed Instance のみの場合、データベースを削除した後で、PITR バックアップ保持率を 0 から 35 日の範囲で設定できます。 

データベースを削除した場合、システムでは、オンライン データベースと同じ方法で、特定の保有期間のバックアップが保持されます。 削除されたデータベースのバックアップ保有期間を変更することはできません。

> [!IMPORTANT]
> サーバーまたはマネージド インスタンスを削除すると、そのサーバーまたはマネージド インスタンス上のすべてのデータベースも削除され、復旧できなくなります。 削除されたサーバーまたはマネージド インスタンスは復元できません。 ただし、データベースまたはマネージド インスタンスに対して長期保有 (LTR) を構成していた場合、長期保有バックアップは削除されず、同じサブスクリプション内の異なるサーバーまたはマネージド インスタンスのデータベースを、長期保有バックアップが作成された特定の時点まで復元するために使用できます。

過去 1 から 35 日以内の PITR のためのバックアップ保有は、短期バックアップ保有とも呼ばれます。 短期保有期間の最大値である 35 日より長くバックアップを保持する必要がある場合は、[長期保有](long-term-retention-overview.md)を有効にすることができます。

### <a name="long-term-retention"></a>長期保存

SQL Database と SQL Managed Instance ではどちらも、最大 10 年間の完全バックアップの長期保有 (LTR) を Azure Blob Storage で構成できます。 LTR ポリシーを構成すると、週に 1 回、完全バックアップが自動的に別のストレージ コンテナーにコピーされます。 各種のコンプライアンス要件を満たすために、毎週、毎月、毎年の完全バックアップに対してさまざまな保有期間を選択できます。 ストレージの使用量は、LTR バックアップについて選択した頻度と保持期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。

LTR の詳細については、[バックアップの長期保有](long-term-retention-overview.md)に関するページを参照してください。

## <a name="storage-costs"></a>ストレージ コスト

バックアップ ストレージの価格は、購入モデル (DTU または仮想コア) と選択したバックアップ ストレージ冗長性オプション、さらにリージョンによっても異なります。 バックアップ ストレージは、使用量 (GB/月) に応じて課金されます。価格については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」と「[Azure SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)」のページを参照してください。

### <a name="dtu-model"></a>DTU モデル

DTU モデルでは、データベースおよびエラスティック プール用のバックアップ ストレージに対する追加請求はありません。 バックアップ ストレージの料金は、データベースまたはプールの価格の一部です。

### <a name="vcore-model"></a>仮想コア モデル

SQL Database の単一データベースの場合は、データベースの最大データ ストレージ サイズの 100% に等しいバックアップ ストレージ容量が、追加料金なしで提供されます。 エラスティック プールとマネージド インスタンスの場合は、それぞれ、プールに対する最大データ ストレージまたは最大インスタンス ストレージ サイズの 100% に等しいバックアップ ストレージ容量が、追加料金なしで提供されます。 

単一データベースの場合、課金対象のバックアップ ストレージ合計使用量の計算には次の式が使用されます。

`Total billable backup storage size = (size of full backups + size of differential backups + size of log backups) – maximum data storage`

プールされたデータベースの場合、課金対象のバックアップ ストレージの合計サイズはプール レベルで集計され、次のように計算されます。

`Total billable backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - maximum pool data storage`

マネージド インスタンスの場合、課金対象のバックアップ ストレージの合計サイズはインスタンス レベルで集計され、次のように計算されます。

`Total billable backup storage size = (total size of full backups + total size of differential backups + total size of log backups) – maximum instance data storage`

課金対象の合計バックアップ ストレージ (存在する場合) は、GB および月単位で課金されます。 このバックアップ ストレージ消費量は、個々のデータベース、エラスティック プール、マネージド インスタンスのワークロードとサイズに依存します。 差分バックアップとログ バックアップのサイズはデータの変更量に比例するため、大幅に変更されたデータベースでは、これらのバックアップが大きくなります。 したがって、そのようなデータベースではバックアップ料金が高くなります。

SQL Database と SQL Managed Instance では、課金対象の合計バックアップ ストレージは、すべてのバックアップ ファイルの累積値として計算されます。 この累積値が 1 時間ごとに Azure 課金パイプラインに報告され、そこで、この時間あたり使用量が集計されて、毎月末にバックアップ ストレージの消費量が算出されます。 データベースが削除されると、古いバックアップが期限切れになって削除されるに従い、バックアップ ストレージの使用量は徐々に減少します。 差分バックアップとログ バックアップでは先行する完全バックアップが復元可能である必要なため、3 つのバックアップの種類すべてが毎週まとめて消去されます。 すべてのバックアップが削除されると、課金は停止します。 

簡単な例として、データベースのバックアップ ストレージが累積で 744 GB になり、データベースは完全にアイドル状態であるため、この量は 1 か月を通して変わらないものとします。 この累積ストレージ消費量を 1 時間あたりの使用量に変換するには、744.0 (1 か月 31 日 * 1 日 24 時間) で割ります。 SQL Database では、データベースで 1 時間あたり 1 GB の PITR バックアップが一定の割合で消費されたことが、Azure 課金パイプラインに報告されます。 Azure の課金では、この消費量を集計し、1 か月全体で 744 GB という使用量が示されます。 コストは、使っているリージョンでの GB 単位の月額料金に基づいて算出されます。

次の例はもっと複雑になります。 同じアイドル状態のデータベースで、保有期間が月の途中で 7 日から 14 日間に増やされたとします。 この増加により、バックアップ ストレージの合計は 1,488 GB に倍増します。 SQL Database では、1 時間から 372 時間まで (月の前半) の使用量が 1 GB と報告されます。 373 時間から 744 時間まで (月の後半) の使用量は 2 GB と報告されます。 この使用量が集計されて最終的な請求は 1,116 GB/月になります。

実際のバックアップの課金シナリオはさらに複雑です。 データベース内の変更の割合はワークロードに依存し、時間の経過と共に変化するので、各差分バックアップとログ バックアップのサイズも変化するため、時間単位のバックアップ ストレージの消費量はそれに応じて変動します。 さらに、各差分バックアップには、前回の完全バックアップ以降にデータベースで行われたすべての変更が含まれるため、すべての差分バックアップの合計サイズは 1 週間の間に徐々に増加した後、完全バックアップ、差分バックアップ、ログ バックアップの古いセットが削除されると、大幅に低下します。たとえば、完全バックアップが完了した直後にインデックスの再構築などの大量の書き込み操作が実行された場合、インデックスの再構築によって行われた変更は、再構築の間に作成されたトランザクション ログ バックアップ、次の差分バックアップ、および次回の完全バックアップが実行されるまでのすべての差分バックアップに含まれます。 大規模なデータベースにおける後者のシナリオでは、そうしないと差分バックアップが過剰に大きくなる場合は、サービスの最適化によって差分バックアップではなく完全バックアップが作成されます。 これにより、次の完全バックアップまで、すべての差分バックアップのサイズが小さくなります。

「[消費量の監視](#monitor-consumption)」で説明されているように、各バックアップの種類 (完全、差分、トランザクション ログ) の合計バックアップ ストレージ使用量を、時間を追って監視できます。

### <a name="backup-storage-redundancy"></a>バックアップ ストレージの冗長性

バックアップ ストレージの冗長性は、バックアップ コストに次のように影響します。
- LRS 価格 = x
- ZRS 価格 = 1.25x
- RA-GRS 価格 = 2x

バックアップ ストレージの価格の詳細については、「[Azure SQL Database の価格](https://azure.microsoft.com/pricing/details/sql-database/single/)」と「[Azure SQL Managed Instance の価格](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)」を参照してください。

> [!IMPORTANT]
> バックアップ用に構成可能なストレージの冗長性は、現在 SQL Managed Instance に利用が限られ、その指定は、マネージド インスタンスの作成プロセス中にのみ行うことができます。 リソースがプロビジョニングされた後に、バックアップ ストレージ冗長性オプションを変更することはできません。

### <a name="monitor-costs"></a>コストを監視する

バックアップ ストレージのコストを把握するには、Azure portal の **[コストの管理と請求]** にアクセスし、 **[コスト管理]** を選択してから、 **[コスト分析]** を選択します。 **[スコープ]** として目的のサブスクリプションを選択し、目的の期間とサービスが得られるようにフィルター処理します。

**[サービス名]** のフィルターを追加し、ドロップダウン リストで **[sql database]** を選択します。 **[測定サブカテゴリ]** フィルターを使用して、サービスの課金カウンターを選択します。 単一データベースまたはエラスティック データベース プールの場合は、 **[single/elastic pool pitr backup storage]** を選択します。 マネージド インスタンスの場合は、 **[mi pitr backup storage]** を選択します。 **[ストレージ]** と **[コンピューティング]** のサブカテゴリも必要に応じて使用できますが、これらはバックアップ ストレージのコストに関連付けられていません。

![バックアップ ストレージのコスト分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

  >[!NOTE]
  > 測定値は、現在使用中のカウンターについてのみ表示されます。 カウンターが利用できない場合、そのカテゴリが現在使用されていないと考えられます。 たとえば、マネージド インスタンスをデプロイしていないユーザーには、マネージド インスタンス カウンターが存在しません。 同様に、ストレージを使用していないリソースについては、ストレージ カウンターは表示されません。 

## <a name="encrypted-backups"></a>暗号化バックアップ

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 Azure SQL のすべての新しいデータベースでは、既定で TDE が有効に構成されます。 TDE の詳細については、[SQL Database および SQL Managed Instance での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) に関するページを参照してください。

## <a name="backup-integrity"></a>バックアップの整合性

Azure SQL のエンジニアリング チームは、自動データベース バックアップに対する復元の自動テストを継続的に行っています。 (このテストは、現在は SQL Managed Instance では使用できません)。ポイントインタイム リストア時に、データベースは DBCC CHECKDB の整合性チェックも受けます。

整合性チェック中に問題が見つかると、エンジニアリング チームにアラートが送信されます。 詳細については、[SQL Database でのデータ整合性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)に関するページを参照してください。

すべてのデータベース バックアップは、バックアップの整合性を高めるために、CHECKSUM オプションを使用して実行されます。

## <a name="compliance"></a>コンプライアンス

DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ回復ポリシーに違反しないように、PITR 保有期間が維持されます。 既定の保有期間がコンプライアンス要件を満たしていない場合は、PITR 保有期間を変更できます。 詳細については、「[PITR バックアップ保有期間を変更する](#change-the-pitr-backup-retention-period)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>PITR バックアップ保有期間を変更する

既定の PITR バックアップ保有期間は、Azure portal、PowerShell、または REST API を使用して変更できます。 次の例では、PITR 保有期間を 28 日間に変更する方法を示します。

> [!WARNING]
> 現在の保有期間を短くすると、新しい保有期間より古い特定の時点に復元することができなくなります。 新しい保有期間内で PITR を提供するために必要がなくなったバックアップは削除されます。 現在の保有期間を長くした場合、新しい保有期間内の古い特定の時点に復元する機能はすぐには利用できません。 システムにより長いバックアップの保有が開始されたら、やがて使用できるようになります。

> [!NOTE]
> これらの API は PITR 保有期間にのみ影響します。 データベースに LTR が構成されている場合、それには影響しません。 LTR の保有期間を変更する方法については、[長期保有](long-term-retention-overview.md)に関するページを参照してください。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Azure portal を使用して PITR バックアップ保有期間を変更する

Azure portal を使用してアクティブなデータベースの PITR バックアップ保持期間を変更するには、保持期間を変更するデータベースのサーバーまたはマネージド インスタンスに移動します。 

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

SQL Database の PITR バックアップ保有期間に対する変更は、ポータルのサーバー ページで行われます。 サーバーでデータベースの PITR 保有期間を変更するには、サーバーの概要ブレードに移動します。 左側のペインで **[バックアップの管理]** を選択し、変更の範囲内のデータベースを選択してから、画面の上部にある **[保有期間の構成]** を選択します。

![PITR 保有期間の変更 (サーバー レベル)](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

SQL Managed Instance の PITR バックアップ保有期間の変更は、個々のデータベース レベルで実行されます。 Azure portal からインスタンス データベースの PITR バックアップ保有期間を変更するには、個々のデータベースの [概要] ブレードに移動します。 次に、画面の上部にある **[バックアップ保有期間の構成]** を選択します。

![PITR 保有期間の変更 (マネージド インスタンス)](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>PowerShell を使用して PITR バックアップ保有期間を変更する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM モジュールは SQL Database と SQL Managed Instance によって引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 詳細については、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールのコマンドの引数は、AzureRm モジュールのものと実質的に同じです。

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

アクティブな Azure SQL データベースの PITR バックアップ保持期間を変更するには、次の PowerShell の例を使用します。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

**個々のアクティブな** SQL Managed Instance データベースの PITR バックアップ保持期間を変更するには、次の PowerShell の例を使用します。

```powershell
# SET new PITR backup retention period on an active individual database
# Valid backup retention must be between 1 and 35 days
Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase -RetentionDays 1
```

**すべてのアクティブな** SQL Managed Instance データベースの PITR バックアップ保持期間を変更するには、次の PowerShell の例を使用します。

```powershell
# SET new PITR backup retention period for ALL active databases
# Valid backup retention must be between 1 and 35 days
Get-AzSqlInstanceDatabase -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 1
```

**個々の削除された** SQL Managed Instance データベースの PITR バックアップ保持期間を変更するには、次の PowerShell の例を使用します。
 
```powershell
# SET new PITR backup retention on an individual deleted database
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database.
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver -DatabaseName testDatabase | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

**すべての削除された** SQL Managed Instance データベースの PITR バックアップ保持期間を変更するには、次の PowerShell の例を使用します。

```powershell
# SET new PITR backup retention for ALL deleted databases
# Valid backup retention must be between 0 (no retention) and 35 days. Valid retention rate can only be lower than the period of the retention period when database was active, or remaining backup days of a deleted database
Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName resourceGroup -InstanceName testserver | Set-AzSqlInstanceDatabaseBackupShortTermRetentionPolicy -RetentionDays 0
```

保持期間をゼロ (0) 日にすると、バックアップがすぐに削除され、削除されたデータベースとして保持されなくなります。
削除したデータベースの PITR バックアップの保持期間を短縮すると、その後は延長できなくなります。

---

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>REST API を使用して PITR バックアップ保有期間を変更する方法

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

## <a name="configure-backup-storage-redundancy"></a>バックアップ ストレージの冗長性を構成する

> [!NOTE]
> バックアップ用に構成可能なストレージの冗長性は、現在 SQL Managed Instance に利用が限られ、その指定は、マネージド インスタンスの作成プロセス中にのみ行うことができます。 リソースがプロビジョニングされた後に、バックアップ ストレージ冗長性オプションを変更することはできません。

マネージド インスタンスのバックアップ ストレージに対する冗長性を設定できるのは、インスタンスの作成時のみです。 既定値は geo 冗長ストレージ (RA-GRS) です。 ローカル冗長 (LRS)、ゾーン冗長 (ZRS)、geo 冗長 (RA-GRS) の各バックアップ ストレージ間の価格の違いについては、[マネージド インスタンスの価格のページ](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)を参照してください。

### <a name="configure-backup-storage-redundancy-by-using-the-azure-portal"></a>Azure portal を使用してバックアップ ストレージの冗長性を構成する

Azure portal には、バックアップ ストレージの冗長性を変更するオプションが **[コンピューティングとストレージ]** ブレードにあります。そこには、SQL Managed Instance の作成時に、 **[基本]** タブの **[Managed Instance の構成]** オプションからアクセスすることができます。
![[コンピューティングとストレージ] 構成ブレード](./media/automated-backups-overview/open-configuration-blade-mi.png)

バックアップ ストレージの冗長性を選択するオプションは、 **[コンピューティングとストレージ]** ブレードにあります。
![バックアップ ストレージの冗長性を構成する](./media/automated-backups-overview/select-backup-storage-redundancy-mi.png)

## <a name="next-steps"></a>次のステップ

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。
- [Azure portal を使用してデータベースを特定の時点に復元する](recovery-using-backups.md)方法について、詳細を確認してください。
- [PowerShell を使用してデータベースを特定の時点に復元する](scripts/restore-database-powershell.md)方法について、詳細を確認してください。
- Azure Blob Storage に長期保有される自動バックアップを Azure portal を使用して構成、管理、復元する方法の詳細については、[Azure portal を使用した長期的なバックアップ保有期間の管理](long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保有される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期的なバックアップ保有期間の管理](long-term-backup-retention-configure.md)に関する記事を参照してください。
- バックアップ ストレージの保持期間と Azure SQL Managed Instance のコストを微調整する方法を学習するには、「[Fine tuning backup storage costs on Managed Instance (Managed Instance でのバックアップ ストレージ コストの微調整)](https://aka.ms/mi-backup-tuning)」を参照してください。
