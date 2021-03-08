---
title: 外部 ID に対する ID プロバイダー - Azure AD
description: Azure AD を外部ユーザーと共有するための既定の ID プロバイダーとして使用する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ed42a1fc9a2750cc928b5fd03eb4b32f770276
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644087"
---
# <a name="identity-providers-for-external-identities"></a>外部 ID に対する ID プロバイダー

"*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 アプリとリソースを外部ユーザーと共有する場合は、Azure AD が共有のための既定の ID プロバイダーです。 つまり、Azure AD アカウントまたは Microsoft アカウントを既に持っている外部ユーザーを招待すると、そのユーザーは自分でそれ以上構成を行わなくても自動的にサインインできます。

ただし、ユーザーがさまざまな ID プロバイダーを使用してサインインできるようにすることができます。

- **Google**: Google フェデレーションでは、外部ユーザーが自分の Gmail アカウントでアプリにサインインすることにより、あなたからの招待を利用することができます。 Google フェデレーションは、セルフサービスのサインアップ ユーザー フローでも使用できます。
   > [!IMPORTANT]
   > **2021 年 1 月 4 日以降**、Google は [WebView サインインのサポートを廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)します。 Gmail で Google フェデレーションまたはセルフサービス サインアップを使用している場合は、[基幹業務ネイティブ アプリケーションの互換性をテストする](google-federation.md#deprecation-of-webview-sign-in-support)必要があります。

- **Facebook**: アプリを構築するときに、セルフサービス サインアップを構成し、Facebook フェデレーションを有効にすることで、ユーザーが自分の Facebook アカウントを使用してアプリにサインアップできるようにすることが可能です。 Facebook は、セルフサービス サインアップ ユーザー フローにのみ使用でき、ユーザーがあなたからの招待を利用するときにサインイン オプションとして使用することはできません。

- **直接フェデレーション**:また、SAML または WS-Fed プロトコルをサポートする任意の外部 ID プロバイダーとのフェデレーションを設定することもできます。 直接フェデレーションでは、外部ユーザーが自分の既存のソーシャル アカウントまたはエンタープライズ アカウントでアプリにサインインすることにより、あなたからの招待を利用することができます。 
   > [!NOTE]
   > 直接フェデレーション ID プロバイダーは、セルフサービス サインアップ ユーザー フローでは使用できません。


## <a name="how-it-works"></a>しくみ

Azure AD 外部 ID セルフサービス サインアップ機能を使用すると、ユーザーは Azure AD、Google、または Facebook アカウントでサインアップできます。 Azure AD テナントでソーシャル ID プロバイダーを設定するには、各 ID プロバイダーでアプリケーションを作成し、資格情報を構成します。 クライアントまたはアプリの ID と、クライアントまたはアプリのシークレットを取得して、Azure AD テナントに追加できます。

Azure AD テナントに ID プロバイダーを追加した後、次のようにします。

- 組織内のアプリまたはリソースに外部ユーザーを招待すると、外部ユーザーはその ID プロバイダーでの自分のアカウントを使用してサインインできるようになります。
- アプリに対して[セルフサービス サインアップ](self-service-sign-up-overview.md)を有効にすると、外部ユーザーは、追加した ID プロバイダーでの自分のアカウントを使用して、アプリにサインアップできます。

> [!NOTE]
> Azure AD はセルフサービス サインアップが既定で有効になっているため、ユーザーは常に Azure AD アカウントを使用してサインアップすることができます。

招待を利用する場合、またはアプリにサインアップする場合、外部ユーザーは、ソーシャル ID プロバイダーを使用してサインインおよび認証を行うことができます。

![Google と Facebook のオプションが表示されたサインイン画面のスクリーンショット](media/identity-providers/sign-in-with-social-identity.png)

最適なサインイン エクスペリエンスにするには、可能な限り ID プロバイダーとフェデレーションします。これにより、招待されたゲストがアプリにアクセスしたときに、シームレスなサインイン エクスペリエンスを提供できるようになります。  

## <a name="next-steps"></a>次のステップ

アプリケーションへのサインイン用に ID プロバイダーを追加する方法については、次の記事を参照してください。

- ソーシャル ID プロバイダーの一覧に [Google を追加する](google-federation.md)
- ソーシャル ID プロバイダーの一覧に [Facebook を追加する](facebook-federation.md)
- ID プロバイダーが SAML 2.0 または WS-Fed プロトコルをサポートしている組織との[直接フェデレーションを設定する](direct-federation.md)。 セルフサービス サインアップのユーザー フローでは直接フェデレーションを使用できないことに注意してください。
