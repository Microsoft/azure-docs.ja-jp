---
title: クイック スタート:Node.js を使用して REST API に検索要求を送信する - Bing Entity Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用すると、C# を使用して Bing Entity Search REST API に要求を送信し、JSON 応答を受信することができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-javascript
ms.openlocfilehash: 82bdd8f3890f1685aa442463287fe72bde08d518
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405961"
---
# <a name="quickstart-send-a-search-request-to-the-bing-entity-search-rest-api-using-nodejs"></a>クイック スタート:Node.js を使用して Bing Entity Search REST API に検索要求を送信する

このクイック スタートを使用すると、Bing Entity Search API への最初の呼び出しを行い、JSON 応答を表示することができます。 この簡単な JavaScript アプリケーションでは、新しい検索クエリを API に送信して、その応答を表示します。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingEntitySearchv7.js) で入手できます。

このアプリケーションは JavaScript で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

## <a name="prerequisites"></a>前提条件

* 最新バージョンの [Node.js](https://nodejs.org/en/download/)。

* [JavaScript Request ライブラリ](https://github.com/request/request)。

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、厳格度と HTTPS の要件を設定します。

    ```javaScript
    'use strict';
    let https = require ('https');
    ```

2. API エンドポイント、サブスクリプション キー、および検索クエリのための変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。

    ```javascript
    let subscriptionKey = 'ENTER YOUR KEY HERE';
    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/entities';
    
    let mkt = 'en-US';
    let q = 'italian restaurant near me';
    ```

3. `query` という文字列の後に、市場とクエリ パラメーターを追加します。 忘れずに、`encodeURI()` でクエリを URL エンコードします。
    ```javascript 
    let query = '?mkt=' + mkt + '&q=' + encodeURI(q);
    ```

## <a name="handle-and-parse-the-response"></a>応答の処理と解析

1. HTTP 呼び出し `response` をパラメーターとして受け取る `response_handler()` という名前の関数を定義します。 

2. この関数内で、JSON 応答の本文を含む変数を定義します。  
    ```javascript
    let response_handler = function (response) {
        let body = '';
    };
    ```

3. `data` フラグが呼び出されたら、応答の本文を格納します。
    ```javascript
    response.on('data', function (d) {
        body += d;
    });
    ```

4. `end` フラグが通知されたら、JSON を解析して、それを表示します。

    ```javascript
    response.on ('end', function () {
    let json = JSON.stringify(JSON.parse(body), null, '  ');
    console.log (json);
    });
        ```

## Send a request

1. Create a function called `Search()` to send a search request. In it, perform the following steps:

2. Within this function, create a JSON object containing your request parameters. Use `Get` for the method, and add your host and path information. Add your subscription key to the `Ocp-Apim-Subscription-Key` header. 

3. Use `https.request()` to send the request with the response handler created previously, and your search parameters.
    
   ```javascript
   let Search = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path + query,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };
    
    let req = https.request (request_params, response_handler);
    req.end ();
   }
      ```

2. `Search()` 関数を呼び出します。

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "https://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API とは](../overview.md )
* [Bing Entity Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)。
