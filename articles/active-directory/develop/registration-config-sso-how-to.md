---
title: 新しいマルチテナント アプリケーションを構成する方法 | Microsoft Docs
description: Azure AD で開発中または登録中のカスタム アプリケーションのシングル サインオンを構成する方法。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc92ff0eea2bbd55330a954746245dd4a5ee75a3
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173234"
---
# <a name="how-to-configure-a-new-multi-tenant-application"></a>新しいマルチテナント アプリケーションを構成する方法

アプリのフェデレーション シングル サインオン (SSO) は、OpenID Connect、SAML 2.0、または WS-Fed を使用して Azure AD 経由でフェデレーションしている場合、自動的に有効になります。 Azure AD とのセッションが既に存在しているのにエンド ユーザーのサインインが必要な場合、アプリが正しく構成されていない可能性があります。

* ADAL/MSAL を使用している場合、**PromptBehavior** を、**Always** ではなく **Auto** にしてください。

* モバイル アプリを作成している場合、仲介型または仲介型でない SSO を有効にするために、追加の構成が必要になることがあります。

Android については、[Android でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)に関するページを参照してください。<br>

iOS については、[iOS でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[Azure AD SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)<br>

[Android でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-android)<br>

[iOS でクロス アプリ SSO を有効にする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-sso-ios)<br>

[アプリと Azure AD の統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Azure AD v2.0 集中型アプリの同意とアクセス許可](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
