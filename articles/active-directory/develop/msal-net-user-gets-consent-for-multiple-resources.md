---
title: いくつかのリソースの同意を得る (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library for .NET (MSAL.NET) を使用して、ユーザーが複数のリソースの事前承認を取得できる方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 6333d935e1a902ba173017f8149c098f44398955
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165874"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>ユーザーによる MSAL.NET を使用した複数リソースの承認の取得
Microsoft ID プラットフォーム エンドポイントは、ユーザーが一度に複数のリソースのトークンを取得することを許可しません。 Microsoft Authentication Library for .NET (MSAL.NET) を使用する場合、トークンの取得メソッドのスコープ パラメーターには、単一リソースのスコープのみが含まれている必要があります。 ただし、`.WithExtraScopeToConsent` ビルダー メソッドを使用して追加のスコープを指定することで、あらかじめ複数のリソースを事前承認できます。

> [!NOTE]
> 複数のリソースの承認の取得は、Microsoft ID プラットフォームでは対応していますが、Azure AD B2C では対応していません。 Azure AD B2C では、管理者による承認のみがサポートされており、ユーザーによる承認はサポートされていません。

たとえば、それぞれ 2 つのスコープを持つ 2 つのリソースがある場合:

- https:\//mytenant.onmicrosoft.com/customerapi (2 つのスコープ: `customer.read` と `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (2 つのスコープ: `vendor.read` と `vendor.write`)

次の例に示すように、*extraScopesToConsent* パラメーターを指定した `.WithExtraScopeToConsent` 修飾子を使用する必要があります。

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

これにより、最初の Web API のアクセス トークンが取得されます。 次に、2 番目の Web API へのアクセスが必要になると、トークン キャッシュから自動的にトークンを取得できます。

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
