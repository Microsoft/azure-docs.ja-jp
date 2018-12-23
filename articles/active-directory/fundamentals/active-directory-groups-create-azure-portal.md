---
title: 基本グループを作成してメンバーを追加する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用した基本グループの作成方法に関する手順です。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.openlocfilehash: be3271a1105e8222ae04e17140c6e925a1c612f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105455"
---
# <a name="create-a-basic-group-and-add-members-using-azure-active-directory"></a>Azure Active Directory を使用して基本グループを作成してメンバーを追加する
Azure Active Directory (Azure AD) ポータルを使用して、基本グループを作成できます。 この記事の目的に合わせて、基本グループは、リソース所有者 (管理者) によって単一リソースに追加され、そのリソースにアクセスする必要がある特定のメンバー (従業員) を含みます。 動的なメンバーシップやルールの作成など、より複雑なシナリオについては、「[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)」を参照してください。

## <a name="create-a-basic-group-and-add-members"></a>基本グループを作成してメンバーを追加する
基本グループを作成し、それと同時にメンバーを追加できます。

### <a name="to-create-a-basic-group-and-add-members"></a>基本グループを作成してメンバーを追加するには、次の手順を実行します。
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]**、**[グループ]**、**[新しいグループ]** と選択します。

    ![[グループ] を表示した Azure AD](media/active-directory-groups-create-azure-portal/group-full-screen.png)

3. **[グループ]** ページで、必要な情報を入力します。

    ![情報の例が入力されている新しいグループ ページ](media/active-directory-groups-create-azure-portal/new-group-blade.png)

    - **[グループの種類] (必須)。** 事前に定義されたグループの種類を選択します。 次のトピックがあります。
        
        - **セキュリティ**。 メンバーを管理し、グループ ユーザーの共有リソースへのコンピューター アクセスを管理するために、使用されます。 たとえば、特定のセキュリティ ポリシーのセキュリティ グループを作成できます。 この方法で作成すると、すべてのメンバーに一連のアクセス許可を一度に付与でき、各メンバーにアクセス許可を個別に追加する必要はありません。 リソースへのアクセス管理の詳細については、[Azure Active Directory グループによるリソースへのアクセス管理](active-directory-manage-groups.md)に関するページを参照してください。
        
        - **Office 365**。 共有メールボックス、カレンダー、ファイル、SharePoint サイトなどへのアクセスをメンバーに付与することで、共同作業の機会を提供します。 また、このオプションでは、組織外のユーザーにグループへのアクセス権を付与することもできます。 Office 365 グループの詳細については、「[Office 365 グループの概要](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2)」を参照してください。

    - **[グループ名] (必須)。** グループの名前を追加します。覚えやすい、意味のある名前にします。

    - **[グループの説明]。** 任意で、グループに説明を追加します。

    - **[メンバーシップの種類] (必須)。** 事前に定義されたメンバーシップの種類を選択します。 次のトピックがあります。

        - **[割り当て済み]。** このグループのメンバーとなり、一意のアクセス許可を保持するように、特定のメンバーを追加できます。 この記事の目的に合わせて、このオプションを使用しています。

        - **[動的ユーザー]。** 動的なグループ ルールを使用して、自動的にメンバーを追加および削除できます。 メンバーの属性が変更されると、システムは、ディレクトリの動的なグループ ルールを確認して、そのメンバーがルール要件を満たしているか (追加される)、またはルール要件を満たさなくなったか (削除される) を判定します。

        - **[動的デバイス]。** 動的なグループ ルールを使用して、自動的にデバイスを追加および削除できます。 デバイスの属性が変更されると、システムは、ディレクトリの動的なグループ ルールを確認して、そのデバイスがルール要件を満たしているか (追加される)、またはルール要件を満たさなくなったか (削除される) を判定します。

        >[!Important]
        >デバイスまたはユーザーのどちらかに対して動的グループを作成することは可能ですが、両方に対して作成することはできません。 また、デバイス所有者の属性に基づいてデバイス グループを作成することはできません。 デバイス メンバーシップ ルールで参照できるのは、デバイスの属性のみです。 ユーザーとデバイスの動的グループの作成に関する詳細については、「[動的グループの作成と状態チェックを行う](../users-groups-roles/groups-create-rule.md)」を参照してください。

4. **作成**を選択します。

    グループが作成され、メンバーを追加する準備ができました。

5. **[グループ]** ページから **[メンバー]** 領域を選択して、**[メンバーの選択]** ページからグループに追加するメンバーの検索を開始します。

    ![グループの作成プロセスの間に、グループのメンバーを選択する](media/active-directory-groups-create-azure-portal/select-members-create-group.png)

6. メンバーの追加が完了したら、**[選択]** をクリックします。

    **[グループの概要]** ページが更新され、グループに追加されたメンバー数が表示されるようになりました。

    ![[グループの概要] ページで、メンバー数が強調されている](media/active-directory-groups-create-azure-portal/group-overview-blade-number-highlight.png)

## <a name="next-steps"></a>次の手順
グループと、少なくとも 1 人のユーザーを追加したので、次の作業が可能になりました。

- [グループとメンバーを表示する](active-directory-groups-view-azure-portal.md)

- [グループ メンバーシップを管理する](active-directory-groups-membership-azure-portal.md)

- [グループ内のユーザーの動的ルールの管理](../users-groups-roles/groups-create-rule.md)

- [グループの設定を編集する](active-directory-groups-settings-azure-portal.md)

- [グループを使用してリソースへのアクセスを管理する](active-directory-manage-groups.md)

- [グループを使用して SaaS アプリへのアクセスを管理する](../users-groups-roles/groups-saasapps.md)

- [PowerShell コマンドを使用してグループを管理する](../users-groups-roles/groups-settings-v2-cmdlets.md)

- [Azure サブスクリプションを Azure Active Directory に関連付けまたは追加する](active-directory-how-subscriptions-associated-directory.md)