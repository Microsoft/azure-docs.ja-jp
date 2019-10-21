---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Velpic SAML の統合 | Microsoft Docs
description: Azure Active Directory と Velpic SAML の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241554"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Velpic SAML の統合

このチュートリアルでは、Velpic SAML と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Velpic SAML を統合すると、次のことができます。

* Velpic SAML にアクセスする Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して Velpic SAML に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Velpic SAML でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Velpic SAML では、**SP** によって開始される SSO がサポートされます

## <a name="adding-velpic-saml-from-the-gallery"></a>ギャラリーからの Velpic SAML の追加

Azure AD への Velpic SAML の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Velpic SAML を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Velpic SAML**」と入力します。
1. 結果のパネルから **Velpic SAML** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Velpic SAML の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Velpic SAML に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Velpic SAML の関連ユーザーとの間にリンク関係を確立する必要があります。

Velpic SAML で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Velpic SAML SSO の構成](#configure-velpic-saml-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Velpic SAML のテスト ユーザーの作成](#create-velpic-saml-test-user)** - Velpic SAML で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Velpic SAML** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<sub-domain>.velpicsaml.net`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > サインオン URL は Velpic SAML チームによって提供され、識別子の値は Velpic SAML 側の SSO プラグインの構成時に入手できます。 Velpic SAML アプリケーションのページからこの値をコピーして、ここに貼り付けてください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Velpic SAML のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Velpic SAML へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧から **[Velpic SAML]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-velpic-saml-sso"></a>Velpic SAML SSO の構成

1. Velpic SAML 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Velpic SAML のセットアップ]** をクリックすると、Velpic SAML アプリケーションに移動します。 そこから、管理者の資格情報を入力して Velpic SAML にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Velpic SAML を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Velpic SAML 企業サイトにサインインして、次の手順を実行します。

4. **[Manage] \(管理)** タブをクリックして **[Integration] \(統合)** セクションに移動します。ここで、 **[Plugins] \(プラグイン)** をクリックして、サインイン用の新しいプラグインを作成する必要があります。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_1.png)

5. **[Add plugin] \(プラグインの追加)** をクリックします。
    
    ![プラグイン](./media/velpicsaml-tutorial/velpic_2.png)

6. [Add plugin] \(プラグインの追加) ページで **[SAML]** タイルをクリックします。
    
    ![プラグイン](./media/velpicsaml-tutorial/velpic_3.png)

7. 新しい SAML プラグインの名前を入力し、 **[追加]** をクリックします。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_4.png)

8. 詳細を次のように入力します。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_5.png)

    a. **[名前]** テキストボックスに、SAML プラグインの名前を入力します。

    b. **[Issuer URL]\(発行者 URL\)** ボックスに、Azure portal の **[サインオンの構成]** ウィンドウからコピーした **Azure AD 識別子**を貼り付けます。

    c. **[Provider Metadata Config] \(Provider メタデータ構成)** で、Azure Portal からダウンロードしたメタデータ XML ファイルをアップロードします。

    d. **Auto create new users \(新規ユーザーの自動作成)** チェックボックスをオンにして、SAML のジャストインタイム プロビジョニングを有効にすることもできます。 Velpic にユーザーが存在せず、このチェックボックスがオンになっていない場合は、Azure からのログインに失敗します。 このチェックボックスがオンになっている場合、ユーザーは、ログイン時に Velpic に自動的にプロビジョニングされます。 

    e. テキストボックスから**シングル サインオン URL** をコピーして、Azure Portal に貼り付けます。
    
    f. **[Save]** をクリックします。

### <a name="create-velpic-saml-test-user"></a>Velpic SAML テスト ユーザーの作成

アプリケーションは、ジャスト イン タイムのユーザー プロビジョニングをサポートしているため、通常、この手順は必要ありません。 自動ユーザー プロビジョニングが有効でない場合は、次の手順にしたがって手動でユーザーを作成することができます。

Velpic SAML 企業サイトに管理者としてサインインし、次の手順に従います。
    
1. [Manage] \(管理) タブをクリックして [Users] \(ユーザー) セクションに移動し、[New] \(新規) をクリックしてユーザーを追加します。

    ![add user](./media/velpicsaml-tutorial/velpic_7.png)

2. **[Create New User] \(新しいユーザーの作成)** ダイアログ ページで、次の手順を実行します。

    ![ユーザー](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. **[First Name]\(名\)** ボックスに、ユーザーの名 B を入力します。

    b. **[Last Name]\(姓\)** ボックスに、ユーザーの姓 Simon を入力します。

    c. **[User Name]\(ユーザー名\)** テキストボックスに、B.Simon のユーザーを入力します。

    d. **[Email]\(電子メール\)** ボックスに、B.Simon@contoso.com アカウントのメール アドレスを入力します。

    e. その他の情報は省略可能です。必要に応じて入力してください。
    
    f. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで [Velpic SAML] タイルをクリックすると、Velpic SAML アプリケーションのログイン ページが表示されます。 サインイン ページに **[Log In With Azure AD] \(Azure AD でログイン)** と表示されます。

    ![プラグイン](./media/velpicsaml-tutorial/velpic_6.png)

1. **[Log In With Azure AD] \(Azure AD でログイン)** をクリックし、Azure AD アカウントを使用して Velpic にログインします。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Velpic SAML を試す](https://aad.portal.azure.com/)

