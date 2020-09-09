---
title: Azure Stack での SQL Server ワークロードのバックアップ
description: この記事では、Azure Stack 上の SQL Server データベースを保護するように Microsoft Azure Backup Server (MABS) を構成する方法について説明します。
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: 706050fa37e4234a0ffc902f6b696ebd84e6701e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032648"
---
# <a name="back-up-sql-server-on-azure-stack"></a>Azure Stack での SQL Server のバックアップ

この記事では、Azure Stack 上の SQL Server データベースを保護するための Microsoft Azure Backup Server (MABS) の構成について説明します。

SQL Server データベースの Azure へのバックアップと Azure からの回復の管理には、次の 3 つの手順が含まれます。

1. SQL Server データベースを保護するためのバックアップ ポリシーを作成する
2. オンデマンドでのバックアップ コピーを作成する
3. ディスクおよび Azure からデータベースを回復する

## <a name="prerequisites-and-limitations"></a>前提条件と制限事項

* データベースのファイルがリモート ファイル共有にある場合、保護はエラー ID 104 で失敗します。 MABS では、リモート ファイル共有上の SQL Server データの保護はサポートされていません。
* リモート SMB 共有に保存されているデータベースを MABS で保護することはできません。
* [可用性グループのレプリカが読み取り専用として構成されている](/sql/database-engine/availability-groups/windows/configure-read-only-access-on-an-availability-replica-sql-server?view=sql-server-ver15)ことを確認します。
* システム アカウント **NTAuthority\System** を SQL Server の Sysadmin グループに明示的に追加する必要があります。
* 部分的な包含データベースに対して別の場所への回復を実行する場合は、ターゲット SQL インスタンスで[包含データベース](/sql/relational-databases/databases/migrate-to-a-partially-contained-database?view=sql-server-ver15#enable)機能が有効になっていることを確認する必要があります。
* ファイル ストリーム データベースに対して別の場所への回復を実行する場合、ターゲット SQL インスタンスで[ファイル ストリーム データベース](/sql/relational-databases/blob/enable-and-configure-filestream?view=sql-server-ver15)機能が有効になっていることを確認する必要があります。
* SQL Server AlwaysOn の保護:
  * 保護グループの作成時に照会を実行するときに、MABS によって可用性グループが検出されます。
  * MABS によってフェールオーバーが検出され、データベース保護が続行されます。
  * MABS では、SQL Server のインスタンスに対するマルチサイト クラスター構成がサポートされます。
* AlwaysOn 機能を使用するデータベースを MABS で保護するときは、次の制限があります。
  * MABS では、バックアップ設定に基づいて SQL Server に設定されている可用性グループに対するバックアップ ポリシーが使用されます。
    * セカンダリ優先 - オンラインになっているのがプライマリ レプリカのみの場合を除き、バックアップは常にセカンダリ レプリカ上で発生します。 セカンダリ レプリカが複数ある場合は、バックアップの優先度が最も高いノードがバックアップ用に選択されます。 プライマリ レプリカのみを使用できる場合、バックアップはプライマリ レプリカ上で発生します。
    * セカンダリのみ - プライマリ レプリカでのバックアップは行いません。 オンラインになっているのがプライマリ レプリカのみの場合、バックアップは発生しません。
    * プライマリ - バックアップは常にプライマリ レプリカ上で発生します。
    * 任意のレプリカ - バックアップは、可用性グループ内の使用可能な任意のレプリカで発生できます。 バックアップ元となるノードは、各ノードのバックアップの優先度によって決まります。
  * 次のことを考慮してください。
    * 読み取り可能なレプリカ (プライマリ、同期セカンダリ、非同期セカンダリ) であれば、どれでもバックアップを発生させることができます。
    * レプリカがバックアップから除外されている場合 (たとえば **[レプリカの除外]** が有効であったり、レプリカが読み取り不可としてマークされていたりする場合) は、どのオプションでも、そのレプリカがバックアップ用に選択されることはありません。
    * 読み取り可能なレプリカが複数ある場合は、バックアップの優先度が最も高いノードがバックアップ用に選択されます。
    * 選択されたノード上でバックアップに失敗した場合、バックアップ操作は失敗します。
    * 元の場所への回復はサポートされていません。
* SQL Server 2014 以降のバックアップに関する問題:
  * [Windows Azure Blob Storage にオンプレミスの SQL Server 用のデータベース](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure?view=sql-server-ver15)を作成するための新機能が SQL Server 2014 に追加されました。 この構成を保護するために MABS を使用することはできません。
  * SQL AlwaysOn オプションの [セカンダリを優先] バックアップ設定には、いくつかの既知の問題があります。 MABS では、常にセカンダリからバックアップが作成されます。 セカンダリが見つからない場合、バックアップは失敗します。

## <a name="before-you-start"></a>開始する前に

[Azure Backup Server をインストールして準備します](backup-mabs-install-azure-stack.md)。

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>SQL Server データベースを保護するための Azure へのバックアップ ポリシーの作成

1. Azure Backup Server の UI で **[保護]** ワークスペースをクリックします。

2. ツール リボンで、 **[新規]** をクリックし、新しい保護グループを作成します。

    ![Create Protection Group](./media/backup-azure-backup-sql/protection-group.png)

    Azure Backup Server によって保護グループ ウィザードが起動されます。ウィザードに従って**保護グループ**を作成します。 **[次へ]** をクリックします。

3. **[保護グループの種類の選択]** 画面で **[サーバー]** を選択します。

    ![Select Protection Group Type - 'Servers'](./media/backup-azure-backup-sql/pg-servers.png)

4. **[グループ メンバーの選択]** 画面の使用可能メンバーの一覧には、各種データ ソースが表示されます。 **+** をクリックすると、フォルダーが展開してサブフォルダーが表示されます。 チェックボックスをクリックして項目を選択します。

    ![Select SQL DB](./media/backup-azure-backup-sql/pg-databases.png)

    選択メンバーの一覧には、選択したすべての項目が表示されます。 保護するサーバーまたはデータベースを選択したら、 **[次へ]** をクリックします。

5. **[データ保護方法の選択]** 画面では、保護グループの名前を指定し、 **[オンライン保護を利用する]** チェックボックスをオンにします。

    ![Data Protection Method - short-term disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)

6. **[短期的な目標値の指定]** 画面では、ディスクへのバックアップ ポイントの作成に必要な情報を入力し、 **[次へ]** をクリックします。

    この例では、 **[リテンション期間]** を **5 日間**、バックアップの頻度である **[同期の間隔]** を **15 分**おきに指定しています。 **[高速完全バックアップ]** は **午後 8 時 00 分**に設定されています。

    ![短期的な目標値](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > この例では、毎日午後 8 時 00 分に前日午後 8 時 00 分のバックアップ ポイント以降の変更データを転送することでバックアップ ポイントが作成されます。 このプロセスは、 **高速完全バックアップ**と呼ばれます。 トランザクション ログは、15 分おきに同期されます。 午後 9 時 00 分にデータベースを復旧する必要がある場合は、直前 (この場合は午後 8 時) の高速完全バックアップ ポイント以降のログからポイントが作成されます。
   >
   >

7. **[ディスク割り当ての確認]** 画面では、使用可能なストレージ領域全体と、考えられるディスク領域を確認します。 **[次へ]** をクリックします。

8. **[レプリカの作成方法の選択]** では、最初の回復ポイントの作成方法を選択します。 初回バックアップの転送は、帯域幅の輻輳を避けるために手動 (オフライン) で行うか、ネットワーク経由で行うことができます。 最初のバックアップを直ちに転送しない場合は、初回の転送時刻を指定できます。 **[次へ]** をクリックします。

    ![Initial replication method](./media/backup-azure-backup-sql/pg-manual.png)

    初回バックアップでは、データ ソース (SQL Server データベース) 全体を運用サーバー (SQL Server コンピューター) から Azure Backup Server に転送する必要があります。 このデータは場合によっては大きくなり、ネットワーク経由で転送すると帯域幅を超える可能性があります。 この理由から、初回バックアップの転送では、帯域幅の輻輳を避けるために**手動**で転送するか (リムーバブル メディアを使用)、指定した時刻に**自動 (ネットワーク経由)** で転送するかを選択できます。

    初期バックアップが完了した後は、残りのバックアップは初期バックアップのコピーに対する増分バックアップになります。 増分バックアップは一般に非常に小さく、ネットワーク経由で容易に転送できます。

9. 整合性チェックをいつ実行するかを選択し、 **[次へ]** をクリックします。

    ![Consistency check](./media/backup-azure-backup-sql/pg-consistent.png)

    Azure Backup Server は、バックアップ ポイントの整合性について整合性チェックを実行します。 Azure Backup Server は、運用サーバー (このシナリオでは SQL Server コンピューター) のバックアップ ファイルとそのファイルのバックアップ データのチェックサムを計算します。 一致しない場合、Azure Backup Server 上のバックアップ ファイルは破損しているものと見なされます。 Azure Backup Server は、チェックサムの不一致に対応するブロックを送信することでバックアップ データを修正します。 整合性チェックはパフォーマンス集約型であるため、スケジュールを設定するか、自動的に実行できます。

10. データ ソースのオンライン保護を指定するには、Azure で保護されるデータベースを選択し、 **[次へ]** をクリックします。

    ![Select datasources](./media/backup-azure-backup-sql/pg-sqldatabases.png)

11. 組織のポリシーに合わせてバックアップ スケジュールとリテンション ポリシーを選択します。

    ![Schedule and Retention](./media/backup-azure-backup-sql/pg-schedule.png)

    この例では、バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成されます。

    > [!NOTE]
    > すばやく回復するために、ディスク上に短期的な回復ポイントをいくつか作っておくことをお勧めします。 これらの回復ポイントは、オペレーショナル リカバリに使用されます。 Azure は、より高い SLA を提供し、可用性を確保するのに適したオフサイトの場所として機能します。
    >
    >

    **ベスト プラクティス**: Azure へのバックアップがローカル ディスク バックアップの完了後に開始されるようにスケジュールすると、常に最新のディスク バックアップが Azure にコピーされます。

12. 保有ポリシーのスケジュールを選択します。 保有ポリシーのしくみの詳細については、「 [Azure Backup を使用してテープのインフラストラクチャを置換する](backup-azure-backup-cloud-as-tape.md)」を参照してください。

    ![Retention Policy](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    次の点に注意してください。

    * バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成され、180 日間保有されます。
    * 土曜日の午後 12 時 00 分のバックアップは、 104 週間保有されます。
    * 先週土曜日の午後 12 時 00 分のバックアップは、 60 か月間保有されます。
    * 3 月の最終土曜日の午後 12 時 00 分のバックアップは、 10 年間保有されます。
13. **[次へ]** をクリックし、初期バックアップのコピーを Azure に転送するための適切なオプションを選択します。 **[自動でネットワーク経由]** を選択できます

14. **[概要]** 画面でポリシーの詳細を確認したら、 **[グループの作成]** をクリックしてワークフローを完了します。 **[閉じる]** をクリックすると、[監視] ワークスペースでジョブの進行状況を監視できます。

    ![Creation of Protection Group In-Progress](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server データベースのオンデマンド バックアップ

前の手順でバックアップ ポリシーを作成しましたが、"回復ポイント" は最初のバックアップ実行時にのみ作成されます。 次の手順は、スケジューラが開始するのを待つ代わりに、回復ポイントの作成を手動でトリガーします。

1. データベースの保護グループの状態に " **OK** " と表示されるのを待ってから、回復ポイントを作成します。

    ![Protection Group Members](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. データベースを右クリックし、 **[回復ポイントの作成]** を選択します。

    ![Create Online Recovery Point](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. ドロップダウン メニューから **[オンライン保護]** を選択し、 **[OK]** をクリックして Azure の回復ポイントの作成を開始します。

    ![[回復ポイントの作成]](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. ジョブの進行状況は **[監視]** ワークスペースに表示されます。

    ![Monitoring console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure からの SQL Server データベースの回復

保護されているエンティティ (SQL Server データベース) を Azure から回復するには、次の手順が必要です。

1. Azure Backup Server の管理コンソールを開きます。 **[回復]** ワークスペースに移動すると、保護されているサーバーを確認できます。 目的のデータベース (この場合は ReportServer$MSDPM2012) を参照します。 **オンライン** ポイントとして指定される時刻を **[回復元]** で選択します。

    ![Select Recovery point](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. データベース名を右クリックし、 **[回復]** をクリックします。

    ![Recover from Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. MABS に復旧ポイントの詳細が表示されます。 **[次へ]** をクリックします。 データベースを上書きするには、回復のタイプとして **[元の SQL Server のインスタンスに回復する]** を選択します。 **[次へ]** をクリックします。

    ![Recover to Original Location](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    この例では、MABS は、データベースを別の SQL Server インスタンスまたはスタンドアロンのネットワーク フォルダーに回復します。

4. **[回復オプションの指定]** 画面で、[ネットワークの使用帯域幅の調整] を選択して回復で使用される帯域幅を調整するなど、回復のオプションを選択できます。 **[次へ]** をクリックします。

5. **[概要]** 画面に、これまでに指定した回復の構成が表示されます。 **[回復]** をクリックします。

    回復の状態に、データベースが回復されていることが表示されます。 **[閉じる]** をクリックしてウィザードを閉じ、 **[監視]** ワークスペースで進行状況を確認できます。

    ![Initiate recovery process](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    回復が完了すると、復元されたデータベースはアプリケーション コンシステントになります。

## <a name="next-steps"></a>次のステップ

[ファイルとアプリケーションのバックアップ](backup-mabs-files-applications-azure-stack.md)に関する記事をご覧ください。
[Azure Stack での SharePoint のバックアップ](backup-mabs-sharepoint-azure-stack.md)に関する記事を参照してください。
