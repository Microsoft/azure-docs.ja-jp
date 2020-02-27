---
title: Azure Migrate を使用した評価と移行に向けて物理サーバーを準備する
description: Azure Migrate を使用した評価と移行に向けて物理サーバーを準備する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 42eb603be0152b9e8cfb36d02e8f0602c40afe54
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031205"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>物理サーバーの評価および Azure への移行を準備する

この記事では、[Azure Migrate](migrate-services-overview.md) を使用して、オンプレミスの物理サーバーの評価および Azure への移行を準備する方法について説明します。

[Azure Migrate](migrate-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 

これはシリーズの最初のチュートリアルであり、Azure Migrate を使用して物理サーバーを評価する方法を示しています。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure を準備します。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * サーバー評価のために、オンプレミスの物理サーバーを準備します。


> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、物理サーバーの評価に関するハウツーを参照してください。


Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prepare-azure"></a>Azure を準備する

### <a name="azure-permissions"></a>Azure のアクセス許可

Azure Migrate のデプロイに対するアクセス許可を設定する必要があります。

**タスク** | **アクセス許可**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するためのアクセス許可が必要です。
**Azure Migrate アプライアンスを登録する** | Azure Migrate は、軽量な Azure Migrate アプライアンスを使用して、Azure Migrate Server Assessment によって物理サーバーを検出し、評価します。 このアプライアンスによってサーバーが検出され、サーバーのメタデータとパフォーマンス データが Azure Migrate に送信されます。<br/><br/>アプライアンスの登録時に、リソースプロバイダー (Microsoft.OffAzure、Microsoft.Migrate、および Microsoft.KeyVault) が、アプライアンスで選択したサブスクリプションに登録されます。 リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 リソースプロバイダーを登録するには、サブスクリプションの共同作成者または所有者のロールが必要です。<br/><br/> オンボードの一環として、Azure Migrate によって Azure Active Directory (Azure AD) アプリが作成されます。<br/> AAD アプリは、アプライアンスで実行されているエージェントと Azure で実行されているそれぞれのサービスとの間の通信 (認証と承認) に使用されます。 このアプリには、任意のリソースに対して ARM 呼び出しや RBAC アクセスを行うための特権はありません。



### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

Azure Migrate プロジェクトを作成するためのアクセス許可があることを確認します。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-permissions-to-register-the-appliance"></a>アプライアンスを登録するためのアクセス許可を割り当てる

次のいずれかの方法を使用して、アプライアンスの登録時に Azure AD アプリを作成するためのアクセス許可を Azure Migrate に割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

> [!NOTE]
> - このアプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
> - 新しいアプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はアプライアンスを設定した後で削除できます。


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます。

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細については、こちらを参照してください](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)。


## <a name="prepare-for-physical-server-assessment"></a>物理サーバーの評価を準備する

物理サーバーの評価を準備するには、物理サーバーの設定を確認し、アプライアンスのデプロイの設定を確認する必要があります。

### <a name="verify-physical-server-settings"></a>物理サーバーの設定を確認する

1. サーバー評価のため、[物理サーバーの要件](migrate-support-matrix-physical.md#physical-server-requirements)を確認します。
2. 物理サーバー上で[必要なポート](migrate-support-matrix-physical.md#port-access)が開かれていることを確認します。


### <a name="verify-appliance-settings"></a>アプライアンスの設定の確認

次のチュートリアルで Azure Migrate アプライアンスを設定して評価を開始する前に、アプライアンスのデプロイの準備を行います。

1. 物理サーバーのアプライアンスの要件を[確認](migrate-appliance.md#appliance---physical)します。
2. アプライアンスがアクセスする必要がある Azure URL を[確認](migrate-appliance.md#url-access)します。
3. 検出および評価中にアプライアンスによって収集されるデータを[確認](migrate-appliance.md#collected-data---vmware)します。
4. 物理サーバー評価のためのポート アクセスの要件に[注意](migrate-support-matrix-physical.md#port-access)します。


### <a name="set-up-an-account-for-physical-server-discovery"></a>物理サーバー検出用のアカウントを設定する

Azure Migrate には、オンプレミスのサーバーを検出するためのアクセス許可が必要です。

- **Windows:** 探索に含めるすべての Windows サーバー上にローカル ユーザー アカウントを設定します。ユーザー アカウントは、リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザーのグループに追加する必要があります。
- **Linux:** 検出する Linux サーバーのルート アカウントが必要です。

## <a name="prepare-for-physical-server-migration"></a>物理サーバーの移行を準備する

物理サーバーの移行の要件を確認します。

- 移行のための物理サーバーの要件を[確認](migrate-support-matrix-physical-migration.md#physical-server-requirements)します。
- Azure Migrate: サーバー移行では、物理サーバーの移行にレプリケーション サーバーを使用します。
    - レプリケーション アプライアンスのデプロイ要件と、アプライアンスでの MySQL のインストールの[オプション](migrate-replication-appliance.md#mysql-installation)を[確認](migrate-replication-appliance.md#appliance-requirements)します。
    - レプリケーション アプライアンスの [URL](migrate-replication-appliance.md#url-access) と[ポート] (migrate-replication-appliance.md#port-access) アクセス要件を確認します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure アカウントのアクセス許可を設定しました。
> * 物理サーバーの評価を準備する

次のチュートリアルに進み、Azure Migrate プロジェクトを作成し、Azure に移行するために物理サーバーを評価します。

> [!div class="nextstepaction"]
> [物理サーバーを評価する](./tutorial-assess-physical.md)
