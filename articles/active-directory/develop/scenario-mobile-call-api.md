---
title: モバイルアプリから Web API を呼び出す | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリの構築方法について説明します (Web API の呼び出し)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db58f94501590eb3150700d282377ec1b2378cea
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962544"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Web API を呼び出すモバイル アプリ - Web API の呼び出し

アプリがユーザーにサインインして、トークンを受信すると、ユーザーとユーザーの環境、および発行されたトークンに関するいくつかの情報が MSAL によって公開されます。 アプリではこれらの値が使用され、Web API を呼び出したり、ウェルカム メッセージをユーザーに表示したりすることができます。

最初に、MSAL の結果について説明し、 次に、`AuthenticationResult` または `result` からのアクセス トークンを使用して保護された Web API を呼び出す方法を説明します。

## <a name="msal-result"></a>MSAL の結果
MSAL には次のような値があります。 

- `AccessToken`:HTTP ベアラー要求で保護された Web API の呼び出しに使用されます。
- `IdToken`:サインインしたユーザーに関する有用な情報 (ユーザー名、ホーム テナント、ストレージの一意識別子など) が含まれます。
- `ExpiresOn`:トークンの有効期限。 MSAL によってアプリの自動更新が処理されます。
- `TenantId`:ユーザーがサインインに使用したテナントの識別子。 ゲスト ユーザー (Azure Active Directory B2B) の場合、この値により、ユーザーのホーム テナントではなく、ユーザーがサインインに使用したテナントが特定されます。  
- `Scopes`:お使いのトークンで付与されたスコープ。 付与されたスコープは、要求したスコープのサブセットである場合があります。

また、MSAL では `Account` の抽象化も提供されます。 `Account` は、現在のユーザーのサインインしたアカウントを表します。

- `HomeAccountIdentifier`:ユーザーのホーム テナントの識別子。
- `UserName`:ユーザーの推奨ユーザー名。 Azure Active Directory B2C ユーザーの場合、これは空の場合があります。
- `AccountIdentifier`:サインインしているユーザーの識別子。 ユーザーが別のテナントのゲストでない限り、ほとんどの場合、この値は `HomeAccountIdentifier` 値と同じになります。

## <a name="call-an-api"></a>API を呼び出す

アクセス トークンがあれば、Web API を呼び出すのは簡単です。 お使いのアプリではトークンを使って HTTP 要求が作成され、実行されます。

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>iOS と macOS での MSAL

トークンを取得するメソッドは `MSALResult` オブジェクトを返します。 `MSALResult` により、Web API を呼び出すために使用できる `accessToken` プロパティが公開されます。 保護された Web API にアクセスするための呼び出しを行う前に、アクセス トークンを HTTP Authorization ヘッダーに追加する必要があります。

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="making-several-api-requests"></a>複数の API 要求を行う

同じ API を複数回呼び出す必要がある場合、または複数の API を呼び出す必要がある場合は、アプリを構築するときに、次の点を考慮してください。

- **増分同意**:Microsoft ID プラットフォームでは、すべて開始時にではなく、必要に応じて、アプリがユーザーの同意をアクセス許可として得られるようにしています。 アプリが API を呼び出す準備ができたら、毎回、使用する必要があるスコープのみを要求します。
- **条件付きアクセス**:特定のシナリオでは、複数の API 要求を行うときに、追加の条件付きアクセスの要件が課される場合があります。 これは、最初の要求に条件付きアクセス ポリシーが適用されておらず、アプリが条件付きアクセスを必要とする新しい API にサイレントでアクセスしようとする場合に発生することがあります。 このシナリオを処理するため、必ずサイレント要求からのエラーをキャッチし、対話型の要求を行えるように準備します。  詳細については、[条件付きアクセスについてのガイダンス](conditional-access-dev-guide.md)のページを参照してください。

## <a name="calling-several-apis-in-xamarin-or-uwp---incremental-consent-and-conditional-access"></a>Xamarin または UWP での複数の API の呼び出し - 増分同意と条件付きアクセス

同じユーザーに対して複数の API を呼び出す必要がある場合、ユーザーのトークンを取得した後、続けて `AcquireTokenSilent` を呼び出してトークンを取得すれば、ユーザーに何度も資格情報の入力を求める必要がなくなります。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

対話が必要になるのは、以下のようなケースです。

- ユーザーが最初の API については同意したが、より多くのスコープ (増分同意) について同意する必要が生じた。
- 最初の API は多要素認証を必要としなかったが、次の API は多要素認証を必要とする。

```CSharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-mobile-production.md)
