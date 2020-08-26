---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workteam の統合 | Microsoft Docs
description: Azure Active Directory と Workteam の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.openlocfilehash: c95fceaa90ca379519379d37c19fb15dcd1de321
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546358"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workteam の統合

このチュートリアルでは、Workteam と Azure Active Directory (Azure AD) を統合する方法について説明します。 Workteam を Azure AD と統合すると、次のことができます。

* Workteam にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Workteam にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workteam でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Workteam では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-workteam-from-the-gallery"></a>ギャラリーからの Workteam の追加

Azure AD への Workteam の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Workteam を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workteam**」と入力します。
1. 結果のパネルから **[Workteam]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Workteam の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workteam に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Workteam の関連ユーザーとの間にリンク関係を確立する必要があります。

Workteam で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Workteam の SSO の構成](#configure-workteam-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Workteam テスト ユーザーの作成](#create-workteam-test-user)** - Workteam で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Workteam** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://app.workte.am`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Workteam のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Workteam へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workteam]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-workteam-sso"></a>Workteam の SSO の構成

1. Workteam 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護された Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Workteam のセットアップ]** をクリックすると、Workteam アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Workteam にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Workteam を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Workteam 企業サイトにサインインして、次の手順を実行します。

4. 右上隅にある**プロファイル ロゴ**をクリックし、 **[Organization settings]\(組織の設定\)** をクリックします。 

    ![Workteam の設定](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. **[AUTHENTICATION]\(認証\)** セクションで、**設定ロゴ**をクリックします。

     ![Workteam - Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. **[SAML Settings]** ページで、次の手順を実行します。

     ![Workteam - SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. **[SAML IdP]** で **[AD Azure]** を選択します。

    b. **[SAML Single Sign-On Service URL]\(SAML シングル サインオン サービス URL\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[SAML Entity ID]\(SAML エンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    d. Azure portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をコピーして **[SAML Signing Certificate (Base64)]\(SAML 署名証明書 (Base64)\)** ボックスに貼り付けます。

    e. **[OK]** をクリックします。

### <a name="create-workteam-test-user"></a>Workteam のテスト ユーザーの作成

Azure AD ユーザーが Workteam にサインインできるようにするには、そのユーザーを Workteam にプロビジョニングする必要があります。 Workteam では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Workteam にサインインします。

2. **[Organization settings]\(組織の設定\)** ページで、上部中央の **[USERS]\(ユーザー\)** をクリックし、 **[NEW USER]\(新しいユーザー\)** をクリックします。

    ![Workteam - ユーザー](./media/workteam-tutorial/tutorial_workteam_user.png)

3. **[New employee]\(新しい従業員\)** ページで、次の手順を実行します。

    ![Workteam の新しいユーザー](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. **[Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **B.Simon**)。

    b. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `B.Simon\@contoso.com`)。

    c. **[OK]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workteam] タイルをクリックすると、SSO を設定した Workteam に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD を使用して Workteam を試す](https://aad.portal.azure.com/)

