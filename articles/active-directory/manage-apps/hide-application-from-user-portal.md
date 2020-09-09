---
title: Azure AD でユーザーのエクスペリエンスからエンタープライズ アプリケーションを非表示にする
description: Azure Active Directory アクセス パネルまたは Office 365 ランチャーでユーザーのエクスペリエンスからエンタープライズ アプリケーションを非表示にする方法
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ba14fba24c9b8e0b460e56b93d0e5212bfb27
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85367701"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Azure Active Directory のエンドユーザーに対してエンタープライズ アプリケーションを非表示にする

エンドユーザーの MyApps パネルや Office 365 ランチャーにアプリケーションが表示されないようにする方法について説明します。 アプリケーションを非表示にしても、アプリケーションに対するユーザーのアクセス許可は維持されます。 

## <a name="prerequisites"></a>前提条件

MyApps パネルと Office 365 ランチャーにアプリケーションが表示されないようにするには、アプリケーション管理者特権が必要です。

すべての Office 365 アプリケーションを非表示にするは、グローバル管理者特権が必要です。


## <a name="hide-an-application-from-the-end-user"></a>エンドユーザーに対してアプリケーションを非表示にする
MyApps パネルと Office 365 アプリケーション ランチャーにアプリケーションが表示されないようにするには、次の手順を使用します。

1.  ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
2.  **[Azure Active Directory]** を選択します。
3.  **[エンタープライズ アプリケーション]** を選択します。 **[Enterprise applications - All applications (エンタープライズ アプリケーション - すべてのアプリケーション)]** ブレードが開きます。
4.  **[アプリケーションの種類]** で、 **[エンタープライズ アプリケーション]** を選択します (まだ選択されていない場合)。
5.  非表示にするアプリケーションを検索し、アプリケーションをクリックします。  アプリケーションの概要が表示されます。
6.  **[プロパティ]** をクリックします。 
7.  **[ユーザーに表示しますか?]** という質問に対し、 **[いいえ]** をクリックします。
8.  **[保存]** をクリックします。

> [!NOTE]
> これらの手順は、エンタープライズ アプリケーションにのみ適用されます。

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell を使用してアプリケーションを非表示にする

MyApps パネルからアプリケーションを非表示にするには、アプリケーションのサービス プリンシパルに HideApp タグを手動で追加します。 次の [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) コマンドを実行して、アプリケーションの **[ユーザーに表示しますか?]** プロパティを **[いいえ]** に設定します。 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>MyApps パネルに Office 365 アプリケーションが表示されないようにする

MyApps パネルに一切の Office 365 アプリケーションが表示されないようにするには、次の手順を使用します。 なお、アプリケーションは Office 365 ポータルには表示されます。

1.  ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com) にサインインします。
2.  **[Azure Active Directory]** を選択します。
3.  **[ユーザー]** を選択します。
4.  **[ユーザー設定]** を選択します。
5.  **[エンタープライズ アプリケーション]** で、 **[エンド ユーザーがアプリケーションを起動して表示する方法を管理する]** をクリックします。
6.  **[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** で、 **[はい]** をクリックします。
7.  **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ
* [自分のグループをすべて表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションの名前またはロゴを変更する](change-name-or-logo-portal.md)

