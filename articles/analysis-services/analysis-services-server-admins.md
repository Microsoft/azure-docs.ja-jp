---
title: "Azure Analysis Services でのサーバー管理者の管理 | Microsoft Docs"
description: "Azure で Analysis Services サーバーのサーバー管理者を管理する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25c8b49401486edfad0a1a60fe2258a586f6adc2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017

---
# <a name="manage-server-administrators"></a>サーバー管理者の管理
サーバー管理者は、サーバーが存在するテナントについて、Azure Active Directory (Azure AD) 内で有効なユーザーまたはグループである必要があります。 Azure Portal のサーバー用コントロール ブレードで **[Analysis Services 管理者]** を使用するか、SSMS のサーバー プロパティを使用して、サーバー管理者を管理できます。 

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Azure Portal を使用してサーバー管理者を追加するには
1. サーバーのコントロール ブレードで **[Analysis Services 管理者]** をクリックします。
2. **[\<サーバー名> - Analysis Services 管理者]** ブレードで、**[追加]** をクリックします。
3. **[サーバー管理者を追加します]** ブレードで、Azure AD からユーザー アカウントを選択するか、メール アドレスで外部のユーザーを招待します。

    ![Azure Portal のサーバー管理者](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>SSMS を使用してサーバー管理者を追加するには
1. サーバーを右クリックして、**[プロパティ]** をクリックします。
2. **[分析サーバーのプロパティ]** で **[セキュリティ]** をクリックします。
3. **[追加]** をクリックし、Azure AD のユーザーまたはグループのメール アドレスを入力します。
   
    ![SSMS でのサーバー管理者の追加](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="next-steps"></a>次のステップ 
[認証とユーザーのアクセス許可](analysis-services-manage-users.md)  
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)  


