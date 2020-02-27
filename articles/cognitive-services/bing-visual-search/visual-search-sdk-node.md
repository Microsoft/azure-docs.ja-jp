---
title: クイック スタート:Node.js 用の SDK を使用して画像に関する分析情報を取得する - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: このクイック スタートを使用すると、Node.js SDK を使用して、Bing Visual Search サービスから画像に関する分析情報を取得することができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: aahi
ms.openlocfilehash: d99aa2d2827716b2b04d059e47d9768eef8cd690
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485099"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-sdk-for-nodejs"></a>クイック スタート:Bing Visual Search SDK for Node.js を使用して画像に関する分析情報を取得する

このクイック スタートを使用すると、Node.js SDK を使用して、Bing Visual Search サービスから画像に関する分析情報を取得することができます。 Bing Visual Search には、ほとんどのプログラミング言語に対応した REST API がありますが、SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/visualSearch.js) にあります。 

## <a name="prerequisites"></a>前提条件
* [Node.js](https://www.nodejs.org/)
* Bing Visual Search SDK for Node.js
    * Bing Visual Search SDK を使用してコンソール アプリケーションを設定するには、以下のコマンドを実行します。
        1. `npm install ms-rest-azure`
        2. `npm install azure-cognitiveservices-visualsearch`


[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

<a name="client"></a>

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 好みの IDE またはエディターで新しい JavaScript ファイルを作成し、次の要件を追加します。 次に、サブスクリプション キー、カスタム構成 ID、およびアップロードする画像へのファイル パス用の変数を作成します。 

    ```javascript
    const os = require("os");
    const async = require('async');
    const fs = require('fs');
    const Search = require('azure-cognitiveservices-visualsearch');
    const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
    
    let keyVar = 'YOUR-VISUAL-SEARCH-ACCESS-KEY';
    let credentials = new CognitiveServicesCredentials(keyVar);
    let filePath = "../Data/image.jpg";
    ```

2. クライアントをインスタンス化します。

    ```javascript
    let visualSearchClient = new Search.VisualSearchClient(credentials);
    ```

## <a name="search-for-images"></a>画像を検索する

1. `fs.createReadStream()` を使用して画像ファイルを読み取り、検索要求と結果のための変数を作成します。 次に、クライアントを使用して画像を検索します。

    ```javascript
    let fileStream = fs.createReadStream(filePath);
    let visualSearchRequest = JSON.stringify({});
    let visualSearchResults;
    try {
        visualSearchResults = await visualSearchClient.images.visualSearch({
            image: fileStream,
            knowledgeRequest: visualSearchRequest
        });
        console.log("Search visual search request with binary of image");
    } catch (err) {
        console.log("Encountered exception. " + err.message);
    }
    ```

2. 上記のクエリの結果を解析します。

    ```javascript
    // Visual Search results
    if (visualSearchResults.image.imageInsightsToken) {
        console.log(`Uploaded image insights token: ${visualSearchResults.image.imageInsightsToken}`);
    }
    else {
        console.log("Couldn't find image insights token!");
    }
    
    // List of tags
    if (visualSearchResults.tags.length > 0) {
        let firstTagResult = visualSearchResults.tags[0];
        console.log(`Visual search tag count: ${visualSearchResults.tags.length}`);
    
        // List of actions in first tag
        if (firstTagResult.actions.length > 0) {
            let firstActionResult = firstTagResult.actions[0];
            console.log(`First tag action count: ${firstTagResult.actions.length}`);
            console.log(`First tag action type: ${firstActionResult.actionType}`);
        }
        else {
            console.log("Couldn't find tag actions!");
        }
    
    }
    else {
        console.log("Couldn't find image tags!");
    }
    
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](tutorial-bing-visual-search-single-page-app.md)
