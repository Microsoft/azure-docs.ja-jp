---
title: シングルページ アプリのサインインとサインアウト - Microsoft ID プラットフォーム | Azure
description: シングルページ アプリケーション (サインイン) を構築する方法を学習する
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: eb75aa53051e7e3c424ffe131cda61324fe86b1a
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159966"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>シングルページ アプリケーション：サインインとサインアウト

ご利用のシングルページ アプリケーションのコードにサインインを追加する方法について学びます。

アプリケーション内の API にアクセスするトークンを取得する前に、認証されたユーザー コンテキストが必要です。 MSAL.js 内のアプリケーションにユーザーをサインインさせる方法には、次の 2 つがあります。

* `loginPopup` メソッドを使用して[ウィンドウをポップアップ](#sign-in-with-a-pop-up-window) する
* `loginRedirect` メソッドを使用して[リダイレクト](#sign-in-with-redirect)する

また、オプションで、サインイン時にユーザーに同意してもらう必要がある API のスコープを渡すこともできます。

> [!NOTE]
> 認証されたユーザー コンテキストまたは ID トークンに、ご利用のアプリケーションから既にアクセスできる場合は、ログイン手順を省略して直接トークンを取得することができます。 詳細については、「[SSO without MSAL.js login](msal-js-sso.md#sso-without-msaljs-login)」を参照してください。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

ご利用のアプリケーション内で、ポップアップ メソッドとリダイレクト メソッドの両方を使用することはできません。 ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドをお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* ポップアップウィンドウが無効になっているブラウザの制約またはポリシーがある場合は、リダイレクト方法を使用できます。 [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) があるので、Internet Explorer ブラウザーでは、リダイレクト メソッドを使用してください。

## <a name="sign-in-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してサインインする

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

MSAL Angular ラッパーを使用すると、ルート定義に `MsalGuard` を追加するだけで、ご利用のアプリケーション内の特定のルートを保護することができます。 このガードにより、そのルートにアクセスすると、サインインのためのメソッドが呼び出されます。

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

ポップアップ ウィンドウ エクスペリエンスの場合は、`popUp` 構成オプションを有効にします。 次のように同意を必要とするスコープを渡すこともできます。

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```
---

## <a name="sign-in-with-redirect"></a>リダイレクトを使用してサインインする

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

リダイレクトメソッドは、メインアプリから離れるため、Promise　を返しません。 返されたトークンを処理し、これにアクセスするには、リダイレクト メソッドを呼び出す前に成功とエラーのコールバックを登録する必要があります。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

このコードは、ポップアップウィンドウを使用したサインインに関するセクションで説明したものと同じです。 既定のフローはリダイレクトです。

> [!NOTE]
> ID トークンには同意されたスコープは含まれず、認証されたユーザーのみが示されます。 同意されたスコープは、次の手順で取得するアクセス トークンで返されます。

---

## <a name="sign-out"></a>サインアウト

MSAL ライブラリには、`logout` メソッドが用意されています。これにより、ブラウザー ストレージ内のキャッシュがクリアされ、Azure Active Directory (Azure AD) にサインアウト要求が送信されます。 サインアウト後、ライブラリは既定でアプリケーションの開始ページにリダイレクトします。

`postLogoutRedirectUri` を設定することで、サインアウト後に、リダイレクトされる URI を構成することができます。 この URI は、アプリケーションの登録のログアウト URI として登録する必要もあります。

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angulartabangular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリのトークンの取得](scenario-spa-acquire-token.md)
