---
title: Windows Virtual Desktop でテナントを作成する - Azure
description: Windows Virtual Desktop のテナントを Azure Active Directory に設定する方法を説明します。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/06/2019
ms.author: helohr
ms.openlocfilehash: 04a65442e86168239f08fb71303b8d9e9e152e72
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679467"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>チュートリアル:Windows Virtual Desktop でテナントを作成する

Windows Virtual Desktop でのテナントの作成は、デスクトップ仮想ソリューションを構築するための最初の手順です。 テナントは、1 つまたは複数のホスト プールのグループです。 各ホスト プールは複数のセッション ホストで構成されていて、Azure において仮想マシンとして動作し、Windows Virtual Desktop サービスに登録されます。 各ホスト プールはさらに、リモート デスクトップおよびリモート アプリケーションのリソースをユーザーに発行するために使用される、1 つ以上のアプリ グループで構成されます。 テナントを使用すると、ホスト プールの構築、アプリ グループの作成、ユーザーの割り合て、サービスを通じた接続の作成を行うことができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory のアクセス許可を Windows Virtual Desktop サービスに付与する。
> * Azure Active Directory テナント内のユーザーに TenantCreator アプリケーション ロールを割り当てる。
> * Windows Virtual Desktop テナントを作成する。

以下に、Windows Virtual Desktop テナントの設定に必要なものを示します。

* Windows Virtual Desktop ユーザー用の [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) テナント ID。
* Azure Active Directory テナント内のグローバル管理者アカウント。
   * これは、顧客に Windows Virtual Desktop テナントを作成するクラウド ソリューション プロバイダー (CSP) 組織にも適用されます。 CSP 組織に所属する場合、顧客の Azure Active Directory インスタンスのグローバル管理者としてサインインできる必要があります。
   * 管理者アカウントは、Windows Virtual Desktop テナントを作成する Azure Active Directory テナントから提供する必要があります。 このプロセスは、Azure Active Directory B2B (ゲスト) アカウントをサポートしません。
   * 管理者アカウントは、職場または学校アカウントである必要があります。
* Azure サブスクリプション。

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Windows Virtual Desktop へのアクセス許可を付与する

この Azure Active Directory インスタンスのアクセス許可を Windows Virtual Desktop に既に付与している場合、このセクションはスキップしてください。

Windows Virtual Desktop サービスにアクセス許可を付与すると、Azure Active Directory に対して、管理タスクおよびエンドユーザー タスクに関するクエリを実行できます。

サービスにアクセス許可を付与するには:

1. ブラウザーを開き、[Windows Virtual Desktop サーバー アプリ](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)に対する管理者の同意フローを開始します。
   > [!NOTE]
   > 顧客を管理していて、顧客のディレクトリに管理者の同意を付与する必要がある場合は、ブラウザーに次の URL を入力し、{tenant} を顧客の Azure AD ドメイン名に置き換えます。 たとえば、顧客の組織が Azure AD のドメイン名 contoso.onmicrosoft.com を登録している場合は、{tenant} を contoso.onmicrosoft.com に置き換えます。
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. グローバル管理者アカウントを使用して Windows Virtual Desktop の同意ページにサインインします。 たとえば、Contoso 組織に属しているとしたら、アカウントは admin@contoso.com や admin@contoso.onmicrosoft.com になるでしょう。
3. **[Accept]\(承認\)** を選択します。
4. Azure AD が同意を記録できるように 1 分間待ちます。
5. ブラウザーを開き、[Windows Virtual Desktop クライアント アプリ](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback)に対する管理者の同意フローを開始します。
   >[!NOTE]
   > 顧客を管理していて、顧客のディレクトリに管理者の同意を付与する必要がある場合は、ブラウザーに次の URL を入力し、{tenant} を顧客の Azure AD ドメイン名に置き換えます。 たとえば、顧客の組織が Azure AD のドメイン名 contoso.onmicrosoft.com を登録している場合は、{tenant} を contoso.onmicrosoft.com に置き換えます。
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. 手順 2 で行ったように、グローバル管理者として Windows Virtual Desktop の同意ページにサインインします。
7. **[Accept]\(承認\)** を選択します。

## <a name="assign-the-tenantcreator-application-role"></a>TenantCreator アプリケーション ロールを割り当てる

Azure Active Directory ユーザーに TenantCreator アプリケーション ロールを割り当てると、そのユーザーは、Azure Active Directory インスタンスに関連付けられた Windows Virtual Desktop テナントを作成できます。 TenantCreator ロールを割り当てるには、グローバル管理者アカウントを使用する必要があります。

TenantCreator アプリケーション ロールを割り当てるには:

1. ブラウザーを開き、グローバル管理者アカウントを使用して [Azure portal](https://portal.azure.com) に接続します。
   
   複数の Azure Active Directory テナントを操作する場合は、プライベート ブラウザー セッションを開き、URL をコピーしてアドレス バーに貼り付けるのがベスト プラクティスです。
2. Azure portal の検索バーで「**エンタープライズ アプリケーション**」を検索し、 **[サービス]** カテゴリの下に表示されるエントリを選択します。
3. **[エンタープライズ アプリケーション]** 内で「**Windows Virtual Desktop**」を検索します。 前のセクションで同意した 2 つのアプリケーションが表示されます。 これらの 2 つのアプリについて、 **[Windows Virtual Desktop]** を選択します。
   ![「エンタープライズ アプリケーション」で "Windows Virtual Desktop" を検索したときの検索結果を示すスクリーンショット。 "Windows Virtual Desktop" という名前のアプリが強調表示されています。](media/tenant-enterprise-app.png)
4. **[ユーザーとグループ]** を選択します。 アプリケーションへの同意を付与した管理者が、**既定のアクセス** ロールを割り当てられた状態で既に表示されている場合があります。 これだけでは Windows Virtual Desktop テナントを作成するのに不十分です。 以降の手順に従って、**TenantCreator** ロールをユーザーに付加します。
   !["Windows Virtual Desktop" エンタープライズ アプリケーションを管理するために割り当てられたユーザーとグループのスクリーンショット。 このスクリーンショットには、"既定のアクセス" の割り当てのみが示されています。](media/tenant-default-access.png)
5. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ブレードで **[ユーザーとグループ]** を選択します。
6. 自分の Windows Virtual Desktop テナントを作成するユーザー アカウントを検索します。 わかりやすいように、これはグローバル管理者アカウントにできます。
   - Microsoft の ID プロバイダー (contosoadmin@live.com、contosoadmin@outlook.com など) を使用している場合、Windows Virtual Desktop にサインインできない場合があります。 代わりにドメイン固有のアカウントを使用することをお勧めします (admin@contoso.com、admin@contoso.onmicrosoft.com など)。

   !["TenantCreator" として追加するユーザーを選択しているスクリーンショット。](media/tenant-assign-user.png)

   > [!NOTE]
   > この Azure Active Directory インスタンスに属しているユーザー (またはユーザーを含むグループ) を選択する必要があります。 ゲスト (B2B) ユーザーまたはサービス プリンシパルを選択することはできません。

7. ユーザー アカウントを選択し、 **[選択]** ボタンを選択し、 **[割り当て]** を選択します。
8. **[Windows Virtual Desktop - ユーザーとグループ]** ページで、Windows Virtual Desktop テナントを作成するユーザーに **TenantCreator** ロールが割り当てられた新しいエントリが表示されていることを確認します。
   !["Windows Virtual Desktop" エンタープライズ アプリケーションを管理するために割り当てられたユーザーとグループのスクリーンショット。 このスクリーンショットには、"TenantCreator" ロールに割り当てられたユーザーを表す 2 つ目のエントリが追加されています。](media/tenant-tenant-creator-added.png)

Windows Virtual Desktop テナントの作成を続行する前に、2 つの情報を入手する必要があります。
- Azure Active Directory のテナント ID (**ディレクトリ ID**)
- Azure サブスクリプション ID

Azure Active Directory のテナント ID (**ディレクトリ ID**) を検索するには:
1. 同じ Azure portal セッションで、検索バーから「**Azure Active Directory**」を検索し、 **[サービス]** カテゴリの下に表示されるエントリを選択します。
   ![Azure portal で "Azure Active Directory" を検索した結果のスクリーンショット。 [サービス] の下の検索結果が強調表示されています。](media/tenant-search-azure-active-directory.png)
2. 下にスクロールし、 **[プロパティ]** を選択します。
3. **[ディレクトリ ID]** を探し、クリップボード アイコンを選択します。 この値を後で **AadTenantId** 値として使用できるように便利な場所に貼り付けます。
   ![Azure Active Directory のプロパティのスクリーンショット。 "ディレクトリ ID" をコピーして貼り付けるために、クリップボード アイコンにマウス ポインターを合わせています。](media/tenant-directory-id.png)

Azure サブスクリプション ID を検索するには:
1. 同じ Azure portal セッションで、検索バーから「**サブスクリプション**」を検索し、 **[サービス]** カテゴリの下に表示されるエントリを選択します。
   ![Azure portal で "Azure Active Directory" を検索した結果のスクリーンショット。 [サービス] の下の検索結果が強調表示されています。](media/tenant-search-subscription.png)
2. Windows Virtual Desktop サービスの通知を受け取るために使用する Azure サブスクリプションを選択します。
3. **サブスクリプション ID** を探し、その値にマウス ポインターを合わせてクリップボード アイコンを表示します。 クリップボード アイコンを選択し、この値を後で **AzureSubscriptionId** 値として使用できるように便利な場所に貼り付けます。
   ![Azure サブスクリプションのプロパティのスクリーンショット。 "サブスクリプション ID" をコピーして貼り付けるために、クリップボード アイコンにマウス ポインターを合わせています。](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Windows Virtual Desktop テナントを作成する

これで、Azure Active Directory に対してクエリを実行するアクセス許可を Windows Virtual Desktop サービスに付与し、TenantCreator ロールをユーザー アカウントに割り当てたため、Windows Virtual Desktop テナントを作成できます。

まず、まだ行っていない場合は、PowerShell セッションで使用する [Windows Virtual Desktop モジュールをダウンロードしてインポートします](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)。

次のコマンドレットと共に TenantCreator ユーザー アカウントを使用して、Windows Virtual Desktop にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

その後、Azure Active Directory テナントに関連付けられた新しい Windows Virtual Desktop テナントを作成します。

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

かっこで囲まれた値は、実際の組織およびテナントに関連する値に置き換えてください。 新しい Windows Virtual Desktop テナントに対して選択する名前は、グローバルに一意である必要があります。 たとえば、お客様が Contoso 組織の Windows Virtual Desktop TenantCreator であるとします。 実行するコマンドレットは次のようになります。

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>次の手順

テナントを作成した後、Azure Active Directory でサービス プリンシパルを作成し、Windows Virtual Desktop 内でそれにロールを割り当てる必要があります。 サービス プリンシパルを使用することで、Azure Marketplace オファリングである Windows Virtual Desktop を正常にデプロイしてホスト プールを作成できます。 ホスト プールについて詳しく確認するために、Windows Virtual Desktop でホスト プールを作成するためのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する](./create-service-principal-role-powershell.md)
