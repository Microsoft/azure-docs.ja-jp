---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
author: rolyon
ms.service: active-directory
ms.topic: include
ms.date: 05/26/2020
ms.author: barclayn
ms.custom: include file
ms.openlocfilehash: ee02200f1fb32d430a858d218e27534561ebc24a
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83868055"
---
## <a name="create-one-or-more-access-reviews"></a>1 つ以上のアクセス レビューを作成する

1. **[新規]** をクリックして、新しいアクセス レビューを作成します。

1. アクセス レビューに名前を付けます。 必要に応じて、そのレビューに説明を加えます。 その名前と説明がレビュアーに示されます。

    ![アクセス レビューの作成 - レビューの名前と説明](./media/active-directory-privileged-identity-management-access-reviews/name-description.png)

1. **[開始日]** を設定します。 既定では、アクセス レビューは 1 回実行されます。作成されたのと同じ時間に開始され、1 か月で終了します。 この開始日と終了日を変更することで、アクセス レビューを後で開始し、必要な日数を好きなだけ確保することができます。

    ![開始日、頻度、期間、終了、回数、および終了日](./media/active-directory-privileged-identity-management-access-reviews/start-end-dates.png)

1. アクセス レビューを繰り返すには、 **[頻度]** 設定を **[1 回]** から **[毎週]** 、 **[毎月]** 、 **[四半期に 1 回]** 、 **[毎年]** 、 **[Semi-annually]\(半年に 1 回\)** に変更します。 **[期間]** スライダーまたはテキスト ボックスを使用し、繰り返しの系列の各レビューでレビュー担当者からの入力を受け付ける日数を決めます。 たとえば、レビューの重複を避けるために、月 1 回のレビューに設定できる最大期間は 27 日です。

1. **[終了]** の設定を使用して、アクセス レビューの繰り返し系列を終了する方法を指定します。 系列は 3 つの方法で終了できます。つまり、継続的に実行して無期限にレビューを開始する、特定の日付まで実行する、または定義された実行回数が完了するまで実行する、です。 あなた自身、別のユーザー管理者、または別の全体管理者は、 **[設定]** で日付を変更してその日付に終了するように指定することで、作成後に系列を停止することができます。

1. **[ユーザー]** セクションで、メンバーシップをレビューする 1 つ以上のロールを選択します。

    ![ロール メンバーシップをレビューするためのユーザー スコープ](./media/active-directory-privileged-identity-management-access-reviews/users.png)

    > [!NOTE]
    > - ここで選択されるロールには、[永続的なロールと資格のあるロール](../articles/active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)の両方が含まれます。
    > - 複数のロールを選択すると、複数のアクセス レビューが作成されます。 たとえば、5 つのロールを選択すると、5 つの別々のアクセス レビューが作成されます。

    **Azure AD ロール**のアクセス レビューを作成する場合の、レビューのメンバーシップ一覧の例を次に示します。

    ![選択できる Azure AD ロールを一覧表示する [レビューのメンバーシップ] ウィンドウ](./media/active-directory-privileged-identity-management-access-reviews/review-membership.png)

    **Azure リソース ロール**のアクセス レビューを作成する場合の、レビューのメンバーシップ一覧の例を次の図に示します。

    ![選択できる Azure リソース ロールを一覧表示する [レビューのメンバーシップ] ウィンドウ](./media/active-directory-privileged-identity-management-access-reviews/review-membership-azure-resource-roles.png)

1. **[レビュー担当者]** セクションで、全ユーザーをレビューする担当者 (複数可) を選びます。 メンバー自身にそのアクセス権をレビューしてもらうことができます。

    ![選択したユーザーまたはメンバー (セルフ) のレビュー担当者の一覧](./media/active-directory-privileged-identity-management-access-reviews/reviewers.png)

    - **選択したユーザー** - アクセスする必要があるユーザーがわからない場合は、このオプションを使用します。 このオプションでは、リソース所有者またはグループ マネージャーにレビューを割り当て、完了してもらうことができます。
    - **メンバー (セルフ)** - ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。

### <a name="upon-completion-settings"></a>完了時の設定

1. レビュー完了後の動作を指定するには、 **[設定完了時]** セクションを展開します。

    ![適用を自動化するための [設定完了時] および [レビュー担当者からの応答なし]](./media/active-directory-privileged-identity-management-access-reviews/upon-completion-settings.png)

1. 拒否されたユーザーのアクセスを自動的に削除する場合は、 **[リソースへの結果の自動適用]** を **[有効]** に設定します。 レビューが完了したときに結果を手動で適用する場合は、スイッチを **[無効]** に設定します。

1. レビューの期間内にレビュー担当者によってレビューされなかったユーザーに対する処理を指定するには、 **[レビュー担当者からの応答なし]** の一覧を使用します。 この設定は、レビュー担当者によって手動でレビューされたユーザーには影響しません。 レビュー担当者の最終的な決定が拒否である場合、ユーザーのアクセスは削除されます。

    - **[変更なし]** - ユーザーのアクセスをそのまま変更しないでおきます
    - **[アクセスの削除]** - ユーザーのアクセスを削除します
    - **[アクセスを承認する]** - ユーザーのアクセスを承認します
    - **[推奨事項の実行]** - ユーザーの継続的なアクセスの拒否または承認に関するシステムの推奨事項を実行します

### <a name="advanced-settings"></a>詳細設定

1. 他の設定を指定するには、 **[詳細設定]** セクションを展開します。

    ![推奨事項の表示、承認理由の必須化、メール通知、およびリマインダーのための [詳細設定]](./media/active-directory-privileged-identity-management-access-reviews/advanced-settings.png)

1. ユーザーの情報に基づくシステムの推奨事項をレビュー担当者に表示するには、 **[推奨事項を表示]** を **[有効]** に設定します。

1. レビュー担当者による承認理由の指定を必須にするには、 **[承認時に理由が必要]** を **[有効]** に設定します。

1. アクセス レビュー開始時に Azure AD からレビュー担当者にメール通知を送信し、レビュー完了時に管理者にメール通知を送信するには、 **[メール通知]** を **[有効]** に設定します。

1. レビューを完了していないレビュー担当者に、進行中のアクセス レビューに関するリマインダーを Azure AD から送信するには、 **[リマインダー]** を **[有効]** に設定します。
