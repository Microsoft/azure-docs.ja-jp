---
title: "Azure Logic Apps の Web API と REST API のデプロイ、呼び出し、認証 | Microsoft Docs"
description: "Azure Logic Apps とのシステム統合のために、ワークフローで Web API と REST API をデプロイし、認証し、呼び出す"
keywords: "web API, REST API, コネクタ, ワークフロー, システム統合, 認証"
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: 
documentationcenter: 
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: 88c62d5ab046d8cf4bce5d23b776e517bb0e1d87
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---

# <a name="deploy-call-and-authenticate-custom-apis-as-connectors-for-logic-apps"></a>ロジック アプリのコネクタとしてカスタム API をデプロイし、呼び出し、認証する

ロジック アプリ ワークフローで使用するアクションまたはトリガーを提供する[カスタム API を作成](./logic-apps-create-api-app.md)したら、API をデプロイする必要があります。デプロイ後に呼び出しが可能になります。 ロジック アプリからあらゆる API を呼び出せますが、[Swagger メタデータ](http://swagger.io/specification/)を追加すると最も効率的になります。このメタデータは API の操作とパラメーターを説明するものです。 この Swagger ファイルを利用することで、API とロジック アプリが効率的に連動し、より簡単に統合されます。

API は [Web アプリ](../app-service-web/app-service-web-overview.md)としてデプロイできますが、[API アプリ](../app-service-api/app-service-api-apps-why-best-platform.md)としてデプロイすることを検討してください。クラウドやオンプレミスで API を構築、ホスト、利用するとき、作業が簡単になります。 API のコードを変更する必要はありません。コードを API アプリにデプロイするだけです。 API は [Azure App Service](../app-service/app-service-value-prop-what-is.md) でホストできます。Azure App Service は、最も効果的で簡単、かつ拡張可能な方法で API ホスティングを提供する PaaS (サービスとしてのプラットフォーム) です。

ロジック アプリから API への呼び出しを認証するために、Azure ポータルで Azure Active Directory を設定できます。コードを更新する必要がありません。 あるいは、API のコードで認証を要求し、強制できます。

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Web アプリまたは API アプリとして API をデプロイする

ロジック アプリからカスタム API を呼び出すには、先に、Web アプリまたは API アプリとして Azure App Service に API をデプロイします。 また、ロジック アプリ デザイナーで Swagger ドキュメントを読めるように、API 定義プロパティを設定し、Web アプリまたは API アプリの [[クロスオリジン リソース共有 (CORS) を使用する]](../app-service-api/app-service-api-cors-consume-javascript.md#corsconfig) をオンにします。

1. Azure Portal で、Web アプリまたは API アプリを選択します。

2. ブレードが開いたら、**[API]** で **[API 定義]** を選択します。 **[API 定義の場所]** を swagger.json ファイルの URL に設定します。

   通常、URL は `https://{name}.azurewebsites.net/swagger/docs/v1)` 形式で表示されます。

   ![カスタム API の Swagger ファイルへのリンク](media/logic-apps-custom-hosted-api/custom-api-swagger-url.png)

3. **[API]** で **[CORS]** を選択します。 **[許可されたオリジン]** の CORS ポリシーを **'*'** に設定します (すべて許可)。

   この設定により、ロジック アプリ デザイナーからの要求が許可されます。

   ![ロジック アプリ デザイナーからカスタム API への要求を許可する](media/logic-apps-custom-hosted-api/custom-api-cors.png)

詳細と例については、次の記事をご覧ください。

* [ASP.NET Web API の Swagger メタデータを追加する](../app-service-api/app-service-api-dotnet-get-started.md#use-swagger-api-metadata-and-ui)
* [Azure App Service に ASP.NET Web API をデプロイする](../app-service-api/app-service-api-dotnet-get-started.md)

## <a name="call-your-custom-api-from-logic-app-workflows"></a>ロジック アプリ ワークフローからカスタム API を呼び出す

API 定義プロパティと CORS を設定すると、カスタム API のトリガーとアクションをロジック アプリ ワークフローに追加できるようになります。 

*  Swagger URL を含む Web サイトを表示するには、ロジック アプリ デザイナーでサブスクリプション Web サイトを閲覧できます。

*  Swagger ドキュメントでポイントし、利用可能なアクションと入力を表示するには、[HTTP + Swagger アクション](../connectors/connectors-native-http-swagger.md)を使用します。

*  [HTTP アクション](../connectors/connectors-native-http.md)を使用した要求をいつでも作成し、Swagger ドキュメントがない API や Swagger ドキュメントを公開しない API を含む、あらゆる API を呼び出すことができます。

## <a name="authenticate-calls-to-your-custom-api"></a>カスタム API の呼び出しを認証する

次の方法でカスタム API の呼び出しを保護できます。

* [コードを変更しない](#no-code): Azure ポータル経由で [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) を利用し、API を保護します。そのため、コードを更新したり、API を再デプロイしたりする必要がありません。

  > [!NOTE]
  > 既定では、Azure Portal で Azure AD 認証をオンにしてもきめ細かい認証は実行されません。 たとえば、この認証は、特定のユーザーやアプリではなく、特定のテナントに API を制限します。 

* [API のコードを更新する](#update-code): コードを利用して[証明書認証](#certificate)、[基本認証](#basic)、[Azure AD 認証](#azure-ad-code)を強制することで API を保護します。

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>コードを変更せず、API の呼び出しを認証する

この方法の一般的な手順を次に示します。

1. 2 つの [Azure Active Directory (Azure AD) アプリケーション ID](../app-service-api/app-service-api-dotnet-service-principal-auth.md) を作成します。ロジック アプリ用に 1 つ、Web アプリ (または API アプリ) 用に 1 つです。

2. API の呼び出しを認証するには、ロジック アプリの Azure AD アプリケーション ID に関連付けられている[サービス プリンシパル](../app-service-api/app-service-api-dotnet-service-principal-auth.md)の資格情報 (クライアント ID とシークレット) を利用します。

3. アプリケーション ID をロジック アプリ定義に追加します。

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>パート 1: ロジック アプリの Azure AD アプリケーション ID を作成する

ロジック アプリはこのアプリケーション ID を利用し、Azure AD に対して認証します。 この ID は、ディレクトリに対して一度だけ設定します。 たとえば、ロジック アプリごとに一意の ID を作成できますが、すべてのロジック アプリに同じ ID を使用することを選択できます。 これらの ID は Azure ポータルと [Azure クラシック ポータル](#app-identity-logic-classic)で設定できます。あるいは、[PowerShell](#powershell) を利用できます。

**Azure ポータルでロジック アプリのアプリケーション ID を作成する**

1. [Azure ポータル](https://portal.azure.com "https://portal.azure.com") で、**[Azure Active Directory]** を選択します。 

2. Web アプリまたは API アプリと同じディレクトリにいることを確認します。

   > [!TIP]
   > ディレクトリを切り替えるには、プロファイルをクリックし、別のディレクトリを選択します。 あるいは、**[概要]** > **[ディレクトリの切り替え]** の順に選択します。

3. ディレクトリ メニューの **[管理]** で、**[アプリの登録]** > **[新しいアプリケーションの登録]** の順に選択します。

   > [!TIP]
   > 既定で、アプリ登録一覧にはディレクトリのすべてのアプリ登録が表示されます。 自分のアプリ登録のみを表示するには、検索ボックスの隣にある **[マイ アプリ]** を選択します。 

   ![新しいアプリ登録を作成する](./media/logic-apps-custom-hosted-api/new-app-registration-azure-portal.png)

4. アプリケーション ID に名前を付け、**[アプリケーションの種類]** を **[Web アプリ / API]** に設定し、**[サインオン URL]** のドメインとして書式設定された一意の文字列を指定し、**[作成]** を選択します。

   ![アプリケーション ID に名前を付け、サインオン URL を指定する](./media/logic-apps-custom-hosted-api/logic-app-identity-azure-portal.png)

   ロジック アプリに作成したアプリケーション ID がアプリ登録一覧に表示されます。

   ![ロジック アプリのアプリケーション ID](./media/logic-apps-custom-hosted-api/logic-app-identity-created.png)

5. アプリ登録一覧で、新しいアプリケーション ID を選択します。 **アプリケーション ID** をコピーして保存し、パート 3 でロジック アプリの "クライアント ID" として使用します。

   ![ロジック アプリのアプリケーション ID をコピーし、保存する](./media/logic-apps-custom-hosted-api/logic-app-application-id.png)

6. アプリケーション ID 設定が表示されない場合、**[設定]** または **[すべての設定]** を選択します。

7. **[API アクセス]** で **[キー]** を選択します。 **[説明]** にキーの名前を入力します。 **[有効期限]** で、キーの有効期間を選択します。

   ここで作成するキーは、アプリケーション ID の "シークレット" またはロジック アプリのパスワードとして機能します。

   ![ロジック アプリ ID のキーを作成する](./media/logic-apps-custom-hosted-api/create-logic-app-identity-key-secret-password.png)

8. ツールバーで、**[保存]** を選択します。 **[値]** にキーが表示されます。 
**後で使用するためにキーをコピーし、保存します**。キー ブレードを閉じるとキーは非表示になるためです。

   パート 3 でロジック アプリを構成するとき、このキーを "シークレット" またはパスワードとして指定します。

   ![後のためにキーをコピーして保存する](./media/logic-apps-custom-hosted-api/logic-app-copy-key-secret-password.png)

<a name="app-identity-logic-classic"></a>

**Azure クラシック ポータルでロジック アプリのアプリケーション ID を作成する**

1. Azure クラシック ポータルで [**[Active Directory]**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) を選択します。

2. Web アプリまたは API アプリに使用するものと同じディレクトリを選択します。

3. **[アプリケーション]** タブで、ページの下にある **[追加]** を選択します。

4. アプリケーション ID に名前を付け、**[次へ]** を選択します (右方向矢印)。

5. **[アプリケーションのプロパティ]** で、**[サインオン URL]** と **[アプリ ID/URI]** にドメインとして書式設定された一意の文字列を指定し、**[完了]** を選択します (チェックマーク)。

6. **[構成]** タブで、パート 3 で使用するロジック アプリの **[クライアント ID]** をコピーして保存します。

7. **[キー]** で、**[時間の選択]** 一覧を開きます。 キーの有効期間を選択します。

   ここで作成するキーは、アプリケーション ID の "シークレット" またはロジック アプリのパスワードとして機能します。

8. ページの下部にある **[保存]** を選択します。 場合によっては数秒待つ必要があります。

9. **[キー]** で、表示されたキーをコピーして保存します。 

   パート 3 でロジック アプリを構成するとき、このキーを "シークレット" またはパスワードとして指定します。

詳細については、「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md)」を参照してください。

<a name="powershell"></a>

**PowerShell でロジック アプリのアプリケーション ID を作成する**

このタスクは Azure Resource Manager と PowerShell で実行できます。 PowerShell で、次のコマンドレットを実行します。

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. **テナント ID** (Azure AD テナントの GUID)、**アプリケーション ID**、使用したパスワードを必ずコピーします。

詳細については、「[リソースにアクセスするためのサービス プリンシパルを Azure PowerShell で作成する](../azure-resource-manager/resource-group-authenticate-service-principal.md)」を参照してください。

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>パート 2: Web アプリまたは API アプリの Azure AD アプリケーション ID を作成する

Web アプリまたは API アプリが既にデプロイされている場合、Azure ポータルで認証をオンにし、アプリケーション ID を作成できます。 デプロイされていない場合、[Resource Manager テンプレートでデプロイするときに認証をオンにできます](#authen-deploy)。 

**デプロイされたアプリに対し、Azure ポータルでアプリケーション ID を作成し、認証をオンにする**

1. [Azure ポータル](https://portal.azure.com "https://portal.azure.com") で、Web アプリまたは API アプリを見つけ、選択します。 

2. **[設定]** で、**[認証/承認]** を選択します。 **[App Service 認証]** で認証を **[オン]** にします。 **[認証プロバイダー]** の下の **[Azure Active Directory]** を選択します。

   ![認証をオンにする](./media/logic-apps-custom-hosted-api/custom-web-api-app-authentication.png)

3. 次に、Web アプリまたは API アプリの Azure AD アプリケーション ID を作成します。 **[Azure Active Directory 設定]** ブレードで、**[管理モード]** を **[簡易]** に設定します。 **[新しい AD アプリを作成する]** を選択します。 アプリケーション ID に名前を付け、**[OK]** を選択します。 

   ![Web アプリまたは API アプリのアプリケーション ID を作成する](./media/logic-apps-custom-hosted-api/custom-api-application-identity.png)

4. **[認証/承認]** ブレードで、**[保存]** を選択します。

次に、Web アプリまたは API アプリに関連付けられているアプリケーション ID のクライアント ID とテナント ID を見つける必要があります。 ID はパート 3 で使用します。 そこで、Azure ポータルまたは [Azure クラシック ポータル](#find-id-classic)の場合、次の手順を続行します。

**Azure ポータルで、Web アプリまたは API アプリのアプリケーション ID のクライアント ID とテナント ID を見つける**

1. **[認証プロバイダー]** の下の **[Azure Active Directory]** を選択します。 

   !["Azure Active Directory" を選択する](./media/logic-apps-custom-hosted-api/custom-api-app-identity-client-id-tenant-id.png)

2. **[Azure Active Directory 設定]** ブレードで、**[管理モード]** を **[詳細]** に設定します。

3. **[クライアント ID]** をコピーし、パート 3 で使用するためにこの GUID を保存します。

   > [!TIP] 
   > **[クライアント ID]** と **[発行者の URL]** が表示されない場合、Azure ポータルを更新し、手順 1 を繰り返してください。

4. **[発行者の URL]** で、パート 3 のために GUID だけをコピーして保存します。 必要であれば、Web アプリまたは API アプリのデプロイ テンプレートでもこの GUID を使用できます。

   この GUID は特定のテナントの GUID ("テナント ID") であり、URL (`https://sts.windows.net/{GUID}`) に表示されます。

5. 変更を保存せず、**[Azure Active Directory の設定]** ブレードを閉じます。

<a name="find-id-classic"></a>

**Azure クラシック ポータルで、Web アプリまたは API アプリのアプリケーション ID のクライアント ID とテナント ID を見つける**

1. Azure クラシック ポータルで [**[Active Directory]**](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) を選択します。

2.  Web アプリまたは API アプリに使用したディレクトリを選択します。

3. **[検索]** ボックスで、Web アプリまたは API アプリのアプリケーション ID を見つけ、選択します。

4. **[構成]** タブで **[クライアント ID]** をコピーし、パート 3 で使用するためにこの GUID を保存します。

5. クライアント ID を取得したら、**[構成]** タブの下で **[エンドポイントの表示]** を選択します。

6. **[フェデレーション メタデータ ドキュメント]** の URL をコピーし、その URL に移動します。

7. メタデータ ドキュメントが開いたら、ルートの **EntityDescriptor ID** 要素を見つけます。これには `https://sts.windows.net/{GUID}` 形式で **entityID** 属性が含まれています。 

      この属性の GUID は、特定のテナントの GUID (テナント ID) です。

8. テナント ID をコピーし、パート 3 で使用するために保存します。必要であれば、Web アプリまたは API アプリのデプロイ テンプレートで使用することもできます。

詳細については、以下のトピックを参照してください。

* [Azure App Service の API Apps でのユーザー認証](../app-service-api/app-service-api-dotnet-user-principal-auth.md)
* [Azure App Service での認証および承認](../app-service/app-service-authentication-overview.md)

<a name="authen-deploy"></a>

**Resource Manager テンプレートでデプロイするときに認証をオンにする**

ロジック アプリのアプリ ID とは異なる Azure AD アプリケーション ID を Web アプリまたは API アプリに作成する必要があります。 アプリケーション ID を作成するには、パート 2 の Azure ポータルの手順に従います。 パート 1 の手順も利用できますが、**[サインオン URL]** と **[アプリ ID/URI]** には Web アプリまたは API アプリの実際の `https://{URL}` を使用してください。 これらの手順では、アプリのデプロイ テンプレートとパート 3 で使用するために、クライアント ID とテナント ID の両方を保存する必要があります。

> [!NOTE]
> Web アプリまたは API アプリの Azure AD アプリケーション ID を作成するときは、PowerShell ではなく、Azure ポータルか Azure クラシック ポータルを使用する必要があります。 PowerShell コマンドレットでは、ユーザーが Web サイトにサインインするために必要なアクセス許可が設定されません。

クライアント ID とテナント ID を取得したら、ID を Web アプリまたは API アプリのサブ リソースとしてデプロイ テンプレートに追加します。

   ```
   "resources": [
       {
           "apiVersion": "2015-08-01",
           "name": "web",
           "type": "config",
           "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
           "properties": {
               "siteAuthEnabled": true,
               "siteAuthSettings": {
                 "clientId": "{client-ID}",
                 "issuer": "https://sts.windows.net/{tenant-ID}/",
               }
           }
       }
   ]
   ```

Azure Active Directory 認証と共に、空の Web アプリやロジック アプリを自動デプロイするには、[ここで完全なテンプレートを表示する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)か、ここで **[Azure に配置する]** をクリックします。

[![Azure へのデプロイ](media/logic-apps-custom-hosted-api/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>パート 3: ロジック アプリの承認セクションの入力

先ほどのテンプレートではこの承認セクションが既に設定されていますが、ロジック アプリを直接作成する場合、承認セクション全体を追加する必要があります。

コード ビューでロジック アプリ定義を開き、**HTTP** アクション セクションに移動し、**[認証]** セクションを見つけ、次の行を追加します。

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| 要素 | Description |
| ------- | ----------- |
| テナント |Azure AD テナントの GUID |
| 対象となる読者 |必須。 アクセスするターゲット リソースの GUID - Web アプリまたは API アプリのアプリケーション ID からのクライアント ID |
| clientId |アクセスを要求するクライアントの GUID - ロジック アプリのアプリケーション ID からのクライアント ID |
| secret |必須。 アクセス トークンを要求しているクライアントのアプリケーション ID からのキーまたはパスワード |
| 型 |認証の種類。 ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth`を使用します。 |

For example:

```
{
   ...
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>コードを利用して API 呼び出しを保護する

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>証明書認証

ロジック アプリから Web アプリまたは API アプリに入ってくる要求を検証するために、クライアント証明書を利用できます。 コードの設定方法については、「[Web アプリの TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。

**[承認]** セクションで、次の行を追加します。 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| 要素 | 説明 |
| ------- | ----------- |
| type |必須。 認証の種類。 SSL クライアント証明書の場合、値として `ClientCertificate` を指定する必要があります。 |
| パスワード |必須。 クライアント証明書 (PFX ファイル) にアクセスするためのパスワード |
| pfx |必須。 Base64 でエンコードされた、クライアント証明書のコンテンツ (PFX ファイル) |

<a name="basic"></a>

#### <a name="basic-authentication"></a>基本認証

ロジック アプリから Web アプリまたは API アプリに入ってくる要求を検証するために、ユーザー名やパスワードなど、基本認証を利用できます。 基本認証は一般的なパターンなので、この認証は Web アプリまたは API アプリの作成にどの言語が使われていても使用できます。

**[承認]** セクションで、次の行を追加します。

`{"type": "basic", "username": "username", "password": "password"}`」を参照してください。

| 要素 | 説明 |
| --- | --- |
| type |必須。 認証の種類。 基本認証の場合、値 `Basic` を使用する必要があります。 |
| username |必須。 認証のユーザー名 |
| パスワード |必須。 認証のパスワード |

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>コードを利用した Azure Active Directory 認証

既定では、Azure Portal で Azure AD 認証をオンにしてもきめ細かい認証は実行されません。 たとえば、この認証は、特定のユーザーやアプリではなく、特定のテナントに API を制限します。 

コードを利用し、ロジック アプリへの API アクセスを制限するには、JSON Web トークン (JWT) が含まれるヘッダーを抽出します。 呼び出し元の ID を確認し、一致しない要求を拒否します。

さらに、この認証全体を独自のコードに実装し、Azure ポータルを使用しない場合は、「[Azure アプリでのオンプレミスの Active Directory を使用した認証](../app-service-web/web-sites-authentication-authorization.md)」を参照してください。

ロジック アプリのアプリケーション ID を作成して API の呼び出しに使用する場合、これまでの手順に従う必要があります。

## <a name="next-steps"></a>次のステップ

* [診断ログと警告によるロジック アプリ パフォーマンスの確認](logic-apps-monitor-your-logic-apps.md)
