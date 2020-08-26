---
title: アーキテクチャの概要
description: Azure Backup サービスによって使用される、アーキテクチャ、コンポーネント、およびプロセスの概要を示します。
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: fc57f275d7693c9cf93adf04dc5dcc7524ba0567
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835733"
---
# <a name="azure-backup-architecture-and-components"></a>Azure Backup のアーキテクチャとコンポーネント

[Azure Backup サービス](backup-overview.md)を使用して、Microsoft Azure クラウド プラットフォームにデータをバックアップすることができます。 この記事では、Azure Backup のアーキテクチャ、コンポーネント、およびプロセスの概要を示します。

## <a name="what-does-azure-backup-do"></a>Azure Backup の機能

Azure Backup では、データ、マシンの状態、オンプレミスのマシンや Azure 仮想マシン (VM) インスタンス上で実行されているワークロードをバックアップします。 Azure Backup のシナリオは数多くあります。

## <a name="how-does-azure-backup-work"></a>Azure Backup のしくみ

複数の方法を使用して、マシンとデータをバックアップすることができます。

- **オンプレミスのマシンをバックアップする**:
  - Azure Backup Microsoft Azure Recovery Services (MARS) エージェントを使用して、オンプレミスの Windows マシンを Azure に直接バックアップできます。 Linux マシンはサポートされていません。
  - オンプレミスのマシンは、バックアップ サーバー (System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server (MABS)) にバックアップできます。 その後、Azure 上の Recovery Services コンテナーにバックアップ サーバーをバックアップできます。

- **Azure VM をバックアップする**:
  - Azure VM を直接バックアップすることができます。 Azure Backup によって、VM 上で実行されている Azure VM エージェントに、バックアップ拡張機能がインストールされます。 この拡張機能は、VM 全体をバックアップします。
  - MARS エージェントを実行することにより、Azure VM 上の特定のファイルおよびフォルダーをバックアップできます。
  - Azure VM を Azure 上で実行されている MABS にバックアップし、MABS を Recovery Services コンテナーにバックアップすることができます。

詳しくは、[バックアップできる内容](backup-overview.md)と[サポートされるバックアップ シナリオ](backup-support-matrix.md)に関するページをご覧ください。

## <a name="where-is-data-backed-up"></a>データはどこにバックアップされますか。

Azure Backup では、バックアップしたデータが Recovery Services コンテナーに格納されます。 コンテナーは、バックアップ コピー、復旧ポイント、バックアップ ポリシーなどのデータを保持するために使用される、Azure 上のオンライン ストレージ エンティティです。

Recovery Services コンテナーには、次の機能があります。

- コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。
- 各 Azure サブスクリプションに、最大 500 個のコンテナーを作成できます。
- Azure VM とオンプレミスのマシンを含む、コンテナーでバックアップされたアイテムを監視することができます。
- [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、コンテナーのアクセスを管理できます。
- 冗長性のためにコンテナー内のデータをレプリケートする方法を指定します。
  - **ローカル冗長ストレージ (LRS)** : データセンターの障害から保護するために、LRS を使用できます。 LRS では、ストレージ スケール ユニットにデータをレプリケートします。 [詳細については、こちらを参照してください](../storage/common/storage-redundancy.md)。
  - **geo 冗長ストレージ (GRS)** :リージョン全体の障害から保護するために、GRS を使用できます。 GRS ではデータがセカンダリ リージョンにレプリケートされます。 [詳細については、こちらを参照してください](../storage/common/storage-redundancy.md)。
  - 既定では、Recovery Services コンテナーでは GRS が使用されます。

## <a name="backup-agents"></a>Backup のエージェント

Azure Backup では、バックアップされるマシンの種類に応じて異なるバックアップ エージェントが用意されています。

**エージェント** | **詳細**
--- | ---
**MARS エージェント** | <ul><li>オンプレミスの Windows Server マシン上で実行され、ファイル、フォルダー、システムの状態をバックアップします。</li> <li>Azure VM 上で実行され、ファイル、フォルダー、システムの状態をバックアップします。</li> <li>DPM/MABS サーバー上で実行され、DPM/MABS のローカル ストレージ ディスクを Azure にバックアップします。</li></ul>
**Azure VM 拡張機能** | Azure VM 上で実行され、コンテナーに VM をバックアップします。

## <a name="backup-types"></a>バックアップの種類

次の表では、さまざまなバックアップの種類と、それらがどのような場合に使用されるかについて説明します。

**バックアップの種類** | **詳細** | **使用方法**
--- | --- | ---
**完全** | 完全バックアップにはデータ ソース全体が含まれます。 差分バックアップや増分バックアップよりも多くのネットワーク帯域幅を使用します。 | 初回バックアップで使用されます。
**差分** |  差分バックアップでは、初回の完全バックアップ以降に変更されたブロックが格納されます。 より少量のネットワークとストレージが使用され、変更されていないデータの冗長コピーは保持されません。<br/><br/> 以降の各バックアップの間には変更されていないデータ ブロックが転送され、格納されるため、非効率です。 | Azure Backup では使用されません。
**増分** | 増分バックアップでは、前回のバックアップ以降に変更されたデータのブロックのみが格納されます。 ストレージとネットワークの効率が高くなります。 <br/><br/> 増分バックアップを完全バックアップで補完する必要はありません。 | ディスク バックアップで DPM/MABS によって使用され、Azure へのすべてのバックアップで使用されます。 SQL Server のバックアップには使用されません。

## <a name="sql-server-backup-types"></a>SQL Server のバックアップの種類

次の表では、SQL Server データベースに使用されるさまざまな種類のバックアップとそれらの使用頻度について説明します。

**バックアップの種類** | **詳細** | **使用方法**
--- | --- | ---
**完全バックアップ** | 完全なデータベース バックアップでは、データベース全体をバックアップします。 特定のデータベース内あるいは一連のファイル グループまたはファイル内のすべてのデータが含まれます。 完全バックアップには、そのデータを復旧するための十分なログも含まれています。 | 最多で、1 日に 1 回の完全バックアップをトリガーできます。<br/><br/> 日次または週次で完全バックアップを取得することを選択できます。
**差分バックアップ** | 差分バックアップは、直近の完全データ バックアップに基づきます。<br/><br/> 完全バックアップ以降に変更されたデータのみがキャプチャされます。 |  最多で、1 日に 1 回の差分バックアップをトリガーできます。<br/><br/> 完全バックアップと差分バックアップを同じ日に構成することはできません。
**トランザクション ログ バックアップ** | ログ バックアップでは、ある特定の時点への復元を、指定した秒に至るまで可能にします。 | 最多で、15 分ごとにトランザクション ログ バックアップを構成できます。

### <a name="comparison-of-backup-types"></a>バックアップの種類の比較

バックアップの種類によって、ストレージの使用量、目標復旧時間 (RTO)、およびネットワークの使用量が異なります。 次の図では、バックアップの種類を比較しています。

- データ ソース A は A1 から A10 の 10 個のストレージ ブロックで構成され、毎月バックアップされます。
- ブロック A2、A3、A4、および A9 は最初の月に変更され、ブロック A5 はその翌月に変更されます。
- 差分バックアップでは、2 か月目に、変更されたブロック A2、A3、A4、および A9 がバックアップされます。 3 か月目は、既にバックアップ済みのブロックが再度バックアップされたうえで、変更された A5 がバックアップされます。 変更されたブロックは、次回完全バックアップが行われるまでバックアップされ続けます。
- 増分バックアップでは、2 か月目にブロック A2、A3、A4、および A9 が変更済みとしてマークされ、転送されます。 3 か月目は、変更された A5 のみがマークされ、転送されます。

![バックアップ方法の比較を示す図](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>バックアップの機能

次の表は、さまざまな種類のバックアップに対してサポートされている機能をまとめたものです。

**機能** | **ファイルとフォルダーの直接バックアップ (MARS エージェントを使用)** | **Azure VM バックアップ** | **DPM/MABS を使用するマシンまたはアプリ**
--- | --- | --- | ---
コンテナーにバックアップする | ![はい][green] | ![はい][green] | ![はい][green]
DPM/MABS ディスクにバックアップしてから、Azure にバックアップする | | | ![はい][green]
バックアップのために送信されるデータを圧縮する | ![はい][green] | データの転送時に圧縮は使用されません。 ストレージが少し大きくなりますが、復元はより高速になります。  | ![はい][green]
増分バックアップを実行する |![はい][green] |![はい][green] |![はい][green]
重複除去されたディスクをバックアップする | | | ![部分的][yellow]<br/><br/> オンプレミスにデプロイされた DPM/MABS サーバーの場合のみ。

![テーブル キー](./media/backup-architecture/table-key.png)

## <a name="backup-policy-essentials"></a>バックアップ ポリシーの基礎

- バックアップ ポリシーはコンテナーごとに作成されます。
- バックアップ ポリシーは次のワークロードのバックアップ用に作成できます。Azure VM、Azure VM 内の SQL、Azure VM 内の SAP HANA、Azure ファイル共有。 MARS エージェントを使用するファイルとフォルダーのバックアップのポリシーは、MARS コンソールで指定します。
  - Azure ファイル共有
- ポリシーは、多くのリソースに割り当てることができます。 Azure VM のバックアップ ポリシーを使用して、多くの Azure VM を保護できます。
- ポリシーは、2 つのコンポーネントで構成されています
  - スケジュール: いつバックアップを作成するか
  - 保持期間: 各バックアップをどれだけの期間保持する必要があるか。
- スケジュールでは、"毎日" または "毎週" の特定の時点として定義できます。
- 保持期間は、"毎日"、"毎週"、"毎月"、"毎年" のバックアップ ポイントに対して定義できます。
  - "毎週" は、特定の曜日のバックアップを指します。
  - "毎月" は、月の特定の日付のバックアップを指します。
  - "毎年" は、年の特定の日付のバックアップを指します。
- "毎月"、"毎年" のバックアップ ポイントに対するデータ保持は、長期保有 (LTR) と呼ばれます。
- コンテナーが作成されると、"DefaultPolicy" も作成され、リソースのバックアップに使用できます。
- バックアップ ポリシーの保持期間に変更が加えられると、新しいものを除き、すべての古い復旧ポイントにさかのぼって適用されます。

### <a name="additional-reference"></a>その他の参照情報

- Azure VM マシン:ポリシーを[作成](./backup-azure-vms-first-look-arm.md#back-up-from-azure-vm-settings)および[変更](./backup-azure-manage-vms.md#manage-backup-policy-for-a-vm)する方法。
- Azure VM マシンの SQL Server データベース:ポリシーを[作成](./backup-sql-server-database-azure-vms.md#create-a-backup-policy)および[変更](./manage-monitor-sql-database-backup.md#modify-policy)する方法。
- Azure ファイル共有:ポリシーを[作成](./backup-afs.md)および[変更](./manage-afs-backup.md#modify-policy)する方法。
- SAP HANA:ポリシーを[作成](./backup-azure-sap-hana-database.md#create-a-backup-policy)および[変更](./sap-hana-db-manage.md#change-policy)する方法。
- MARS:ポリシーを[作成](./backup-windows-with-mars-agent.md#create-a-backup-policy)および[変更](./backup-azure-manage-mars.md#modify-a-backup-policy)する方法。
- [バックアップのスケジュールに、ワークロードの種類に基づく制限はありますか。](./backup-azure-backup-faq.md#are-there-limits-on-backup-scheduling)
- [アイテム保持ポリシーを変更した場合、既存の回復ポイントはどうなりますか。](./backup-azure-backup-faq.md#what-happens-when-i-change-my-backup-policy)

## <a name="architecture-built-in-azure-vm-backup"></a>アーキテクチャ:組み込みの Azure VM バックアップ

1. Azure VM のバックアップを有効にすると、指定したスケジュールに従ってバックアップが実行されます。
1. 最初のバックアップ時に、バックアップ拡張機能が VM にインストールされます (VM が実行されている場合)。
    - Windows VM の場合は、VMSnapshot 拡張機能がインストールされます。
    - Linux VM の場合は、VMSnapshot Linux 拡張機能がインストールされます。
1. この拡張機能によって、ストレージ レベルのスナップショットが取得されます。
    - 実行されている Windows VM については、Backup は Windows ボリューム シャドウ コピー サービス (VSS) と連携して VM のアプリ整合性スナップショットを取得します。 既定では、Backup によって完全 VSS バックアップが取得されます。 Backup でアプリ整合性スナップショットを取得できない場合、ファイル整合性スナップショットが取得されます。
    - Linux VM の場合、Backup ではファイル整合性スナップショットが取得されます。 アプリ整合性スナップショットの場合は、事前/事後スクリプトを手動でカスタマイズする必要があります。
    - Backup は、各 VM ディスクを並行してバックアップすることで最適化されます。 バックアップ対象の各ディスクについては、Azure Backup でディスク上のブロックが読み取られ、変更されたデータのみが格納されます。
1. スナップショットが取得された後、データはコンテナーに転送されます。
    - 最後のバックアップ以降に変更されたデータのブロックのみがコピーされます。
    - データは暗号化されません。 Azure Backup では、Azure Disk Encryption を使用して暗号化された Azure VM をバックアップできます。
    - スナップショット データはコンテナーにすぐにコピーされない場合があります。 ピーク時には、バックアップに数時間かかる場合があります。 毎日のバックアップ ポリシーでは、VM のバックアップの合計時間は 24 時間未満になります。
1. データがコンテナーに送信されると、復旧ポイントが作成されます。 既定では、スナップショットは 2 日間保持されてから削除されます。 この機能では、復元時間を削減して、これらのスナップショットから復元操作を行うことができます。 それにより、コンテナーからデータを変換して元の場所にコピーするために必要な時間が削減されます。 [Azure Backup のインスタント リストア機能](./backup-instant-restore-capability.md)に関するページを参照してください。

Azure VM をバックアップするために、インターネット接続を明示的に許可する必要はありません。

![Azure VM のバックアップ](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders"></a>アーキテクチャ:オンプレミスの Windows Server マシンや Azure VM ファイルまたはフォルダーの直接バックアップ

1. シナリオを設定するには、MARS エージェントをマシンにダウンロードしてインストールします。 次に、バックアップ対象、バックアップの実行時期、それらを Azure に保持する期間を選択します。
1. 初回バックアップは、バックアップ設定に従って実行されます。
1. MARS エージェントでは、VSS を使用して、バックアップ用に選択されたボリュームの特定の時点のスナップショットを取得します。
    - MARS エージェントでは、Windows システムによる書き込み操作のみを使用してスナップショットがキャプチャされます。
    - エージェントではアプリケーションの VSS ライターは使用されないため、アプリ整合性スナップショットはキャプチャされません。
1. VSS を使用してスナップショットを取得した後、MARS エージェントでは、バックアップの構成時に指定したキャッシュ フォルダーに仮想ハード ディスク (VHD) が作成されます。 エージェントでは、各データ ブロックのチェックサムも格納されます。
1. 増分バックアップは、オンデマンド バックアップが実行されない限り、指定したスケジュールに従って実行されます。
1. 増分バックアップでは、変更されたファイルが識別され、新しい VHD が作成されます。 VHD は圧縮および暗号化されて、コンテナーに送信されます。
1. 増分バックアップが完了した後、新しい VHD は初期レプリケーション後に作成された VHD にマージされます。 このマージされた VHD では、進行中のバックアップの比較に使用される最新の状態が提供されます。

![MARS エージェントを使用するオンプレミス Windows Server マシンのバックアップ](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>アーキテクチャ:DPM/MABS へのバックアップ

1. DPM または MABS 保護エージェントを保護対象のマシンにインストールします。 次に、マシンを DPM 保護グループに追加します。
    - オンプレミス マシンを保護するには、DPM または MABS サーバーをオンプレミスに配置する必要があります。
    - Azure VM を保護するには、MABS サーバーを Azure に配置し、Azure VM として実行する必要があります。
    - DPM/MABS を使用して、バックアップ ボリューム、共有、ファイル、およびフォルダーを保護できます。 マシンのシステムの状態 (ベア メタル) も保護でき、アプリ対応のバックアップ設定で特定のアプリを保護することができます。
1. DPM/MABS 上でマシンまたはアプリの保護を設定する場合、短期的な格納のためには MABS/DPM ローカル ディスクにバックアップし、オンライン保護のためには Azure にバックアップすることを選択します。 また、ローカル DPM/MABS ストレージへのバックアップを実行するタイミングと、Azure へのオンライン バックアップを実行するタイミングも指定します。
1. 指定したスケジュールに従って、保護されたワークロードのディスクがローカル MABS/DPM ディスクにバックアップされます。
1. DPM/MABS ディスクは、DPM/MABS サーバー上で実行されている MARS エージェントによって、コンテナーにバックアップされます。

![DPM または MABS によって保護されたマシンとワークロードのバックアップ](./media/backup-architecture/architecture-dpm-mabs.png)

## <a name="azure-vm-storage"></a>Azure VM ストレージ

Azure VM ではディスクを使用して、そのオペレーティング システム、アプリ、およびデータを格納します。 各 Azure VM には、オペレーティング システム ディスクと一時ディスクの少なくとも 2 つのディスクがあります。 Azure VM ではアプリ データ用のデータ ディスクも使用できます。 ディスクは VHD として格納されます。

- Azure では、Standard または Premium Storage アカウントにページ BLOB として VHD が格納されます。
  - **Standard Storage:** 待機時間の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポート。 Standard Storage には、標準のソリッド ステート ドライブ (SSD) ディスクまたは標準のハード ディスク ドライブ (HDD) ディスクを使用できます。
  - **Premium Storage:** 高パフォーマンスのディスク サポート。 Premium SSD ディスクを使用します。
- ディスクにはさまざまなパフォーマンス レベルがあります。
  - **Standard HDD ディスク:** HDD によってサポートされ、コスト効率の高いストレージに使用されます。
  - **Standard SSD ディスク:** Premium SSD ディスクと Standard HDD ディスクの要素を兼ね備えています。 一貫したパフォーマンスと信頼性が HDD よりも優れていますが、コスト効果に優れています。
  - **Premium SSD ディスク:** SSD によってサポートされ、I/O 集中型ワークロードを実行する VM 用の高パフォーマンスと低待機時間を提供します。
- ディスクには、次のようなマネージドとアンマネージドがあります。
  - **アンマネージド ディスク:** VM で使用されるディスクの従来の種類。 これらのディスクでは、独自のストレージ アカウントを作成し、ディスクの作成時にそれを指定します。 VM のストレージ リソースを最大化する方法を確認する必要があります。
  - **マネージド ディスク**: Azure によりストレージ アカウントが作成、管理されます。 ディスク サイズとパフォーマンス レベルの指定はユーザーが行い、マネージド ディスクの作成は Azure で行います。 ユーザーがディスクを追加し、VM をスケーリングすると、Azure によってストレージ アカウントが処理されます。

VM 用のディスク ストレージと使用可能なディスクの種類について詳しくは、以下の記事をご覧ください。

- [Windows VM 用の Azure マネージド ディスク](../virtual-machines/windows/managed-disks-overview.md)
- [Linux VM 用の Azure マネージド ディスク](../virtual-machines/linux/managed-disks-overview.md)
- [VM で利用できるディスクの種類](../virtual-machines/windows/disks-types.md)

### <a name="back-up-and-restore-azure-vms-with-premium-storage"></a>Premium Storage を使用する Azure VM のバックアップと復元

Azure Backup で Premium Storage を使用して、Azure VM をバックアップすることができます。

- Premium Storage での VM のバックアップの処理中、Backup サービスによって、ストレージ アカウントに *AzureBackup-* という名前の一時的なステージング場所が作成されます。 このステージング場所のサイズは、復旧ポイントのスナップショットのサイズと同じです。
- Premium Storage アカウントに、一時的なステージング場所として使用できる十分な空き領域があることを確認してください。 詳細については、「[Premium ページ BLOB ストレージ アカウントのスケーラビリティ ターゲット](../storage/blobs/scalability-targets-premium-page-blobs.md)」を参照してください。 ステージングの場所は変更しないでください。
- バックアップ ジョブが完了した後、ステージングの場所は削除されます。
- ステージングの場所に使用されるストレージの価格は、[Premium Storage の価格](../virtual-machines/windows/disks-types.md#billing)に準じます。

Premium Storage を使用して Azure VM を復元する場合、Premium または Standard Storage に復元できます。 通常は、Premium Storage に復元します。 ただし、VM のファイルのサブセットのみ必要な場合は、Standard Storage に復元する方がコス効果が高くなる可能性があります。

### <a name="back-up-and-restore-managed-disks"></a>マネージド ディスクをバックアップおよび復元する

マネージド ディスクを使用して Azure VM をバックアップすることができます。

- 他の Azure VM の場合と同じように、マネージド ディスクで VM をバックアップします。 仮想マシン設定から直接 VM をバックアップすることも、Recovery Services コンテナーで VM のバックアップを有効にすることもできます。
- マネージド ディスクに VM をバックアップするには、マネージド ディスク上に構築された RestorePoint コレクションを使用します。
- Azure Backup では、Azure Disk Encryption を使用して暗号化されたマネージド ディスクでの VM のバックアップもサポートされます。

マネージド ディスクで VM を復元する場合、マネージド ディスクで完全な VM に復元するか、ストレージ アカウントに復元することができます。

- 復元プロセス中に、Azure によってマネージド ディスクが処理されます。 ユーザーがストレージ アカウント オプションを使用している場合は、復元プロセス中に作成されたストレージ アカウントを管理します。
- 暗号化されたマネージド VM を復元する場合、復元プロセスを開始する前に、VM のキーとシークレットがキー コンテナーに存在することを確認します。

## <a name="next-steps"></a>次のステップ

- サポート マトリックスを確認し、[バックアップ シナリオでサポートされる機能と制限事項について学習します](backup-support-matrix.md)。
- 以下のシナリオのいずれかのバックアップを設定します。
  - [Azure VM をバックアップする](backup-azure-arm-vms-prepare.md)。
  - バックアップ サーバーなしで、[Windows マシンを直接バックアップ](tutorial-backup-windows-server-to-azure.md)します。
  - Azure へのバックアップのために [MABS を設定](backup-azure-microsoft-azure-backup.md)してから、MABS にワークロードをバックアップします。
  - Azure へのバックアップのために [DPM を設定](backup-azure-dpm-introduction.md)してから、DPM にワークロードをバックアップします。

[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png
