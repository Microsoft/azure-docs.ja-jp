---
title: クイック スタート:オーディオ ファイルから音声を認識する、Python - Speech サービス
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/30/2020
ms.author: dapine
ms.openlocfilehash: f3a6ffeede05c1a2e9a883d40df19819e6a4bb3b
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77446211"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [LUIS アプリケーションを作成し、エンドポイント キーを取得する](../../../../quickstarts/create-luis.md)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>サポートと更新プログラム

Speech SDK Python パッケージの更新プログラムは、PyPI を通じて配布され、[リリース ノート](~/articles/cognitive-services/Speech-Service/releasenotes.md)で発表されます。
新しいバージョンが利用可能な場合は、コマンド `pip install --upgrade azure-cognitiveservices-speech` を使用してそれに更新できます。
`azure.cognitiveservices.speech.__version__` 変数を調べて、現在インストールされているバージョンを確認します。

問題があるか、機能が欠落している場合は、「[サポート オプションとヘルプ オプション](~/articles/cognitive-services/Speech-Service/support.md)」を参照してください。

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Speech SDK を使用する Python アプリケーションの作成

### <a name="run-the-sample"></a>サンプルを実行する

このクイック スタートからソース ファイル `quickstart.py` に[サンプル コード](#sample-code)をコピーして、お使いの IDE またはコンソールで実行することができます。

```sh
python quickstart.py
```

また、このクイック スタート チュートリアルを [Speech SDK のサンプル リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)から [Jupyter](https://jupyter.org) ノートブックとしてダウンロードして、ノートブックとして実行することもできます。

### <a name="sample-code"></a>サンプル コード

> [!NOTE]
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Speech SDK と Visual Studio Code をインストールして使用する

1. [Python](https://www.python.org/downloads/) の 64 ビット バージョン (3.5 以降) をお使いのコンピューターにダウンロードして、インストールします。
1. [Visual Studio Code](https://code.visualstudio.com/Download) をダウンロードして、インストールします。
1. Visual Studio Code を開いて、Python 拡張機能をインストールします。 メニューで **[ファイル]**  >  **[基本設定]**  >  **[拡張機能]** の順に選択します。 **Python** を検索します。

   ![Python 拡張機能のインストール](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. プロジェクトの保存先となるフォルダーを作成します。 たとえば、エクスプローラーを使用して行います。
1. Visual Studio Code の **[ファイル]** アイコンを選択します。 次に、作成したフォルダーを開きます。

   ![フォルダーを開く](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 新しいファイル アイコンを選択して、新しい Python ソース ファイル `speechsdk.py` を作成します。

   ![ファイルを作成する](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 新しく作成されたファイルに [Python コード](#sample-code)をコピーして貼り付け、保存します。
1. お使いの Speech サービスのサブスクリプション情報を挿入します。
1. Python インタープリターが選択されている場合は、ウィンドウの下部にあるステータス バーの左側にそれが表示されます。
   そうでない場合は、使用可能な Python インタープリターの一覧が表示されます。 コマンド パレットを開いて (<kbd>Ctrl + Shift + P</kbd>)、「**Python: Select Interpreter**」と入力します。 適切なものを選択します。
1. Visual Studio Code 内から Speech SDK Python パッケージをインストールできます。 選択した Python インタープリター用にまだインストールされていない場合は、インストールします。
   Speech SDK パッケージをインストールするには、ターミナルを開きます。 コマンド パレットをもう一度開いて (<kbd>Ctrl + Shift + P</kbd>)、「**Terminal: Create New Integrated Terminal**」と入力してターミナルを開きます。
   開いたターミナルに、コマンド `python -m pip install azure-cognitiveservices-speech` か、システムに応じた適切なコマンドを入力します。
1. サンプル コードを実行するには、エディター内のどこかを右クリックします。 **[Run Python File in Terminal]\(ターミナル内の Python ファイルを実行する\)** を選択します。
   オーディオ ファイルから最初の 15 秒間の音声入力が認識され、コンソール ウィンドウにログが記録されます。

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

これらの手順で問題が発生した場合は、より詳しい [Visual Studio Code Python チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
