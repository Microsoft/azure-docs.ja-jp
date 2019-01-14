---
title: 'クイック スタート: 音声を認識する (.NET Framework (Windows)) - Speech Services'
titleSuffix: Azure Cognitive Services
description: このガイドでは、.NET framework for Windows と Speech SDK を使用して音声変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: f5b33f0710070ff71ab52a1798773fe97965ef34
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719066"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>クイック スタート:.NET Framework (Windows) 用 Speech SDK を使用して音声を認識する

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

このガイドでは、.NET framework for Windows と Speech SDK を使用して音声変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。

## <a name="prerequisites"></a>前提条件

このプロジェクトを完了するには、以下が必要になります。

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* 音声サービス用のサブスクリプション キー。 [無料で 1 つ取得します](get-started.md)。
* お使いのコンピューターに備わっているマイクへのアクセス

## <a name="create-a-visual-studio-project"></a>Visual Studio プロジェクトを作成する

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `Program.cs` を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. `YourSubscriptionKey` という文字列を探し、実際の Speech Service のサブスクリプション キーに置き換えます。

1. `YourServiceRegion` という文字列を探し、実際のサブスクリプションに関連付けられている[リージョン](regions.md)に置き換えます。 たとえば、無料試用版を使用している場合、リージョンは `westus` になります。

1. プロジェクトへの変更内容を保存します。

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

1. メニュー バーから、**[ビルド]** > **[ソリューションのビルド]** を選択します。 これで、コードは、エラーなしでコンパイルされます。

    ![[ソリューションのビルド] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnet-windows-08-build.png "成功したビルド")

1. メニュー バーから **[デバッグ]** > **[デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。

    ![[デバッグの開始] オプションを強調表示した Visual Studio アプリケーションのスクリーンショット](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "アプリのデバッグの開始")

1. コンソール ウィンドウが表示され、何か話すように求められます。 何か英語で話してみてください。 音声が Speech Service に送信されて、リアルタイムにテキストに変換されます。 その結果はコンソールに出力されます。

    ![正常に認識された後のコンソールの出力のスクリーンショット](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "正常に認識された後のコンソールの出力")

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C# のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
