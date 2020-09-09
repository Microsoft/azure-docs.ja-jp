---
title: 条件付きアクセス ポリシーのユーザーおよびグループ - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーのユーザーおよびグループとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba1fc856ee9093b628bd86b9847f8fc70b7189c2
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552902"
---
# <a name="conditional-access-users-and-groups"></a>条件付きアクセス:ユーザーとグループ

条件付きアクセス ポリシーには、決定プロセスのシグナルの 1 つとしてユーザー割り当てが含まれている必要があります。 ユーザーは条件付きアクセス ポリシーに含めることも除外することもできます。 

![条件付きアクセスによって下される決定のシグナルとしてのユーザー](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>ユーザーを含める

このユーザーの一覧には、通常、組織が条件付きアクセス ポリシーの対象としているすべてのユーザーが含まれます。 

条件付きアクセス ポリシーの作成時には、含めるために以下のオプションを使用できます。

- なし
   - どのユーザーも選択されていません
- すべてのユーザー
   - ディレクトリ内に存在するすべてのユーザー (B2B ゲストを含む)。
- ユーザーとグループを選択
   - すべてのゲストと外部ユーザー
      - この選択には、`user type` 属性が `guest` に設定されているすべてのユーザーを含めて、すべての B2B ゲストと外部ユーザーが含まれます。 この選択は、クラウド ソリューション プロバイダー (CSP) のような別の組織からサインインしたすべての外部ユーザーにも適用されます。 
   - ディレクトリ ロール
      - 管理者は、割り当てを決定するために使用される特定の Azure AD ディレクトリ ロールを選択できます。 たとえば組織で、全体管理者ロールを割り当てられるユーザーに対してより制限の厳しいポリシーを作成する場合があります。
   - ユーザーとグループ
      - 特定のユーザーのセットを対象にできます。 たとえば組織で人事部アプリがクラウド アプリとして選択されている場合は、人事部のすべてのメンバーを含むグループを選択できます。 Azure AD 内の任意の種類のグループを指定できます。これには、動的なグループや、割り当て済みのセキュリティ グループおよび配布グループが含まれます。 ポリシーは、入れ子になったユーザーおよびグループに適用されます。

> [!WARNING]
> ユーザーまたはグループが 2048 を超えるグループのメンバーである場合、そのアクセスはブロックされる可能性があります。 この制限は、直接と入れ子の両方のグループ メンバーシップに適用されます。

> [!WARNING]
> 条件付きアクセス ポリシーは、[管理単位にスコープ指定されている](../users-groups-roles/roles-admin-units-assign-roles.md)ディレクトリ ロール、または ([カスタム ロール](../users-groups-roles/roles-create-custom.md)経由などで) オブジェクトに直接スコープ指定されているディレクトリ ロールが割り当てられているユーザーをサポートしません。

## <a name="exclude-users"></a>ユーザーを除外する

組織がユーザーまたはグループの追加と除外の両方を実行すると、ポリシーでは除外アクションが追加よりも優先されるため、ユーザーまたはグループがポリシーから除外されます。 除外は一般的に、緊急アクセス アカウントや非常用アカウントのために使用されます。 緊急アクセス アカウントとそれが重要である理由の詳細については、以下の記事を参照してください。 

* [Azure AD で緊急アクセス用アカウントを管理する](../users-groups-roles/directory-emergency-access.md)
* [Azure Active Directory で回復性があるアクセス制御管理戦略を作成する](../authentication/concept-resilient-controls.md)

条件付きアクセス ポリシーの作成時には、除外するために以下のオプションを使用できます。

- すべてのゲストと外部ユーザー
   - この選択には、`user type` 属性が `guest` に設定されているすべてのユーザーを含めて、すべての B2B ゲストと外部ユーザーが含まれます。 この選択は、クラウド ソリューション プロバイダー (CSP) のような別の組織からサインインしたすべての外部ユーザーにも適用されます。 
- ディレクトリ ロール
   - 管理者は、割り当てを決定するために使用される特定の Azure AD ディレクトリ ロールを選択できます。 たとえば組織で、全体管理者ロールを割り当てられるユーザーに対してより制限の厳しいポリシーを作成する場合があります。
- ユーザーとグループ
   - 特定のユーザーのセットを対象にできます。 たとえば組織で人事部アプリがクラウド アプリとして選択されている場合は、人事部のすべてのメンバーを含むグループを選択できます。 Azure AD 内の任意の種類のグループを指定できます。これには、動的なグループや、割り当て済みのセキュリティ グループおよび配布グループが含まれます。

### <a name="preventing-administrator-lockout"></a>管理者のロックアウトを防ぐ

**すべてのユーザー**および**すべてのアプリ**に適用されるポリシーを作成するときに、管理者自身が自分のディレクトリからロックアウトされるのを防ぐために、次の警告が表示されます。

> 自分自身をロックアウトしないでください。 まずは少数のユーザーにポリシーを適用して、想定どおりに動作するかどうかを確認することをお勧めします。 また、このポリシーから少なくとも 1 人の管理者を除外することをお勧めします。 こうすることで、アクセス権を保持し、変更が必要な場合にポリシーを更新できます。 影響を受けるユーザーとアプリを確認してください。

既定では、ポリシーには現在のユーザーをポリシーから除外するオプションが用意されていますが、次の図に示すように、管理者はこの既定値を上書きできます。 

![警告。自分自身をロックアウトしないでください。](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:クラウド アプリまたはアクション](concept-conditional-access-cloud-apps.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
