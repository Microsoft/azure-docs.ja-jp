---
title: PIM で Azure リソース ロールのアクセス レビューを作成する - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) で Azure リソース ロールに対するアクセス レビューを作成する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 04/05/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87c0ce72348f67c22759915a3a15c69193ad2f60
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552795"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management で Azure リソース ロールのアクセス レビューを作成する

従業員による特権 Azure リソース ロールへのアクセスの必要性は、時間の経過に伴って変化します。 古くなったロールの割り当てに関連するリスクを軽減するために、アクセスを定期的に確認する必要があります。 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、Azure リソース ロールへの特権アクセスのアクセス レビューを作成できます。 自動的に実行される定期的なアクセス レビューを構成することもできます。 この記事では、1 つ以上のアクセス レビューを作成する方法について説明します。

## <a name="prerequisite-license"></a>事前に必要なライセンス

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]. PIM のライセンスについての詳細は、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

> [!Note]
>  現時点では、アクセス レビューのスコープを、Azure AD へのアクセス権を持つサービス プリンシパルと、テナントで Azure Active Directory Premium P2 エディションがアクティブになっている Azure リソース ロール (プレビュー) に設定できます。 サービス プリンシパルのライセンス モデルは、この機能の一般提供のために終了する予定です。このため、追加のライセンスが必要になる場合があります。

## <a name="prerequisite-role"></a>事前に必要なロール

 アクセス レビューを作成するには、リソースの Azure ロールの[所有者](../../role-based-access-control/built-in-roles.md#owner)または[ユーザー アクセス管理者](../../role-based-access-control/built-in-roles.md#user-access-administrator)に割り当てられている必要があります。

## <a name="open-access-reviews"></a>アクセス レビューを開く

1. いずれかの事前に必要なロールに割り当てられているユーザーで [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Identity Governance]** を選択します。
 
1. 左側のメニューで、 **[Azure AD Privileged Identity Management]** の下にある **[Azure リソース]** を選択します。

1. サブスクリプションなど、管理するリソースを選択します。

1. [管理] の下の **[アクセス レビュー]** を選択します。

    ![すべてのレビューの状態を示す [Azure リソース] - [アクセス レビュー] 一覧](./media/pim-resource-roles-start-access-review/access-reviews.png)

1. **[新規]** をクリックして、新しいアクセス レビューを作成します。

1. アクセス レビューに名前を付けます。 必要に応じて、そのレビューに説明を加えます。 その名前と説明がレビュアーに示されます。

    ![アクセス レビューの作成 - レビューの名前と説明](./media/pim-resource-roles-start-access-review/name-description.png)

1. **[開始日]** を設定します。 既定では、アクセス レビューは 1 回実行されます。作成されたのと同じ時間に開始され、1 か月で終了します。 この開始日と終了日を変更することで、アクセス レビューを後で開始し、必要な日数を好きなだけ確保することができます。

    ![開始日、頻度、期間、終了、回数、および終了日](./media/pim-resource-roles-start-access-review/start-end-dates.png)

1. アクセス レビューを繰り返すには、 **[頻度]** 設定を **[1 回]** から **[毎週]** 、 **[毎月]** 、 **[四半期に 1 回]** 、 **[毎年]** 、 **[Semi-annually]\(半年に 1 回\)** に変更します。 **[期間]** スライダーまたはテキスト ボックスを使用し、繰り返しの系列の各レビューでレビュー担当者からの入力を受け付ける日数を決めます。 たとえば、レビューの重複を避けるために、月 1 回のレビューに設定できる最大期間は 27 日です。

1. **[終了]** の設定を使用して、アクセス レビューの繰り返し系列を終了する方法を指定します。 系列は 3 つの方法で終了できます。つまり、継続的に実行して無期限にレビューを開始する、特定の日付まで実行する、または定義された実行回数が完了するまで実行する、です。 あなた自身、別のユーザー管理者、または別の全体管理者は、 **[設定]** で日付を変更してその日付に終了するように指定することで、作成後に系列を停止することができます。

1. **[ユーザー]** セクションで、レビューのスコープを選択します。 ユーザーをレビューするには、 **[ユーザー]** を選択します。また、Azure ロールへのアクセス権を持つマシン アカウントをレビューするには、[(Preview) Service Principals]\((プレビュー) サービス プリンシパル\) を選択します。   

    ![ロール メンバーシップをレビューするためのユーザー スコープ](./media/pim-resource-roles-start-access-review/users.png)


1. **[ロール メンバーシップをレビュー]** で、レビューする特権 Azure ロールを選択します。 

    > [!NOTE]
    > - ここで選択されるロールには、[永続的なロールと資格のあるロール](../privileged-identity-management/pim-how-to-add-role-to-user.md)の両方が含まれます。
    > - 複数のロールを選択すると、複数のアクセス レビューが作成されます。 たとえば、5 つのロールを選択すると、5 つの別々のアクセス レビューが作成されます。
    **Azure AD ロール** のアクセス レビューを作成する場合の、レビューのメンバーシップ一覧の例を次に示します。

    ![選択できる Azure AD ロールを一覧表示する [レビューのメンバーシップ] ウィンドウ](./media/pim-resource-roles-start-access-review/review-membership.png)

    **Azure リソース ロール** のアクセス レビューを作成する場合の、レビューのメンバーシップ一覧の例を次の図に示します。

    ![選択できる Azure リソース ロールを一覧表示する [レビューのメンバーシップ] ウィンドウ](./media/pim-resource-roles-start-access-review/review-membership-azure-resource-roles.png)

1. **[レビュー担当者]** セクションで、全ユーザーをレビューする担当者 (複数可) を選びます。 メンバー自身にそのアクセス権をレビューしてもらうことができます。

    ![選択したユーザーまたはメンバー (セルフ) のレビュー担当者の一覧](./media/pim-resource-roles-start-access-review/reviewers.png)

    - **選択したユーザー** - レビューを実行する特定のユーザーを指定するには、このオプションを使用します。 このオプションは、レビューのスコープに関係なく使用できます。選択したレビュー担当者は、ユーザーとサービス プリンシパルをレビューできます。 
    - **メンバー (セルフ)** - ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。 このオプションは、レビューのスコープが **[ユーザー]** に設定されている場合にのみ使用できます。
    - **マネージャー** – ユーザーのマネージャーにロールの割り当てをレビューしてもらうには、このオプションを使用します。 このオプションは、レビューのスコープが **[ユーザー]** に設定されている場合にのみ使用できます。 マネージャーを選択すると、フォールバック レビュー担当者を指定するオプションも表示されます。 フォールバック レビュー担当者は、ディレクトリにマネージャーが指定されていない場合に、ユーザーをレビューするように求められます。 

### <a name="upon-completion-settings"></a>完了時の設定

1. レビュー完了後の動作を指定するには、 **[設定完了時]** セクションを展開します。

    ![適用を自動化するための [設定完了時] および [レビュー担当者からの応答なし]](./media/pim-resource-roles-start-access-review/upon-completion-settings.png)

1. 拒否されたユーザーのアクセスを自動的に削除する場合は、 **[リソースへの結果の自動適用]** を **[有効]** に設定します。 レビューが完了したときに結果を手動で適用する場合は、スイッチを **[無効]** に設定します。

1. レビューの期間内にレビュー担当者によってレビューされなかったユーザーに対する処理を指定するには、 **[レビュー担当者からの応答なし]** の一覧を使用します。 この設定は、レビュー担当者によって手動でレビューされたユーザーには影響しません。 レビュー担当者の最終的な決定が拒否である場合、ユーザーのアクセスは削除されます。

    - **[変更なし]** - ユーザーのアクセスをそのまま変更しないでおきます
    - **[アクセスの削除]** - ユーザーのアクセスを削除します
    - **[アクセスを承認する]** - ユーザーのアクセスを承認します
    - **[推奨事項の実行]** - ユーザーの継続的なアクセスの拒否または承認に関するシステムの推奨事項を実行します

### <a name="advanced-settings"></a>詳細設定

1. 他の設定を指定するには、 **[詳細設定]** セクションを展開します。

    ![推奨事項の表示、承認理由の必須化、メール通知、およびリマインダーのための [詳細設定]](./media/pim-resource-roles-start-access-review/advanced-settings.png)

1. ユーザーの情報に基づくシステムの推奨事項をレビュー担当者に表示するには、 **[推奨事項を表示]** を **[有効]** に設定します。

1. レビュー担当者による承認理由の指定を必須にするには、 **[承認時に理由が必要]** を **[有効]** に設定します。

1. アクセス レビュー開始時に Azure AD からレビュー担当者にメール通知を送信し、レビュー完了時に管理者にメール通知を送信するには、 **[メール通知]** を **[有効]** に設定します。

1. レビューを完了していないレビュー担当者に、進行中のアクセス レビューに関するリマインダーを Azure AD から送信するには、 **[リマインダー]** を **[有効]** に設定します。
1. レビュー担当者に送信されるメールの内容は、レビュー名、リソース名、期限などのレビューの詳細に基づいて自動生成されます。追加の指示や連絡先情報などの追加情報を伝達する方法が必要な場合は、割り当てられたレビュー担当者に送信される招待メールと通知メールに含まれる、**レビュー担当者のメールに関する追加のコンテンツ** でこれらの詳細を指定することができます。 下の強調表示されているセクションは、この情報が表示される場所です。

    ![レビュー担当者に送信される電子メールの内容 (ハイライトを含む)](./media/pim-resource-roles-start-access-review/email-info.png)

## <a name="start-the-access-review"></a>アクセス レビューを開始する

アクセス レビューの設定を指定したら、 **[開始]** をクリックします。 アクセス レビューはステータスと共にリストに表示されます。

![開始されたレビューの状態を示す [アクセス レビュー] 一覧](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

既定では、レビューの開始直後に Azure AD からレビュー担当者宛てにメールが送信されます。 Azure AD からメールを送信することを選択しなかった場合は、アクセス レビューが実行待ちになっていることを必ずレビュー担当者に伝えてください。 レビュー担当者には、[Azure リソース ロールのアクセス レビューを実行する](pim-resource-roles-perform-access-review.md)手順を案内することができます。

## <a name="manage-the-access-review"></a>アクセスレビューを管理する

アクセス レビューの **[概要]** ページでは、レビュー担当者が完了したレビューの進捗状況を追跡できます。 ディレクトリのアクセス権は、[レビューが完了する](pim-resource-roles-complete-access-review.md)まで変更されません。

![レビューの詳細を示す [アクセス レビューの概要] ページ](./media/pim-resource-roles-start-access-review/access-review-overview.png)

これが 1 回限りのレビューである場合は、アクセス レビュー期間が終了するか、管理者がアクセス レビューを停止した後に、[Azure リソース ロールのアクセス レビューを完了する](pim-resource-roles-complete-access-review.md)手順に従って結果を確認および適用します。  

アクセス レビューの系列を管理するには、アクセス レビューに移動し、[Scheduled]\(スケジュール済み\) レビューで今後予定されている実行を見つけ、それに応じて終了日を編集したり、レビュー担当者を追加/削除したりします。

**[Upon completion]\(完了時\)** 設定での選択に基づいて、レビューの終了日の後、またはレビューを手動で停止したときに自動適用が実行されます。 レビューの状態は、 **[完了]** から **[適用中]** などの中間状態を経由して、最後に状態 **[適用済み]** まで変化します。 拒否されたユーザーが存在する場合は、それらのユーザーが数分以内にロールから削除されることを確認できます。

## <a name="next-steps"></a>次のステップ

- [Azure リソース ロールのアクセス レビューを実行する](pim-resource-roles-perform-access-review.md)
- [Azure リソース ロールのアクセス レビューを完了する](pim-resource-roles-complete-access-review.md)
- [Azure AD ロールのアクセス レビューを作成する](pim-how-to-start-security-review.md)
