---
title: チュートリアル:Azure Active Directory と Wdesk の統合 | Microsoft Docs
description: Azure Active Directory と Wdesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: a4cfcf20fc7a6a3532f65c3e797da6c876844d2c
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985556"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Wdesk の統合

このチュートリアルでは、Wdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Wdesk を統合すると、次のことができます。

* Wdesk にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Wdesk に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Wdesk でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Wdesk では、**SP** と **IDP** によって開始される SSO がサポートされます
* Wdesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-wdesk-from-the-gallery"></a>ギャラリーからの Wdesk の追加

Azure AD への Wdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Wdesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Wdesk**」と入力します。
1. 結果のパネルから **[Wdesk]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、Wdesk で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと Wdesk 内の関連ユーザー間にリンク関係が確立されている必要があります。

Wdesk で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Wdesk の SSO の構成](#configure-wdesk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Wdesk のテスト ユーザーの作成](#create-wdesk-test-user)** - Wdesk で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

Wdesk で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **Wdesk** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![[Wdesk のドメインと URL] のシングル サインオン情報](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![[Wdesk のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://<subdomain>.wdesk.com/auth/login/saml/<instancename>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値は、SSO を構成するときに WDesk ポータルから得られます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Wdesk のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、Britta Simon に Wdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[Wdesk]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Wdesk]** を選択します。

    ![アプリケーションの一覧の Wdesk のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

## <a name="configure-wdesk-sso"></a>Wdesk の SSO の構成

1. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Wdesk にサインインします。

2. 左下の **[Admin]\(管理者\)** をクリックし、 **[Account Admin]\(アカウント管理者\)** を選びます。
 
     ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Wdesk 管理ツールで、 **[Security]\(セキュリティ\)** に移動した後、 **[SAML]**  >  **[SAML Settings]\(SAML の設定\)** の順に移動します。

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. **[General Settings]\(一般設定\)** で、 **[Enable SAML Single Sign On]\(SAML のシングル サインオンを有効にする\)** をオンにします。

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. **[Service Provider Details]\(サービス プロバイダーの詳細\)** で、以下の手順を実行します。

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. **[Login URL]\(ログイン URL\)** をコピーし、Azure Portal の **[サインオン URL]** ボックスに貼り付けます。
   
      b. **[Metadata Url]\(メタデータ URL\)** をコピーし、Azure Portal の **[識別子]** ボックスに貼り付けます。
       
      c. **[Consumer url]\(コンシューマー URL\)** をコピーし、Azure Portal の **[応答 URL]** ボックスに貼り付けます。
   
      d. Azure Portal の **[保存]** をクリックして、変更を保存します。      

6. **[Configure IdP Settings]\(IdP の設定の構成\)** をクリックして、 **[Edit IdP Settings]\(IdP の設定の編集\)** ダイアログを開きます。 **[Choose File]\(ファイルの選択\)** をクリックし、Azure Portal から保存した **Metadata.xml** ファイルを選択して、アップロードします。
    
    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. **[変更を保存]** をクリックします。

    ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Wdesk テスト ユーザーの作成

Azure AD ユーザーが Wdesk にサインインできるようにするには、ユーザーを Wdesk にプロビジョニングする必要があります。 Wdesk では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Wdesk にサインインします。

2. **[Admin]\(管理者\)**  >  **[Account Admin]\(アカウント管理者\)** の順に移動します。

     ![Configure single sign-on](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. **[People]\(ユーザー\)** の **[Members]\(メンバー\)** をクリックします。

4. **[Add Member]\(メンバーの追加\)** をクリックして、 **[Add Member]\(メンバーの追加\)** ダイアログ ボックスを開きます。 
   
    ![Azure AD のテスト ユーザーの作成](./media/wdesk-tutorial/createuser1.png)  

5. **[User]\(ユーザー\)** ボックスにユーザー名を入力し (例: brittasimon@contoso.com)、 **[Continue]\(続行\)** をクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/wdesk-tutorial/createuser3.png)

6.  次のように、詳細を入力します。
  
    ![Azure AD のテスト ユーザーの作成](./media/wdesk-tutorial/createuser4.png)
 
    a. **[E-mail]\(電子メール\)** ボックスに、ユーザーのメール アドレスを入力します (例: brittasimon@contoso.com)。

    b. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Britta**)。

    c. **[Last Name]\(姓\)** ボックスに、ユーザーの姓を入力します (例: **Simon**)。

7. **[Save Member]\(メンバーの保存\)** ボタンをクリックします。  

    ![Azure AD のテスト ユーザーの作成](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Wdesk] タイルをクリックすると、SSO を設定した Wdesk に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)