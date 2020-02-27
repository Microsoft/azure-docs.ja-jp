---
title: チュートリアル:ThousandEyes のユーザー プロビジョニング - Azure AD
description: Azure Active Directory を構成して、ユーザー アカウントを ThousandEyes に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d87cffce636146eac3e557670ffc4fb2fc34ae38
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062882"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>チュートリアル:ThousandEyes を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から ThousandEyes にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで ThousandEyes と Azure AD で実行する必要がある手順について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* [Standard プラン](https://www.thousandeyes.com/pricing)以上が有効な ThousandEyes テナント 
* Admin アクセス許可がある ThousandEyes のユーザー アカウント 

> [!NOTE]
> Azure AD プロビジョニング統合では、Standard プラン以上の ThousandEyes チームで使用できる [ThousandEyes SCIM API](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK) が必要です。

## <a name="assigning-users-to-thousandeyes"></a>ThousandEyes へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、ThousandEyes アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを ThousandEyes アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>ユーザーを ThousandEyes に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを ThousandEyes に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* ThousandEyes にユーザーを割り当てるときは、**ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選ぶ必要があります。 **[既定のアクセス]** ロールはプロビジョニングでは使うことができず、このロールのユーザーはスキップされます。

## <a name="configuring-user-provisioning-to-thousandeyes"></a>ThousandEyes へのユーザー プロビジョニングの構成 

このセクションでは、Azure AD を ThousandEyes のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを ThousandEyes で作成、更新、無効化する手順を説明します。

> [!TIP]
> ThousandEyes では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Azure AD で ThousandEyes への自動ユーザー アカウント プロビジョニングを構成する

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために ThousandEyes を既に構成している場合は、検索フィールドで ThousandEyes のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **ThousandEyes** を検索します。 検索結果から ThousandEyes を選択してアプリケーションの一覧に追加します。

3. ThousandEyes のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![ThousandEyes のプロビジョニング](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. **[管理者資格情報]** セクションで、ThousandEyes のアカウントによって生成された **[OAuth ベアラー トークン]** を入力します (トークンは、ThousandEyes アカウントの **[プロファイル]** で確認できます)。

    ![ThousandEyes のプロビジョニング](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が ThousandEyes アプリに接続できることを確認します。 接続できない場合は、使用中の ThousandEyes アカウントに管理者アクセス許可があることを確認してから、手順 5 をもう一度試します。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、[エラーが発生したときにメール通知を送信します] チェック ボックスをオンにします。

8. **[保存]** をクリックします。

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to ThousandEyes]\(Azure Active Directory ユーザーを ThousandEyes に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から ThousandEyes に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で ThousandEyes のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. ThousandEyes に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. **[保存]** をクリックします。

これにより、[ユーザーとグループ] セクションで ThousandEyes に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
