---
title: チュートリアル:Zscaler を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Zscaler に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 8add1f57b566d746d464c1ca165938fc112a9784
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062712"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>チュートリアル:Zscaler を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、Azure AD が自動的にユーザーまたはグループを Zscaler にプロビジョニングまたは Zscaler からプロビジョニング解除するように構成するために、Zscaler と Azure Active Directory (Azure AD) で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../active-directory-saas-app-provisioning.md)」を参照してください。
>

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure AD テナント。
* Zscaler テナント。
* 管理者アクセス許可がある Zscaler のユーザー アカウント。

> [!NOTE]
> Azure AD プロビジョニング統合では、Enterprise パッケージを含むアカウントについて Zscaler 開発者が使用できる Zscaler SCIM API が必要です。

## <a name="adding-zscaler-from-the-gallery"></a>ギャラリーからの Zscaler の追加

Azure AD で自動ユーザー プロビジョニング用に Zscaler を構成する前に、Zscaler を Azure AD アプリケーション ギャラリーから管理対象の SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから Zscaler を追加するには、次の手順を行います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Zscaler**」と入力し、結果パネルで **Zscaler** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Zscaler](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Zscaler にユーザーを割り当てる

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、Zscaler へのアクセスが必要な Azure AD のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順に従って、これらのユーザーやグループを Zscaler に割り当てることができます。

* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>ユーザーを Zscaler に割り当てるときの重要なヒント

* 単一の Azure AD ユーザーを Zscaler に割り当て、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Zscaler にユーザーを割り当てるときは、有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Zscaler への自動ユーザー プロビジョニングの構成

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Zscaler のユーザーやグループを作成、更新、無効化する手順について説明します。

> [!TIP]
> Zscaler では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Zscaler シングル サインオンのチュートリアル](zscaler-tutorial.md)に関するページで説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Azure AD で Zscaler の自動ユーザー プロビジョニングを構成するには

1. [Azure portal](https://portal.azure.com) にサインインし、 **[エンタープライズ アプリケーション]** 、 **[すべてのアプリケーション]** 、 **[Zscaler]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Zscaler]** を選択します。

    ![アプリケーションの一覧の Zscaler のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. **[管理者資格情報]** セクションで、手順 6 で説明する Zscaler アカウントの **[テナント URL]** と **[シークレット トークン]** を入力します。

6. **[テナント URL]** と **[シークレット トークン]** を取得するには、Zscaler ポータル ユーザー インターフェイスで **[管理]、[認証の設定]** の順に移動し、 **[認証の種類]** で **[SAML]** をクリックします。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    **[Configure SAML]\(SAML の構成\)** をクリックし、 **[Configuration SAML]\(構成 SAML\)** オプションを開きます。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    **[Enable SCIM-Based Provisioning]\(SCIM ベースのプロビジョニングを有効にする\)** を選択して、**ベース URL** と**ベアラー トークン**を取得し、設定を保存します。 Azure portal で**ベース URL**を**テナント URL** にコピーし、**ベアラー トークン**を**シークレット トークン**にコピーします。

7. 手順 5 の各フィールドに値を入力したら、 **[テスト接続]** をクリックして、Azure AD が Zscaler に接続できることを確認します。 接続できない場合は、使用中の Zscaler アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/test-connection.png)

8. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/notification.png)

9. **[保存]** をクリックします。

10. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to Zscaler]\(Azure Active Directory ユーザーを Zscaler に同期する\)** を選択します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. **[属性マッピング]** セクションで、Azure AD から Zscaler に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. **[マッピング]** セクションで **[Synchronize Azure Active Directory Groups to Zscaler]\(Azure Active Directory グループを Zscaler に同期する\)** を選択します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. **[属性マッピング]** セクションで、Azure AD から Zscaler に同期されるグループ属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Zscaler のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](./../active-directory-saas-scoping-filters.md)の次の手順を参照してください。

15. Zscaler に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Zscaler にプロビジョニングするユーザーやグループを定義します。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/scoping.png)

17. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![Zscaler プロビジョニング](./media/zscaler-provisioning-tutorial/save-provisioning.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって Zscaler に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../active-directory-saas-provisioning-reporting.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
