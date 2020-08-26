---
title: 'チュートリアル: Azure Active Directory と PingBoard の統合 | Microsoft Docs'
description: Azure Active Directory と PingBoard の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 0e7b09c13cd27bd8197f6b65a1213d3154db6ac3
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553833"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>チュートリアル: Azure Active Directory と PingBoard の統合

このチュートリアルでは、PingBoard と Azure Active Directory (Azure AD) を統合する方法について説明します。
PingBoard と Azure AD の統合には、次の利点があります。

* Pingboard にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントで Pingboard に自動的にサインイン (シングル サインオン) できるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

PingBoard と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* Pingboard でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Pingboard では、**SP** と **IDP** によって開始される SSO がサポートされます

* Pingboard では、[自動化されたユーザー プロビジョニング](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)がサポートされます 

## <a name="adding-pingboard-from-the-gallery"></a>ギャラリーからの PingBoard の追加

Azure AD への PingBoard の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PingBoard を追加する必要があります。

**ギャラリーから PingBoard を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Pingboard**」と入力し、結果パネルで **[Pingboard]** を選択し、 **[追加]** をクリックしてアプリケーションを追加します。

     ![結果一覧の PingBoard](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Pingboard で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Pingboard 内の関連ユーザー間にリンク関係が確立されている必要があります。

PingBoard で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Pingboard のシングル サインオンの構成](#configure-pingboard-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Pingboard のテスト ユーザーの作成](#create-pingboard-test-user)** - Pingboard で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Pingboard で Azure AD シングル サインオンを構成するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com/) の **Pingboard** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Pingboard のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** テキスト ボックスに、`http://app.pingboard.com/sp` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<entity-id>.pingboard.com/auth/saml/consume` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Pingboard のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<sub-domain>.pingboard.com/sign_in` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[PingBoard クライアント サポート チーム](https://support.pingboard.com/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[Pingboard の設定]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-pingboard-single-sign-on"></a>Pingboard のシングル サインオンの構成

1. Pingboard 側で SSO を構成するには、新しいブラウザー ウィンドウを開き、Pingboard アカウントにサインインします。 シングル サインオンを設定するには、PingBoard 管理者である必要があります。

2. 上部のメニューで、 **[アプリ]、[統合]** の順に選択します。

    ![Configure single sign-on](./media/pingboard-tutorial/Pingboard_integration.png)

3. **[Integrations]** ページで、 **[Azure Active Directory]** タイルを探してクリックします。

    ![PingBoard シングル サインオン統合](./media/pingboard-tutorial/Pingboard_aad.png)

4. 続いて表示されるモーダルで、 **[Configure]** をクリックします。

    ![PingBoard 構成ボタン](./media/pingboard-tutorial/Pingboard_configure.png)

5. 次のページに、"Azure SSO 統合が有効になっている" ことを示す通知が表示されます。 ダウンロードしたメタデータ XML ファイルをメモ帳で開き、コンテンツを **[IDP Metadata]** に貼り付けます。

    ![PingBoard SSO 構成画面](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. ファイルが検証されます。すべて正しい場合は、シングル サインオンが有効になります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「brittasimon@yourcompanydomain.extension」と入力します。 たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PingBoard へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Pingboard]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[PingBoard]** を選択します。

    ![アプリケーションの一覧の PingBoard のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-pingboard-test-user"></a>Pingboard テスト ユーザーの作成

このセクションの目的は、Pingboard に Bitta Simon というユーザーを作成することです。 Pingboard では、　自動ユーザー プロビジョニングがサポートされています。この設定は、規定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](pingboard-provisioning-tutorial.md)をご覧ください。

**ユーザーを手動で作成する必要がある場合は、次の手順を実行します:**

1. Pingboard 企業サイトに管理者としてサインインします。

2. **[Directory]** ページで **[Add Employee]** をクリックします。

    ![従業員の追加](./media/pingboard-tutorial/create_testuser_add.png)

3. **[従業員の追加]** ダイアログ ページで、次の手順に従います。

    ![[ユーザーの招待]](./media/pingboard-tutorial/create_testuser_name.png)

    a. **[フル ネーム]** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。

    b. **[メール]** ボックスに、ユーザーのメール アドレス ( **brittasimon@contoso.com** など) を入力します。

    c. **[Job Title]** ボックスに、Britta Simon の役職を入力します。

    d. **[Location]** ドロップダウン リストで、Britta Simon の場所を選択します。

    e. **[追加]** をクリックします。

4. ユーザーの追加を確認するための確認画面が表示されます。

    ![確認](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Pingboard] タイルをクリックすると、SSO を設定した Pingboard に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
