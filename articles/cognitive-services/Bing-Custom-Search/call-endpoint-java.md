---
title: クイック スタート:Java を使用して Bing Custom Search エンドポイントを呼び出す | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Java で Bing Custom Search インスタンスに検索結果を要求します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 9090bf01c25c85158781160626f0d1bb72ab8ff2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352067"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-java"></a>クイック スタート:Java を使用して Bing Custom Search エンドポイントを呼び出す

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイックスタートでは、Bing Custom Search インスタンスに検索結果を要求する方法を説明します。 このアプリケーションは Java で記述されていますが、Bing Custom Search API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingCustomSearchv7.java) で入手できます。

## <a name="prerequisites"></a>前提条件

- Bing Custom Search インスタンス。 詳細については、「[クイック スタート: 最初の Bing Custom Search インスタンスの作成](quick-start.md)」を参照してください。

- 最新の [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html).

- [Gson ライブラリ](https://github.com/google/gson)。

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。

    ```java
    import java.io.InputStream;
    import java.net.URL;
    import java.net.URLEncoder;
    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;
    import java.util.Scanner;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. `CustomSrchJava` という名前のクラスを作成し、サブスクリプション キー、カスタム検索エンドポイント、および検索インスタンスのカスタム構成 ID の変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。
    ```java
    public class CustomSrchJava {
        static String host = "https://api.cognitive.microsoft.com";
        static String path = "/bingcustomsearch/v7.0/search";
        static String subscriptionKey = "YOUR-SUBSCRIPTION-KEY"; 
        static String customConfigId = "YOUR-CUSTOM-CONFIG-ID";
        static String searchTerm = "Microsoft";  
    ...
    ```

3. Bing Custom Search インスタンスからの応答を格納するために、`SearchResults` という名前の別のクラスを作成します。

    ```java
    class SearchResults {
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }
    ```

4. Bing Custom Search API からの JSON 応答を書式設定するために、`prettify()` という名前の関数を作成します。

    ```java
        // pretty-printer for JSON; uses GSON parser to parse and re-serialize
        public static String prettify(String json_text) {
            JsonParser parser = new JsonParser();
            JsonObject json = parser.parse(json_text).getAsJsonObject();
            Gson gson = new GsonBuilder().setPrettyPrinting().create();
            return gson.toJson(json);
        }
    ```

## <a name="send-and-receive-a-search-request"></a>検索要求の送信と受信 

1. 要求を送信して `SearchResults` オブジェクトを返す `SearchWeb()` という名前の関数を作成します。 カスタム構成 ID、クエリ、エンドポイントの情報を組み合わせることにより、要求の URL を作成します。 お使いのサブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            // construct the URL for your search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchTerm, "UTF-8") + "&CustomConfig=" + customConfigId);
            HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
            connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ...
    ```

2. ストリームを作成し、JSON の応答を `SearchResults` オブジェクトに格納します。

    ```java
    public class CustomSrchJava {
    ...
        public static SearchResults SearchWeb (String searchQuery) throws Exception {
            ...
            // receive the JSON body
            InputStream stream = connection.getInputStream();
            String response = new Scanner(stream).useDelimiter("\\A").next();
        
            // construct result object for return
            SearchResults results = new SearchResults(new HashMap<String, String>(), response);
            
            stream.close();
            return results;
        }
    ```

3. JSON の応答を出力します。

    ```java
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(result.jsonResponse));
    ```

4. プログラムを実行します。
    
## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Custom Search Web アプリの作成](./tutorials/custom-search-web-page.md)