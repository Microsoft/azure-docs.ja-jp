---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Ekarda の統合 | Microsoft Docs
description: Azure Active Directory と Ekarda の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: b1cf45fd57e159993cdb3a677c505911d013122e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544249"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Ekarda の統合

このチュートリアルでは、Ekarda と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Ekarda を統合すると、次のことができます。

* Ekarda にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Ekarda に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Ekarda でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Ekarda では、**SP と IDP** によって開始される SSO がサポートされます
* Ekarda では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

* Ekarda を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-ekarda-from-the-gallery"></a>ギャラリーからの Ekarda の追加

Azure AD への Ekarda の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ekarda を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Ekarda**」と入力します。
1. 結果のパネルから **[Ekarda]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Ekarda の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Ekarda に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Ekarda の関連ユーザーとの間にリンク関係を確立する必要があります。

Ekarda で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Ekarda SSO の構成](#configure-ekarda-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Ekarda テスト ユーザーの作成](#create-ekarda-test-user)** - Ekarda で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Ekarda** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。
    
    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、Ekarda セクションのテキスト ボックスに自動的に設定されます

    > [!Note]
    > **識別子**と**応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. **サービス プロバイダーのメタデータ ファイル**がなければ、 **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://my.ekarda.com/users/saml_acs/<COMPANY_ID>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[Ekarda クライアント サポート チーム](mailto:contact@ekarda.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[Set up Single Sign-On with SAML] (SAML でのシングル サインオンの設定)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして**証明書 (Base64)** をコピーし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Ekarda のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Ekarda へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Ekarda]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ekarda-sso"></a>Ekarda SSO の構成

1. 別の Web ブラウザー ウィンドウで、Ekarda 企業サイトに管理者としてサインインします。

1. **[管理]**  ->  **[マイ アカウント]** をクリックします。

    ![Ekarda の構成](./media/ekarda-tutorial/ekarda.png)    

1. ページの下部に、 **[SAML SETTINGS] (SAML の設定)** セクションが表示されます。ここで、その SAML 統合を構成します。
1. 次のページで、以下の手順を実行します。

    ![Ekarda の構成](./media/ekarda-tutorial/ekarda1.png)

    a. **[Service Provider metadata] (サービス プロバイダーのメタデータ)** リンクをクリックし、それをファイルとしてコンピューターに保存します。

    b. **[Enable SAML] (SAML の有効化)** チェック ボックスをオンにします。

    c. **[IDP Entity ID] (IDP エンティティ ID)** ボックスに、Azure portal からコピーした**エンティティ ID** の値を貼り付けます。

    d. **[IDP Login URL] (IDP ログイン URL)** テキスト ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    e. **[IDP Logout URL] (IDP ログアウト URL)** テキスト ボックスに、Azure portal からコピーした**ログアウト URL** の値を貼り付けます。

    f. Azure portal からダウンロードした**証明書 (Base64)** をメモ帳で開き、その内容を **[IDP x509 Certificate] (IDP x509 証明書)** ボックスに貼り付けます。

    g. **[オプション]** セクションで **[Enable SLO] (SLO を有効にする)** を選択します。

    h. **[更新]** をクリックします。

### <a name="create-ekarda-test-user"></a>Ekarda テスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Ekarda に作成します。 Ekarda では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Ekarda 内にまだユーザーが存在していない場合は、認証後に新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Ekarda] タイルをクリックすると、SSO を設定した Ekarda に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Ekarda を試す](https://aad.portal.azure.com/)

- [Ekarda のエンタープライズ eCard ソリューション](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise)を使用すると、会社のロゴが入った eCards を顧客や同僚に送信できるように、任意の数のスタッフをプロビジョニングできます。 SSO ソリューションとして Ekarda をプロビジョニングする方法の詳細については、[こちら](https://support.ekarda.com/#SSO-Implementation)を参照してください。

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Ekarda を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
