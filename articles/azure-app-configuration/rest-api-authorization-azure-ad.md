---
title: Azure App Configuration REST API - Azure Active Directory 認証
description: REST API を使用した Azure App Configuration に対する認可に Azure Active Directory を使用する
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: f144c1f09f87e0b915daf86ba0391c2934e60095
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932678"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory 認可 - REST API リファレンス

Azure Active Directory (Azure AD) 認証を使用する場合、認可は Azure ロール ベースのアクセス制御 (RBAC) によって処理されます。 RBAC では、リソースへのアクセスを付与するために、ユーザーがロールに割り当てられている必要があります。 各ロールには、ロールに割り当てられたユーザーが実行できる一連のアクションが含まれています。

## <a name="roles"></a>ロール

Azure サブスクリプションで既定で使用できるロールを次に示します。

- **Azure App Configuration データ所有者**:このロールでは、すべての操作に対するフルアクセスを提供します。
- **Azure App Configuration データ閲覧者**:このロールでは、読み取り操作が有効になります。

## <a name="actions"></a>Actions

ロールには、そのロールに割り当てられたユーザーが実行できるアクションの一覧が含まれています。 Azure App Configuration では、以下のアクションがサポートされています。

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`:このアクションは、App Configuration のキー値リソース (/kv や /labels など) に対する読み取りアクセスを許可します。
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`:このアクションでは、App Configuration のキー値リソースに対する書き込みアクセスを許可します。
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`:このアクションでは、App Configuration のキー値リソースを削除できます。 リソースを削除すると、削除されたキー値が返されることにご注意ください。

## <a name="error"></a>エラー

```http
HTTP/1.1 403 Forbidden
```

**理由:** 要求元のプリンシパルには、要求された操作を実行するために必要なアクセス許可がありません。
**解決方法:** 要求された操作を実行するために必要なロールを、要求元のプリンシパルに割り当てます。

## <a name="managing-role-assignments"></a>ロールの割り当ての管理

ロールの割り当ての管理は、すべての Azure サービスで標準となっている [RBAC の手順](../role-based-access-control/overview.md)を使用して行えます。 これを行うには、Azure CLI、PowerShell、および Azure portal を使用します。 詳細については、「[Azure portal を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-portal.md)」を参照してください。