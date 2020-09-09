---
title: チュートリアル:Azure Active Directory と GoToMeeting の統合 | Microsoft Docs
description: Azure Active Directory と GoToMeeting の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: b410c4a106f65c8913dc41f5957cdaf10cad3c89
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-gotomeeting"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と GoToMeeting の統合

このチュートリアルでは、GoToMeeting と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と GoToMeeting を統合すると、次のことができます。

* GoToMeeting にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して GoToMeeting に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* GoToMeeting でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* GoToMeeting では、**IDP** Initiated SSO がサポートされます。
* GoToMeeting を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-gotomeeting-from-the-gallery"></a>ギャラリーからの GoToMeeting の追加

Azure AD への GoToMeeting の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に GoToMeeting を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**GoToMeeting**」と入力します。
1. 結果のパネルから **[GoToMeeting]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-gotomeeting"></a>GoToMeeting の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、GoToMeeting に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと GoToMeeting の関連ユーザーとの間にリンク関係を確立する必要があります。

GoToMeeting で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[GoToMeeting の SSO の構成](#configure-gotomeeting-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[GoToMeeting テスト ユーザーの作成](#create-gotomeeting-test-user)** - GoToMeeting で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **GoToMeeting**  アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://authentication.logmeininc.com/saml/sp` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://authentication.logmeininc.com/saml/acs` のパターンを使用して URL を入力します

    c. **[追加の URL を設定します]** をクリックして、以下の URL を構成します

    d. **サインオン URL** (空白のまま)

    e. **[RelayState]** ボックスに、次のパターンを使用して URL を入力します。

   - GoToMeeting App の場合は、`https://global.gotomeeting.com` を使用します。

   - GoToTraining の場合は、`https://global.gototraining.com` を使用します。

   - GoToWebinar の場合は、`https://global.gotowebinar.com` を使用します。 

   - GoToAssist の場合は、`https://app.gotoassist.com` を使用します。

     > [!NOTE]
     > これらは実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[GoToMeeting クライアント サポート チーム](https://go.microsoft.com/fwlink/?linkid=845985)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[GoToMeeting のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL


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

このセクションでは、B.Simon に GoToMeeting へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[GoToMeeting]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-gotomeeting-sso"></a>GoToMeeting の SSO の構成

1. 別のブラウザー ウィンドウで、[GoToMeeting Organization Center](https://organization.logmeininc.com/) にログインします。 IdP が更新されていることを確認するように求められます。

2. [My Identity Provider has been updated with the new domain]\(マイ ID プロバイダーが新しいドメインで更新されました\) チェックボックスをオンにします。 終了したら、 **[完了]** をクリックします。

### <a name="create-gotomeeting-test-user"></a>GoToMeeting のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを GoToMeeting に作成します。 GoToMeeting では、Just-In-Time プロビジョニングがサポートされています。これは既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ GoToMeeting に存在しない場合は、GoToMeeting にアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[GoToMeeting サポート チーム](https://support.logmeininc.com/gotomeeting)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [GoToMeeting] タイルをクリックすると、SSO を設定した GoToMeeting に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で GoToMeeting を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって GoToMeeting を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
