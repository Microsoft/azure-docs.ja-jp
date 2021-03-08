---
title: 'クイックスタート: MSAL Node を使用して Node Web アプリに認証を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Node.js Web アプリと Node.js 用 Microsoft Authentication Library (MSAL) を使用して認証を実装する方法について学習します。
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2020
ms.author: marsma
ms.custom: aaddev, scenarios:getting-started, languages:js, devx-track-js
ms.openlocfilehash: 5ca8c41dc1e6a05975227555abd91f5d6725285a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092173"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-node-web-app-using-the-auth-code-flow"></a>クイックスタート: 認証コード フローを使用して Node.js Web アプリ内でユーザーをサインインさせ、アクセス トークンを取得する

このクイックスタートでは、Node.js Web アプリで承認コード フローを使用してユーザーをサインインする方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、Microsoft Graph API を呼び出すためのアクセス トークンを取得する方法も示します。 

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

このクイックスタートでは、承認コード フローで Node.js 用 Microsoft Authentication Library (MSAL Node) を使用します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [Azure サブスクリプションを無料で作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>クイック スタート アプリケーションを登録してダウンロードする
>
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **[名前]** を入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
> 1. **[リダイレクト URI]** の値を `http://localhost:3000/redirect` に設定します。
> 1. **[登録]** を選択します。 
> 1. 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
> 1. **[管理]** で、 **[Certificates & secrets]\(証明書およびシークレット\)**  >  **[新しいクライアント シークレット]** の順に選択します。  説明を空白のままにし、既定の有効期限をそのままにして、 **[追加]** を選択します。
> 1. 後で使用するために、 **[クライアント シークレット]** の **値** を書き留めます。

#### <a name="step-2-download-the-project"></a>手順 2:プロジェクトのダウンロード

> [!div renderon="docs"]
> Node.js を使用して Web サーバーでプロジェクトを実行するために、[コア プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)します。

> [!div renderon="portal" class="sxs-lookup"]
> Node.js を使用して Web サーバーでプロジェクトを実行する

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-node-app"></a>手順 3:Node アプリの構成
>
> プロジェクトを展開します。*ms-identity-node-main* フォルダーを開き、*index.js* ファイルを開きます。
> `clientID` に **[アプリケーション (クライアント) ID]** の値を設定します。
> `clientSecret` に **[クライアント シークレット]** の **値** を設定します。
>
>```javascript
>const config = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "https://login.microsoftonline.com/common",
>        clientSecret: "Enter_the_Client_Secret_Here"
>    },
>    system: {
>        loggerOptions: {
>            loggerCallback(loglevel, message, containsPii) {
>                console.log(message);
>            },
>            piiLoggingEnabled: false,
>            logLevel: msal.LogLevel.Verbose,
>        }
>    }
>};
> ```

> [!div renderon="docs"]
>
> 以下の説明に従って、`config` セクションの値を変更します。
>
> - `Enter_the_Application_Id_Here` は、登録したアプリケーションの **アプリケーション (クライアント) ID** です。
> - `Enter_the_Client_Secret_Here` は、登録したアプリケーションの **[クライアント シークレット]** の **値** です。
>
> 既定値の `authority` は、メイン (グローバル) Azure クラウドを表します。
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > **[アプリケーション (クライアント) ID]** の値を見つけるには、Azure portal でアプリ登録の **[概要]** ページに移動します。 **[証明書とシークレット]** の下に移動して、**クライアント シークレット** を取得するか、新しく生成します。
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
>
> [!div renderon="docs"]
>
> #### <a name="step-4-run-the-project"></a>手順 4:プロジェクトを実行する

Node.js を使用してプロジェクトを実行します。

1. サーバーを起動するために、プロジェクト ディレクトリ内から次のコマンドを実行します。
    ```console
    npm install
    npm start
    ```
1. `http://localhost:3000/` を参照します。

1. **[Sign In]\(サインイン\)** を選択して、サインイン プロセスを開始します。

    初回サインイン時に、自分のプロファイルにアプリケーションがアクセスして自分をサインインさせることについて同意を求められます。 正常にサインインすると、コマンド ラインにログ メッセージが表示されます。

## <a name="more-information"></a>詳細情報

### <a name="how-the-sample-works"></a>このサンプルのしくみ

このサンプルを実行すると、ローカル ホストのポート 3000 で Web サーバーがホストされます。 Web ブラウザーがこのサイトにアクセスすると、ユーザーはすぐに Microsoft 認証ページにリダイレクトされます。 このため、このサンプルには *html* または表示要素は含まれていません。 認証に成功すると、"OK" というメッセージが表示されます。

### <a name="msal-node"></a>MSAL Node

MSAL Node ライブラリは、ユーザーをサインインさせ、Microsoft ID プラットフォームによって保護された API へのアクセスに使用されるトークンを要求します。 次のように Node.js Package Manager (npm) を使用して、最新バージョンをダウンロードできます。

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [既存の Web アプリに認証を追加する - GitHub コード サンプル >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/auth-code)
