---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: cd6388e6c6313ba84978d43d388855b114a4875d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508543"
---
この記事では、Custom Vision Java クライアント ライブラリを使用して画像分類モデルを構築する基本的な方法について説明します。 作成後、タグを追加し、イメージをアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用してイメージをプログラミングでテストできます。 この例は、独自の Java アプリケーションを構築するためのテンプレートとしてご利用ください。 分類モデルの構築と使用のプロセスをコード "_なし_" で行う場合は、[ブラウザー ベースのガイダンス](../../getting-started-build-a-classifier.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- 任意の Java IDE
- [JDK 7 または 8](https://aka.ms/azure-jdks) がインストールされていること。
- [Maven](https://maven.apache.org/) がインストールされていること
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Custom Vision クライアント ライブラリとサンプル コードを入手する

Custom Vision を使用する Java アプリを作成するには、Custom Vision maven パッケージが必要となります。 これらのパッケージは、これからダウンロードするサンプル プロジェクトに含まれていますが、ここから個別にアクセスすることもできます。

Custom Vision クライアント ライブラリは、Maven Central Repository にあります。

- [Training SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [Prediction SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

[Cognitive Services Java SDK サンプル](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) プロジェクトを複製またはダウンロードします。 **Vision/CustomVision/** フォルダーに移動します。

この Java プロジェクトは、__Sample Java Project__ という名前の新しい Custom Vision 画像分類プロジェクトを作成します。作成したプロジェクトには、[Custom Vision Web サイト](https://customvision.ai/)からアクセスすることができます。 その後、イメージをアップロードして分類子のトレーニングおよびテストを行います。 このプロジェクトでは、木が __Hemlock__ (ドクニンジン) であるか __Japanese Cherry__ (桜) であるかを判別することが、分類子の目的となります。

[!INCLUDE [get-keys](../../includes/get-keys.md)]

このプログラムは、キー データを環境変数として参照するように構成されています。 **Vision/CustomVision** フォルダーに移動し、次の PowerShell コマンドを入力して環境変数を設定します。 

> [!NOTE]
> Windows 以外のオペレーティング システムを使用している場合は、[環境変数の構成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication)に関するセクションの手順を参照してください。

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>コードの理解

Java IDE で `Vision/CustomVision` プロジェクトを読み込み、_CustomVisionSamples.java_ ファイルを開きます。 **runSample** メソッドを探して、**ObjectDetection_Sample** メソッド呼び出しをコメント アウトしてください&mdash;このメソッドで物体検出のシナリオが実行されますが、このガイドの対象外となります。 この例の主な機能は、**ImageClassification_Sample** メソッドに実装されます。このメソッドの定義に移動し、コードを詳しく調べてみましょう。

### <a name="create-a-custom-vision-service-project"></a>Custom Vision Service プロジェクトを作成する

画像分類プロジェクトは、この最初の数行のコードで作成されます。 作成したプロジェクトは、先ほどアクセスした [Custom Vision Web サイト](https://customvision.ai/)に表示されます。 プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) メソッドのオーバーロードを参照してください ([分類子の構築](../../getting-started-build-a-classifier.md)に関する Web ポータル ガイドで説明されています)。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>プロジェクトにタグを作成する

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

サンプル画像は、プロジェクトの **src/main/resources** フォルダーに格納されています。 そこから読み取られて、それぞれの適切なタグと共にサービスにアップロードされます。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

前のコード スニペットでは、リソース ストリームとして画像を取得してサービスにアップロードする 2 つのヘルパー関数を使用しています (1 回のバッチで最大 64 個の画像をアップロードできます)。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>分類器をトレーニングしてする公開する

このコードにより、予測モデルの最初のイテレーションが作成され、そのイテレーションが予測エンドポイントに公開されます。 公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>予測エンドポイントを使用する

予測エンドポイントは、現在のモデルに画像を送信して分類予測を取得する際に使用できる参照で、ここでは `predictor` で表されます。 このサンプルでは、どこか他の場所で、予測キーの環境変数を使用して `predictor` が定義されています。

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>アプリケーションの実行

Maven を使用してソリューションをコンパイル、実行するには、コマンド プロンプトでプロジェクト ディレクトリ (**Vision/CustomVision**) に移動し、次のコマンドを実行します。

```bash
mvn compile exec:java
```

アプリケーションのコンソール出力は次のテキストのようになります。

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

テスト画像の予測結果 (最後の数行の出力) が正しいことを確認してください。

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>次のステップ

物体検出処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](../../test-your-model.md)
