---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: e9cedbe90518ba32d752b0fa9d5afb37042a9eea
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375454"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Gradle を使用してプロジェクトを初期化する

まずは、このプロジェクトの作業ディレクトリを作成しましょう。 コマンド ライン (またはターミナル) から、次のコマンドを実行します。

```console
mkdir length-sentence-sample
cd length-sentence-sample
```

次に、Gradle プロジェクトを初期化します。 次のコマンドを実行すると、Gradle の重要なビルド ファイルが作成されます。特に重要なのは `build.gradle.kts` です。これは、アプリケーションを作成して構成するために、実行時に使用されます。 作業ディレクトリから次のコマンドを実行します。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

## <a name="configure-the-build-file"></a>ビルド ファイルを構成する

`build.gradle.kts` の場所を特定し、好みの IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。

```
plugins {
    java
    application
}
application {
    mainClassName = "BreakSentence"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

このサンプルでは、HTTP 要求の処理に OkHttp を使用し、JSON の処理と解析に Gson を使用していることに注意してください。 ビルド構成について詳しくは、「[Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/)」(新しい Gradle ビルドの作成) をご覧ください。

## <a name="create-a-java-file"></a>Java ファイルを作成する

サンプル アプリ用のフォルダーを作成しましょう。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

次に、このフォルダー内に `BreakSentence.java` というファイルを作成します。

## <a name="import-required-libraries"></a>必要なライブラリをインポートする

`BreakSentence.java` を開き、次のインポート ステートメントを追加します。

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>変数の定義

まず、プロジェクトのパブリック クラスを作成する必要があります。

```java
public class BreakSentence {
  // All project code goes here...
}
```

次の行を `BreakSentence` クラスに追加します。 まず、サブスクリプション キーとエンドポイントが環境変数から読み取られます。 `api-version` と共に、入力言語を定義できることがわかります。 このサンプルでは英語です。

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/breaksentence?api-version=3.0&language=en";
```
Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のパラメーターに `Ocp-Apim-Subscription-Region` も含める必要があります。 [マルチサービス サブスクリプションを使用した認証の詳細を参照してください](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="create-a-client-and-build-a-request"></a>クライアントを作成して要求をビルドする

次の行を `BreakSentence` クラスに追加して、`OkHttpClient` をインスタンス化します。

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

次に、POST 要求をビルドしましょう。 テキストは変更してかまいません。 テキストはエスケープする必要があります。

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"How are you? I am fine. What did you do today?\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>応答を解析するための関数を作成する

このシンプルな関数は、Translator サービスからの JSON 応答を解析し、整形するものです。

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

最後に、要求を実行して応答を取得します。 次の行をプロジェクトに追加します。

```java
public static void main(String[] args) {
    try {
        BreakSentence breakSentenceRequest = new BreakSentence();
        String response = BreakSentenceRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、サンプル アプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) で作業ディレクトリのルートに移動して、次のコマンドを実行します。

```console
gradle build
```

ビルドが完了したら、次のコマンドを実行します。

```console
gradle run
```

## <a name="sample-response"></a>応答のサンプル

成功した応答は、次の例に示すように JSON で返されます。

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>次のステップ

API のリファレンスを見て、Translator でできるすべてのことを理解してください。

> [!div class="nextstepaction"]
> [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
