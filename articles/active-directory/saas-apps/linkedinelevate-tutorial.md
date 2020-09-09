---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Elevate の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Elevate の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 1f4569a45b9ed0eee7c375e660df97925335313b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549806"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Elevate の統合

このチュートリアルでは、LinkedIn Elevate と Azure Active Directory (Azure AD) を統合する方法について説明します。 LinkedIn Elevate を Azure AD と統合すると、次のことができます。

* LinkedIn Elevate にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LinkedIn Elevate に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LinkedIn Elevate でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。



* LinkedIn Elevate では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* LinkedIn Elevate では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* LinkedIn Elevate では、[**自動化された**ユーザー プロビジョニング](linkedinelevate-provisioning-tutorial.md)がサポートされます

## <a name="adding-linkedin-elevate-from-the-gallery"></a>ギャラリーからの LinkedIn Elevate の追加

Azure AD への LinkedIn Elevate の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Elevate を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LinkedIn Elevate**」と入力します。
1. 結果のパネルから **[LinkedIn Elevate]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>LinkedIn Elevate の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、LinkedIn Elevate に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LinkedIn Elevate の関連ユーザーとの間にリンク関係を確立する必要があります。

LinkedIn Elevate で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LinkedIn Elevate の SSO の構成](#configure-linkedin-elevate-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LinkedIn Elevate のテスト ユーザーの作成](#create-linkedin-elevate-test-user)** - LinkedIn Elevate で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **LinkedIn Elevate** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、**エンティティ ID** の値を入力します。エンティティ ID の値は、このチュートリアルで後述する Linkedin Portal からコピーします。

    b. **[応答 URL]** ボックスに、**Assertion Consumer Access (ACS) URL** の値を入力します。Assertion Consumer Access (ACS) URL の値は、このチュートリアルで後述する LinkedIn Portal からコピーします。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` という形式で URL を入力します。

1. LinkedIn Elevate アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 LinkedIn Elevate アプリケーションでは、nameidentifier が **user.mail** にマップされると想定されているため、[編集] アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. その他に、LinkedIn Elevate アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | -------| -------------|
    | department | user.department |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[LinkedIn Elevate のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に LinkedIn Elevate へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LinkedIn Elevate]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-linkedin-elevate-sso"></a>LinkedIn Elevate の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として LinkedIn Elevate テナントにサインオンします。

1. **[Account Center (アカウント センター)]** で、 **[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 また、ドロップダウン リストから **[Elevate - Elevate AAD Test (Elevate - Elevate AAD テスト)]** を選択します。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックして、次の手順を実行します。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. **[Entity ID]\(エンティティ ID\)** をコピーして、Azure portal の **[基本的な SAML 構成]** にある **[識別子]** ボックスに貼り付けます。

    b. **Assertion Consumer Access (ACS) URL** をコピーし、Azure portal で **[基本的な SAML 構成]** の **[応答 URL]** ボックスに貼り付けます。

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 [Upload XML file]\(XML ファイルのアップロード\) オプションをクリックして、Azure portal からダウンロードした XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[未接続]** から **[接続済み]** に変更されます

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>LinkedIn Elevate のテスト ユーザーの作成

LinkedIn Elevate アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされています。認証後に、ユーザーが自動的に、アプリケーションに作成されます。 LinkedIn Elevate ポータルの管理者設定ページで、スイッチ **[Automatically Assign licenses (ライセンスを自動的に割り当てる)]** を切り替えて、ジャストインタイム プロビジョニングを有効にします。これにより、ユーザーにライセンスも割り当てられます。 LinkedIn Elevate は、　自動ユーザー プロビジョニングもまた、サポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](linkedinelevate-provisioning-tutorial.md)をご覧ください。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LinkedIn Elevate] タイルをクリックすると、SSO を設定した LinkedIn Elevate に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で LinkedIn Elevate を試す](https://aad.portal.azure.com/)

