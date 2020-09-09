---
title: Azure Active Directory B2C を使用して QQ アカウントでのサインアップおよびサインインを設定する
description: Azure Active Directory B2C を使用するアプリケーションで QQ アカウントを持つ顧客にサインアップとサインインを提供します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d230bc8a1e9bf388e1cca4e3a3a691223146d734
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85387985"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して QQ アカウントでのサインアップおよびサインインを設定する

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-qq-application"></a>QQ アプリケーションを作成する

Azure Active Directory B2C (Azure AD B2C) で ID プロバイダーとして QQ アカウントを使用するには、テナントにそれを表すアプリケーションを作成する必要があります。 まだ QQ アカウントを持っていない場合は、[https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) でサインアップできます。

### <a name="register-for-the-qq-developer-program"></a>QQ 開発者プログラムに登録する

1. QQ アカウントの資格情報を使用して、[QQ 開発者ポータル](http://open.qq.com)にサインインします。
1. サインインしたら、[https://open.qq.com/reg](https://open.qq.com/reg) に移動して開発者として登録します。
1. **[个人 (個人開発者)]** を選択します。
1. 必要な情報を入力し、 **[下一步 (次のステップ)]** を選択します。
1. 電子メールによる確認プロセスを完了します。 開発者として登録したら、承認されるまで数日待つ必要があります。

### <a name="register-a-qq-application"></a>QQ アプリケーションを登録する

1. [https://connect.qq.com/index.html](https://connect.qq.com/index.html) に移動します。
1. **[应用管理 (アプリの管理)]** を選択します。
1. **[创建应用 (アプリの作成)]** を選択し、必要な情報を入力します。
1. **[授权回调域 (コールバック URL)]** に、「`https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp`」と入力します。 たとえば、`tenant_name` が contoso の場合は、URL を `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` に設定します。
1. **[创建应用 (アプリの作成)]** を選択します。
1. 確認ページで、 **[应用管理 (アプリの管理)]** を選択してアプリの管理ページに戻ります。
1. 作成したアプリの横の **[查看 (表示)]** を選択します。
1. **[修改 (編集)]** を選択します。
1. **アプリ ID** と**アプリ キー**をコピーします。 ID プロバイダーをテナントに追加するには、これらの両方の値が必要です。

## <a name="configure-qq-as-an-identity-provider"></a>QQ を ID プロバイダーとして構成する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[QQ (プレビュー)]** を選択します。
1. **[名前]** を入力します。 たとえば、「*QQ*」とします。
1. **[クライアント ID]** には、前に作成した QQ アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ キーを入力します。
1. **[保存]** を選択します。
