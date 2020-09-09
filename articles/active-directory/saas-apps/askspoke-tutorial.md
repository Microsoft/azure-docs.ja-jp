---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と askSpoke の統合 | Microsoft Docs
description: Azure Active Directory と askSpoke の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.openlocfilehash: 280dda4d3864f2c659272ffb63d6ac2566524c45
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549434"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askspoke"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と askSpoke の統合

このチュートリアルでは、askSpoke と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と askSpoke を統合すると、次のことができます。

* askSpoke にアクセスできるユーザーを Azure AD で制御できます。
* ご自分のユーザーが自分の Azure AD アカウントを使用して askSpoke に自動的にサインインするようにできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* askSpoke でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* askSpoke では、**SP Initiated SSO と IDP Initiated SSO** がサポートされています
* askSpoke では、**Just-In-Time** ユーザー プロビジョニングがサポートされています
* askSpoke を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-askspoke-from-the-gallery"></a>ギャラリーからの askSpoke の追加

Azure AD に askSpoke を統合するには、ギャラリーからご自分の管理対象 SaaS アプリの一覧に askSpoke を追加構成する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**askSpoke**」と入力します。
1. 結果のパネルから **[askSpoke]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-askspoke"></a>askSpoke の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、askSpoke に Azure AD SSO を構成してテストします。 SSO が機能するには、Azure AD ユーザーと askSpoke のその関連ユーザーとの間にリンク関係を確立する必要があります。

askSpoke で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[askSpoke SSO の構成](#configure-askspoke-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[askSpoke テスト ユーザーの作成](#create-askspoke-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを askSpoke で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **askSpoke** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.askspoke.com/saml/callback` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値は、[askSpoke のクライアント サポート チーム](mailto:support@askspoke.com)に問い合わせて取得してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに URL として「`https://askspoke.com/login`」と入力します。

1. askSpoke アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングをお使いの SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、askSpoke アプリケーションでは、いくつかの属性が SAML 応答で返されることを想定しています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------| --------- |
    | firstName | User.givenname |
    | lastName | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[askSpoke の設定]** セクションで、ご自分の要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に askSpoke へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[askSpoke]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-askspoke-sso"></a>askSpoke SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として askSpoke Web サイトにサインインします。

1. 左側のナビゲーション ウィンドウで **[設定]** タブをクリックします。

    ![askSpoke の構成](./media/askspoke-tutorial/configure1.png)

1. **[SSO]** までスクロールダウンし、 **[接続]** をクリックします。

    ![askSpoke の構成](./media/askspoke-tutorial/configure2.png)

1. **[Enable SAML & SCIM]** \(SAML および SCIM の有効化\) セクションで、次の手順を実行します。

    ![askSpoke の構成](./media/askspoke-tutorial/configure3.png)

    1. **[シングル サインオン URL]** テキストボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    1. **[発行者]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    1. Azure portal からダウンロードした**証明書 (Base64)** ファイルをメモ帳で開き、証明書ファイルの内容をコピーして **[公開証明書]** テキストボックスに貼り付けます。

    1. **[ACS URL]** \(ACS URL\) の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    1. **[Test SAML connection]** \(SAML 接続のテスト\) をクリックします。

### <a name="create-askspoke-test-user"></a>askSpoke テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを askSpoke に作成します。 askSpoke では、Just-In-Time プロビジョニングがサポートされており、これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ askSpoke に存在しない場合は、askSpoke にアクセスしたときに新しいユーザーが作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [askSpoke] タイルをクリックすると、SSO を設定した askSpoke に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で askSpoke を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって askSpoke を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

