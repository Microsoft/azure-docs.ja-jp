---
title: クイック スタート:Python を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: このクイック スタートでは、Python を使用して、Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: 9534a60e66f194bf653e1bfd28d6d6f2a96ba90a
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558721"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>クイック スタート:Python を使用して Bing Custom Search エンドポイントを呼び出す

このクイック スタートでは、Bing Custom Search インスタンスに検索結果を要求します。 このアプリケーションは Python で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。
- [Python](https://www.python.org/) 2.x または 3.x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. お気に入りの IDE またはエディターで新しい Python ファイルを作成し、次の import ステートメントを追加します。 サブスクリプション キー、カスタム構成 ID、検索語句に使用する変数を作成します。 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 検索語句を `q=` クエリ パラメーターに、また検索インスタンスのカスタム構成 ID を `customconfig=` に追加して要求 URL を作成します。 パラメーターの区切りには、`&` 文字を使用します。 

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Bing Custom Search インスタンスに要求を送信し、返された検索結果を出力します。  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)