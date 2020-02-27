---
title: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する
description: Azure Migrate を使用した評価と移行に向けて VMware VM を準備する方法について説明します。
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: f00d5ba4841427098b0ab79ad1930e357008b6e0
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030797"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>評価および Azure への移行のために VMware VM を準備する

この記事は、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの VMware VM の評価や Azure への移行を準備する場合に役立ちます。

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。


これはシリーズの最初のチュートリアルであり、VMware VM を評価して移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate と連携するように Azure を準備します。
> * VMware for VM の評価を準備します。
> * VMware for VM の移行を準備します。

> [!NOTE]
> チュートリアルでは、シナリオの最も簡単なデプロイ パスを示します。 これらは、デプロイを設定する方法を学習する場合に、また簡単な概念実証として役立ちます。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、VMware の評価と移行に関するハウツーを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

これらのアクセス許可が必要です。

**タスク** | **アクセス許可**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するためのアクセス許可が必要です。
**Azure Migrate アプライアンスを登録する** | Azure Migrate では、軽量の Azure Migrate アプライアンスを使用して、Azure Migrate Server Assessment で VMware VM を評価し、Azure Migrate Server Migration で VMware VM の[エージェントレス移行](server-migrate-overview.md)を実行します。 このアプライアンスでは VM が検出され、VM のメタデータとパフォーマンス データが Azure Migrate に送信されます。<br/><br/>アプライアンスの登録時に、リソースプロバイダー (Microsoft.OffAzure、Microsoft.Migrate、および Microsoft.KeyVault) が、アプライアンスで選択したサブスクリプションに登録されます。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。<br/><br/> オンボードの一環として、Azure Migrate では 2 つの Azure Active Directory (Azure AD) アプリが作成されます。<br/> - 最初のアプリは、アプライアンスで実行されているエージェントと Azure で実行されているそれぞれのサービスとの間の通信 (認証と承認) に使用されます。 このアプリには、任意のリソースに対して ARM 呼び出しや RBAC アクセスを行うための特権はありません。<br/> - 2 番目のアプリは、エージェントレス移行の目的でユーザーのサブスクリプション内に作成されたキー コンテナーにアクセスするためにのみ使用されます。 アプライアンスから検出が開始されると、(顧客のテナント内で作成された) Azure キー コンテナーで RBAC アクセスが可能になります。
**キー コンテナーの作成** | Azure Migrate Server Migration を使用して VMware VM を移行するために、Azure Migrate によってキー コンテナーが作成され、ご自分のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーが管理されます。 コンテナーを作成するには、Azure Migrate プロジェクトが存在しているリソース グループに対するロールの割り当てアクセス許可が必要です。






### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

アプライアンスを登録するには、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てます。 次のいずれかの方法を使用して、アクセス許可を割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

> [!NOTE]
> - アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
> - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。 これは、重要ではない既定の設定です。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。

### <a name="assign-permissions-to-create-a-key-vault"></a>キー コンテナーを作成するためのアクセス許可を割り当てる

Azure Migrate でキー コンテナーを作成できるようにするには、次のようにアクセス許可を割り当てます。

1. Azure portal で、リソース グループの **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。

    - サーバー評価を実行するには、**共同作成者**のアクセス許可で十分です。
    - エージェントレスのサーバー移行を実行するには、**所有者** (または**共同作成者**および**ユーザー アクセス管理者**) のアクセス許可が必要です。

3. 必要なアクセス許可がない場合は、リソース グループの所有者にそれらを依頼してください。



## <a name="prepare-for-vmware-vm-assessment"></a>VMware VM の評価の準備

VMware VM の評価を準備するには、以下が必要です。

- **VMware の設定を確認します**。 移行する vCenter Server と VM が要件を満たしていることを確認します。
- **評価アカウントを設定します**。 評価のための VM を検出するには、Azure Migrate が vCenter Server にアクセスする必要があります。
- **アプライアンスの要件を確認します**。 評価に使用する Azure Migrate アプライアンスのデプロイ要件を確認します。

### <a name="verify-vmware-settings"></a>VMware の設定の確認

1. 評価に関する VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#vmware-requirements)します。
2. 必要なポートが vCenter サーバー上で開かれていることを[確認](migrate-support-matrix-vmware.md#port-access)します。
3. vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がアカウントにあることを確認します。 OVA ファイルを使用して、Azure Migrate アプライアンスを VMware VM としてデプロイします。


### <a name="set-up-an-account-for-assessment"></a>評価のためのアカウントの設定

評価およびエージェントレス移行のための VM を検出するには、Azure Migrate が vCenter Server にアクセスする必要があります。

- アプリケーションを検出したりエージェントレスで依存関係を視覚化したりする予定がある場合は、読み取り専用のアクセス権を持つ vCenter Server アカウントを作成し、 **[仮想マシン]**  >  **[ゲスト操作]** の権限を有効にします。

  ![vCenter Server アカウントの権限](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- アプリケーションの検出やエージェントレスでの依存関係の視覚化を行う予定がなければ、vCenter Server の読み取り専用アカウントを設定します。

### <a name="verify-appliance-settings-for-assessment"></a>評価用のアプライアンス設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. VMware VM のアプライアンス要件を[確認](migrate-appliance.md#appliance---vmware)します。
2. アプライアンスがアクセスする必要がある Azure URL を[確認](migrate-appliance.md#url-access)します。 URL ベースのファイアウォールまたはプロキシを使用している場合は、必要な URL へのアクセスが許可されていることを確認します。
3. 検出および評価中にアプライアンスによって収集されるデータを[確認](migrate-appliance.md#collected-data---vmware)します。
4. アプライアンスのポート アクセス要件に[注意](migrate-support-matrix-vmware.md#port-access)します。




## <a name="prepare-for-agentless-vmware-migration"></a>エージェントレスの VMware 移行の準備

VMware VM のエージェントレス移行の要件を確認します。

1. VMware サーバー要件と、Azure Migrate Server Migration を使用するエージェントレス移行で Azure Migrate が vCenter Server にアクセスするために必要な[アクセス許可](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)を[確認](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)します。
2. エージェントレス移行を使用して Azure に移行する VMware VM の要件を[確認](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms)します。
4. エージェントレス移行に Azure Migrate アプライアンスを使用するための要件を[確認](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance)します。
5. エージェントレス移行に必要な [URL アクセス](migrate-appliance.md#url-access)と[ポート アクセス](migrate-support-matrix-vmware-migration.md#agentless-ports)に注意します。


## <a name="prepare-for-agent-based-vmware-migration"></a>エージェントベースの VMware 移行の準備

VMware VM の[エージェントベース移行](server-migrate-overview.md)の要件を確認します。

1. VMware サーバー要件と、Azure Migrate Server Migration を使用するエージェントベースの移行で Azure Migrate が vCenter Server にアクセスするために必要なアクセス許可を[確認](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers)します。
2. エージェントベース移行を使用して Azure に移行する VMware VM の要件 (移行する各 VM へのモビリティ サービスのインストールなど) を[確認](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms)します。
3. エージェントベース移行では、レプリケーション アプライアンスを使用します。
    - レプリケーション アプライアンスのデプロイ要件と、アプライアンスでの MySQL のインストールの[オプション](migrate-replication-appliance.md#mysql-installation)を[確認](migrate-replication-appliance.md#appliance-requirements)します。
    - レプリケーション アプライアンスの [URL](migrate-replication-appliance.md#url-access) と[ポート](migrate-replication-appliance.md#port-access) アクセス要件を確認します。
    
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アクセス許可を設定しました。
> * 評価と移行のために VMware を準備しました。


2 番目のチュートリアルに進み、Azure Migrate プロジェクトを設定し、Azure に移行するために VMware VM を評価します。

> [!div class="nextstepaction"]
> [VMware VM の評価](./tutorial-assess-vmware.md)
