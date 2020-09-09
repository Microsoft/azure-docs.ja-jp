---
title: Bing Video Search C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929273"
---
このクイックスタートでは、Bing Video Search の C# 用のクライアント ライブラリを使用してニュースを検索する方法を説明します。 Bing Video Search の REST API はほとんどのプログラミング言語に対応していますが、このクライアント ライブラリを使用すると、サービスをお使いのアプリケーションに簡単に統合することができます。 このサンプルのソース コードは [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) にあり、追加の注釈と機能があります。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017 またはそれ以降](https://visualstudio.microsoft.com/downloads/)の任意のエディション。
* [NuGet パッケージ](https://www.nuget.org/packages/Newtonsoft.Json/)として入手できる Json.NET フレームワーク。

ご自分のプロジェクトに Bing Video Search のクライアント ライブラリを追加するには、Visual Studio の**ソリューション エクスプローラー**から **[NuGet パッケージの管理]** をクリックします。 `Microsoft.Azure.CognitiveServices.Search.VideoSearch` パッケージを追加します。

[NuGet Video Search SDK パッケージ](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)をインストールすると、次の依存関係もインストールされます。

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>プロジェクトの作成と初期化

1. Visual Studio で、新しい C# コンソール ソリューションを作成します。 次に、メイン コード ファイルに次の内容を追加します。

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. サブスクリプション キーで新しい `ApiKeyServiceClientCredentials` オブジェクトを作成し、コンストラクターを呼び出して、クライアントをインスタンス化します。

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>検索要求を送信して結果を処理する

1. クライアントを使用して検索要求を送信します。 検索クエリの "SwiftKey" を使用します。

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. 結果が返された場合は、最初の結果を `videoResults.Value[0]` で取得します。 その後、動画の ID、タイトル、URL を出力します。

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [シングル ページ Web アプリを作成する](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>関連項目 

* [Bing Video Search API とは](../../overview.md)
* [Cognitive Services の .NET 向け SDK のサンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
