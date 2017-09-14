---
title: "チュートリアル: ZenDesk を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs"
description: "Azure Active Directory を構成して、ユーザー アカウントを ZenDesk に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: stevenpo
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: asmalser-msft
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 1a1414eefd20e6d7c025da08cfd5ae7c45daad33
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="tutorial-configuring-zendesk-for-automatic-user-provisioning"></a>チュートリアル: ZenDesk を構成し、自動ユーザー プロビジョニングに対応させる


このチュートリアルでは、Azure AD から ZenDesk にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで ZenDesk と Azure AD で実行する必要がある手順について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

*   Azure Active Directory テナント
*   [Enterprise プラン](https://www.zendesk.com/product/pricing/)以上の有効な ZenDesk テナント 
*   Admin アクセス許可がある ZenDesk のユーザー アカウント 

> [!NOTE]
> Azure AD プロビジョニング統合では、Essential プラン以上の ZenDesk チームで使用できる [ZenDesk REST API](https://developer.zendesk.com/rest_api/docs/core/introduction#the-api) が必要です。

## <a name="assigning-users-to-zendesk"></a>ZenDesk へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。 

プロビジョニング サービスを構成して有効にする前に、ZenDesk アプリへのアクセスが必要なユーザーを表す Azure AD 内のユーザーやグループを決定しておく必要があります。 決定し終えたら、次の手順でこれらのユーザーを ZenDesk アプリに割り当てることができます。

[エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-zendesk"></a>ユーザーを ZenDesk に割り当てる際の重要なヒント

*   単一の Azure AD ユーザーを ZenDesk に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

*   ZenDesk にユーザーを割り当てるときは、**ユーザー** ロールまたは別の有効なアプリケーション固有ロール (使用可能な場合) を割り当てダイアログで選ぶ必要があります。 **[既定のアクセス]** ロールはプロビジョニングでは使うことができず、このロールのユーザーはスキップされます。

> [!NOTE]
> 追加された機能として、プロビジョニング サービスは、ZenDesk で定義されているカスタム ロールを読み取り、Azure AD にインポートします。インポートされたロールは、[ロールの選択] ダイアログで選択できます。 プロビジョニング サービスが有効にされて、同期サイクルが 1 回完了した後、これらのロールは Azure Portal に表示されます。

## <a name="configuring-user-provisioning-to-zendesk"></a>ZenDesk へのユーザー プロビジョニングの構成 

このセクションでは、Azure AD を ZenDesk のユーザー アカウント プロビジョニング API に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを ZenDesk で作成、更新、無効化する手順を説明します。

> [!TIP] 
> ZenDesk では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。


### <a name="configure-automatic-user-account-provisioning-to-zendesk-in-azure-ad"></a>Azure AD で ZenDesk への自動ユーザー アカウント プロビジョニングを構成する


1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために ZenDesk を既に構成している場合は、検索フィールドで ZenDesk のインスタンスを検索します。 構成していない場合は、**[追加]** を選択してアプリケーション ギャラリーで **ZenDesk** を検索します。 検索結果から ZenDesk を選択してアプリケーションの一覧に追加します。

3. ZenDesk のインスタンスを選択してから、**[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![ZenDesk のプロビジョニング](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk1.png)

5. **[管理者資格情報]** セクションで、**[管理ユーザー名]、[tokenkey]\(トークン キー\)、[ドメイン]** に ZenDesk のアカウントによって生成された値 (トークンは **[Admin]\(管理\)** > **[API]** > **[Settings]\(設定\)** で確認できます) を入力します。 

    ![ZenDesk のプロビジョニング](./media/active-directory-saas-zendesk-provisioning-tutorial/ZenDesk2.png)

6. Azure Portal で、**[テスト接続]** をクリックして Azure AD が ZenDesk アプリに接続できることを確認します。 接続できない場合は、使用中の ZenDesk アカウントに管理者アクセス許可があることを確認してから、手順 5 をもう一度試します。

7. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、[エラーが発生したときにメール通知を送信します] チェック ボックスをオンにします。

8. [ **Save**] をクリックします。 

9. [マッピング] セクションの **[Synchronize Azure Active Directory Users to ZenDesk]\(Azure Active Directory ユーザーを ZenDesk に同期する\)** を選びます。

10. **[属性マッピング]** セクションで、Azure AD から ZenDesk に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で ZenDesk のユーザー アカウントとの照合に使用されます。 [保存] ボタンをクリックして変更をコミットします。

11. ZenDesk に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

12. [ **Save**] をクリックします。 

これにより、[ユーザーとグループ] セクションで ZenDesk に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、プロビジョニング サービスによって実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)」をご覧ください。


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](active-directory-enterprise-apps-manage-provisioning.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](active-directory-saas-provisioning-reporting.md)

