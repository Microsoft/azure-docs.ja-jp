---
title: "VMM クラウドの Hyper-V VM を Azure にレプリケートする | Microsoft Docs"
description: "System Center VMM クラウドで管理されている Hyper-V VM の Azure へのレプリケーション、フェールオーバー、復旧を調整します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 958b61f5de732a882e0a2682b8dd4e18504a6ae7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Azure Portal の Site Recovery を使用して VMM クラウド内の Hyper-V 仮想マシンを Azure にレプリケートする
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [Azure クラシック](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell クラシック](site-recovery-deploy-with-powershell.md)


この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、System Center VMM クラウドで管理されているオンプレミスの Hyper-V 仮想マシンを Azure にレプリケートする方法について説明します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。

Azure にマシンを (フェールバックなしで) 移行する方法について詳しくは、[この記事](site-recovery-migrate-to-azure.md)をご覧ください。


## <a name="deployment-steps"></a>デプロイメントの手順

記事に従って、次のデプロイ手順を実行します。


1. このデプロイのアーキテクチャの[詳細を確認](site-recovery-components.md)します。 また、Site Recovery で Hyper-V レプリケーションがどのように動作するかの[詳細を確認](site-recovery-hyper-v-azure-architecture.md)します。
2. 前提条件と制限事項を確認します。
3. Azure ネットワークおよびストレージ アカウントを設定します。
4. オンプレミスの VMM サーバーと Hyper-V ホストを準備します。
5. Recovery Services コンテナーを作成します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。
6. ソース設定を指定します。 VMM サーバーをコンテナーに登録します。 Azure Site Recovery プロバイダーを VMM サーバーにインストールし、Microsoft Recovery Services エージェントを Hyper-V ホストにインストールします。
7. ターゲットとレプリケーションを設定します。
8. VM のレプリケーションを有効にします。
9. テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。



## <a name="prerequisites"></a>前提条件


**サポート要件** | **詳細**
--- | ---
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を確認します。
**オンプレミスのサーバー** | オンプレミスの VMM サーバーと Hyper-V ホストの要件の[詳細を確認](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure)します。
**オンプレミスの Hyper-V VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。
**Azure の URL** | VMM サーバーは、これらの URL にアクセスできる必要があります。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。


## <a name="prepare-for-deployment"></a>デプロイの準備をする
デプロイを準備するには、次の手順に従います。

1. [Azure ネットワークをセットアップする](#set-up-an-azure-network) 。
2. [Azure Storage アカウントを設定](#set-up-an-azure-storage-account) します。
3. [VMM サーバーを準備する](#prepare-the-vmm-server) 。
4. ネットワーク マッピングを準備する。 Site Recovery のデプロイ時にネットワーク マッピングを構成できるように、ネットワークをセットアップする。

### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップする
フェールオーバー後に作成された Azure VM の接続先となる Azure ネットワークが必要です。

* ネットワークは、Recovery Services コンテナーと同じリージョンにある必要があります。
* フェールオーバーされた Azure VM に使用するリソース モデルに応じて、Azure ネットワークを [Resource Manager モード](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)または[クラシック モード](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)でセットアップします。
* ネットワークをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。
Site Recovery に使用されている Azure ネットワークは、同じまたは異なるサブスクリプション内で[移動](../azure-resource-manager/resource-group-move-resources.md)できません。

### <a name="set-up-an-azure-storage-account"></a>Azure Storage アカウントを設定
* Azure にレプリケートされたデータを保持するには、Standard または Premium の Azure Storage アカウントが必要です。[Premium Storage](../storage/common/storage-premium-storage.md) は、IO を集中的に行うワークロードをホストするために、常に高い IO パフォーマンスと低遅延時間を必要とする仮想マシンに使用されます。 レプリケートされたデータを Premium アカウントを使用して保存する場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを保存するために、Standard ストレージ アカウントも必要になります。 アカウントは、Recovery Services コンテナーと同じリージョンにある必要があります。
* フェールオーバーされた Azure VM に使用するリソース モデルに応じて、アカウントを [Resource Manager モード](../storage/common/storage-create-storage-account.md)または[クラシック モード](../storage/common/storage-create-storage-account.md)でセットアップします。
* アカウントをセットアップしてから、以下の作業を開始することをお勧めします。 行わない場合は、Site Recovery のデプロイ中に行う必要があります。
- Site Recovery で使用されているストレージ アカウントは、同じまたは異なるサブスクリプション内で[移動](../azure-resource-manager/resource-group-move-resources.md)できないことに注意してください。

### <a name="prepare-the-vmm-server"></a>VMM サーバーを準備する
* VMM サーバーが [前提条件](#prerequisites)に準拠しているかどうかを確認します。
* Site Recovery のデプロイ時に、VMM サーバー上のすべてのクラウドを Azure Portal で使用できるようにするかを指定できます。 特定のクラウドのみをポータルに表示する場合は、そのための設定を VMM 管理コンソールで対象クラウドに対して有効にできます。

### <a name="prepare-for-network-mapping"></a>ネットワーク マッピングを準備する
Site Recovery のデプロイ中にネットワーク マッピングをセットアップする必要があります。 ネットワーク マッピングは、ソースの VMM VM ネットワークとターゲットの Azure ネットワークを対応付けることで、以下のことを実現します。

* 同じネットワーク上でフェールオーバーするマシンは、同じ方法や同じ復旧計画でフェールオーバーされなかった場合でも、相互に接続できます。
* ターゲットの Azure ネットワークにネットワーク ゲートウェイをセットアップすると、Azure 仮想マシンがオンプレミスの仮想マシンに接続できます。
* ネットワーク マッピングをセットアップするには、次の準備が必要です。

  * ソース Hyper-V ホスト サーバー上の VM が VMM VM ネットワークに接続されていることを確認します。 そのネットワークは、クラウドに関連付けられた論理ネットワークにリンクされている必要があります。
  * [前述](#set-up-an-azure-network)

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** > 、**[監視 + 管理]**、 > **[Backup and Site Recovery (OMS) (バックアップと Site Recovery (OMS))]** の順にクリックします。

    ![新しいコンテナー](./media/site-recovery-vmm-to-azure/new-vault3.png)
3. **[名前]**に、コンテナーを識別するフレンドリ名を入力します。 複数のサブスクリプションがある場合は、いずれかを選択します。
4. [リソース グループを作成](../azure-resource-manager/resource-group-template-deploy-portal.md)するか、既存のリソース グループを選択します。 Azure リージョンを指定します。 マシンは、このリージョンにレプリケートされます。 サポートされているリージョンを確認するには、「 [Azure Site Recovery Pricing Details (Azure Site Recovery の価格の詳細)](https://azure.microsoft.com/pricing/details/site-recovery/)
5. ダッシュボードからコンテナーにすばやくアクセスするには、**[ダッシュボードにピン留めする]** > **[コンテナーの作成]** をクリックします。

    ![新しいコンテナー](./media/site-recovery-vmm-to-azure/new-vault.png)

新しいコンテナーは、**[ダッシュボード]** > **[すべてのリソース]** と、メインの **[Recovery Services コンテナー]** ブレードに表示されます。


## <a name="select-the-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** で、コンテナーを選択します。
2. **[作業の開始]** で、**[Site Recovery]** > 、**[インフラストラクチャの準備]** > 、**[保護の目標]** の順にクリックします。

    ![Choose goals](./media/site-recovery-vmm-to-azure/choose-goals.png)
3. **[保護の目標]** で、**[To Azure (Azure へ)]** を選択し、**[Yes, with Hyper-V (はい、Hyper-V を使用する)]** を選択します。 Hyper-V ホストと復旧サイトの管理に VMM を使用しているかどうかの確認に対して、 **[はい]** を選択します。 次に、 **[OK]**をクリックします

## <a name="set-up-the-source-environment"></a>ソース環境をセットアップする

Azure Site Recovery プロバイダーを VMM サーバーにインストールし、サーバーをコンテナーに登録します。 Azure Recovery Services エージェントを Hyper-V ホストにインストールします。

1. **[インフラストラクチャの準備]**、 > **[ソース]** の順にクリックします。

    ![Set up source](./media/site-recovery-vmm-to-azure/set-source1.png)

2. **[ソースの準備]** で **[+ VMM]** をクリックして、VMM サーバーを追加します。

    ![Set up source](./media/site-recovery-vmm-to-azure/set-source2.png)

3. **[サーバーの追加]** で、**[サーバーの種類]** に **System Center VMM サーバー**が表示され、その VMM サーバーが[前提条件と URL 要件](#prerequisites)を満たしていることを確認します。
4. Azure Site Recovery プロバイダーのインストール ファイルをダウンロードします。
5. 登録キーをダウンロードします。 セットアップを実行する際に、これが必要になります。 キーは生成後 5 日間有効です。

    ![Set up source](./media/site-recovery-vmm-to-azure/set-source3.png)


## <a name="install-the-provider-on-the-vmm-server"></a>VMM サーバーにプロバイダーをインストールする

1. VMM サーバーでプロバイダーのセットアップ ファイルを実行します。
2. **[Microsoft Update]** で更新プログラムを登録すると、Microsoft Update ポリシーに従ってプロバイダーの更新プログラムがインストールされます。
3. **[インストール]** で、プロバイダーの既定のインストール先をそのまま使用するか、インストール先を変更して、**[インストール]** をクリックします。

    ![インストール場所](./media/site-recovery-vmm-to-azure/provider2.png)
4. インストールの完了後、**[登録]** をクリックして、VMM サーバーをコンテナーに登録します。
5. **[資格情報コンテナー設定]** ページで **[参照]** をクリックし、コンテナー キー ファイルを選択します。 Azure Site Recovery サブスクリプションとコンテナー名を指定します。

    ![サーバー登録](./media/site-recovery-vmm-to-azure/provider10.PNG)
6. **[インターネット接続]**で、VMM サーバーで実行中のプロバイダーがインターネット経由で Site Recovery に接続する方法を指定します。

   * プロバイダーから直接接続するように指定する場合は、**[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
   * 既存のプロキシに認証が必要な場合、またはカスタム プロキシを使用する場合は、**[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。
   * カスタム プロキシを使用する場合には、アドレス、ポート、資格情報を指定します。
   * プロキシを使用している場合は、[前提条件](#on-premises-prerequisites)に記載されている URL をあらかじめ許可しておく必要があります。
   * カスタム プロキシを使用する場合、指定されたプロキシの資格情報を使用して VMM RunAs アカウント (DRAProxyAccount) が自動的に作成されます。 このアカウントが正しく認証されるようにプロキシ サーバーを構成します。 VMM RunAs アカウントの設定は VMM コンソールで変更できます。 **[設定]** で、**[セキュリティ]** > **[実行アカウント]** の順に展開し、DRAProxyAccount のパスワードを変更します。 新しい設定を有効にするには、VMM サービスを再起動する必要があります。

     ![internet](./media/site-recovery-vmm-to-azure/provider13.PNG)
7. データの暗号化用に自動的に生成された SSL 証明書を保存する場所を、既定のまま使用するか変更します。 この証明書は、Azure Site Recovery ポータル内で Azure によって保護されているクラウドのデータ暗号化が有効な場合に使用されます。 この証明書を安全な場所に保管します。 Azure へのフェールオーバーを実行する際に、データの暗号化が有効になっている場合の暗号化解除にこれが必要になります。

    > [!NOTE]
    > Azure Site Recovery で提供されるデータ暗号化オプションを使用する代わりに、Azure で提供されている暗号化機能を使用して保存データを暗号化することをお勧めします。 Azure で提供される暗号化機能は、ストレージ アカウントに対して有効にすることができ、暗号化/暗号化解除が Azure Storage で処理されるときのパフォーマンス向上に役立ちます。
    > [Azure の Storage サービスの暗号化の詳細については、こちらを参照してください](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption)。
    
8. **[サーバー名]**に、コンテナーで VMM サーバーを識別する表示名を入力します。 クラスター構成で、VMM クラスターのロール名を指定します。
9. VMM サーバー上のすべてのクラウドのメタデータをコンテナーと同期する場合は、**[Sync cloud metadata (クラウド メタデータの同期)]** を有効にします。 この操作は、各サーバーで 1 回のみ実行する必要があります。 すべてのクラウドを同期したくない場合は、この設定をオフのままにして、VMM コンソールのクラウドのプロパティで各クラウドを個別に同期できます。 **[登録]** をクリックしてプロセスを完了します。

    ![サーバー登録](./media/site-recovery-vmm-to-azure/provider16.PNG)
10. 登録が開始されます。 登録が完了すると、**[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]** > **[VMM サーバー]** に、サーバーが表示されます。


## <a name="install-the-azure-recovery-services-agent-on-hyper-v-hosts"></a>Hyper-V ホストへの Azure Recovery Services エージェントのインストール

1. プロバイダーのセットアップが完了したら、Azure Recovery Services エージェントのインストール ファイルをダウンロードする必要があります。 VMM クラウド内の各 Hyper-V サーバーでセットアップを実行します。

    ![Hyper-V sites](./media/site-recovery-vmm-to-azure/hyperv-agent1.png)
2. **[前提条件の確認]** で **[次へ]** をクリックします。 不足している前提条件があると自動的にインストールされます。

    ![Recovery Services エージェントの前提条件](./media/site-recovery-vmm-to-azure/hyperv-agent2.png)
3. **[インストール設定]** で、インストール先とキャッシュの場所を既定のまま使用するか変更します。 利用可能な記憶域が 5 GB 以上あるドライブにキャッシュを構成できますが、キャッシュ ドライブには 600 GB 以上の空き領域を確保することをお勧めします。 その後、 **[インストール]**をクリックします。
4. インストールが完了したら、 **[閉じる]** をクリックして終了します。

    ![Register MARS Agent](./media/site-recovery-vmm-to-azure/hyperv-agent3.png)

### <a name="command-line-installation"></a>コマンド ラインを使用したインストール
Microsoft Azure Recovery Services エージェントは、コマンド ラインで次のコマンドを使用してインストールできます。

     marsagentinstaller.exe /q /nu

### <a name="set-up-internet-proxy-access-to-site-recovery-from-hyper-v-hosts"></a>Hyper-V ホストから Site Recovery へのインターネット プロキシ アクセスをセットアップする

Hyper-V ホストで実行されている Recovery Services エージェントは、VM のレプリケーションのために、Azure へのインターネット アクセスが必要です。 プロキシを介してインターネットにアクセスしている場合は、次のようにセットアップします。

1. Hyper-V ホストの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。
3. **[プロキシの構成]** タブで、プロキシ サーバーの情報を指定します。

    ![Register MARS Agent](./media/site-recovery-vmm-to-azure/mars-proxy.png)
4. [前提条件](#on-premises-prerequisites)に記載されている URL にエージェントが到達できることを確認します。

## <a name="set-up-the-target-environment"></a>ターゲット環境をセットアップする
レプリケーションに使用する Azure ストレージ アカウントと、フェールオーバー後に Azure VM が接続する Azure ネットワークを指定します。

1. **[インフラストラクチャの準備]** > **[ターゲット]** の順にクリックし、フェールオーバーされた仮想マシンを作成するサブスクリプションとリソース グループを選択します。 Azure で、フェールオーバーされた仮想マシンに使用するデプロイ モデル (クラシックまたはリソース管理) を選択します。

    ![Storage](./media/site-recovery-vmm-to-azure/enablerep3.png)

2. Site Recovery によって、互換性のある Azure ストレージ アカウントとネットワークが 1 つ以上あるかどうかが確認されます。

    ![Storage](./media/site-recovery-vmm-to-azure/compatible-storage.png)

3. まだストレージ アカウントを作成しておらず、Resource Manager を使用して作成する場合は、**[+ ストレージ アカウント]** をクリックしてインラインで作成します。  **[ストレージ アカウントの作成]** ブレードで、アカウント名、種類、サブスクリプション、場所を指定します。 アカウントは、Recovery Services コンテナーと同じ場所にある必要があります。

   ![Storage](./media/site-recovery-vmm-to-azure/gs-createstorage.png)


   * クラシック モデルを使用してストレージ アカウントを作成する場合は、Azure Portal で作成できます。 [詳細情報](../storage/common/storage-create-storage-account.md)
   * レプリケートされたデータに Premium ストレージ アカウントを使用している場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを格納するために、追加の Standard ストレージ アカウントをセットアップします。
5. まだ Azure ネットワークを作成しておらず、Resource Manager を使用して作成する場合は、**[+ ネットワーク]** をクリックしてインラインで作成します。 **[仮想ネットワークの作成]** ブレードで、ネットワーク名、アドレス範囲、サブネットの詳細、サブスクリプション、場所を指定します。 ネットワークは、Recovery Services コンテナーと同じ場所にある必要があります。

   ![ネットワーク](./media/site-recovery-vmm-to-azure/gs-createnetwork.png)

   クラシック モデルを使用してネットワークを作成する場合は、Azure Portal で作成できます。 [詳細情報](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)。

### <a name="configure-network-mapping"></a>ネットワーク マッピングの構成

* [こちら](#prepare-for-network-mapping) をご覧ください。
* VMM サーバー上の仮想マシンが VM ネットワークに接続されており、1 つ以上の Azure 仮想ネットワークが作成されていることを確認してください。 1 つの Azure ネットワークに対して複数の VM ネットワークをマップできます。

マッピングは次のように構成します。

1. **[Site Recovery Infrastructure (Site Recovery インフラストラクチャ)]** > **[ネットワーク マッピング]** > **[ネットワーク マッピング]** の順にクリックし、**[+ ネットワーク マッピング]** アイコンをクリックします。

    ![ネットワーク マッピング](./media/site-recovery-vmm-to-azure/network-mapping1.png)
2. **[ネットワーク マッピングの追加]** で、ソース VMM サーバーを選択し、ターゲットとして **[Azure]** を選択します。
3. サブスクリプションとフェールオーバー後のデプロイメント モデルを確認します。
4. **[ソース ネットワーク]**で、VMM サーバーに関連付けられている一覧から、マップするソースのオンプレミス VM ネットワークを選択します。
5. **[ターゲット ネットワーク]**で、レプリカの Azure VM が作成されたときに配置される Azure ネットワークを選択します。 次に、 **[OK]**をクリックします

    ![ネットワーク マッピング](./media/site-recovery-vmm-to-azure/network-mapping2.png)

ネットワーク マッピングが開始されると、次のように動作します。

* ソースの VM ネットワーク上の既存の VM は、マッピングが開始されるとターゲット ネットワークに接続します。 ソースの VM ネットワークに接続する新しい VM は、レプリケーションが実行されると、マップされた Azure ネットワークに接続します。
* 既存のネットワーク マッピングを変更すると、レプリカの仮想マシンは新しい設定を使用して接続します。
* ターゲット ネットワークに複数のサブネットがあり、そのサブネットのいずれかが、ソースの仮想マシンが配置されているサブネットと同じ名前である場合、フェールオーバー後、レプリカの仮想マシンはそのターゲット サブネットに接続します。
* ターゲットのサブネットで名前が一致するものがなければ、仮想マシンはネットワークの最初のサブネットに接続します。

## <a name="configure-replication-settings"></a>レプリケーションの設定を構成する
1. 新しいレプリケーション ポリシーを作成するには、**[インフラストラクチャの準備]** > **[レプリケーションの設定]** > **[+ 作成と関連付け]** の順にクリックします。

    ![ネットワーク](./media/site-recovery-vmm-to-azure/gs-replication.png)
2. **[ポリシーの作成と関連付け]**で、ポリシー名を指定します。
3. **[コピーの頻度]**で、初期レプリケーションの後、差分データをレプリケートする頻度 (30 秒ごと、5 分ごと、または 15 分ごと) を指定します。

    > [!NOTE]
    >  Premium Storage にレプリケートするときには、30 秒の頻度はサポートされません。 上限は、Premium Storage によってサポートされる blob ごとのスナップショットの数 (100) によって決まります。 [詳細情報](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. **[復旧ポイントの保持期間]**で、各復旧ポイントのリテンション期間の長さを時間単位で指定します。 保護されたマシンはこの期間内のどのポイントにも復旧できます。
5. **[アプリ整合性スナップショットの頻度]** で、アプリケーション整合性スナップショットを含む復旧ポイントの作成頻度 (1 ～ 12 時間) を指定します。 Hyper-V では 2 種類のバックアップを使用します。1 つは標準バックアップで、仮想マシン全体の増分バックアップを実行します。もう 1 つは、アプリケーション整合性スナップショットで、仮想マシン内部のアプリケーション データの特定の時点のスナップショットを作成します。 アプリケーション整合性スナップショットでは、ボリューム シャドウ コピー サービス (VSS) を使用して、スナップショットを作成するときにアプリケーションを一貫性のある状態に保ちます。 アプリケーション整合性スナップショットを有効にすると、ソースの仮想マシンで実行するアプリケーションのパフォーマンスに影響があります。 設定する値は、追加で構成する復旧ポイントの数より少ない数にしてください。
6. **[初期レプリケーションの開始時刻]**で、初期レプリケーションを開始する時刻を指定します。 レプリケーションはご利用のインターネット帯域幅で行われるため、トラフィックの多い時間帯を避けるようにスケジュールを設定することをお勧めします。
7. **[Azure に格納されるデータの暗号化]**で、Azure ストレージに格納されるデータを暗号化するかどうかを指定します。 次に、 **[OK]**をクリックします

    ![Replication policy](./media/site-recovery-vmm-to-azure/gs-replication2.png)
8. 新しいポリシーを作成すると、自動的に VMM クラウドに関連付けられます。 **[OK]**をクリックします。 追加の VMM クラウド (およびその内部にある VM) を、このレプリケーション ポリシーに関連付けるには、**[レプリケーション]** > ポリシー名 > **[Associate VMM Cloud (VMM クラウドを関連付ける)]** の順に選択します。

    ![Replication policy](./media/site-recovery-vmm-to-azure/policy-associate.png)

## <a name="capacity-planning"></a>容量計画

基本的なインフラストラクチャのセットアップが完了したので、キャパシティ プランニングを行い、追加のリソースが必要かどうかを検討できます。

Site Recovery が備えている Capacity Planner を使用して、ソース環境、Site Recovery のコンポーネント、ネットワーク、およびストレージに適切なリソースを割り当てることができます。 このキャパシティ プランニング ツールは、VM、ディスク、ストレージの平均数に基づく見積もりを使用するクイック モードか、ワークロード レベルで数値を入力する詳細モードで実行できます。 開始する前に次の操作を実行してください。

* VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
* レプリケートされたデータの 1 日の変更 (チャーン) 率を見積もる。 この見積もりには、 [Capacity Planner for Hyper-V Replica](https://www.microsoft.com/download/details.aspx?id=39057) が役立ちます。

1. **[ダウンロード]** をクリックしてツールをダウンロードし、実行する。 [こちらの記事をご覧ください](site-recovery-capacity-planner.md) 。
2. 作業が完了したら、**[Have you run the Capacity Planner? (Capacity Planner を実行しましたか?)]** で **[はい]** を選択する。

   ![容量計画](./media/site-recovery-vmm-to-azure/gs-capacity-planning.png)

   [ネットワーク帯域幅の制御](#network-bandwidth-considerations)について




## <a name="enable-replication"></a>Enable replication

開始する前に、Azure ユーザー アカウントに、新しい仮想マシンを Azure にレプリケートできるようにするために必要な特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が付与されていることを確認してください。

レプリケーションを有効にするには、次の手順に従います。

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にします。

    ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication1.png)
2. **[ソース]** ブレードで、VMM サーバーと、Hyper-V ホストが配置されているクラウドを選択します。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication-source.png)
3. **[ターゲット]** で、サブスクリプション、フェールオーバー後のデプロイメント モデル、レプリケートされたデータに使用するストレージ アカウントを選択します。

    ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication-target.png)
4. 使用するストレージ アカウントを選択します。 現在所有しているのと異なるストレージ アカウントを使用する場合は、[ストレージ アカウントを作成](#set-up-an-azure-storage-account)できます。 レプリケートされたデータに Premium Storage アカウントを使用している場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを格納するために、追加の Standard ストレージ アカウントを選択する必要があります。Resource Manager モデルを使用してストレージ アカウントを作成するには、**[新規作成]** をクリックします。 クラシック モデルを使用してストレージ アカウントを作成する場合は、[Azure Portal](../storage/common/storage-create-storage-account.md) で作成できます。 次に、 **[OK]**をクリックします
5. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 現在所有しているのと異なるネットワークを使用する場合は、[ネットワークを作成](#set-up-an-azure-network)できます。 Resource Manager モデルを使用してネットワークを作成するには、**[新規作成]** をクリックします。 クラシック モデルを使用してネットワークを作成する場合は、[Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) で作成できます。 該当する場合は、サブネットを選択します。 次に、 **[OK]**をクリックします
6. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication5.png)

7. **[プロパティ]**  >  **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。

    - Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認します。   
    - 既定では、VM のすべてのディスクがレプリケーションの対象として選択されていますが、ディスクを消去して除外することもできます。

        - レプリケーションの帯域幅を減らすために、ディスクを除外したほうがよい場合があります。 たとえば、一時的なデータや、マシンまたはアプリを再起動するたびに更新されるデータ (pagefile.sys や Microsoft SQL Server tempdb など) が保存されたディスクをレプリケーションから除外できます。 ディスクをレプリケーションから除外するには、ディスクの選択を解除します。
        - 除外できるのは、ベーシック ディスクだけです。 OS ディスクを除外することはできません。
        - ダイナミック ディスクは除外しないことをお勧めします。 Site Recovery は、ゲスト VM 内の仮想ハード ディスクがベーシック ディスクであるかダイナミック ディスクであるかを識別できません。 依存するダイナミック ボリューム ディスクすべてが除外されていない場合に VM がフェールオーバーし、そのディスクのデータにアクセスできなくなると、保護されたダイナミック ディスクは障害が発生したディスクとして表示されます。
        - レプリケーションが有効になった後で、レプリケーション用のディスクを追加または削除することはできません。 ディスクを追加または除外する場合は、VM の保護を無効にし、再度有効にする必要があります。
        - Azure で手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけが Azure から Hyper-V にフェールバックされます。 Hyper-V から Azure へのフェールバックまたはレプリケーションの反転に、手動で作成されたディスクを含めることはできません。
        - アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。

    **[OK]** をクリックして変更を保存します。 後で追加のプロパティを設定できます。

    ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication6-with-exclude-disk.png)

8. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。 次に、 **[OK]**をクリックします レプリケーション ポリシーを変更するには、**[レプリケーション ポリシー]**ポリシー名 > **[設定の編集]** の順にクリックします。 適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。

   ![Enable replication](./media/site-recovery-vmm-to-azure/enable-replication7.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。

### <a name="view-and-manage-vm-properties"></a>VM プロパティを表示して管理する

ソース マシンのプロパティを確認することをお勧めします。 Azure VM の名前は、 [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠している必要があります。

1. **[保護されたアイテム]** で、**[レプリケートされたアイテム]** をクリックし、マシンを選択すると、その詳細が表示されます。

    ![Enable replication](./media/site-recovery-vmm-to-azure/vm-essentials.png)
2. **[プロパティ]** で、VM のレプリケーションとフェールオーバーの情報を確認できます。

    ![Enable replication](./media/site-recovery-vmm-to-azure/test-failover2.png)
3. **[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、Azure VM の名前とターゲットのサイズを指定できます。 必要に応じて、 [Azure の要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) に準拠するように名前を変更します。 Azure VM に割り当てられているターゲット ネットワーク、サブネット、IP アドレスに関する情報を表示し、変更することもできます。
以下の点に注意してください。

   * ターゲット IP アドレスを設定できます。 アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。 フェールオーバーで使用できないアドレスが設定された場合、フェールオーバーは失敗します。 テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
   * ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。

     * ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     * ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     * たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるものの、サポートされているターゲット サイズで 1 つしかサポートされていない場合、ターゲット マシンのアダプターの数は 1 つのみになります。     
     * VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。

     ![Enable replication](./media/site-recovery-vmm-to-azure/test-failover4.png)

4. **[ディスク]** で、レプリケートされる VM のオペレーティング システム ディスクとデータ ディスクを確認できます。

#### <a name="managed-disks"></a>管理ディスク

Azure への移行時に管理ディスクをマシンに接続する場合、**[コンピューティングとネットワーク]** > **[コンピューティングのプロパティ]** で、VM の [管理ディスクを使用] 設定を [はい] に設定することができます。 管理ディスクを使用すると、VM ディスクに関連付けられているストレージ アカウントを管理できるため、Azure IaaS VM のディスク管理が簡素化されます。 [管理ディスクの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview)。

   - 管理ディスクは、Azure へのフェールオーバー時にのみ作成され、仮想マシンに接続されます。 保護を有効にする際、オンプレミスのマシンからのデータは引き続きストレージ アカウントにレプリケートされます。
   管理ディスクは、Resource Manager デプロイメント モデルでデプロイされた仮想マシンに対してのみ作成することができます。  

  > [!NOTE]
  > Azure からオンプレミス Hyper-V 環境へのフェールバックは、現時点では、管理ディスクを使用するマシンではサポートされていません。 このマシンを Azure に移行する場合にのみ、[管理ディスクを使用] を [はい] に設定してください。

   - [管理ディスクを使用] を [はい] に設定すると、そのリソース グループ内で [管理ディスクを使用] が [はい] に設定されている可用性セットのみ選択できるようになります。 これは、管理ディスクを使用する仮想マシンが、必ず [管理ディスクを使用] プロパティが [はい] に設定された可用性セットの一部になるためです。 フェールオーバー時に管理ディスクを使用するかどうかの判断に基づいて、[管理ディスクを使用] プロパティが設定された可用性セットを作成するようにしてください。  同様に、[管理ディスクを使用] を [いいえ] に設定すると、そのリソース グループ内で [管理ディスクを使用] プロパティが [いいえ] に設定されている可用性セットのみ選択できるようになります。 [管理ディスクと可用性セットの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)。

  > [!NOTE]
  > レプリケーションに使用されるストレージ アカウントが、どこかの時点で Storage Service Encryption で暗号化されていた場合、フェールオーバー中の管理ディスクの作成は失敗します。 [管理ディスクを使用] を [いいえ] に設定してフェールオーバーを再試行できるほか、仮想マシンの保護を無効にしてから、Storage Service Encryption が有効になっていないストレージ アカウントで、任意の時点でその仮想マシンを保護することもできます。
  > [Storage Service Encryption と管理ディスクの詳細をご覧ください](https://docs.microsoft.com/en-us/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)。


## <a name="test-the-deployment"></a>展開をテスト

デプロイをテストするために、単一の仮想マシンに対して、または 1 つ以上の仮想マシンを含む復旧計画に対してテスト フェールオーバーを実行できます。

### <a name="before-you-start"></a>開始する前に

 - フェールオーバー後に RDP を使用して Azure VM に接続する場合は、[接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)の詳細情報を確認します。
 - すべてをテストするには、テスト環境に Active Directory と DNS をコピーする必要があります。 [詳細情報](site-recovery-active-directory.md#test-failover-considerations)。

### <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. 1 つの VM をフェールオーバーする場合は、**[レプリケートされたアイテム]** で、VM をクリックして **[+ テスト フェールオーバー]** をクリックします。
2. 復旧計画をフェールオーバーする場合は、**[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてプロパティを開くか、**[Site Recovery ジョブ]** の **[テスト フェールオーバー]** をクリックします。
5. フェールオーバーの完了後は、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
6. フェールオーバー後の接続の準備が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[Cleanup test failover (テスト フェールオーバーのクリーンアップ)]** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 これで、テスト フェールオーバー中に作成された仮想マシンが削除されます。

詳しくは、[Azure へのテスト フェールオーバー](site-recovery-test-failover-to-azure.md)に関する記事をご覧ください。

## <a name="monitor-the-deployment"></a>デプロイの監視

Site Recovery デプロイメントの構成設定、状態、および正常性を監視する方法を次に示します。

1. コンテナー名をクリックして、 **[要点]** ダッシュボードにアクセスします。 このダッシュボードで、Site Recovery ジョブ、レプリケーションの状態、復旧計画、サーバーの状態、およびイベントを確認できます。  **[要点]** ダッシュボードをカスタマイズして、他の Site Recovery コンテナーや Backup コンテナーの状態など、ニーズに最適なタイルとレイアウトを表示できます。

    ![[要点]](./media/site-recovery-vmm-to-azure/essentials.png)
2. **[正常性]** で、オンプレミス サーバー (VMM または構成サーバー) の問題と、Site Recovery によって過去 24 時間以内に発生したイベントを監視できます。
3. **[レプリケートされたアイテム]**、**[復旧計画]**、**[Site Recovery Jobs (Site Recovery ジョブ)]** の各タイルで、レプリケーションの管理と監視を実行できます。 **[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックすると、ジョブの詳細を確認できます。

## <a name="command-line-installation-for-the-azure-site-recovery-provider"></a>Azure Site Recovery プロバイダーのコマンド ライン インストール

Azure Site Recovery プロバイダーは、コマンド ラインからインストールできます。 この方法を使用すると、Windows Server 2012 R2 の Server Core にプロバイダーをインストールできます。

1. プロバイダーのインストール ファイルと登録キーをフォルダーにダウンロードします。 たとえば、C:\ASR です。
2. 管理者特権のコマンド プロンプトで次のコマンドを実行して、プロバイダーのインストーラーを抽出します。

            C:\Windows\System32> CD C:\ASR
            C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q
3. 次のコマンドを実行して、コンポーネントをインストールします。

            C:\ASR> setupdr.exe /i
4. 次のコマンドを実行して、サーバーをコンテナーに登録します。

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>       

各値の説明:

* **/Credentials**: 登録キー ファイルが配置されている場所を指定する必須パラメーターです。  
* **/Friendlyname**: Azure Site Recovery ポータルに表示される、Hyper-V ホスト サーバーの名前を表す必須パラメーターです。
* * **/EncryptionEnabled**: 省略可能。VMM クラウド内の Hyper-V VM を Azure にレプリケートする場合に指定します。 Azure で仮想マシンを暗号化するかどうかを指定します (保存時の暗号化)。 ファイル名の拡張子が **.pfx** であることを確認してください。 既定では、暗号化は無効になっています。

    > [!NOTE]
    > Azure Site Recovery で提供される暗号化オプション (EncryptionEnabled オプション) を使用する代わりに、Azure で提供されている暗号化機能を使用して保存データを暗号化することをお勧めします。 Azure で提供される暗号化機能は、ストレージ アカウントに対して有効にすることができ、暗号化/暗号化解除が Azure Storage で実行されるときのパフォーマンス向上に役立ちます  
    > 。
    > [Azure の Storage サービスの暗号化の詳細については、こちらを参照してください](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption)。
    
* **/proxyAddress**: 省略可能。プロキシ サーバーのアドレスを指定します。
* **/proxyport**: 省略可能。プロキシ サーバーのポートを指定します。
* **/proxyUsername**: 省略可能。プロキシのユーザー名を指定します (認証が必要なプロキシの場合)。
* **/proxyPassword**: 省略可能。プロキシ サーバーでの認証に使用するパスワードを指定します (認証が必要なプロキシの場合)。


### <a name="network-bandwidth-considerations"></a>ネットワーク帯域幅に関する考慮事項
Capacity Planner を使用して、レプリケーション (初期レプリケーションとその後の差分レプリケーション) に必要な帯域幅を計算できます。 レプリケーションの帯域幅の使用量を制御する方法には、いくつかのオプションがあります。

* **帯域幅のスロットル**: セカンダリ サイトにレプリケートされる Hyper-V トラフィックは特定の Hyper-V ホストを通過します。 このホスト サーバーの帯域幅をスロットルできます。
* **帯域幅の微調整**: いくつかのレジストリ キーを使用して、レプリケーションに使用される帯域幅に影響を与えることができます。

#### <a name="throttle-bandwidth"></a>帯域幅のスロットル
1. Hyper-V ホスト サーバーの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

    ![帯域幅を調整する](./media/site-recovery-vmm-to-azure/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。 サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

#### <a name="influence-network-bandwidth"></a>ネットワーク帯域幅に影響を与える
**UploadThreadsPerVM** レジストリ値を使用して、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を制御できます。 値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。 **DownloadThreadsPerVM** レジストリ値では、フェールバック時にデータ転送に使用されるスレッドの数を指定できます。

1. レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。

   * 値 **UploadThreadsPerVM** を変更して (キーが存在しない場合は作成して)、ディスクのレプリケーションに使用されるスレッドを制御できます。
   * 値 **DownloadThreadsPerVM** を変更して (キーが存在しない場合は作成して)、Azure からのフェールバック トラフィックに使用されるスレッドを制御できます。
2. 既定値は 4 ですが、 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。 最大値は 32 です。 トラフィックを監視して値を最適化できます。

## <a name="next-steps"></a>次のステップ

初期レプリケーションが完了し、デプロイをテストしたら、必要に応じてフェールオーバーを呼び出すことができます。 さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。

