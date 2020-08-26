---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SD Elements の統合 | Microsoft Docs
description: Azure Active Directory と SD Elements の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 323d6324bc99028a3df4cb9cada133ad8cd990aa
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543263"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SD Elements の統合

このチュートリアルでは、SD Elements と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SD Elements を統合すると、次のことができます。

* SD Elements にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SD Elements に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SD Elements でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SD Elements では、**IDP** Initiated SSO がサポートされます

## <a name="adding-sd-elements-from-the-gallery"></a>ギャラリーから SD Elements を追加します。

Azure AD への SD Elements の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SD Elements を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SD Elements**」と入力します。
1. 結果のパネルから **[SD Elements]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>SD Elements の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SD Elements に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SD Elements の関連ユーザーとの間にリンク関係を確立する必要があります。

SD Elements で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SD Elements の SSO の構成](#configure-sd-elements-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[SD Elements のテスト ユーザーの作成](#create-sd-elements-test-user)** - SD Elements で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SD Elements** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<tenantname>.sdelements.com/sso/saml2/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<tenantname>.sdelements.com/sso/saml2/acs/` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[SD Elements クライアント サポート チーム](mailto:support@sdelements.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. SD Elements アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、SD Elements アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | --- | --- |
    | email |User.mail |
    | firstname |User.givenname |
    | lastname |User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SD Elements のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SD Elements へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SD Elements]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sd-elements-sso"></a>SD Elements の SSO の構成

1. シングル サインオンを有効にするには、 [SD Elements サポート チーム](mailto:support@sdelements.com) に連絡し、ダウンロードした証明書ファイルを提示します。

1. 別のブラウザー ウィンドウで、管理者として SD Elements テナントにサインオンします。

1. 上部のメニューで **[System]\(システム\)** をクリックし、 **[Single Sign-on]\(シングル サインオン\)** をクリックします。

    ![Configure single sign-on](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. **[Single Sign-On Settings]** ダイアログで、次の手順を実行します。

    ![Configure single sign-on](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. **SSO 型** として **SAML** を選びます。

    b. **[Identity Provider Entity ID]\(ID プロバイダーのエンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    c. **[Identity Provider Single Sign-On Service]\(ID プロバイダーのシングル サインオン サービス\)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    d. **[保存]** をクリックします。

### <a name="create-sd-elements-test-user"></a>SD Elements のテスト ユーザーの作成

このセクションの目的は、SD Elements で B.Simon というユーザーを作成することです。 SD Elements の場合、SD Elements ユーザーは手動で作成します。

**SD Elements で B.Simon を作成するには、次の手順に従います。**

1. Web ブラウザー ウィンドウで、管理者として SD Elements 企業サイトにサインオンします。

1. 上部のメニューで、 **[ユーザー管理]** 、 **[ユーザー]** の順にクリックします。

    ![SD Elements テスト ユーザーの作成](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. **[新しいユーザーの追加]** をクリックします。

    ![SD Elements テスト ユーザーの作成](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. **[新規ユーザーの追加]** ダイアログで、次の手順を実行します。

    ![SD Elements テスト ユーザーの作成](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. **[電子メール]** テキスト ボックスに、ユーザーの電子メール ( **b.simon@contoso.com** など) を入力します。

    b. **[First Name]\(名\)** テキストボックスに、ユーザーの名前 (名) を入力します (この例では **B.** )。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    d. **[ロール]** として **[ユーザー]** を選びます。

    e. **[Create User]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SD Elements] タイルをクリックすると、SSO を設定した SD Elements に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で SD Elements を試す](https://aad.portal.azure.com/)