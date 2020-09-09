---
title: 'チュートリアル: Azure Active Directory と Picturepark の統合 | Microsoft Docs'
description: Azure Active Directory と Picturepark の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: bb12b666315f6d30df64b33b6a87d6ce831fd225
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553852"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>チュートリアル: Azure Active Directory と Picturepark の統合

このチュートリアルでは、Picturepark と Azure Active Directory (Azure AD) を統合する方法について説明します。
Picturepark と Azure AD の統合には、次の利点があります。

* Picturepark にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Picturepark に自動的にサインイン (シングル サインオン) できるようにすることが可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Picturepark と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* Picturepark でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Picturepark では、**SP** によって開始される SSO がサポートされます

## <a name="adding-picturepark-from-the-gallery"></a>ギャラリーからの Picturepark の追加

Azure AD への Picturepark の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Picturepark を追加する必要があります。

**ギャラリーから Picturepark を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Picturepark**」と入力し、結果パネルで **Picturepark** を選び、 **[追加]** ボタンをクリックして、アプリケーションを追加します。

     ![結果リストの Picturepark](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Picturepark で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Picturepark 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

Picturepark で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Picturepark のシングル サインオンの構成](#configure-picturepark-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Picturepark テスト ユーザーの作成](#create-picturepark-test-user)** - Picturepark で Britta Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Picturepark で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Picturepark** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Picturepark のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.picturepark.com`

    b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Picturepark クライアント サポート チーム](https://picturepark.com/company/picturepark-customer-support)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[Thumbprint]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[Picturepark のセットアップ]** セクションで、要件に従って適切な URL をコピーします。 **[ログイン URL]** には、次のパターンの値を使用します: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ は Azure AD サブスクリプションのテナント ID です。

    ![構成 URL のコピー](./media/picturepark-tutorial/configurls.png)

    a. Azure AD 識別子

    b. ログアウト URL

### <a name="configure-picturepark-single-sign-on"></a>Picturepark のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、Picturepark 企業サイトに管理者としてサインインします。

2. 上部にあるツールバーで **[Administrative tools]** 、 **[Management Console]** の順にクリックします。
   
    ![[Management Console]](./media/picturepark-tutorial/ic795062.png "[Management Console]")

3. **[Authentication]** 、 **[Identity providers]** の順にクリックします。
   
    ![認証](./media/picturepark-tutorial/ic795063.png "認証")

4. **[Identity provider configuration]** セクションで、次の手順に従います。
   
    ![[Identity provider configuration]](./media/picturepark-tutorial/ic795064.png "[Identity provider configuration]")
   
    a. **[追加]** をクリックします。
  
    b. 構成の名前を入力します。
   
    c. **[Set as default]** を選びます。
   
    d. **[発行者 URI]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。
   
    e. **[Trusted Issuer Thumb Print]\(信頼された発行者の拇印\)** ボックスに、**[SAML 署名証明書]** セクションからコピーした**拇印**の値を貼り付けます。 

5. **[JoinDefaultUsersGroup]** をクリックします。

6. **[Claim]\(要求\)** ボックスで **[Emailaddress]\(電子メール アドレス\)** 属性を設定するには、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力して **[Save]\(保存\)** をクリックします。

      ![構成](./media/picturepark-tutorial/ic795065.png "構成")

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

このセクションでは、Britta Simon に Picturepark へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Picturepark]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Picturepark]** を選択します。

    ![アプリケーションの一覧の [Picturepark] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-picturepark-test-user"></a>Picturepark テスト ユーザーの作成

Azure AD ユーザーが Picturepark にサインインできるようにするには、ユーザーを Picturepark にプロビジョニングする必要があります。 Picturepark の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Picturepark** テナントにサインインします。

1. 上部にあるツールバーで **[Administrative tools]** 、 **[Users]** の順にクリックします。
   
    ![ユーザー](./media/picturepark-tutorial/ic795067.png "ユーザー")

1. **[Users overview]** タブで、 **[New]** をクリックします。
   
    ![[User management]](./media/picturepark-tutorial/ic795068.png "[ユーザー管理]")

1. **[Create User]\(ユーザーの作成\)** ダイアログで、プロビジョニングする有効な Azure Active Directory ユーザーを次の手順で設定します。
   
    ![ユーザーの作成](./media/picturepark-tutorial/ic795069.png "[Create User]")
   
    a. **[Email Address]\(電子メール アドレス\)** テキストボックスに、ユーザーの**電子メール アドレス**を「`BrittaSimon@contoso.com`」と入力します。  
   
    b. **[Password]\(パスワード\)** および **[Confirm Password]\(確認パスワード\)** ボックスに、BrittaSimon の**パスワード**を入力します。 
   
    c. **[First Name]\(名\)** ボックスに、ユーザーの**名**を「**Britta**」と入力します。 
   
    d. **[Last Name]\(姓\)** ボックスに、ユーザーの**姓**を「**Simon**」と入力します。
   
    e. **[Company]\(会社\)** ボックスに、ユーザーの**会社名**を入力します。 
   
    f. **[Country]\(国\)** テキストボックスに、ユーザーの**国/リージョン**を入力します。
  
    g. **[ZIP]\(郵便番号\)** ボックスに、ユーザーの**郵便番号**を入力します。
   
    h. **[City]\(市区町村\)** ボックスに、ユーザーの**市区町村名**を入力します。

    i. **[Language]** を選択します。
   
    j. **Create** をクリックしてください。

>[!NOTE]
>Picturepark から提供されている他の Picturepark ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。
>

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Picturepark] タイルをクリックすると、SSO を設定した Picturepark に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

