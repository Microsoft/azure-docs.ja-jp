---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic (限定的リリース) の統合 | Microsoft Docs
description: Azure Active Directory と New Relic (限定的リリース) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ffbcf4a-b6b4-45ac-922f-358abe4c05e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad229e7964b6e7ad0e3f0d58cfc09669ee2fb301
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088266"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-limited-release"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と New Relic (限定的リリース) の統合

このチュートリアルでは、New Relic (限定的リリース) と Azure Active Directory (Azure AD) を統合する方法について説明します。 New Relic (限定的リリース) を Azure AD と統合すると、次のことができます。

* New Relic (限定的リリース) にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して自動的に New Relic (限定的リリース) にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* New Relic (限定的リリース) でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* 新しい New Relic (限定的リリース) では、**IDP** によって開始される SSO がサポートされます

* New Relic を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-new-relic-limited-release-from-the-gallery"></a>ギャラリーからの New Relic (限定的リリース) の追加

Azure AD への New Relic (限定的リリース) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に New Relic (限定的リリース) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**New Relic (限定的リリース)** 」と入力します。
1. 結果パネルから **[New Relic (限定的リリース)]** を選択し、アプリを追加 します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-new-relic-limited-release"></a>New Relic (限定的リリース) の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、New Relic (限定的リリース) に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと New Relic (限定的リリース) の関連ユーザーとの間にリンク関係を確立する必要があります。

New Relic (限定的リリース) で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[New Relic (限定的リリース) SSO の構成](#configure-new-relic-limited-release-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[New Relic (限定的リリース) テスト ユーザーの作成](#create-new-relic-limited-release-test-user)** - New Relic (限定的リリース) で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **New Relic (限定的リリース)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル**がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image](common/upload-metadata.png)

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![image](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子**と**応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![image](common/idp-intiated.png)

    > [!Note]
    > **識別子**と**返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に New Relic (限定的リリース) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[New Relic (限定的リリース)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-new-relic-limited-release-sso"></a>New Relic (限定的リリース) SSO を構成する

**New Relic (限定的リリース)** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [New Relic (限定的リリース) サポート チーム](https://support.newrelic.com/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。


### <a name="create-new-relic-limited-release-test-user"></a>New Relic (限定的リリース) テストユーザーを作成する

このセクションでは、New Relic (限定的リリース) で B.Simon というユーザーを作成します。  [New Relic (限定的リリース) サポート チーム](https://support.newrelic.com/) と連携して、New Relic (限定的リリース) プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [New Relic (限定的リリース)] タイルをクリックすると、SSO を設定した New Relic (限定的リリース) に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で New Relic (限定的リリース) を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)