---
title: Azure Active Directory でリンクされたサインオンを構成する
description: Azure AD でリンクされたサインオンを構成します。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 07/30/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: bad4fe7f0cf090e5d61506e775fccf677df45fa5
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641978"
---
# <a name="configure-linked-sign-on"></a>リンクされたサインオンの構成

アプリケーション管理の[クイックスタート シリーズ](view-applications-portal.md)では、アプリケーションの ID プロバイダー (IdP) として Azure AD を使用する方法について学習しました。 このクイックスタート ガイドでは、SAML ベースの SSO を設定します。 他に、**リンク**というオプションがあります。 この記事では、リンクのオプションについて詳しく説明します。

**[リンク]** オプションを使用すると、ユーザーが組織の[マイ アプリ](https://myapps.microsoft.com/)、または Office 365 ポータルでアプリを選択するときにターゲットとなる場所を構成できます。

リンク オプションが役立つ一般的なシナリオには、次のようなものがあります。
- Active Directory フェデレーション サービス (AD FS) など、現在フェデレーションを使用しているカスタム Web アプリケーションにリンクを追加する。
- ユーザーのアクセス パネルに表示するだけの特定の SharePoint ページまたは他の Web ページにディープ リンクを追加する。
- 認証を必要としないアプリにリンクを追加する。 
 
 **[リンク]** オプションでは、Azure AD 資格情報を使用したサインオン機能は提供されません。 ただし、**エンタープライズ アプリケーション**のその他の機能の一部を使用することはできます。 たとえば、監査ログを使用したり、カスタム ロゴとアプリ名を追加したりできます。

## <a name="before-you-begin"></a>開始する前に

短時間で利用できるようにするには、アプリケーション管理に関する[クイックスタート シリーズ](view-applications-portal.md)を参照してください。 シングル サインオンを構成するクイックスタートでは、 **[リンク]** オプションも表示されます。 

**[リンク]** オプションは Azure AD を使用したサインオン機能を提供しません。 このオプションは、[マイ アプリ](https://myapps.microsoft.com/)、または Microsoft 365 アプリ ランチャーでアプリを選択したときにユーザーが送信される場所が設定されるだけです。

> [!IMPORTANT] 
> 一部のシナリオでは、**エンタープライズ アプリケーション**内のアプリケーションのナビゲーションに **[シングル サインオン]** オプションが表示されません。 
>
> アプリケーションが**アプリの登録**を使用して登録された場合、既定では、OIDC OAuth を使用するようにシングル サインオン機能が設定されます。 この場合、 **[シングル サインオン]** オプションは、 **[エンタープライズ アプリケーション]** の下のナビゲーションに表示されません。 **アプリの登録**を使用してカスタム アプリを追加する場合は、マニフェスト ファイルでオプションを構成します。 マニフェスト ファイルの詳細については、「[Azure Active Directory のアプリ マニフェスト](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)」を参照してください。 SSO 標準の詳細については、「[Microsoft ID プラットフォームを使用した認証と承認](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform)」を参照してください。 
>
> ナビゲーションに **[シングル サインオン]** が表示されないその他のシナリオには、アプリケーションが別のテナントでホストされている場合や、アカウントに必要なアクセス許可 (グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者) がない場合などがあります。 アクセス許可によっては、 **[シングル サインオン]** を開くことができるが、保存できないシナリオが発生する場合もあります。 Azure AD の管理者ロールの詳細については、(https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) を参照してください。

### <a name="configure-link"></a>リンクを構成する

アプリのリンクを設定するには、 **[シングル サインオン]** ページで **[リンク]** を選択します。 リンクを入力し、 **[保存]** を選択します。 これらのオプションをどこで検索すればいいかの情報が必要ですか。 [一連のクイックスタート](view-applications-portal.md)をご覧ください。
 
アプリを構成したら、これにユーザーとグループを割り当てます。 ユーザーを割り当てるときに、アプリケーションが[マイ アプリ](https://myapps.microsoft.com/)、または Microsoft 365 アプリ ランチャーに表示されるタイミングを制御できます。

## <a name="next-steps"></a>次のステップ

- [アプリケーションにユーザーまたはグループを割り当てる](methods-for-assigning-users-and-groups.md)
- [自動ユーザー アカウント プロビジョニングを構成する](../app-provisioning/configure-automatic-user-provisioning-portal.md)
