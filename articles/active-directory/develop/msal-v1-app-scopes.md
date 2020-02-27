---
title: v1.0 アプリ用のスコープ (MSAL) | Azure
description: Microsoft Authentication Library (MSAL) を使用した v1.0 アプリケーションのスコープについて説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: e0ef46d23ba267bbfc1ff539eb659fafe2b44f8e
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085688"
---
# <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>v1.0 トークンを受け入れる Web API のスコープ

OAuth 2 アクセス許可は、開発者向け Azure Active Directory (Azure AD) (v1.0) Web API (リソース) アプリケーションでクライアント アプリケーションに公開するアクセス許可スコープです。 これらのアクセス許可スコープは、同意中にクライアント アプリケーションに付与できます。 [Azure Active Directory アプリケーション マニフェスト リファレンス](reference-app-manifest.md#manifest-reference)に関するページの `oauth2Permissions` についてのセクションを参照してください。

## <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>v1.0 アプリケーションの特定の OAuth2 アクセス許可へのアクセス権を要求するスコープ

v1.0 アプリケーションの特定のスコープのためにトークンを取得する場合 (Azure AD グラフ、 https://graph.windows.net) など)、目的のリソース ID とそのリソースの目的の OAuth2 アクセス許可を連結して、スコープを作成します。

たとえば、ユーザーに代わって、アプリ ID URI が `ResourceId` である v1.0 Web API にアクセスするには次のように指定します。

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

```javascript
var scopes = [ ResourceId + "/user_impersonation"];
```

Azure AD Graph API (https:\//graph.windows.net/) を使用して、MSAL.NET Azure AD で読み取りと書き込みを行うには、次の例に示すようにスコープの一覧を作成する必要があります。

```csharp
string ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

```javascript
var ResourceId = "https://graph.windows.net/";
var scopes = [ ResourceId + "Directory.Read", ResourceID + "Directory.Write"];
```

Azure Resource Manager API (https:\//management.core.windows.net/) に対応するスコープを書き込むには、次のスコープを要求する必要があります (2 つのスラッシュに注意してください)。

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

> [!NOTE]
> Azure Resource Manager API ではその対象ユーザーの要求 (aud) でスラッシュが予期されるため、2 つのスラッシュを使用する必要があり、したがって、API 名をスコープと区別するためのスラッシュがあります。

Azure AD で使用されるロジックは次のとおりです。

- v1.0 アクセス トークン (使用可能な場合のみ) を使用する ADAL (Azure AD v1.0) エンドポイントの場合、aud=resource となります
- v2.0 トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (Microsoft ID プラットフォーム (v2.0)) エンドポイントの場合は、`aud=resource.AppId` となります
- v1.0 アクセス トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL (v2.0 エンドポイント) の場合 (上記の例の場合)、Azure AD では、最後のスラッシュの前のすべてを取得し、それをリソース ID として使用することで、要求されたスコープからの目的の対象ユーザーを解析します。 そのため、https:\//database.windows.net で "https:\//database.windows.net/" の対象ユーザーを必要とする場合、"https:\//database.windows.net//.default" のスコープを要求する必要があります。 GitHub の問題「[#747:リソース URL の末尾のスラッシュが省略されたため、SQL 認証エラーが発生した](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747)」も参照してください。

## <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>v1.0 アプリケーションのすべてのアクセス許可へのアクセス権を要求するスコープ

v1.0 アプリケーションのすべての静的なスコープのためにトークンを取得する場合は、API のアプリ ID URI に "default" を付加します。

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

```javascript
var ResourceId = "someAppIDURI";
var scopes = [ ResourceId + "/.default"];
```

## <a name="scopes-to-request-for-a-client-credential-flowdaemon-app"></a>クライアント資格証明フロー/デーモン アプリ用に要求するスコープ

クライアント資格情報フローの場合、`/.default` スコープも渡します。 これにより、管理者がアプリケーションの登録で同意したアプリレベルのすべてのアクセス許可が、Azure AD に示されます。
