---
title: クイック スタート:REST API と Ruby を使用してスペルをチェックする - Bing Spell Check
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API と Ruby を使用してスペルと文法をチェックしてみましょう。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.openlocfilehash: 46b62b5721eca622fcf2795a395653071bb88c95
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352645"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-ruby"></a>クイック スタート:Bing Spell Check REST API と Ruby を使用してスペルをチェックする

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

このクイック スタートを使用して、Bing Spell Check の REST API を Ruby から呼び出してみましょう。 このシンプルなアプリケーションは、API に要求を送信して、一連の修正候補を返します。 

このアプリケーションは Ruby で記述されていますが、この API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。 このアプリケーションのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/ruby/Search/BingSpellCheckv7.rb) で入手できます。

## <a name="prerequisites"></a>前提条件

* [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) 以降

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>アプリケーションを作成して初期化する

1. 普段使用しているエディターまたは IDE で新しい Ruby ファイルを作成し、次の要件を追加します。 

    ```ruby
    require 'net/http'
    require 'uri'
    require 'json'
    ```

2. サブスクリプション キー、エンドポイントの URI、パスの変数を作成します。 次のコードのグローバル エンドポイントを使用するか、Azure portal に表示される、対象のリソースの[カスタム サブドメイン](../../../cognitive-services/cognitive-services-custom-subdomains.md) エンドポイントを使用することができます。 要求のパラメーターを作成します。

   1. `=` 演算子を使用して、`mkt` パラメーターに市場コードを割り当てます。 市場コードは、要求の送信元となる国/地域のコードです。 

   1. `&` 演算子を使用して `mode` パラメーターを追加し、スペルチェック モードを割り当てます。 モードは `proof` (スペルまたは文法のほとんどのエラーが検出されます)、または `spell` (スペル ミスはほとんど検出されますが、文法エラーの検出数は相対的に少なくなります) のいずれかにすることができます。 

    ```ruby
    key = 'ENTER YOUR KEY HERE'
    uri = 'https://api.cognitive.microsoft.com'
    path = '/bing/v7.0/spellcheck?'
    params = 'mkt=en-us&mode=proof'
    ```

## <a name="send-a-spell-check-request"></a>スペル チェック要求を送信する

1. ホストの URI、パス、パラメーターの文字列から URI を作成します。 スペル チェックの対象となるテキストを含むようにクエリを設定します。

   ```ruby
   uri = URI(uri + path + params)
   uri.query = URI.encode_www_form({
      # Request parameters
   'text' => 'Hollo, wrld!'
   })
   ```

2. 前に作成した URI を使用して要求を作成します。 お使いのキーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

    ```ruby
    request = Net::HTTP::Post.new(uri)
    request['Content-Type'] = "application/x-www-form-urlencoded"
    request['Ocp-Apim-Subscription-Key'] = key
    ```

3. 要求を送信します。

    ```ruby
    response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
        http.request(request)
    end
    ```

4. JSON 応答を取得してコンソールに出力します。 

    ```ruby
    result = JSON.pretty_generate(JSON.parse(response.body))
    puts result
    ```

## <a name="run-the-application"></a>アプリケーションの実行

プロジェクトをビルドして実行します。 コマンド ラインを使用している場合は、次のコマンドを使用してアプリケーションを実行します。

   ```bash
   ruby <FILE_NAME>.rb
   ```

## <a name="example-json-response"></a>JSON の応答例

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [単一ページの Web アプリの作成](../tutorials/spellcheck.md)

- [Bing Spell Check API とは](../overview.md)
- [Bing Spell Check API v7 リファレンス](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)