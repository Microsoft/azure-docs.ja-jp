---
title: クイック スタート:Java 用 Bing Visual Search クライアント ライブラリ | Microsoft Docs
description: Bing Visual Search SDK を使用して画像をアップロードし、画像に関する分析情報を取得します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 09/30/2019
ms.author: aahi
ms.openlocfilehash: 7fb00fd3ce588aeeba4f315f191f6b82d6b75715
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695726"
---
# <a name="quickstart-bing-visual-search-client-library-for-java"></a>クイック スタート:Java 用 Bing Visual Search クライアント ライブラリ

Java 用 Bing Visual Search クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Java 用 Bing Visual Search クライアント ライブラリを使用して、次のことを行います。

* ビジュアル検索要求を送信するために、画像をアップロードします。
* 画像の分析情報トークンとビジュアル検索タグを取得します。

[リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [成果物 (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Java Development Kit(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle ビルド ツール](https://gradle.org/install/)、または別の依存関係マネージャー

## <a name="setting-up"></a>設定

### <a name="create-a-bing-visual-search-azure-resource"></a>Bing Visual Search の Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) またはローカル コンピューター上の [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、Bing Visual Search 用のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。  
* [Azure portal](https://portal.azure.com/) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、`BING_SEARCH_V7_SUBSCRIPTION_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

### <a name="create-a-new-gradle-project"></a>新しい Gradle プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 

```console
mkdir myapp && cd myapp
```

作業ディレクトリから `gradle init` コマンドを実行します。 このコマンドによって、Gradle 用の重要なビルド ファイルが作成されます。たとえば、実行時にアプリケーションを作成して構成するために使用される *build.gradle.kts* などです。

```console
gradle init --type basic
```

**DSL** を選択するよう求められたら、**Kotlin** を選択します。

*build.gradle.kts* を検索し、任意の IDE またはテキスト エディターで開きます。 その後、次のビルド構成をコピーします。

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

サンプル アプリ用のフォルダーを作成します。 作業ディレクトリから、次のコマンドを実行します。

```console
mkdir -p src/main/java
```

API にアップロードする画像のためのフォルダーを作成します。 画像を **resources** フォルダー内に配置します。

```console
mkdir -p src/main/resources
``` 

新しいフォルダーに移動し、*BingVisualSearchSample.java* という名前のファイルを作成します。 それを任意のエディターまたは IDE で開き、以下の `import` ステートメントを追加します。

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

次に、新しいクラスを作成します。

```java
public class BingVisualSearchSample {
}
```

アプリケーションの `main` メソッドで、対象のリソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。 次に、アップロードしている画像用の `byte[]` を作成します。 後で定義するメソッド用の `try` ブロックを作成し、画像を読み込んで、`toByteArray()` を使用してバイトに変換します。

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

このクイックスタートでは、Gradle 依存関係マネージャーを使用します。 クライアント ライブラリとその他の依存関係マネージャーの情報については、[Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/) を参照してください。

プロジェクトの *build.gradle.kts* ファイルに、`implementation` ステートメントとしてクライアント ライブラリを含めます。 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>コード例

以下のコード スニペットでは、Bing Visual Search クライアント ライブラリと Java を使用して、以下のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [ビジュアル検索要求を送信する](#send-a-visual-search-request)
* [画像の分析情報トークンとビジュアル検索タグを出力する](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、`BING_SEARCH_V7_SUBSCRIPTION_KEY` という名前の Bing Visual Search キーのために[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)してあることを前提としています。


main メソッドでは、[BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) オブジェクトをインスタンス化するときに、必ずサブスクリプション キーを使用してください。

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>ビジュアル検索要求を送信する

新しいメソッドで、クライアントの [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) メソッドを使用して、画像バイト配列 (`main()` メソッドで作成されたもの) を送信します。 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>画像の分析情報トークンとビジュアル検索タグを出力する

[ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) オブジェクトが null であるかどうかを確認します。 そうでない場合は、画像分析情報トークン、タグの数、アクションの数、および最初のアクションの種類を出力します。

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>アプリケーションの実行

次を使用してアプリをビルドできます。

```console
gradle build
```

`run` ゴールを使用してアプリケーションを実行します。

```console
gradle run
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [シングルページ Web アプリの作成](tutorial-bing-visual-search-single-page-app.md)
