---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 4db81bcb65077de8cb923117905daebd80532685
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377115"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../overview.md#try-the-speech-service-for-free)
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `Program.cs` を開きます。
3. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> をダウンロードし、プロジェクトに追加します。
    - `Program.cs` ファイルの横に *whatstheweatherlike.wav* ファイルを保存します。
    - **ソリューション エクスプローラー**で、プロジェクト名を右クリックし、 **[追加]、[既存の項目]** の順に選択します。
    - *whatstheweatherlike.wav* ファイルを選択し、 **[追加]** ボタンを選択します。
    - 新たに追加したファイルを右クリックし、 **[プロパティ]** を選択します。
    - **[出力ディレクトリにコピー]** を **[常にコピーする]** に変更します。

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。 `RecognizeSpeechAsync()` という非同期メソッドを作成済みであることを確認します。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
        }
    }
}
```

## <a name="create-a-speech-configuration"></a>Speech 構成を作成する

`SpeechRecognizer` オブジェクトを初期化するには、サブスクリプション キーとサブスクリプション リージョンを使用する構成を作成する必要があります。 このコードを `RecognizeSpeechAsync()` メソッドに挿入します。

> [!NOTE]
> このサンプルでは、`FromSubscription()` メソッドを使用して `SpeechConfig` をビルドします。 使用可能なメソッドの完全な一覧については、[SpeechConfig クラス](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet)に関する記事を参照してください。
> Speech SDK では、既定で認識される言語が en-us です。ソース言語の選択については、「[音声テキスト変換のソース言語を指定する](../../../../how-to-specify-source-language.md)」を参照してください。

```csharp
// Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="create-an-audio-configuration"></a>オーディオ構成を作成する

ここで、使用するオーディオ ファイルを指し示す `AudioConfig` オブジェクトを作成する必要があります。 アンマネージド リソースが確実に正しく解放されるように、このオブジェクトは using ステートメント内に作成されます。 このコードを Speech 構成のすぐ下にある `RecognizeSpeechAsync()` メソッドに挿入します。

```csharp
using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
{
}
```

## <a name="initialize-a-speechrecognizer"></a>SpeechRecognizer を初期化する

ここで、前に作成した `SpeechConfig` オブジェクトと `AudioConfig` オブジェクトを使用して `SpeechRecognizer` オブジェクトを作成しましょう。 アンマネージド リソースが確実に正しく解放されるように、このオブジェクトは using ステートメント内にも作成されます。 ```AudioConfig``` オブジェクトをラップする using ステートメント内の `RecognizeSpeechAsync()` メソッドに、このコードを挿入します。

```csharp
using (var recognizer = new SpeechRecognizer(config, audioInput))
{
}
```

## <a name="recognize-a-phrase"></a>語句を認識する

`SpeechRecognizer` オブジェクトから、`RecognizeOnceAsync()` メソッドを呼び出します。 認識の対象として 1 つの語句を送信しようとしていること、また、その語句が識別された後で、音声認識を停止しようとしていることが、このメソッドを通じて Speech サービスに伝えられます。

using ステートメント内に、このコードを追加します。

```csharp
Console.WriteLine("Recognizing first result...");
var result = await recognizer.RecognizeOnceAsync();
```

## <a name="display-the-recognition-results-or-errors"></a>認識結果 (またはエラー) を表示する

音声サービスによって認識結果が返されたら、それを使用して何らかの操作を行います。 シンプルに保ち、結果をコンソールに出力します。

using ステートメント内の `RecognizeOnceAsync()` の下に、このコードを追加します。

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"We recognized: {result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="check-your-code"></a>コードを確認する

この時点で、コードは次のようになります。

```csharp
//
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE.md file in the project root for full license information.
//

using System;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

namespace HelloWorld
{
    class Program
    {
        static async Task Main()
        {
            await RecognizeSpeechAsync();
        }

        static async Task RecognizeSpeechAsync()
        {
            var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");

            using (var audioInput = AudioConfig.FromWavFileInput("whatstheweatherlike.wav"))
            using (var recognizer = new SpeechRecognizer(config, audioInput))
            {
                Console.WriteLine("Recognizing first result...");
                var result = await recognizer.RecognizeOnceAsync();

                switch (result.Reason)
                {
                    case ResultReason.RecognizedSpeech:
                        Console.WriteLine($"We recognized: {result.Text}");
                        break;
                    case ResultReason.NoMatch:
                        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        break;
                    case ResultReason.Canceled:
                        var cancellation = CancellationDetails.FromResult(result);
                        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
                
                        if (cancellation.Reason == CancellationReason.Error)
                        {
                            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
                            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
                            Console.WriteLine($"CANCELED: Did you update the subscription info?");
                        }
                        break;
                }
            }
        }
    }
}
```

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

1. コードをコンパイルします。*Visual Studio* のメニュー バーで、 **[ビルド]**  >  **[ソリューションのビルド]** の順に選択します。
2. アプリを起動します。メニュー バーで **[デバッグ]**  >  **[デバッグの開始]** の順に選択するか、**F5** キーを押します。
3. 認識を開始します。オーディオ ファイルが Speech サービスに送信され、テキストとして文字起こしされて、コンソールに表示されます。

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
