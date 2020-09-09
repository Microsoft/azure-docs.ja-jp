---
title: 'チュートリアル: Azure Active Directory と Andromeda の統合 | Microsoft Docs'
description: Azure Active Directory と Andromeda の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 31fc1877dd0edd2fd670f0f9466692ac7bc8ad15
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88531125"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>チュートリアル: Azure Active Directory と Andromeda の統合

このチュートリアルでは、Andromeda と Azure Active Directory (Azure AD) を統合する方法について説明します。
Andromeda と Azure AD の統合には、次の利点があります。

* Andromeda にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Andromeda に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Andromeda と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Andromeda でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Andromeda では、**SP と IDP** によって開始される SSO がサポートされます
* Andromeda では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-andromeda-from-the-gallery"></a>ギャラリーからの Andromeda の追加

Azure AD への Andromeda の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Andromeda を追加する必要があります。

**ギャラリーから Andromeda を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Andromeda** 」と入力し、結果パネルで **[Andromeda ]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Andromeda](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Andromeda で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Andromeda 内の関連ユーザー間にリンク関係が確立されている必要があります。

Andromeda で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Andromeda シングル サインオンの構成](#configure-andromeda-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Andromeda のテスト ユーザーの作成](#create-andromeda-test-user)** - Andromeda で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Andromeda で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **Andromeda** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Andromeda のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<tenantURL>.ngcxpress.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Andromeda のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL に値を置き換えます。実際の値については後で説明します。

6. Andromeda アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

    > [!Important]
    > これらの設定中に名前空間の定義をクリアします。

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、**編集アイコン**を使用して要求を編集するか、 **[新しい要求の追加]** を使用して要求を追加することで、上の図のように SAML トークン属性を構成し、次の手順を実行します。 

    | Name | ソース属性|
    | ------ | -----------|
    | ロール (role)        | アプリ固有のロール |
    | type        | アプリの種類 |
    | company       | CompanyName |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値はデモ用なので、実際の組織のロールを使ってください。

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[保存]** をクリックします。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

9. **[Andromeda のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-andromeda-single-sign-on"></a>Andromeda シングル サインオンの構成

1. Andromeda 企業サイトに管理者としてサインオンします。

2. メニュー バーの上部にある **[Admin]\(管理者\)** をクリックし、 **[Administration]\(管理\)** に移動します。

    ![Andromeda 管理者](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. ツール バーの左側の **[Interfaces]\(インターフェイス\)** セクションで、 **[SAML Configuration]\(SAML の構成\)** をクリックします。

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. **[SAML Configuration]\(SAML の構成\)** セクション ページで、次の手順を実行します。

    ![Andromeda の構成](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. **[Enable SSO with SAML]\(SAML で SSO を有効にする\)** をオンにします。

    b. **[Andromeda Information]\(Andromeda 情報\)** セクションで、 **[SP Identity]\(SP ID\)** の値をコピーして、 **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    c. **[Consumer URL]\(コンシューマー URL\)** の値をコピーし、 **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    d. **[Logon URL]\(ログオン URL\)** の値をコピーし、 **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    e. **[SAML Identity Provider]\(SAML ID プロバイダー\)** セクションで、IDP 名を入力します。

    f. **[Single Sign On End Point]\(シングル サインオン エンドポイント\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    g. Azure Portal からダウンロードした **Base64 でエンコードされた証明書**をメモ帳で開き、 **[X.509 証明書]** ボックスに貼り付けます。
    
    h. 以下の属性を対応する値にマップし、Azure AD からの SSO ログインを容易にします。 **ユーザー ID** 属性はログインに必要です。 プロビジョニングの場合は、**電子メール**、**会社**、**ユーザーの種類**、**ロール**が必要です。 このセクションでは、Azure Portal 内で定義されている値に関連付ける属性マッピング (名前と値) を定義します。

    ![Andromeda 属性マップ](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Andromeda へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Andromeda]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Andromeda]** を選択します。

    ![アプリケーションの一覧の [Andromeda] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-andromeda-test-user"></a>Andromeda のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Andromeda に作成します。 Andromeda では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Andromeda にユーザーがまだ存在していない場合は、認証後に新しく作成されます。 ユーザーを手動で作成する必要がある場合は、[Andromeda クライアント サポート チーム](https://www.ngcsoftware.com/support/)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Andromeda] タイルをクリックすると、SSO を設定した Andromeda に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)