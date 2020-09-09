---
title: チュートリアル:お使いのアプリケーションに ID プロバイダーを追加する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory B2C 内のアプリケーションに ID プロバイダーを追加する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9f9abf9105da773ec5f8321c0f8e70e20516618c
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922151"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する

アプリケーションでは、ユーザーが異なる ID プロバイダーでサインインできるようにすることができます。 "*ID プロバイダー*" では、ID 情報の作成、保守、管理、およびアプリケーションへの認証サービスの提供が行われます。 Azure portal を使用して、Azure Active Directory B2C (Azure AD B2C) によってサポートされる ID プロバイダーを[ユーザー フロー](user-flow-overview.md)に追加できます。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * ID プロバイダー アプリケーションを作成する
> * Facebook と Azure Active Directory の両方の ID プロバイダーをテナントに追加する。
> * ID プロバイダーをユーザー フローに追加する

通常、アプリケーションでは ID プロバイダーを 1 つだけ使用しますが、さらに追加することもできます。 このチュートリアルでは、Azure AD の ID プロバイダーと Facebook の ID プロバイダーをアプリケーションに追加する方法を示します。 これらの ID プロバイダーを両方ともアプリケーションに追加することはオプションです。 [Amazon](identity-provider-amazon.md)、[GitHub](identity-provider-github.md)、[Google](identity-provider-google.md)、[LinkedIn](identity-provider-linkedin.md)、[Microsoft](identity-provider-microsoft-account.md)、[Twitter](identity-provider-twitter.md).などの他の ID プロバイダーを追加することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[ユーザー フローを作成](tutorial-create-user-flows.md)して、ユーザーがアプリケーションにサインアップおよびサインインできるようにします。

## <a name="create-applications"></a>アプリケーションの作成

ID プロバイダー アプリケーションでは、Azure AD B2C テナントと通信できるようにするための識別子とキーが提供されます。 チュートリアルのこのセクションでは、テナントに ID プロバイダーを追加するための識別子とキーの取得元になる、Azure AD アプリケーションと Facebook アプリケーションを作成します。 ID プロバイダーを 1 つだけ追加する場合は、そのプロバイダー用のアプリケーションを作成するだけでかまいません。

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

Azure AD のユーザーのサインインを有効にするには、Azure AD テナント内でアプリケーションを登録する必要があります。 Azure AD テナントは、Azure AD B2C テナントと同じものではありません。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックして、ご利用の Azure AD テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択し、 **[アプリの登録]** を検索して選択します。
1. **[新規登録]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、「 `Azure AD B2C App` 」のように入力します。
1. このアプリケーションには **[この組織のディレクトリ内のアカウントのみ]** の選択をそのまま使用します。
1. **[リダイレクト URI]** では、値 **[Web]** をそのまま使用し、次の URL をすべて小文字で入力します。`your-B2C-tenant-name` は、お使いの Azure AD B2C テナントの名前に置き換えます。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    たとえば、「 `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 」のように入力します。

    ここでは、すべての URL で [b2clogin.com](b2clogin.md) を使用してください。

1. **[登録]** を選択し、後の手順で使用する **[Application (client) ID]\(アプリケーション (クライアント) ID\)** をメモします。
1. アプリケーション メニューの **[管理]** で **[証明書とシークレット]** を選択してから、 **[新しいクライアント シークレット]** を選択します。
1. クライアント シークレットの **[説明]** を入力します。 たとえば、「 `Azure AD B2C App Secret` 」のように入力します。
1. 有効期限を選択します。 このアプリケーションには、 **[1 年]** の選択をそのまま使用します。
1. **[追加]** を選択し、後の手順で使用する新しいクライアント シークレットの値をメモします。

### <a name="create-a-facebook-application"></a>Facebook アプリケーションを作成する

Azure AD B2C で ID プロバイダーとして Facebook アカウントを使用するには、Facebook でアプリケーションを作成する必要があります。 まだ Facebook アカウントを持っていない場合は、[https://www.facebook.com/](https://www.facebook.com/) で取得できます。

1. Facebook アカウントの資格情報を使用して、[開発者向けの Facebook](https://developers.facebook.com/)にサインインします。
1. まだ登録していない場合は、Facebook 開発者として登録する必要があります。 これを行うには、ページの右上隅にある **[Get Started]\(スタートガイド\)** を選択し、Facebook のポリシーに同意して登録手順を完了します。
1. **[マイ アプリ]** を選択し、 **[アプリの作成]** を選択します。
1. **[表示名]** および有効な **[連絡先の電子メール]** を入力します。
1. **[Create App ID] \(アプリ ID の作成)** をクリックします。 Facebook プラットフォームのポリシーを受け入れ、オンライン セキュリティ チェックを完了する必要があります。
1. **[設定]**  >  **[基本]** を選択します。
1. **カテゴリ**を選択します。たとえば`Business and Pages`。 この値は Facebook では必須ですが、Azure AD B2C では使用されません。
1. ページの下部で、 **[プラットフォームの追加]** 、 **[Web サイト]** の順に選択します。
1. **サイト URL** に、`https://your-tenant-name.b2clogin.com/`を入力して`your-tenant-name`をお使いのテナントの名前に置き換えてください。
1. **[Privacy Policy URL] (プライバシー ポリシーの URL)** に URL (`http://www.contoso.com/` など) を入力します。 プライバシー ポリシーの URL は、アプリケーションのプライバシーに関する情報を提供するために維持されるページです。
1. **[変更の保存]** を選択します。
1. ページの上部にある **[App ID]\(アプリ ID\)** の値をメモします。
1. **[App Secret]\(アプリケーション シークレット\)** の横にある **[Show]\(表示\)** を選択し、その値をメモします。 アプリ ID とアプリケーション シークレットの両方を使用して、テナントで ID プロバイダーとして Facebook を構成します。 **[App Secret]\(アプリケーション シークレット\)** は、安全に保管する必要がある重要なセキュリティ資格情報です。
1. **[Products]\(製品\)** を選択し、 **[Facebook Login]\(Facebook ログイン\)** で **[Set up]\(セットアップ\)** を選択します。
1. 左側のメニューの **[Facebook Login]\(Facebook ログイン\)** で **[Settings]\(設定\)** を選択します。
1. **[有効な OAuth リダイレクト URI]** に「`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`」を入力します。 `your-tenant-name` をテナントの名前に置き換えます。 ページの下部にある **[Save Changes]\(変更の保存\)** を選択します。
1. Facebook アプリケーションを Azure AD B2C で使用できるようにするには、ページの右上にある**状態**セレクターをクリックし、 **[オン]** に設定してアプリケーションを公開し、 **[確認]** をクリックします。 この時点で、状態は**開発**から**ライブ**に変更されます。

## <a name="add-the-identity-providers"></a>ID プロバイダーを追加する

追加する ID プロバイダー用のアプリケーションを作成した後、ID プロバイダーをテナントに追加します。

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory の ID プロバイダーを追加する

1. Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば､「*Contoso Azure AD*」と入力します。
1. **[メタデータ URL]** には、次の URL を入力します。`your-AD-tenant-domain` は、利用する Azure AD テナントのドメイン名に置き換えます。

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    たとえば、「 `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 」のように入力します。

1. **[クライアント ID]** には、前に記録したアプリケーション ID を入力します。
1. **[クライアント シークレット]** には、前に記録したクライアント シークレットを入力します。
1. **[スコープ]** 、 **[応答の種類]** 、および **[応答モード]** の既定値はそのままにします。
1. (省略可能) **Domain_hint** に値を入力します。 たとえば、「*ContosoAD*」とします。 [ドメイン ヒント](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)は、アプリケーションからの認証要求に含まれるディレクティブです。 ドメイン ヒントを使用して、ユーザーのフェデレーション IdP サインイン ページへの移動を高速化できます。 または、マルチテナント アプリケーションで使用して、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。
1. **[ID プロバイダー要求のマッピング]** で、次の要求マッピング値を入力します。

    * **[ユーザー ID]** : *oid*
    * **[表示名]** : *name*
    * **[名]** : *given_name*
    * **[姓]** : *family_name*
    * **[電子メール]** : *unique_name*

1. **[保存]** を選択します。

### <a name="add-the-facebook-identity-provider"></a>Facebook の ID プロバイダーを追加する

1. **[ID プロバイダー]** を選択してから、 **[Facebook]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Facebook*」とします。
1. **[クライアント ID]** には、前に作成した Facebook アプリケーションのアプリ ID を入力します。
1. **[クライアント シークレット]** には、記録したアプリ シークレットを入力します。
1. **[保存]** を選択します。

## <a name="update-the-user-flow"></a>ユーザー フローを更新する

前提条件の一部として完了したチュートリアルでは、*B2C_1_signupsignin1* という名前の、サインアップおよびサインイン用のユーザー フローを作成しました。 このセクションでは、ID プロバイダーを *B2C_1_signupsignin1* ユーザー フローに追加します。

1. **[ユーザー フロー]** を選択し、*B2C_1_signupsignin1* ユーザー フローを選択します。
2. **[ID プロバイダー]** を選択し、追加した **Facebook** および **Contoso Azure AD** の ID プロバイダーを選択します。
3. **[保存]** を選択します。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローの [概要] ページで、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択し、前に追加した ID プロバイダーでサインインします。
1. 追加した別の ID プロバイダーを使用して、ステップ 1 から 3 を繰り返します。

サインイン操作が成功した場合は、`https://jwt.ms` にリダイレクトされ、次のようなデコードされたトークンが表示されます。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * ID プロバイダー アプリケーションを作成する
> * ID プロバイダーをテナントに追加する
> * ID プロバイダーをユーザー フローに追加する

次に、アプリケーションの ID エクスペリエンスの一部としてユーザーに表示されるページの UI をカスタマイズする方法について説明します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内のアプリケーションのユーザー インターフェイスをカスタマイズする](tutorial-customize-ui.md)
