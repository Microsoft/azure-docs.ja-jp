---
title: ライセンスの割り当てと削除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory ライセンスをユーザーまたはグループに割り当てたり、それらのライセンスを削除したりする手順を紹介します。
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 869713967a6a89611ab2ea9ca7b7516f090a5f92
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034281"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルでのライセンスの割り当てと削除

多くの Azure Active Directory (Azure AD) サービスでは、ユーザーまたはグループ (および関連するメンバー) ごとにそのサービスのライセンスを付与する必要があります。 アクティブなライセンスを持つユーザーのみが、該当するライセンスされている Azure AD サービスにアクセスおよび使用できるようになります。

## <a name="available-license-plans"></a>使用可能なライセンス プラン

Azure AD サービスには、次のようないくつかのライセンス プランが用意されています。

- Azure AD Free

- Azure AD Premium P1

- Azure AD Premium P2

各ライセンス プランと関連するライセンスの詳細に関する固有の情報については、「[必要なライセンス](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

すべての Microsoft サービスが、すべての場所で利用できるわけではありません。 ライセンスをグループに割り当てる前に、お客様はすべてのメンバーに対して **[利用場所]** を指定しておく必要があります。 この値は Azure AD の **[Azure Active Directory] &gt; [ユーザー] &gt; [プロファイル] &gt; [設定]** 領域で設定できます。 利用場所が指定されていないユーザーは、Azure AD 組織の場所を継承します。

## <a name="view-license-plans-and-plan-details"></a>ライセンス プランとプランの詳細を表示する

個別のライセンスなどの使用可能なサービス プランを表示し、保留中の有効期限を確認し、利用可能な割り当て数を表示することができます。

### <a name="to-find-your-service-plan-and-plan-details"></a>サービス プランとプランの詳細を確認するには

1. Azure AD 組織のライセンス管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. **[Azure Active Directory]** を選択し、 **[ライセンス]** を選択します。

    ![購入したサービス数と割り当てられたライセンスを示す [ライセンス] ページ](media/license-users-groups/license-details-blade.png)

1. **[購入済み]** のリンクを選択して **[製品]** ページを表示し、ライセンス プランの **[割り当て済み]** 、 **[利用可能]** 、 **[まもなく有効期限切れ]** の数を確認します。

    ![サービス ライセンス プランと関連付けられているライセンス情報を示すサービス ページ](media/license-users-groups/license-products-blade-with-products.png)

1. プラン名を選択して、そのライセンスされているユーザーとグループを確認します。

## <a name="assign-licenses-to-users-or-groups"></a>ユーザーまたはグループにライセンスを割り当てる

ライセンスされている Azure AD サービスを使用する必要がある任意のユーザーに適切なライセンスがあることを確認します。 ライセンスの権利は、ユーザーまたはグループ全体に追加できます。

### <a name="to-assign-a-license-to-a-user"></a>ライセンスをユーザーに割り当てるには

1. **[製品]** ページで、ユーザーに割り当てる必要があるライセンス プラン名を選択します。

    ![サービス ライセンス プランが強調表示されたサービス ページ](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. ライセンス プランの概要ページで、 **[割り当て]** を選択します。

    ![[割り当て] オプションが強調表示されているサービス ページ](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. **[割り当て]** ページで **[ユーザーとグループ]** を選択し、ライセンスを割り当てるユーザーを検索して選択します。

    ![検索と [選択] オプションが強調表示されている [ライセンスの割り当て] ページ](media/license-users-groups/assign-license-blade-with-highlight.png)

1. **[割り当てオプション]** を選択して、適切なライセンス オプションをオンにしていることを確認し、 **[OK]** を選択します。

    ![ライセンス プランで利用可能なすべてのオプションを示している [ライセンス オプション] ページ](media/license-users-groups/license-option-blade-assignments.png)

    **[ライセンスの割り当て]** ページが更新され、ユーザーが選択され、割り当てが構成されていることが示されます。

    > [!NOTE]
    > すべての Microsoft サービスが、すべての場所で利用できるわけではありません。 ライセンスをユーザーに割り当てる前に、お客様は **[利用場所]** を指定しておく必要があります。 この値は Azure AD の **[Azure Active Directory] &gt; [ユーザー] &gt; [プロファイル] &gt; [設定]** 領域で設定できます。 利用場所が指定されていないユーザーは、Azure AD 組織の場所を継承します。

1. **[割り当て]** を選択します。

    ユーザーがライセンスされているユーザーの一覧に追加され、含まれる Azure AD サービスにアクセスできます。

### <a name="to-assign-a-license-to-a-group"></a>ライセンスをグループに割り当てるには

1. **[製品]** ページで、ユーザーに割り当てる必要があるライセンス プラン名を選択します。

    ![製品ライセンス プランが強調表示されている [製品] ブレード](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. **[Azure Active Directory Premium プラン 2]** ページで、 **[割り当て]** を選択します。

    ![[割り当て] オプションが強調表示されている [製品] ページ](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. **[割り当て]** ページで **[ユーザーとグループ]** を選択し、ライセンスを割り当てるグループを検索して選択します。

    ![検索と [選択] オプションが強調表示されている [ライセンスの割り当て] ページ](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. **[割り当てオプション]** を選択して、適切なライセンス オプションをオンにしていることを確認し、 **[OK]** を選択します。

    ![ライセンス プランで利用可能なすべてのオプションを示している [ライセンス オプション] ページ](media/license-users-groups/license-option-blade-group-assignments.png)

    **[ライセンスの割り当て]** ページが更新され、ユーザーが選択され、割り当てが構成されていることが示されます。

1. **[割り当て]** を選択します。

    グループはライセンスされているグループのリストに追加され、そのすべてのメンバーが含まれる Azure AD サービスにアクセスできます。

## <a name="remove-a-license"></a>ライセンスを削除する

ライセンスの削除は、ユーザーの Azure AD ユーザー ページから行ったり、グループ割り当ての場合はグループの概要ページから行ったり、ライセンスに対するユーザーとグループを表示できる Azure AD の **[ライセンス]** ページから開始したりすることもできます。

### <a name="to-remove-a-license-from-a-user"></a>ユーザーからライセンスを削除するには

1. サービス プランの **[ライセンスされているユーザー]** ページで、ライセンスが必要なくなったユーザーを選択します。 たとえば、_Alain Charon_ を選びます。

1. **[ライセンスを削除する]** を選択します。

    ![[ライセンスを削除する] オプションが強調表示されている [ライセンスされているユーザー] ページ](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> ユーザーがグループから継承したライセンスを直接削除することはできません。 代わりに、ライセンスを継承したグループからユーザーを削除する必要があります。

### <a name="to-remove-a-license-from-a-group"></a>グループからライセンスを削除するには

1. ライセンス プランの **[ライセンスされているグループ]** ページで、ライセンスが必要なくなったグループを選択します。

1. **[ライセンスを削除する]** を選択します。

    ![[ライセンスを削除する] が強調表示されている [ライセンスされているグループ] ページ](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)

## <a name="next-steps"></a>次の手順

ライセンスを割り当てた後、次のプロセスを実行できます。

- [ライセンスの割り当てに関する問題を特定して解決する](../users-groups-roles/licensing-groups-resolve-problems.md)

- [ライセンス付与の目的でライセンスされているユーザーをグループに追加する](../users-groups-roles/licensing-groups-migrate-users.md)

- [Azure Active Directory のライセンス管理にグループを使用する際のシナリオ、制限、および既知の問題](../users-groups-roles/licensing-group-advanced.md)

- [ユーザー プロファイル情報の追加または変更](active-directory-users-profile-azure-portal.md)
