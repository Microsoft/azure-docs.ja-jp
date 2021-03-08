---
title: Web API を呼び出すデーモン アプリを運用環境へ移行する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデーモン アプリを運用環境へ移行する方法 について学ぶ
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582843"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Web API を呼び出すデーモン アプリ - 運用環境への移行

これでトークンを取得してサービス間の呼び出しに使用する方法がわかったので、運用環境にアプリを移行する方法について説明します。

## <a name="deployment---multitenant-daemon-apps"></a>デプロイ - マルチテナント デーモン アプリ

複数のテナントで実行できるデーモン アプリケーションを作成している ISV では、テナントによって確実に以下の管理を行ってください。

- アプリケーション用にサービス プリンシパルをプロビジョニングする。
- アプリケーションへの同意を許可する。

顧客に対しては、これらの操作の実行方法を説明する必要があります。 詳しくは、「[テナント全体の同意を要求する](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)」をご覧ください。

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>コード サンプル

# <a name="net"></a>[.NET](#tab/dotnet)

- 以下に関するリファレンス ドキュメント:
  - [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) のインスタンス化
  - [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder) の呼び出し
- 他のサンプル/チュートリアル:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) では、Microsoft Graph へのクエリを実行するテナントのユーザーを表示する小さい .NET Core デーモン コンソール アプリケーションを取り上げています。

    ![サンプル デーモン アプリのトポロジ](media/scenario-daemon-app/daemon-app-sample.svg)

    同じサンプルには、証明書を利用したバリエーションも示されています。

    ![サンプル デーモン アプリのトポロジ - 証明書](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-Webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) では、ユーザーの代理ではなく、アプリケーションの ID を使用して Microsoft Graph からのデータを同期する ASP.NET MVC の Web アプリケーションを取り上げています。 このサンプルには、管理者の同意プロセスも示されています。

    ![トポロジ](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>次のステップ

詳細については、次のリンクを参照してください。

# <a name="python"></a>[Python](#tab/python)

クイックスタート「[トークンを取得し、Python コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](./quickstart-v2-python-daemon.md)」をお試しください。

# <a name="java"></a>[Java](#tab/java)

クイックスタート「[トークンを取得し、Java コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す](./quickstart-v2-java-daemon.md)」をお試しください。

---
