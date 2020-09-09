---
title: ユーザー フローを通じてアクセス トークンをご自身のアプリに渡す
titleSuffix: Azure AD B2C
description: OAuth 2.0 ID プロバイダーのアクセス トークンを Azure Active Directory B2C のユーザー フローで要求として渡す方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b834dda926b7da1241a325e1453143eccafaf30
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488773"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C のユーザー フローを通じてアクセス トークンをご自身のアプリケーションに渡す

Azure Active Directory B2C (Azure AD B2C) の[ユーザー フロー](user-flow-overview.md)は、アプリケーションのユーザーに、ID プロバイダーを使用してサインアップまたはサインインする機会を提供します。 ジャーニーが開始されると、Azure AD B2C は ID プロバイダーから[アクセス トークン](tokens-overview.md)を受け取ります。 Azure AD B2C はそのトークンを使用して、そのユーザーに関する情報を取得します。 ユーザー フローで要求を有効にして、Azure AD B2C に登録するアプリケーションにそのトークンを渡します。

Azure AD B2C は現在、[Facebook](identity-provider-facebook.md) や [Google](identity-provider-google.md) などの [OAuth 2.0](authorization-code-flow.md) ID プロバイダーのアクセス トークンのみを渡すことができます。 その他すべての ID プロバイダーについては、要求が空で返されます。

## <a name="prerequisites"></a>前提条件

* 対象のアプリケーションで、[推奨されるユーザー フロー](user-flow-versions.md)が使用されている。
* 対象のユーザー フローが OAuth 2.0 ID プロバイダーを使用して構成されている。

## <a name="enable-the-claim"></a>要求を有効にする

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ユーザー フロー (ポリシー)]** を選択し、ご自身のユーザー フローを選択します。 たとえば、**B2C_1_signupsignin1** などです。
5. **[アプリケーション クレーム]** を選択します。
6. **[ID プロバイダーのアクセス トークン]** 要求を有効にします。

    ![[ID プロバイダーのアクセス トークン] 要求を有効にする](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. **[保存]** をクリックしてユーザー フローを保存します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

Azure AD B2C でアプリケーションをテスト中に、Azure AD B2C トークンを `https://jwt.ms` に返して、その中の要求を見直すと便利なことがあります。

1. ユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
2. **[アプリケーション]** には、前に登録したアプリケーションを選択します。 以下の例でトークンを表示するには、 **[応答 URL]** に `https://jwt.ms` が表示される必要があります。
3. **[ユーザー フローを実行します]** をクリックし、その後ご自身のアカウントの資格情報でサインインします。 ID プロバイダーのアクセス トークンは **idp_access_token** 要求に表示されます。

    次の例のようなコードが表示されます。

    ![Idp_access_token block が強調表示されている jwt.ms 内のデコードされたトークン](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>次のステップ

[Azure AD B2C トークンの概要](tokens-overview.md)に関する記事で詳細を参照してください。
