---
title: 'チュートリアル: Azure Active Directory と Zscaler Two の統合 | Microsoft Docs'
description: Azure Active Directory と Zscaler Two の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2019
ms.author: jeedes
ms.openlocfilehash: c76e37e56adc9fc282b0c6b869db1a2ad8e7ebad
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545826"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>チュートリアル: Azure Active Directory と Zscaler Two の統合

このチュートリアルでは、Zscaler Two と Azure Active Directory (Azure AD) を統合する方法について説明します。
Zscaler Two と Azure AD の統合には、次の利点があります。

* Zscaler Two にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Zscaler Two に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Zscaler Two と Azure AD の統合を構成するには、次の項目が必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Zscaler Two でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Zscaler Two では、**SP** Initiated SSO がサポートされます

* Zscaler Two では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-zscaler-two-from-the-gallery"></a>ギャラリーからの Zscaler Two の追加

Azure AD への Zscaler Two の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler Two をギャラリーから追加する必要があります。

**ギャラリーから Zscaler Two を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zscaler Two**」と入力し、結果パネルで **[Zscaler Two]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの Zscaler Two](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Zscaler Two で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Zscaler Two 内の関連ユーザー間にリンク関係が確立されている必要があります。

Zscaler Two で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Zscaler Two のシングル サインオンの構成](#configure-zscaler-two-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Zscaler Two のテスト ユーザーの作成](#create-zscaler-two-test-user)** - Zscaler Two で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Zscaler Two で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Zscaler Two** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![Zscaler Two のドメインと URL のシングル サインオン情報](common/sp-signonurl.png)

    [サインオン URL] ボックスに、ユーザーが Zscaler Two アプリケーションへのサインオンに使用する URL を入力します。

    > [!NOTE]
    > 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Zscaler Two クライアント サポート チーム](https://www.zscaler.com/company/contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. Zscaler Two アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、 **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. その他に、Zscaler Two アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。
    
    | Name | ソース属性 |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** は空白のままにします。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。
    
    f. **[保存]** をクリックします。

    > [!NOTE]
    > Azure AD で役割を構成する方法については、[ここ](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management)をクリックしてください

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

8. **[Set up Zscaler Two]\(Zscaler Two のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-zscaler-two-single-sign-on"></a>Zscaler Two のシングル サインオンの構成

1. Zscaler Two 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **[My Apps Secure Sign-in browser extension]\(My Apps Secure Sign-in ブラウザー拡張機能\)** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Set up Zscaler Two]\(Zscaler Two のセットアップ\)** をクリックすると、Zscaler Two アプリケーションに移動します。 そこから、管理者資格情報を提供して Zscaler Two にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![SSO の設定](common/setup-sso.png)

3. Zscaler Two を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zscaler Two 企業サイトにサインインして、次の手順のようにします。

4. **[Administration]\(管理\) > [Authentication]\(認証\) > [Authentication Settings]\(認証の設定\)** に移動し、次の手順を実行します。
   
    ![管理](./media/zscaler-two-tutorial/ic800206.png "管理")

    a. [Authentication Type]\(認証の種類\)で **[SAML]** を選択します。

    b. **[Configure SAML]** をクリックします。

5. **[Edit SAML]\(SAML の編集)** ウィンドウで次の手順を実行して、[保存] をクリックします。  
            
    ![ユーザーと認証の管理](./media/zscaler-two-tutorial/ic800208.png "[ユーザーと認証の管理]")
    
    a. **[SAML Portal URL]\(SAML ポータル URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** を貼り付けます。

    b. **[Login Name Attribute]\(ログイン名属性\)** テキスト ボックスに **NameID** の値を入力します。

    c. **アップロード** をクリックして、Azure portal からダウンロードした Azure SAML 署名証明書を **Public SSL Certificate\(パブリック SSL 証明書\)** にアップロードします。

    d. **[SAML 自動プロビジョニングを有効にする]** を選択します。

    e. displayName 属性に SAML 自動プロビジョニングを有効にするには、 **[User Display Name Attribute]\(ユーザー表示名属性\)** テキスト ボックスに **displayName** の値を入力します。

    f. memberOf 属性に SAML 自動プロビジョニングを有効にするには、 **[Group Name Attribute]\(グループ名属性\)** テキスト ボックスに **memberOf** の値を入力します。

    g. department 属性に SAML 自動プロビジョニングを有効にするには、 **[Department Name Attribute]\(部署名属性\)** テキスト ボックスに **department** の値を入力します。

    h. **[保存]** をクリックします。

6. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![管理](./media/zscaler-two-tutorial/ic800207.png)

    a. 左下の **[Activation]\(アクティブ化\)** メニューにポインターを置きます。

    b. **[アクティブ化]** をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、 **[インターネット オプション]** ダイアログを開きます。   
    
     ![[インターネット オプション]](./media/zscaler-two-tutorial/ic769492.png "[インターネット オプション]")

3. **[接続]** タブをクリックします。   
  
     ![接続](./media/zscaler-two-tutorial/ic769493.png "接続")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![[プロキシ サーバー]](./media/zscaler-two-tutorial/ic769494.png "プロキシ サーバー")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] テキスト ボックスに「**gateway.Zscaler Two.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]** を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

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

このセクションでは、Britta Simon に Zscaler Two へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Zscaler Two]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler Two]** を選択します。

    ![アプリケーションの一覧の Zscaler Two のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの一覧から **[Britta Simon]** などのユーザーを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_users.png)

6. **[ロールの選択]** ダイアログで、一覧から適切なユーザー ロールを選択し、画面の下部にある **[選択]** ボタンをクリックします。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_roles.png)

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンを選択します。

    ![image](./media/zscaler-two-tutorial/tutorial_zscalertwo_assign.png)

### <a name="create-zscaler-two-test-user"></a>Zscaler Two のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Zscaler Two に作成します。 Zscaler Two では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Zscaler Two にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Zscaler Two のサポート チーム](https://www.zscaler.com/company/contact)にお問い合わせください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zscaler Two] タイルをクリックすると、SSO を設定した Zscaler Two に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

