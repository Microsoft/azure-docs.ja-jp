---
title: Web API を呼び出すデーモン アプリ (アプリの登録) - Microsoft ID プラットフォーム
description: Web API を呼び出すデーモン アプリを構築する方法について説明します - アプリの登録
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fddd95564e3683aaee067f0442573c41e93376dd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264389"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Web API を呼び出すデーモン アプリ - アプリの登録

デーモン アプリケーションでアプリを登録するときに知っておく必要があることを以下に示します。

## <a name="supported-account-types"></a>サポートされているアカウントの種類

デーモン アプリケーションが Azure AD テナントでのみ意味を持つことを考えると、アプリケーションを作成するときには、以下を選択する必要があります。

- **この組織のディレクトリ内のアカウントのみ**。 デーモン アプリケーションは通常、基幹業務 (LOB) の開発者によって記述されるため、この選択が最も一般的なケースです。
- または**任意の組織のディレクトリ内のアカウント**。 お客様が顧客にユーティリティ ツールを提供する ISV である場合は、この選択を行います。 顧客のテナント管理者に承認してもらう必要があります。

## <a name="authentication---no-reply-uri-needed"></a>認証 - 応答 URI は不要

機密クライアント アプリケーションでクライアントの資格情報フロー**のみ**使用する場合、応答 URL を登録する必要はありません。 アプリケーションの構成/構築でも必要ありません。 クライアント資格情報フローでは使用しません。

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API のアクセス許可 - アプリのアクセス許可および管理者の同意

デーモン アプリケーションでは、(委任されたアクセス許可ではなく) API に対するアプリケーションのアクセス許可のみを要求できます。 アプリケーション登録の「**API のアクセス許可**」ページで **[アクセス許可の追加]** を選択して API ファミリを選択した後に、 **[アプリケーションのアクセス許可]** を選択して、該当のアクセス許可を選択します

![アプリのアクセス許可と管理者の同意](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> 呼び出す Web API では、委任されたアクセス許可ではなく、**アプリケーションのアクセス許可 (アプリ ロール)** を定義する必要があります。 そのような API を公開する方法の詳細については、[保護された Web API: アプリの登録 - デーモン アプリで Web API が呼び出される場合](https://docs.microsoft.com/en-us/azure/active-directory/develop/scenario-protected-web-api-app-registration#if-your-web-api-is-called-by-a-daemon-app)に関するページを参照してください。

デーモン アプリケーションには、Web API を呼び出すアプリケーションに対するテナント管理者の事前同意が必要です。 この同意は、テナント管理者が **[ *組織* に管理者の同意を与えます]** を選択することによって、同じ「 **API のアクセス許可** 」ページで提供されます

お客様がマルチテナント アプリケーションを構築する ISV である場合は、「[デプロイメント - マルチテナント デーモン アプリのケース](scenario-daemon-production.md#deployment---case-of-multi-tenant-daemon-apps)」パラグラフを確認することをお勧めします。

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デーモン アプリ - アプリのコード構成](./scenario-daemon-app-configuration.md)
