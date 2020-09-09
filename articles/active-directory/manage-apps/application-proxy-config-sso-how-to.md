---
title: アプリケーション プロキシ アプリに対するシングル サインオンを構成する方法
description: アプリケーション プロキシ アプリケーションに対するシングル サインオンを迅速に構成する方法
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/12/2019
ms.author: kenwith
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54673e0018f83024d1c01599d0096c71b6b1d0ae
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642131"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーションに対するシングル サインオンを構成する方法

シングル サインオン (SSO) を導入すると、ユーザーに何度も認証することなく、アプリケーションにアクセスできるようになります。 クラウド内で Azure Active Directory に対して 1 回認証を行えば、それ以降アプリケーションから追加で認証要求があったとしても、サービスまたはコネクタがユーザーに代わって本人性を証明します。

## <a name="how-to-configure-single-sign-on"></a>シングル サインオンの構成方法
SSO を構成するにはまず、Azure Active Directory で事前認証を行うようにアプリケーションを構成する必要があります。 この構成を行うには、 **[Azure Active Directory]**  -&gt; **[エンタープライズ アプリケーション]**  -&gt; **[すべてのアプリケーション]**  -&gt; <目的のアプリケーション> **-&gt; [アプリケーション プロキシ]** の順に移動します。 このページに [事前認証] フィールドがあるので、それを [Azure Active Directory] に設定してください。 

個々の事前認証方法の詳細については、[アプリの発行に関するドキュメント](application-proxy-add-on-premises-application.md)の手順 4 を参照してください。

   ![Azure portal の事前認証方法](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>アプリケーション プロキシ アプリケーションに使用するシングル サインオン モードの構成
具体的なシングル サインオンの種類を構成します。 サインオン方法は、バックエンド アプリケーションで使用される認証の種類に基づいて分類されます。 アプリケーション プロキシ アプリケーションは、次の 3 種類のサインオンに対応します。

-   **[パスワード ベースのサインオン]** : サインオンにユーザー名フィールドとパスワード フィールドを使うアプリケーションには、パスワード ベースのサインオンを使用できます。 構成手順については、[Azure AD ギャラリー アプリケーションのパスワード シングル サインオンの構成](configure-password-single-sign-on-non-gallery-applications.md)に関するページに記載されています。

-   **[統合 Windows 認証]** : 統合 Windows 認証 (IWA) を使うアプリケーションでは、Kerberos の制約付き委任 (KCD) を通じてシングル サインオンが実現されます。 このメソッドは、Active Directory において、見かけ上ユーザーとして振る舞い、本人に代わってトークンを送受信する権限をアプリケーション プロキシ コネクタに与えます。 KCD の構成の詳細については、[KCD によるシングル サインオンに関するドキュメント](application-proxy-configure-single-sign-on-with-kcd.md)を参照してください。

-   **[ヘッダーベースのサインオン]** : ヘッダーベースのサインオンはパートナーシップを通じて実現されるものであり、特別な構成は不要です。 パートナーシップに関する詳しい情報と、認証にヘッダーを使うアプリケーションに対してシングル サインオンを構成する具体的な手順については、[PingAccess for Azure AD のドキュメント](application-proxy-configure-single-sign-on-with-ping-access.md)を参照してください。

-   **SAML によるシングル サインオン**:SAML によるシングル サインオンでは、ユーザーの Azure AD アカウントを使用して、Azure AD がアプリケーションに対して認証を行います。 Azure AD は、接続プロトコルを通してアプリケーションにシングル サインオンの情報を伝達します。 SAML ベースのシングル サインオンでは、SAML 要求で定義するルールに基づいて、ユーザーを特定のアプリケーション ロールにマップできます。 SAML によるシングル サインオンの設定の詳細については、[アプリケーション プロキシを使用したシングル サインオンの場合の SAML](application-proxy-configure-single-sign-on-on-premises-apps.md) に関するページを参照してください。

上記の各オプションは、[エンタープライズ アプリケーション] で目的のアプリケーションに移動し、左側のメニューで **[シングル サインオン]** ページを開くと表示されます。 ご自分のアプリケーションが以前のポータルで作成されたものである場合は一部のオプションが表示されない可能性があることに注意してください。

このページには、さらに別のサインオン オプションとして [リンクされたサインオン] も表示されます。 このオプションもアプリケーション プロキシでサポートされています。 ただしこのオプションは、アプリケーションにシングル サインオンを追加するものではありません。 とは言うものの、アプリケーションには、Active Directory フェデレーション サービスなどの別のサービスを使って既にシングル サインオンが実装されている場合があります。 

管理者はこのオプションを選択することで、ユーザーがアプリケーションにアクセスしたときに最初に目にする、アプリケーションへのリンクを作成することができます。 たとえば、Active Directory Federation Services 2.0 を使用してユーザーを認証するように構成されたアプリケーションがある場合、管理者は、[リンクされたサインオン] オプションを使用して、マイ アプリにそのアプリケーションへのリンクを作成することができます。

## <a name="next-steps"></a>次のステップ
- [アプリケーション プロキシを使用したシングル サインオンでのパスワードの保管](application-proxy-configure-single-sign-on-password-vaulting.md)
- [アプリケーション プロキシを使用したシングル サインオンでの Kerberos の制約付き委任](application-proxy-configure-single-sign-on-with-kcd.md)
- [アプリケーション プロキシを使用したシングル サインオンでのヘッダーベースの認証](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [アプリケーション プロキシを使用したシングル サインオンの場合の SAML](application-proxy-configure-single-sign-on-on-premises-apps.md)
