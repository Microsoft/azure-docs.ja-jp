---
title: クイック スタート:C# 用の SDK を使用して物体検出プロジェクトを作成する - Custom Vision
titleSuffix: Azure Cognitive Services
description: .NET SDK と C# を使用して、プロジェクトの作成、タグの追加、画像のアップロード、プロジェクトのトレーニング、物体の検出を行います。
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 12/05/2019
ms.author: areddish
ms.openlocfilehash: ed86a286051ba9240fe23951ef7cb72de1e96201
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74960572"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-net-sdk"></a>クイック スタート:Custom Vision .NET SDK を使用して物体検出プロジェクトを作成する

この記事では、Custom Vision SDK と C# を使用して物体検出モデルを構築する基本的な方法について説明します。 作成後、タグ付きのリージョンを追加し、画像をアップロードし、プロジェクトをトレーニングし、プロジェクトの既定の予測エンドポイント URL を取得し、エンドポイントを使用して画像をプログラミングでテストできます。 この例は、独自の .NET アプリケーションを構築するためのテンプレートとしてご利用ください。 

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Custom Vision SDK とサンプル コードを入手する

Custom Vision を使用する .NET アプリを作成するには、Custom Vision NuGet パッケージが必要となります。 これらのパッケージは、これからダウンロードするサンプル プロジェクトに含まれていますが、ここから個別にアクセスすることもできます。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET サンプル](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) プロジェクトを複製またはダウンロードします。 **CustomVision/ObjectDetection** フォルダーに移動して、_ObjectDetection.csproj_ を Visual Studio で開きます。

この Visual Studio プロジェクトは、__My New Project__ という名前の新しい Custom Vision プロジェクトを作成します。作成したプロジェクトには、[Custom Vision Web サイト](https://customvision.ai/)からアクセスすることができます。 次に、画像をアップロードして物体検出モデルのトレーニングおよびテストを行います。 このプロジェクトでは、画像からフォークとハサミを検出するためのモデルがトレーニングされます。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>コードの理解

_Program.cs_ ファイルを開いて、コードを詳しく調べます。 トレーニング キーと予測キーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)し、それぞれ `CUSTOM_VISION_TRAINING_KEY`、`CUSTOM_VISION_PREDICTION_KEY` という名前を付けます。 スクリプトによってこれらの変数が検索されます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

また、Custom Vision Web サイトの [設定] ページからエンドポイント URL を取得します。 それを `CUSTOM_VISION_ENDPOINT` という環境変数に保存します。 スクリプトによって、この環境変数への参照がクラスのルートに保存されます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>新しい Custom Vision Service プロジェクトを作成する

次の数行のコードは、物体検出プロジェクトを作成します。 作成したプロジェクトは、先ほどアクセスした [Custom Vision Web サイト](https://customvision.ai/)に表示されます。 プロジェクトを作成するときに他のオプションを指定するには、[CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) メソッドを参照してください ([検出機能の構築](get-started-build-detector.md)に関する Web ポータル ガイドで説明されています)。  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>プロジェクトにタグを追加する

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>画像をアップロードし、タグ付けする

物体検出プロジェクトで画像にタグを付ける際は、タグ付けする各物体の領域を正規化座標を使用して指定する必要があります。 次のコードは、それぞれのサンプル画像とそのタグ付け領域とを関連付けるものです。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

さらに、この関連付けのマップを使用して、それぞれのサンプル画像を対応する領域の座標と共にアップロードします。 1 回のバッチで最大 64 個の画像をアップロードできます。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

この時点で、すべてのサンプル画像はアップロード済みです。それぞれの画像には、関連するピクセル四角形と共にタグ (**fork** または **scissors**) が付けられています。

### <a name="train-the-project"></a>プロジェクトをトレーニングする

このコードは、プロジェクトにおけるトレーニングの初回イテレーションを作成するものです。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>現在のイテレーションを公開する

公開されたイテレーションに付けられた名前は、予測要求を送信するために使用できます。 イテレーションは、公開されるまで予測エンドポイントで利用できません。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>予測エンドポイントを作成する

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>予測エンドポイントを使用する

この部分のスクリプトでは、テスト画像を読み込み、モデル エンドポイントを照会して、予測データをコンソールに出力します。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行すると、コンソール ウィンドウが開いて、次の出力が書き込まれます。

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

**Images/Test/** 内のテスト画像にタグが適切に付けられていること、また検出の領域が正しいことを確認してください。 この時点で、任意のキーを押してアプリケーションを終了できます。

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>次の手順

以上、物体検出処理の各ステップをコードでどのように実装するかを見てきました。 このサンプルで実行したトレーニングのイテレーションは 1 回だけですが、多くの場合、精度を高めるために、モデルのトレーニングとテストは複数回行う必要があります。 次のガイドでは、画像の分類について取り上げていますが、その原理は物体の検出と似ています。

> [!div class="nextstepaction"]
> [モデルのテストと再トレーニング](test-your-model.md)
