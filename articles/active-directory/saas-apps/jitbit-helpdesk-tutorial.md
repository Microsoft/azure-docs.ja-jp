---
title: チュートリアル:Azure Active Directory と Jitbit Helpdesk の統合 | Microsoft Docs
description: Azure Active Directory と Jitbit Helpdesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 5d4900713cd8d96180bf74b300a738a8b676421e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547156"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>チュートリアル:Azure Active Directory と Jitbit Helpdesk の統合

このチュートリアルでは、Jitbit Helpdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。
Jitbit Helpdesk と Azure AD の統合には、次の利点があります。

* Jitbit Helpdesk にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Jitbit Helpdesk に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Jitbit Helpdesk と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Jitbit Helpdesk でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Jitbit Helpdesk では、**SP** によって開始される SSO がサポートされます

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>ギャラリーからの Jitbit Helpdesk の追加

Azure AD への Jitbit Helpdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jitbit Helpdesk を追加する必要があります。

**ギャラリーから Jitbit Helpdesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Jitbit Helpdesk**」と入力し、結果パネルで **[Jitbit Helpdesk]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果リストの Jitbit Helpdesk](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Jitbit Helpdesk で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Jitbit Helpdesk 内の関連ユーザー間にリンク関係が確立されている必要があります。

Jitbit Helpdesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Jitbit Helpdesk のシングル サインオンの構成](#configure-jitbit-helpdesk-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Jitbit Helpdesk テスト ユーザーの作成](#create-jitbit-helpdesk-test-user)** - Jitbit Helpdesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Jitbit Helpdesk で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **Jitbit Helpdesk** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[Jitbit Helpdesk のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次の形式で URL を入力します。
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Jitbit Helpdesk クライアント サポート チーム](https://www.jitbit.com/support/)にお問い合わせください。

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://www.jitbit.com/web-helpdesk/` という URL を入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Jitbit Helpdesk の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Jitbit Helpdesk のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、Jitbit Helpdesk の企業サイトに管理者としてサインインします。

1. 上部のツールバーで **[管理]** をクリックします。

    ![管理](./media/jitbit-helpdesk-tutorial/ic777681.png "管理")

1. **[全般設定]** をクリックします。

    ![[Users, companies, and permissions]\(ユーザー、会社、およびアクセス許可\)](./media/jitbit-helpdesk-tutorial/ic777680.png "ユーザー、会社、およびアクセス許可")

1. **[認証設定]** 構成セクションで、次の手順を実行します。

    ![[Authentication settings]\(認証設定\)](./media/jitbit-helpdesk-tutorial/ic777683.png "[認証設定]")

    a. **[Enable SAML 2.0 single sign on]\(SAML 2.0 シングル サインオンを有効にする\)** を選択して、**OneLogin** でシングル サインオン (SSO) を使用してサインインします。

    b. **[EndPoint URL]\(EndPoint の URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** ボックスに貼り付けます。

    d. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Jitbit Helpdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Jitbit Helpdesk]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Jitbit Helpdesk]** を選択します。

    ![アプリケーションの一覧の [Jitbit Helpdesk] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit Helpdesk テスト ユーザーの作成

Azure AD ユーザーが Jitbit Helpdesk にサインインできるようにするには、そのユーザーを Jitbit Helpdesk にプロビジョニングする必要があります。 Jitbit Helpdesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Jitbit Helpdesk** テナントにサインインします。

1. 上部のメニューで **[管理]** をクリックします。

    ![管理](./media/jitbit-helpdesk-tutorial/ic777681.png "管理")

1. **[ユーザー、会社、およびアクセス許可]** をクリックします。

    ![[Users, companies, and permissions]\(ユーザー、会社、およびアクセス許可\)](./media/jitbit-helpdesk-tutorial/ic777682.png "ユーザー、会社、およびアクセス許可")

1. **[ユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/jitbit-helpdesk-tutorial/ic777685.png "ユーザーの追加")

1. [作成] セクションで、プロビジョニングする Azure AD アカウントのデータを次のように入力します。

    ![作成](./media/jitbit-helpdesk-tutorial/ic777686.png "作成")

   a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのユーザー名を入力します (この例では **BrittaSimon**)。

   b. **[電子メール]** ボックスに、ユーザーの電子メール ( **BrittaSimon@contoso.com** など) を入力します。

   c. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

   d. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

   e. **Create** をクリックしてください。

> [!NOTE]
> Jitbit Helpdesk から提供されている他の Jitbit Helpdesk ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Jitbit Helpdesk] タイルをクリックすると、SSO を設定した Jitbit Helpdesk に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
