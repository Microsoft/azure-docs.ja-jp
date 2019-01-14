---
title: クイック スタート:C# を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: このクイック スタートでは、C# で Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: maheshb
ms.openlocfilehash: 764a6a060a3ce2c7af9e4051b02ad45d14d27900
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557820"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>クイック スタート:C# を使用して Bing Custom Search エンドポイントを呼び出す 

このクイック スタートでは、Bing Custom Search インスタンスに検索結果を要求します。 このアプリケーションは C# で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs) にあります。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 「[クイック スタート:初めての Bing Custom Search インスタンスを作成する](quick-start.md)」で詳細を確認する。
- Microsoft [.Net Core](https://www.microsoft.com/net/download/core)
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) のいずれかのエディション
- Linux/macOS を使用している場合、このアプリケーションは [Mono](http://www.mono-project.com/) を使用して実行できます。
- [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) パッケージがインストール済みであること。 
    - Visual Studio でソリューション エクスプ ローラーから、プロジェクトを右クリックして、メニューから `Manage NuGet Packages` を選択します。 `Microsoft.Azure.CognitiveServices.Search.CustomSearch` パッケージをインストールします。 NuGet Custom Search パッケージをインストールすると、次のアセンブリもインストールされます。
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. Visual Studio で、新しい C# コンソール アプリケーションを作成します。 次のパッケージをプロジェクトに追加します。

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Bing Custom Search API から返された検索結果を格納する次のクラスを作成します。

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. プロジェクトのメイン メソッドで、Bing Custom Search API のサブスクリプション キー、検索インスタンスのカスタム構成 ID、検索語句に使用する各変数を作成します。

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. 検索語句を `q=` クエリ パラメーターに、また検索インスタンスのカスタム構成 ID を `customconfig=` に追加して要求 URL を作成します。 パラメーターの区切りには、`&` 文字を使用します。 

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 要求のクライアントを作成し、`Ocp-Apim-Subscription-Key` ヘッダーにサブスクリプション キーを追加します。

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. 検索要求を実行し、応答を JSON オブジェクトとして取得します。

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>結果の処理と表示

1. 応答オブジェクトを反復処理して、それぞれの検索結果についての情報 (名前、URL、Web ページが最後にクロールされた日付など) を表示します。

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)