---
title: "チュートリアル: Azure Active Directory と Tangoe Command Premium Mobile の統合 | Microsoft Docs"
description: "Azure Active Directory と Tangoe Command Premium Mobile の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2b0b544c-9c2c-49cd-862b-ec2ee9330126
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 595541e7248a7486e58123927389c552af0e50f7
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---
# <a name="tutorial-azure-active-directory-integration-with-tangoe-command-premium-mobile"></a>チュートリアル: Azure Active Directory と Tangoe Command Premium Mobile の統合

このチュートリアルでは、Tangoe Command Premium Mobile と Azure Active Directory (Azure AD) を統合する方法について説明します。

Tangoe Command Premium Mobile と Azure AD の統合には、次の利点があります。

- Tangoe Command Premium Mobile にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが各自の Azure AD アカウントで Tangoe Command Premium Mobile に自動的にサインオン (シングル サインオン) するように設定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Tangoe Command Premium Mobile の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Tangoe Command Premium Mobile でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Tangoe Command Premium Mobile の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-tangoe-command-premium-mobile-from-the-gallery"></a>ギャラリーからの Tangoe Command Premium Mobile の追加
Azure AD への Tangoe Command Premium Mobile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Tangoe Command Premium Mobile を追加する必要があります。

**ギャラリーから Tangoe Command Premium Mobile を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Tangoe Command Premium Mobile**」と入力して、結果パネルで **Tangoe Command Premium Mobile** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![ギャラリーからの Tangoe Command Premium Mobile の追加 ](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Tangoe Command Premium Mobile で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Tangoe Command Premium Mobile ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Tangoe Command Premium Mobile の関連ユーザーの間で、リンク関係が確立されている必要があります。

Tangoe Command Premium Mobile で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Tangoe Command Premium Mobile で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Tangoe Command Premium Mobile テスト ユーザーの作成](#create-a-tangoe-command-premium-mobile-test-user)** - Tangoe Command Premium Mobile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Tangoe Command Premium Mobile アプリケーションでシングル サインオンを構成します。

**Tangoe Command Premium Mobile で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Tangoe Command Premium Mobile** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![SAML ベースのサインオン](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_samlbase.png)

3. **[Tangoe Command Premium Mobile のドメインと URL]** セクションで、次の手順を実行します。

    ![Tangoe Command Premium Mobile のドメインと URL](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_url.png)

    a. **[サインオン URL]** ボックスに、`https://sso.tangoe.com/sp/startSSO.ping?PartnerIdpId=<tenant issuer>&TARGET=<target page url>` のパターンを使用して URL を入力します。

    b. **[応答 URL]** ボックスに、`https://sso.tangoe.com/sp/ACS.saml2` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 [Tangoe Command Premium Mobile クライアント サポート チーム](https://www.tangoe.com/contact-2/)に問い合わせてこれらの値を入手します。 

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[保存] ボタン](./media/active-directory-saas-tangoe-tutorial/tutorial_general_400.png)
    
6. **[Tangoe Command Premium Mobile 構成]** セクションで **[Tangoe Command Premium Mobile の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[Tangoe Command Premium Mobile の構成] セクション](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_configure.png) 

7. アプリケーション用に構成された SSO を入手するには、[Tangoe Command Premium Mobile クライアント サポート チーム](https://www.tangoe.com/contact-2/)に連絡し、次のものを情報として提供してください。

   - ダウンロードしたメタデータ ファイル
   - **SAML エンティティ ID**
   - **SAML シングル サインオン サービス URL**
   - **サインアウト URL**

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-tangoe-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] -> [すべてのユーザー]](./media/active-directory-saas-tangoe-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![ユーザーの追加](./media/active-directory-saas-tangoe-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ページ](./media/active-directory-saas-tangoe-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="create-a-tangoe-command-premium-mobile-test-user"></a>Tangoe Command Premium Mobile テスト ユーザーの作成

このセクションでは、Tangoe Command Premium Mobile で Britta Simon というユーザーを作成します。 

Tangoe Command Premium Mobile アプリケーションでは、シングル サインオンを行う前に、すべてのユーザーをアプリケーションにプロビジョニングする必要があります。 [Tangoe Command Premium Mobile クライアント サポート チーム](https://www.tangoe.com/contact-2/)と協力して、すべてのユーザーをアプリケーションにプロビジョニングします。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Tangoe Command Premium Mobile へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Tangoe Command Premium Mobile に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Tangoe Command Premium Mobile]**を選択します。

    ![アプリ一覧の Tangoe Command Premium Mobile](./media/active-directory-saas-tangoe-tutorial/tutorial_tangoe_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで [Tangoe Command Premium Mobile] タイルをクリックすると、自動的に Tangoe Command Premium Mobile アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tangoe-tutorial/tutorial_general_203.png


