---
title: Azure Active Directory でロールを割り当てるためのグループを作成する | Microsoft Docs
description: ID 管理を委任するためのカスタム Azure AD ロールをプレビューします。 Azure portal、PowerShell、または Graph API で Azure ロールを管理します。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/27/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f816c95b5e210e86e2215cadf8407f442d69ebe
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475909"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Azure Active Directory でロールを割り当て可能なグループを作成する

ロールを割り当てることができるグループは、'isAssignableToRole' プロパティを True に設定して作成したものか、Azure AD ポータルで **[グループに Azure AD ロールを割り当てることができる]** をオンにして作成したものだけです。 このグループ属性を使用すると、そのグループを Azure Active Directory (Azure AD) のロールに割り当てることができるようになります。 この記事では、この特別な種類のグループを作成する方法について説明します。

## <a name="using-azure-ad-admin-center"></a>Azure AD 管理センターの使用

1. Azure AD 組織の特権ロール管理者またはグローバル管理者のアクセス許可を使用して [Azure AD 管理センター](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)にサインインします。
1. **[グループ]**  >  **[すべてのグループ]**  >  **[新しいグループ]** の順に選択します。

    [![Azure Active Directory を開いて新しいグループを作成する。](media/roles-groups-create-eligible/new-group.png "Azure Active Directory を開いて新しいグループを作成する。")](media/roles-groups-create-eligible/new-group.png#<lightbox>)

1. **[新しいグループ]** タブで、グループの種類、名前、説明を指定します。
1. **[グループに Azure AD ロールを割り当てることができる]** をオンにします。 このスイッチは、特権ロール管理者とグローバル管理者にのみ表示されます。この 2 つのロールのみが、このスイッチを設定できます。

    [![新しいグループをロールの割り当ての対象にする](media/roles-groups-create-eligible/eligible-switch.png "新しいグループをロールの割り当ての対象にする")](media/roles-groups-create-eligible/eligible-switch.png#<lightbox>)

1. グループのメンバーと所有者を選択します。 オプションでグループにロールを割り当てることもできますが、こちらでロールを割り当てる必要はありません。

    [![ロールを割り当て可能なグループにメンバーを追加し、ロールを割り当てる。](media/roles-groups-create-eligible/specify-members.png "ロールを割り当て可能なグループにメンバーを追加し、ロールを割り当てる。")](media/roles-groups-create-eligible/specify-members.png#<lightbox>)

1. メンバーと所有者を指定したら、 **[作成]** を選択します。

    [![ページの下部にある [作成] ボタン。](media/roles-groups-create-eligible/create-button.png "ページの下部にある [作成] ボタン。")](media/roles-groups-create-eligible/create-button.png#<lightbox>)

ロールを割り当てていた場合にはそれが付いた状態で、グループが作成されます。

## <a name="using-powershell"></a>PowerShell の使用

### <a name="install-the-azure-ad-preview-module"></a>Azure AD プレビュー モジュールをインストールする

```powershell
install-module azureadpreview
import-module azureadpreview
```

モジュールを使用する準備ができているかどうかを確認するため、次のコマンドを実行します。

```powershell
get-module azureadpreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>ロールに割り当てることができるグループを作成する

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

この種類のグループでは、`isPublic` は常に false、`isSecurityEnabled` は常に true になります。

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>ロールを割り当て可能なグループに 1 つのグループのユーザーとサービス プリンシパルをコピーする

```powershell
#Basic set up
install-module azureadpreview
import-module azureadpreview
get-module azureadpreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating O365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph API の使用

### <a name="create-a-role-assignable-group-in-azure-ad"></a>ロールを割り当て可能なグループを Azure AD で作成する

```powershell
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

この種類のグループでは、`isPublic` は常に false、`isSecurityEnabled` は常に true になります。

## <a name="next-steps"></a>次のステップ

- [クラウド グループにロールを割り当てる](roles-groups-assign-role.md)
- [クラウド グループを使用してロールの割り当てを管理する](roles-groups-concept.md)
- [クラウド グループに割り当てられているロールのトラブルシューティング](roles-groups-faq-troubleshooting.md)
