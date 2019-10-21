---
title: クイック スタート:音声を認識する、C++ (Linux) - Speech Service
titleSuffix: Azure Cognitive Services
description: Linux で C++ と Speech SDK を使用して音声を認識する方法について説明します
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: f1b87d0219bdf1b317eed41f91e65bf318b31e5a
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803363"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-by-using-the-speech-sdk"></a>クイック スタート:Linux で C++ と Speech SDK を使用して音声を認識する

クイック スタートは[テキスト読み上げ](quickstart-text-to-speech-cpp-linux.md)にも使用できます。

必要に応じて、別のプログラミング言語や環境に切り替えます。<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

この記事では、Linux (Ubuntu 16.04、Ubuntu 18.04、Debian 9) 用の C++ コンソール アプリケーションを作成します。 Cognitive Services [Speech SDK](speech-sdk.md) を使用して、リアルタイムに PC のマイクからの音声をテキストに文字起こします。 このアプリケーションは、[Linux 向け Speech SDK](https://aka.ms/csspeech/linuxbinary) と Linux ディストリビューションの C++ コンパイラ (たとえば `g++`) を使用して構築します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、Speech Services サブスクリプション キーが必要です。 1 つ無料で取得できます。 詳しくは、「[Speech Service を無料で試す](get-started.md)」を参照してください。

## <a name="install-speech-sdk"></a>Speech SDK をインストールする

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Linux 用 Speech SDK は、64 ビット アプリケーションと 32 ビット アプリケーションのどちらの構築にも使用できます。 必要なライブラリとヘッダー ファイルは、 https://aka.ms/csspeech/linuxbinary から tar ファイルとしてダウンロードできます。

SDK を次のようにダウンロードしてインストールします。

1. SDK の依存関係がインストールされていることを確認します。

   * Ubuntu の場合:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * Debian 9 の場合:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

1. Speech SDK のファイル抽出先にする必要があるディレクトリを選択し、そのディレクトリを指すように `SPEECHSDK_ROOT` 環境変数を設定します。 この変数によって、後のコマンドでこのディレクトリを参照することが容易になります。 たとえば、ホーム ディレクトリで `speechsdk` ディレクトリを使用する場合、次のようなコマンドを使用します。

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. このディレクトリがまだ存在しない場合は作成します。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK バイナリを含む `.tar.gz` アーカイブをダウンロードして抽出します。

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. 展開されたパッケージの最上位ディレクトリの内容を検証します。

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   ディレクトリの一覧には、サード パーティの通知やライセンスのファイルと、ヘッダー (`.h`) ファイルを格納している `include` ディレクトリおよびライブラリを格納している `lib` ディレクトリが含まれているはずです。

   [!INCLUDE [Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-sample-code"></a>サンプル コードを追加する

1. `helloworld.cpp` という名前で C++ ソース ファイルを作成し、その中に次のコードを貼り付けます。

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. この新しいファイルで、文字列 `YourSubscriptionKey` を、Speech Services のサブスクリプション キーで置き換えます。

1. 文字列 `YourServiceRegion` を、サブスクリプションに関連付けられた[リージョン](regions.md) (たとえば、無料試用版サブスクリプションでは `westus`) に置き換えます。

## <a name="build-the-app"></a>アプリのビルド

> [!NOTE]
> 次のコマンドを必ず _1 つのコマンド ライン_ として入力してください。 各コマンドの横にある **[Copy]\(コピー)** ボタンを使用してコマンドをコピーし、シェル プロンプトでそれを貼り付けるのが、それを行う最も簡単な方法です。

* **x64** (64 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libasound.so.2
  ```

* **x86** (32 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libasound.so.2
  ```

* **ARM64** (64 ビット) システムでは、次のコマンドを実行してアプリケーションをビルドします。

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/arm64" -l:libasound.so.2
  ```

## <a name="run-the-app"></a>アプリの実行

1. Speech SDK ライブラリを指すようにローダーのライブラリ パスを構成します。

   * **x64** (64 ビット) システムでは、次のコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * **x86** (32 ビット) システムでは、このコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

   * **ARM64** (64 ビット) システムでは、次のコマンドを入力します。

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/arm64"
     ```

1. アプリケーションを実行します。

   ```sh
   ./helloworld
   ```

1. コンソール ウィンドウに、何か発言するよう求めるプロンプトが表示されます。 英語の語句または文を読み上げます。 音声が Speech Services に送信されてテキストに変換され、同じウィンドウに表示されます。

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で C++ のサンプルを詳しく見てみる](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>関連項目

- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
