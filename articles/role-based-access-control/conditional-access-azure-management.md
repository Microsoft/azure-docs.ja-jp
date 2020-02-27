---
title: Azure AD で条件付きアクセスを使用して Azure 管理へのアクセスを管理する
description: Azure AD での条件付きアクセスを使用して、Azure 管理へのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137406"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>条件付きアクセスを使用して Azure 管理へのアクセスを管理する

> [!CAUTION]
> Azure 管理へのアクセスを管理するポリシーを設定する前に、条件付きアクセスのしくみについて理解しておくようにしてください。 ポータルへのアクセスをブロックする条件を作成しないようにしてください。

Azure Active Directory (Azure AD) での条件付きアクセスでは、指定した特定の条件に基づいてクラウド アプリへのアクセスが制御されます。 アクセスを許可するには、ポリシーの要件を満たしているかどうかに基づいてアクセスを許可またはブロックする条件付きアクセス ポリシーを作成します。 

通常は、クラウド アプリへのアクセスを制御する場合に条件付きアクセスを使用します。 特定の条件 (サインイン リスク、場所、またはデバイスなど) に基づいて Azure 管理へのアクセスを制御したり、して、多要素認証 のような要件を施行したりするために、ポリシーを設定することもできます。

Azure 管理のポリシーを作成するには、ポリシーを適用するアプリを選択するときに**クラウド アプリ**の **Microsoft Azure Management** を選択します。

![Azure 管理の条件付きアクセス](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

作成したポリシーは、次のようなすべての Azure 管理エンドポイントに適用されます。

- Azure portal
- Azure Resource Manager プロバイダー
- 従来の Service Management API
- Azure PowerShell
- Visual Studio サブスクリプション管理者ポータル
- Azure DevOps
- Azure Data Factory ポータル

ポリシーは Azure Resource Manager API を呼び出す Azure PowerShell に適用されることに注意してください。 Microsoft Graph を呼び出す [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) には適用されません。


条件付きアクセスをセットアップして使用する方法の詳細については、 「[Azure Active Directory での条件付きアクセス](../active-directory/active-directory-conditional-access-azure-portal.md)」を参照してください。
