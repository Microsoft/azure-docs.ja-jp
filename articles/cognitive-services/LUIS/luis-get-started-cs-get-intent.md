---
title: 意図の取得、C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: この C# クイック スタートでは、提供されているパブリック LUIS アプリを使って、会話形式のテキストからユーザーの意図を判断します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0e58fb35fa29cde16b1ccf50dc20dacc693a4757
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141766"
---
# <a name="quickstart-get-intent-using-c"></a>クイック スタート: C# による意図の取得

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>前提条件

* [Visual Studio Community 2017 エディション](https://visualstudio.microsoft.com/vs/community/)
* C# プログラミング言語 (VS Community 2017 に含まれています)
* パブリック アプリ ID: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>LUIS キーを取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>ブラウザーで意図を取得する

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>プログラムで意図を取得する

前のセクションでブラウザー ウィンドウに表示された結果は、C# を使って予測エンドポイントの GET [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) を照会することでも取得できます。 

1. Visual Studio で、新しいコンソール アプリケーションを作成します。 

    ![Visual Studio で新しいコンソール アプリケーションを作成する](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. Visual Studio プロジェクトで、ソリューション エクスプローラーの **[参照の追加]** を選択し、[アセンブリ] タブから **[System.Web]** を選択します。

    ![[参照の追加] を選択して [アセンブリ] タブから System.Web を選択する](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Program.cs を次のコードで上書きします。
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. `YOUR_KEY` の値を、実際の LUIS キーに置き換えます。

5. コンソール アプリケーションをビルドして実行します。 前の手順でブラウザー ウィンドウに表示されたものと同じ JSON が表示されます。

    ![コンソール ウィンドウに LUIS の JSON の結果が表示される](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>LUIS キー

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイック スタートを完了したら、Visual Studio プロジェクトを閉じ、ファイル システムからプロジェクトのディレクトリを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [C# を使った発話の追加とトレーニング](luis-get-started-cs-add-utterance.md)