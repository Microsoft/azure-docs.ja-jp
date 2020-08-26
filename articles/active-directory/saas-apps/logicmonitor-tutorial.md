---
title: 'チュートリアル: Azure Active Directory と LogicMonitor の統合 | Microsoft Docs'
description: Azure Active Directory と LogicMonitor の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4c5906d9cca193129a4213f697815f70ec639d8b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549757"
---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>チュートリアル: Azure Active Directory と LogicMonitor の統合

このチュートリアルでは、LogicMonitor と Azure Active Directory (Azure AD) を統合する方法について説明します。
LogicMonitor と Azure AD の統合には、次の利点があります。

* LogicMonitor にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LogicMonitor に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

LogicMonitor と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* LogicMonitor でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* LogicMonitor では、**SP** によって開始される SSO がサポートされます

## <a name="adding-logicmonitor-from-the-gallery"></a>ギャラリーからの LogicMonitor の追加

Azure AD への LogicMonitor の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LogicMonitor を追加する必要があります。

**ギャラリーから LogicMonitor を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**LogicMonitor**」と入力し、結果パネルで **[LogicMonitor]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果一覧の LogicMonitor](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、LogicMonitor で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと LogicMonitor 内の関連ユーザー間にリンク関係が確立されている必要があります。

LogicMonitor で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[LogicMonitor シングル サインオンの構成](#configure-logicmonitor-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[LogicMonitor のテスト ユーザーの作成](#create-logicmonitor-test-user)** - LogicMonitor で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

LogicMonitor で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **LogicMonitor** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[LogicMonitor のドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.logicmonitor.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.logicmonitor.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[LogicMonitor クライアント サポート チーム](https://www.logicmonitor.com/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[LogicMonitor のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-logicmonitor-single-sign-on"></a>LogicMonitor のシングル サインオンの構成

1. **LogicMonitor** の企業サイトに管理者としてログインします。

2. 上部のメニューで **[Settings]** をクリックします。

    ![[設定]](./media/logicmonitor-tutorial/ic790052.png "設定")

3. 左側にあるナビゲーション バーで、 **[シングル サインオン]**

    ![シングル サインオン](./media/logicmonitor-tutorial/ic790053.png "[Single Sign-On]")

4. **[シングル サインオンの設定]** セクションで、次の手順に従います。

    ![シングル サインオンの設定](./media/logicmonitor-tutorial/ic790054.png "[シングル サインオンの設定]")

    a. **[シングル サインオンを有効にする]** を選択します。

    b. **[Default Role Assignment (既定のロールの割り当て)]** で、 **[読み取り専用]** を選択します。

    c. ダウンロードしたメタデータ ファイルをメモ帳で開き、ファイルの内容を **[Identity Provider Metadata (ID プロバイダーのメタデータ)]** ボックスに貼り付けます。

    d. **[変更を保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LogicMonitor へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[LogicMonitor]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[LogicMonitor]** を選択します。

    ![アプリケーションの一覧の [LogicMonitor] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-logicmonitor-test-user"></a>LogicMonitor のテスト ユーザーの作成

Azure AD ユーザーがサインインできるように、Azure Active Directory ユーザー名を使用して、LogicMonitor アプリケーションにユーザーをプロビジョニングする必要があります。

**ユーザー プロビジョニングを構成するには、次の手順に従います。**

1. LogicMonitor の企業サイトに管理者としてログインします。

2. 上部のメニューで、 **[設定]** 、 **[Roles and Users (ロールとユーザー)]** の順にクリックします。

    ![ロールとユーザー](./media/logicmonitor-tutorial/ic790056.png "[Roles and Users (ロールとユーザー)]")

3. **[追加]** をクリックします。

4. **[アカウントの追加]** セクションで、次の手順に従います。

    ![アカウントを追加する](./media/logicmonitor-tutorial/ic790057.png "[アカウントの追加]")

    a. 関連するテキスト ボックスにプロビジョニングする Azure Active Directory ユーザーの**ユーザー名**、**メール**、**パスワード**、**パスワードの再入力**の値を入力します。

    b. **[ロール]** 、 **[アクセス許可の表示]** 、 **[状態]** の順に選択します。

    c. **[送信]** をクリックします。

> [!NOTE]
> LogicMonitor から提供されている他の LogicMonitor ユーザー アカウント作成ツールや API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [LogicMonitor] タイルをクリックすると、SSO を設定した LogicMonitor に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

