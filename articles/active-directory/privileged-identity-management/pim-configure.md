---
title: Privileged Identity Management とは? - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) の概要について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 08/08/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4a081bc5a8040fa3ccd0599341eb2671bbe2d4d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047821"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management とは

 Privileged Identity Management (PIM) は、お客様の組織内の重要なリソースへのアクセスを管理、制御、監視することができる、Azure Active Directory (Azure AD) のサービスです。 これらのリソースには、Azure AD、Azure、およびその他の Microsoft Online Services (Office 365、Microsoft Intune など) 内のリソースが含まれます。

## <a name="reasons-to-use"></a>使用する理由

組織では、セキュリティで保護された情報またはリソースへのアクセス権を持つユーザーの数を最小限に抑える必要があります。そうすることで、悪意のあるアクターがこのようなアクセス権を手にしたり、許可されているユーザーの不注意で機密性の高いリソースに影響が及んだりする可能性が抑えられるためです。 しかし一方で、ユーザーは Azure AD、Azure、Office 365、または SaaS アプリケーションで特権操作を実行する必要があります。 組織は、Azure リソースおよび Azure AD への Just-In-Time の特権アクセスをユーザーに付与できます。 そこで、そうしたユーザーが管理者特権で何をしているかについて監視することが必要となります。

## <a name="what-does-it-do"></a>内容

Privileged Identity Management では、時間ベースおよび承認ベースのロールのアクティブ化を提供して、対象リソースに対する過剰、不要、または誤用であるアクセス許可のリスクを軽減します。 以下に、Privileged Identity Management の主な機能をいくつか示します。

- Azure AD と Azure のリソースに対する **Just-In-Time** の特権アクセスを提供する
- 開始日と終了日を使用した**期限付き**アクセス権をリソースに割り当てる
- 特権ロールをアクティブ化するために**承認**を要求する
- ロールをアクティブ化するために**多要素認証**を強制する
- なぜユーザーをアクティブ化するのかを把握するために**理由**を使用する
- 特権ロールがアクティブ化されたときに**通知**を受ける
- 継続してユーザーにロールが必要であることを確認するために**アクセス レビュー**を実施する
- 社内監査または外部監査に使用する**監査履歴**をダウンロードする

## <a name="what-can-i-do-with-it"></a>これによってできること

Privileged Identity Management を設定すると、左側のナビゲーション メニューに **[タスク]** 、 **[管理]** 、 **[アクティビティ]** の各オプションが表示されます。 お客様は管理者として、**Azure AD ロール**の管理、**Azure リソース** ロールの管理、特権アクセス グループなどのオプションを選択することになります。 管理対象を選択すると、そのオプションに対する適切な一連のオプションが表示されます。

![Azure portal での Privileged Identity Management のスクリーンショット](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>だれが何をできるか

Privileged Identity Management での Azure AD ロールの場合、他の管理者の割り当てを管理できるのは、特権ロール管理者またはグローバル管理者ロールに属しているユーザーだけです。 [Privileged Identity Management を管理するために、他の管理者にアクセス権を付与する](pim-how-to-give-access-to-pim.md)ことができます。 グローバル管理者、セキュリティ管理者、グローバル閲覧者、およびセキュリティ閲覧者も、Privileged Identity Management における Azure AD ロールへの割り当てを表示することができます。

Privileged Identity Management での Azure リソースのロールの場合、サブスクリプション管理者、リソース所有者、またはリソース ユーザー アクセス管理者だけが、他の管理者の割り当てを管理できます。 既定では、特権ロール管理者、セキュリティ管理者、またはセキュリティ閲覧者であるユーザーは、Privileged Identity Management での Azure リソースのロールへの割り当てを表示することはできません。

## <a name="scenarios"></a>シナリオ

Privileged Identity Management では、次のシナリオがサポートされています。

### <a name="privileged-role-administrator-permissions"></a>特権ロール管理者のアクセス許可

- 特定のロールの承認を有効化する
- 要求を承認するユーザーまたはグループを指定する
- すべての特権ロールの要求と承認の履歴を表示する

### <a name="approver-permissions"></a>承認者のアクセス許可

- 保留中の承認 (要求) を表示する
- ロールの昇格の要求を承認または拒否する (単独および一括)
- 自分の承認または却下の理由を説明する

### <a name="eligible-role-user-permissions"></a>有資格のロール ユーザーのアクセス許可

- 承認が必要なロールのアクティブ化を要求する
- アクティブ化要求の状態を表示する
- アクティブ化が承認された場合に Azure AD でタスクを完了する

## <a name="terminology"></a>用語

Privileged Identity Management とそのドキュメントについてより深く理解するために、次の用語を確認してください。

| 用語または概念 | ロールの割り当てのカテゴリ | 説明 |
| --- | --- | --- |
| 有資格 | Type | ロールを使用するためにユーザーが少なくとも 1 つのアクションを実行する必要があるロールの割り当て。 あるロールに対して資格があるとは、特権タスクを実行する必要が生じたときに、ユーザーがそのロールをアクティブ化できることを意味します。 ロールへの永続的な割り当てと、ロールの候補としての割り当てに、アクセスの違いはありません。 常時のアクセスを必要としないユーザーがいる、というのが唯一の違いです。 |
| active | Type | ロールを使用するためにユーザーが何のアクションも実行する必要がないロールの割り当て。 アクティブとして割り当てられたユーザーは、そのロールに割り当てられた特権を持ちます。 |
| アクティブ化 |  | ユーザーに資格のあるロールを使用するために、1 つまたは複数のアクションを実行するプロセス。 要求されるアクションには、多要素認証 (MFA) チェックの実行、業務上の妥当性の指定、指定された承認者に対する承認要求などがあります。 |
| 割り当て済み | State | アクティブなロールの割り当てを持つユーザー。 |
| アクティブ化済み | State | ロールの割り当ての資格を持ち、ロールをアクティブ化するためのアクションを実行して、アクティブになったユーザー。  アクティブになったユーザーは、事前構成済みの期間、そのロールを使用することができ、その期間の経過後は再度アクティブ化する必要があります。 |
| 永続的に有資格 | Duration | ロールをアクティブ化する資格が常にユーザーにあるロールの割り当て。 |
| 永続的にアクティブ | Duration | 何もアクションを実行しなくても、ユーザーがロールを常に使用できるロールの割り当て。 |
| 有効期限付きの有資格 | Duration | ユーザーに、指定された開始日と終了日の範囲内でロールをアクティブ化する資格があるロールの割り当て。 |
| 有効期限付きアクティブ | Duration | 指定された開始日と終了日の範囲内であれば、何もアクションを実行しなくてもユーザーがロールを使用できるロールの割り当て。 |
| Just-In-Time (JIT) アクセス |  | 悪意のあるユーザーまたは未承認ユーザーがアクセス許可の期限が切れた後にアクセスできないように、ユーザーに特権タスクを実行する一時的なアクセス許可を与えるモデル。 ユーザーが必要な場合にのみ、アクセスが許可されます。 |
| 最小特権アクセスの原則 |  | すべてのユーザーに、実行権限があるタスクを実行するのに必要な最小特権のみを与える、推奨されるセキュリティ プラクティス。 このプラクティスではグローバル管理者の数を最小限にし、代わりに特定のシナリオで特定の管理者ロールが使用されます。 |

## <a name="license-requirements"></a>ライセンスの要件

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

ユーザーのライセンスについては、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)
- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management をデプロイする](pim-deployment-plan.md)
