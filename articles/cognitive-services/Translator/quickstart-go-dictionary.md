---
title: 'クイック スタート: 翻訳の代替候補を探す (Go) - Translator Text API'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Translator Text API と Go を使って、文脈中における用語の例と翻訳の代替候補を探します。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: erhopf
ms.openlocfilehash: bcda716d143bd675f9510b1ecf5974ab9c28a394
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000585"
---
# <a name="quickstart-use-the-translator-text-api-to-get-alternate-translations-using-go"></a>クイック スタート: Translator Text API と Go を使用して翻訳の代替候補を取得する

このクイック スタートでは、Go と Translator Text REST API を使用して、翻訳の代替候補を探す方法と特定のテキストの使用例を紹介します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [Go](https://golang.org/doc/install)
* Translator Text の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Go プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `alt-translations.go` という名前のファイルにコピーします。

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## <a name="create-the-main-function"></a>main 関数を作成する

このサンプルでは、環境変数 `TRANSLATOR_TEXT_KEY` から Translator Text のサブスクリプション キーが読み取られるよう試行されます。 環境変数を使い慣れていない場合は、`subscriptionKey` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our altTranslations function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    altTranslations(subscriptionKey)
}
```

## <a name="create-a-function-to-get-alternate-translations"></a>翻訳の代替候補を取得するための関数を作成する

翻訳の代替候補を取得する関数を作成しましょう。 この関数では、単一の引数である Translator Text サブスクリプション キーを使用します。

```go
func altTranslations(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

次に、URL を構築しましょう。 URL は、`Parse()` メソッドと `Query()` メソッドを使用して構築されます。 `Add()` メソッドによってパラメーターが追加されることに気付くでしょう。 このサンプルでは、英語からスペイン語に翻訳しています。

このコードを `altTranslations` 関数内にコピーします。

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0")
q := u.Query()
q.Add("from", "en")
q.Add("to", "es")
u.RawQuery = q.Encode()
```

>[!NOTE]
> エンドポイント、ルート、および要求パラメーターの詳細については、「[Translator Text API 3.0: 辞書検索](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-dictionary-lookup)」をご覧ください。

## <a name="create-a-struct-for-your-request-body"></a>要求本文の構造体を作成する

次に、要求本文の匿名の構造体を作成し、`json.Marshal()` を使用して JSON としてエンコードします。 そのコードを `altTranslations` 関数に追加します。

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
    Text string
}{
    {Text: "Pineapples"},
}
b, _ := json.Marshal(body)
```

## <a name="build-the-request"></a>要求を作成する

要求本文を JSON としてエンコードしたので、ご自分の POST 要求を作成し、Translator Text API を呼び出すことができます。

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## <a name="handle-and-print-the-response"></a>応答を処理して出力する

次のコードを `altTranslations` 関数に追加して、JSON 応答をデコードした後、結果を書式設定して出力します。

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
    log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator Text API を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
go run alt-translations.go
```

作成したコードをサンプル コードと比較したい場合は、完全なサンプルを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go) から入手できます。

## <a name="sample-response"></a>応答のサンプル

```json
[
  {
    "displaySource": "pineapples",
    "normalizedSource": "pineapples",
    "translations": [
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 158,
            "normalizedText": "pineapples",
            "numExamples": 5
          },
          {
            "displayText": "cones",
            "frequencyCount": 13,
            "normalizedText": "cones",
            "numExamples": 5
          },
          {
            "displayText": "piña",
            "frequencyCount": 5,
            "normalizedText": "piña",
            "numExamples": 3
          },
          {
            "displayText": "ganks",
            "frequencyCount": 3,
            "normalizedText": "ganks",
            "numExamples": 2
          }
        ],
        "confidence": 0.7016,
        "displayTarget": "piñas",
        "normalizedTarget": "piñas",
        "posTag": "NOUN",
        "prefixWord": ""
      },
      {
        "backTranslations": [
          {
            "displayText": "pineapples",
            "frequencyCount": 16,
            "normalizedText": "pineapples",
            "numExamples": 2
          }
        ],
        "confidence": 0.2984,
        "displayTarget": "ananás",
        "normalizedTarget": "ananás",
        "posTag": "NOUN",
        "prefixWord": ""
      }
    ]
  }
]
```

## <a name="next-steps"></a>次の手順

GitHub の [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) から Cognitive Services API の Go パッケージを詳しく見てみましょう。

> [!div class="nextstepaction"]
> [GitHub で Go パッケージを詳しく見てみる](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## <a name="see-also"></a>関連項目

Translator Text API を使用して以下を実行する方法を確認します。

* [テキストを翻訳する](quickstart-go-translate.md)
* [テキストを表記変換する](quickstart-go-transliterate.md)
* [入力によって言語を識別する](quickstart-go-detect.md)
* [サポートされている言語の一覧を取得する](quickstart-go-languages.md)
* [入力から文章の長さを判定する](quickstart-go-sentences.md)
