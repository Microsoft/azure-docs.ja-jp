---
title: PIM で Azure リソース ロールをゲストに割り当てる - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で外部ゲスト ユーザーを招待して Azure リソース ロールを割り当てる方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763441d023e4a707c6a1edc09abfb6d8e5525723
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782738"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management でゲスト ユーザーを招待して Azure リソース ロールを割り当てる

Azure Active Directory (Azure AD) ゲスト ユーザーは Azure AD 内の企業間 (B2B) コラボレーション機能の一部であるため、外部のゲスト ユーザーとベンダーを Azure AD でゲストとして管理することができます。 B2B コラボレーションと Azure AD Privileged Identity Management (PIM) を組み合わせると、コンプライアンスとガバナンスの要件をゲストにまで広げることができます。 たとえば、Azure ID タスクに対する次のような Privileged Identity Management の機能をゲストで使用できます。

- 特定の Azure リソースにアクセスを割り当てる
- ジャストインタイム アクセスを有効にする
- 割り当て期間と終了日を指定する
- アクティブな割り当てまたはアクティブ化で多要素認証を要求する
- アクセス レビューを実行する
- アラートや監査ログを使用する

この記事では、Privileged Identity Management を使用して、組織にゲストを招待し、ゲストの Azure リソースへのアクセスを管理する方法について説明します。

## <a name="when-would-you-invite-guests"></a>いつゲストを招待しますか?

ゲストを組織に招待する場合の例を次にいくつか示します。

- プロジェクトの Azure リソースにアクセスするために、電子メール アカウントしか持っていない外部の自営仕入先を許可します。
- オンプレミスの Active Directory フェデレーション サービスを使用する大規模な組織で、経費アプリケーションにアクセスすることを外部のパートナーを許可します。
- 組織外のサポート エンジニア (Microsoft サポートなど) に、問題をトラブルシューティングしてもらうために Azure リソースに一時的にアクセスすることを許可します。

## <a name="how-does-collaboration-using-b2b-guests-work"></a>B2B ゲストを使用するコラボレーションのしくみはどのようなものですか?

B2B コラボレーションを使用する場合は、外部ユーザーをゲストとして貴社の組織に招待することができます。 ゲストは貴社の組織内のユーザーとして管理できますが、ゲストの認証は Azure AD 組織ではなく、ゲスト自身のホーム組織で行う必要があります。 つまり、ゲストは自分のホーム組織へのアクセス権を失った場合、貴社の組織へのアクセス権も失うことになります。 たとえば、ゲストが所属する組織を離れた場合、貴社が何もしなくても、ゲストは Azure AD 内で貴社と共有しているリソースへのアクセス権を自動的に失います。 B2B コラボレーションの詳細については、「[Azure Active Directory B2B のゲスト ユーザー アクセスとは](../external-identities/what-is-b2b.md)」を参照してください。

![ゲスト ユーザーが自身のホーム ディレクトリでどのように認証されるかを示す図](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>ゲスト コラボレーションの設定を確認する

組織にゲストを招待できるかを確認するには、ご利用のゲスト コラボレーション設定を確認する必要があります。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

1. **[Azure Active Directory]**  >  **[ユーザー設定]** の順に選択します。

1. **[Manage external collaboration settings]\(外部コラボレーションの設定の管理\)** を選択します。

    ![アクセス許可、招待、およびコラボレーション制限の設定が表示される [外部コラボレーションの設定] ページ](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **「管理者とゲスト招待元ロールのユーザーは招待ができる」** スイッチが **[はい]** に設定されていることを確認します。

## <a name="invite-a-guest-and-assign-a-role"></a>ゲストを招待してロールを割り当てる

Privileged Identity Management を使用すると、ゲストを招待し、Azure リソース ロールの資格を付与することができます。

1. [特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)または[ユーザー管理者](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)ロールのメンバー ユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure リソース]** を選択します。

1. **[リソース フィルター]** を使用して、管理対象リソースの一覧をフィルター処理します。

1. リソース、リソース グループ、サブスクリプション、管理グループなど、管理するリソースを選択します。

    ゲストが必要とするものにのみスコープを設定してください。

1. [管理] で **[ロール]** を選択して、Azure リソースのロール一覧を表示します。

    ![アクティブなユーザーおよび資格のあるユーザーの数が表示される Azure リソースのロール一覧](./media/pim-resource-roles-external-users/resources-roles.png)

1. ユーザーが必要とする最小限のロールを選択します。

    ![そのロールの現在のメンバーが一覧表示される [選択されたロール] ページ](./media/pim-resource-roles-external-users/selected-role.png)

1. [ロール] ページで **[メンバーの追加]** を選択して、[新しい割り当て] ペインを開きます。

1. **[メンバーまたはグループの選択]** をクリックします。

    ![[招待] オプションとともにユーザーとグループが一覧表示される [新しい割り当て] - [メンバーまたはグループの選択] ウィンドウ](./media/pim-resource-roles-external-users/select-member-group.png)

1. ゲストを招待するには、 **[招待]** をクリックします。

    ![電子メール アドレスを入力するためのボックス、および個人宛メッセージを指定するためのボックスがある [ゲストを招待する] ページ](./media/pim-resource-roles-external-users/invite-guest.png)

1. ゲストを選択したら、 **[招待]** をクリックします。

    ゲストは、選択されたメンバーとして追加する必要があります。

1. **[メンバーまたはグループの選択]** ウィンドウで、 **[選択]** をクリックします。

1. **[メンバーシップ設定]** ウィンドウで、割り当ての種類と期間を選択します。

    ![割り当ての種類、開始日、および終了日を指定するためのオプションがある [新しい割り当て] - [メンバーシップ設定] ページ](./media/pim-resource-roles-external-users/membership-settings.png)

1. 割り当てを完了するには、 **[完了]** を選択してから **[追加]** を選択します。

    ゲストのロールの割り当ては、ご利用のロールのリストに表示されます。

    ![有資格のゲストが一覧表示される [ロール] ページ](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>ゲストとしてロールをアクティブ化する

外部のユーザーの場合、Azure AD 組織のゲストになるための招待を受け入れて、場合によっては、ロールの割り当てをアクティブ化する必要があります。

1. 招待状が含まれる電子メールを開きます。 電子メールは次のような内容です。

    ![ディレクトリ名、個人宛メッセージ、および開始リンクが含まれる電子メール招待状](./media/pim-resource-roles-external-users/email-invite.png)

1. 電子メールの **[開始する]** リンクを選択します。

1. アクセス許可を確認した後に、 **[同意する]** をクリックします。

    ![組織があなたに確認を求めるアクセス許可の一覧が含まれる、ブラウザー内の [アクセス許可の確認] ページ](./media/pim-resource-roles-external-users/invite-accept.png)

1. 利用規約に同意し、サインインしたままにするかどうかを指定するように求められる場合があります。 Azure portal では、ロールの "*資格*" があっても、リソースにはまだアクセスできません。

1. ロールの割り当てをアクティブ化するには、ロールをアクティブ化するリンクがある電子メールを開きます。 電子メールは次のような内容です。

    ![ロールをアクティブ化するリンクが含まれた、ユーザーがロールの資格があることを示す電子メール](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **[Activate role]\(ロールのアクティブ化\)** を選択して、Privileged Identity Management で自分に資格があるロールを開きます。

    ![資格があるロールが一覧表示された Privileged Identity Management 内の [自分のロール] ページ](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. [アクション] の下の **[アクティブ化]** リンクを選択します。

    ロールの設定に応じて、ロールをアクティブ化するためのいくらかの情報を指定する必要があります。

1. ロールの設定を指定したら、 **[アクティブ化]** をクリックしてロールをアクティブ化します。

    ![スコープと開始時刻、期間、および理由を指定するためのオプションが一覧表示される [アクティブ化] ページ](./media/pim-resource-roles-external-users/activate-role.png)

    管理者が要求を承認する必要がない限り、指定されたリソースへのアクセスが必要です。

## <a name="view-activity-for-a-guest"></a>ゲストのアクティビティの表示

監査ログを表示して、ゲストが行っていることを追跡できます。

1. 管理者は、Privileged Identity Management を開き、共有されているリソースを選択します。

1. **[リソースの監査]** を選択して、そのリソースのアクティビティを表示します。 リソース グループのアクティビティの例を次に示します。

    ![時刻、要求元、およびアクションが一覧表示される [Azure リソース] - [リソースの監査] ページ](./media/pim-resource-roles-external-users/audit-resource.png)

1. ゲストのアクティビティを表示するには、 **[Azure Active Directory]**  >  **[ユーザー]**  > *ゲスト名*の順に選択します。

1. **[監査ログ]** を選択して、組織の監査ログを表示します。 必要に応じて、フィルターを指定できます。

    ![日付、ターゲット、開始者、およびアクティビティが一覧表示される Directory 監査ログ](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD 管理者ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Azure Active Directory B2B コラボレーションのゲスト ユーザー アクセスとは](../external-identities/what-is-b2b.md)