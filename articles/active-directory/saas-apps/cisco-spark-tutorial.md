---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex の統合 | Microsoft Docs
description: Azure Active Directory と Cisco Webex の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4fd6b46353cdeb4493c1a767c92e75d602e52843
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528896"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Cisco Webex の統合

このチュートリアルでは、Cisco Webex を Azure Active Directory (Azure AD) と統合する方法について説明します。 Cisco Webex を Azure AD と統合すると、次のことができます。

* Cisco Webex にアクセスする Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Cisco Webex に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Cisco Webex でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Cisco Webex では、**SP** Initiated SSO がサポートされます。
* Cisco Webex では、**自動化された**ユーザー プロビジョニングがサポートされます。
* Cisco Webex を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-cisco-webex-from-the-gallery"></a>ギャラリーからの Cisco Webex の追加

Azure AD への Cisco Webex の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Cisco Webex を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Cisco Webex**」と入力します。
1. 結果パネルで **[Cisco Webex]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Cisco Webex の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Cisco Webex に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Cisco Webex の関連ユーザーの間で、リンク関係を確立する必要があります。

Cisco Webex で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Cisco Webex の構成](#configure-cisco-webex)** - アプリケーション側で SSO 設定を構成します。
    1. **[Cisco Webex のテスト ユーザーの作成](#create-cisco-webex-test-user)** - Cisco Webex で B.Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Cisco Webex** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、ダウンロードした**サービス プロバイダー メタデータ ファイル**をアップロードし、次の手順を実行してアプリケーションを構成します。

    >[!Note]
    >サービス プロバイダーのメタデータ ファイルは、このチュートリアルで後述する「**Cisco Webex の構成**」セクションで取得します。 

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ**をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. サービス プロバイダー メタデータ ファイルのアップロードが正常に完了すると、次のように、**識別子**と**応答 URL** の値が **[基本的な SAML 構成]** セクションに自動的に入力されます。

    **[サインオン URL]** テキストボックスに、SP メタデータ ファイルのアップロードによって自動入力される **[応答 URL]** の値を貼り付けます。

1. Cisco Webex アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Cisco Webex アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
  
    | 名前 |  ソース属性|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Cisco Webex のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Cisco Webex へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Cisco Webex]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cisco-webex"></a>Cisco Webex の構成

1. Cisco Webex 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Cisco Webex のセットアップ]** をクリックすると、Cisco Webex アプリケーションに移動します。 そこから、管理者の資格情報を入力して Cisco Webex にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Cisco Webex を手動で設定する場合は、完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にサインインします。

4. **[設定]** を選択し、 **[認証]** セクションの下で **[変更]** をクリックします。

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. **[Integrate a 3rd-party identity provider.(Advanced)] \(サード パーティの ID プロバイダーを統合する。(詳細))** を選択して、次の画面に移動します。

6. **[Import Idp Metadata] \(Idp メタデータのインポート)** ページで、ページ上に Azure AD メタデータ ファイルをドラッグ アンド ドロップするか、ファイルのブラウザー オプションを使用して Azure AD メタデータ ファイルを見つけてアップロードします。 次に、 **[Require certificate signed by a certificate authority in Metadata (more secure)(証明機関の署名付き証明書がメタデータに必要 (高セキュリティ))]** を選択し、 **[Next (次へ)]** をクリックします。

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. **[Test SSO Connection (SSO 接続のテスト)]** を選択し、ブラウザーの新しいタブが開いたら、サインインして Azure AD で認証します。

8. ブラウザーの **[Cisco Cloud Collaboration Management]** タブに戻ります。テストが成功した場合は、 **[This test was successful.Enable Single Sign-On option] \(このテストは正常に完了しました。シングル サインオン オプションを有効にします)** を選択して、 **[次へ]** をクリックします。

### <a name="create-cisco-webex-test-user"></a>Cisco Webex のテスト ユーザーの作成

このセクションでは、Cisco Webex で B.Simon というユーザーを作成します。 このセクションでは、Cisco Webex で B.Simon というユーザーを作成します。

1. 完全な管理者権限を備えた資格情報を使って [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) にアクセスします。

2. **[Users]\(ユーザー\)** と **[Manage Users ]\(ユーザーの管理\)** を順にクリックします。
   
    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. **[Manage User (ユーザーの管理)]** ウィンドウで、 **[Manually add or modify users (ユーザーを手動で追加または変更)]** を選択し、 **[Next (次へ)]** をクリックします。

4. **[Names and Email address (名前と電子メール アドレス)]** を選択します。 次のようにテキスト ボックスに入力します。

    ![Configure single sign-on](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **B**)。

    b. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (b.simon@contoso.com など) を入力します。

5. プラス記号をクリックして、B.Simon を追加します。 次に、 **[次へ]** をクリックします。

6. **[Add Services for Users (ユーザーのサービスを追加)]** ウィンドウで、 **[Save (保存)]** をクリックしてから **[Finish (完了)]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Cisco Webex] タイルを選択すると、SSO を設定した Cisco Webex に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Cisco Webex を試す](https://aad.portal.azure.com)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Cisco Webex を保護する方法](https://docs.microsoft.com/cloud-app-security/protect-webex)