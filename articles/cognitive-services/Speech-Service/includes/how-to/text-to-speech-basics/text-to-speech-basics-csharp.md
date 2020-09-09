---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/25/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: a6c498b1f68ca4e8c40a2baf30cc81176a0898bb
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934695"
---
## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../get-started.md)。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に Speech SDK をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet&pivots=programming-language-csharp" target="_blank">.NET Framework <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnetcore&pivots=programming-language-csharp" target="_blank">.NET Core <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=unity&pivots=programming-language-csharp" target="_blank">Unity <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=uwps&pivots=programming-language-csharp" target="_blank">UWP <span class="docon docon-navigate-external x-hidden-focus"></span></a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=xaml&pivots=programming-language-csharp" target="_blank">Xamarin <span class="docon docon-navigate-external x-hidden-focus"></span></a>

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、スクリプトの先頭に次の `using` ステートメントを含めます。

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
```

## <a name="create-a-speech-configuration"></a>音声構成を作成する

Speech SDK を使用して Speech Service を呼び出すには、[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を作成する必要があります。 このクラスには、キー、関連付けられたリージョン、エンドポイント、ホスト、または認証トークンなど、ご利用のサブスクリプションに関する情報が含まれています。

> [!NOTE]
> 音声認識、音声合成、翻訳、またはインテント認識のどれを実行するのかに関係なく、必ず構成を作成します。

[`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を初期化するには、次に示すようないくつかの方法があります。

* サブスクリプションの場合: キーと、それに関連付けられたリージョンを渡します。
* エンドポイントの場合: Speech Service エンドポイントを渡します。 キーまたは認証トークンは省略可能です。
* ホストの場合: ホスト アドレスを渡します。 キーまたは認証トークンは省略可能です。
* 認証トークンの場合: 認証トークンと、それに関連付けられたリージョンを渡します。

この例では、サブスクリプション キーとリージョンを使用して [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) を作成します。 リージョン識別子を確認するには、[リージョンのサポート](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk)に関するページを参照してください。 また、この記事の残りの部分で使用する、基本的な定型コードをいくつか作成します。これを変更して、さまざまなカスタマイズを行います。

```csharp
public class Program 
{
    static async Task Main()
    {
        await SynthesizeAudioAsync();
    }

    static async Task SynthesizeAudioAsync() 
    {
        var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    }
}
```

## <a name="synthesize-speech-to-a-file"></a>音声をファイルに合成する

次に、[`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) オブジェクトを作成します。これにより、テキストから音声への変換と、スピーカー、ファイル、またはその他の出力ストリームへの出力が実行されます。 [`SpeechSynthesizer`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer?view=azure-dotnet) は、前の手順で作成した [`SpeechConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?view=azure-dotnet) オブジェクトと、出力結果の処理方法を指定する [`AudioConfig`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig?view=azure-dotnet) オブジェクトをパラメーターとして受け取ります。

まず、`FromWavFileOutput()` 関数を使用して `.wav` ファイルに出力を自動的に書き込む `AudioConfig` を作成し、次にそれを `using` ステートメントでインスタンス化します。 このコンテキストの `using` ステートメントによって、アンマネージド リソースが自動的に破棄され、破棄後にオブジェクトがスコープ外になります。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
}
```

次に、別の `using` ステートメントを使用して `SpeechSynthesizer` をインスタンス化します。 `config` オブジェクトと `audioConfig` オブジェクトをパラメーターとして渡します。 こうすることで、音声合成を実行してファイルに書き込むことが、テキスト文字列を使用して `SpeakTextAsync()` を実行するのと同じぐらい簡単になります。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var audioConfig = AudioConfig.FromWavFileOutput("path/to/write/file.wav");
    using var synthesizer = new SpeechSynthesizer(config, audioConfig);
    await synthesizer.SpeakTextAsync("A simple test to write to a file.");
}
```

このプログラムを実行すると、合成された `.wav` ファイルが、指定した場所に書き込まれます。 以上は最も基本的な使用方法の好例ですが、この次は、カスタム シナリオに対応できるよう、出力をカスタマイズし、出力応答をインメモリ ストリームとして処理する方法について説明します。

## <a name="synthesize-to-speaker-output"></a>スピーカー出力に合成する

場合によっては、合成された音声をスピーカーに直接出力することが必要になる場合があります。 これは、上記の例で `SpeechSynthesizer` を作成するときに、`AudioConfig` パラメーターを省略するだけで実行できます。 これにより、現在のアクティブな出力デバイスに対して出力が行われます。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config);
    await synthesizer.SpeakTextAsync("Synthesizing directly to speaker output.");
}
```

## <a name="get-result-as-an-in-memory-stream"></a>結果をインメモリ ストリームとして取得する

音声アプリケーション開発の多くのシナリオでは、結果として得られたオーディオ データは、ファイルに直接書き込むのではなく、インメモリ ストリームとして必要となるケースがよくあります。 その場合、次のようなカスタム動作を構築できます。

* 結果として得られたバイト配列を、カスタム ダウンストリーム サービス向けのシーク可能なストリームとして抽象化する。
* 結果を他の API またはサービスと統合する。
* オーディオ データの変更やカスタム `.wav` ヘッダーの記述などを行う。

この変更は、前の例から簡単に行うことができます。 まず、`AudioConfig` ブロックを削除します。これは、この時点から出力動作を手動で管理して制御を強化するためです。 次に、`SpeechSynthesizer` コンストラクターの `AudioConfig` に `null` を渡します。 

> [!NOTE]
> 前述のスピーカー出力の例のように省略するのではなく、`AudioConfig` に `null` を渡した場合、既定ではオーディオは現在のアクティブな出力デバイスで再生されません。

今回は、結果を [`SpeechSynthesisResult`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisresult?view=azure-dotnet) 変数に保存します。 `AudioData` プロパティには、出力データの `byte []` が含まれます。 この `byte []` を手動で操作することも、[`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) クラスを使用してインメモリ ストリームを管理することもできます。 この例では、`AudioDataStream.FromResult()` 静的関数を使用して、結果からストリームを取得します。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var result = await synthesizer.SpeakTextAsync("Getting the response as an in-memory stream.");
    using var stream = AudioDataStream.FromResult(result);
}
```

ここから、結果として得られた `stream` オブジェクトを使用して、任意のカスタム動作を実装できます。

## <a name="customize-audio-format"></a>オーディオ形式をカスタマイズする

次のセクションでは、次のようなオーディオ出力属性をカスタマイズする方法について説明します。

* オーディオ ファイルの種類
* サンプルレート
* ビット深度

オーディオ形式を変更するには、`SpeechConfig` オブジェクトで `SetSpeechSynthesisOutputFormat()` 関数を使用します。 この関数には [`SpeechSynthesisOutputFormat`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet) 型の `enum` が必要です。これは、出力形式を選択するために使用します。 使用できる[オーディオ形式の一覧](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesisoutputformat?view=azure-dotnet)については、リファレンス ドキュメントを参照してください。

要件に応じて、ファイルの種類ごとにさまざまなオプションがあります。 定義上、`Raw24Khz16BitMonoPcm` のような未加工の形式にはオーディオ ヘッダーが含まれないことに注意してください。 未加工の形式は、ダウンストリームの実装で未加工のビットストリームをデコードできることがわかっている場合か、ビット深度、サンプル レート、チャネル数などに基づいてヘッダーを手動で作成する場合にのみ使用してください。

この例では、`SpeechConfig` オブジェクトに `SpeechSynthesisOutputFormat` を設定することにより、高忠実度の RIFF 形式 `Riff24Khz16BitMonoPcm` を指定します。 前のセクションの例と同様に、[`AudioDataStream`](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.audiodatastream?view=azure-dotnet) を使用して結果のインメモリ ストリームを取得し、それをファイルに書き込みます。

```csharp
static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    config.SetSpeechSynthesisOutputFormat(SpeechSynthesisOutputFormat.Riff24Khz16BitMonoPcm);

    using var synthesizer = new SpeechSynthesizer(config, null);
    var result = await synthesizer.SpeakTextAsync("Customizing audio output format.");

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

プログラムをもう一度実行すると、指定したパスに `.wav` ファイルが書き込まれます。

## <a name="use-ssml-to-customize-speech-characteristics"></a>SSML を使用して音声の特徴をカスタマイズする

音声合成マークアップ言語 (SSML) を使用すると、XML スキーマから要求を送信して、テキスト読み上げ出力のピッチ、発音、読み上げ速度、ボリュームなどを微調整することができます。 このセクションでは実用的な使用例をいくつか紹介しますが、詳細なガイドについては、[SSML の操作方法に関する記事](../../../speech-synthesis-markup.md)を参照してください。

SSML を使用したカスタマイズを開始するには、音声を切り替える単純な変更を加えます。
まず、ルート プロジェクト ディレクトリに SSML 構成用の新しい XML ファイルを作成します (この例では `ssml.xml`)。 ルート要素は常に `<speak>` であり、テキストを `<voice>` 要素でラップすることで、`name` パラメーターを使用して音声を変更できます。 この例では、音声を男性の英語 (英国) の音声に変更します。 この音声が**標準**音声であることに注意してください。これは、**ニューラル**音声とは価格や可用性が異なります。 サポートされている**標準**音声の[全一覧](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#standard-voices)を参照してください。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    When you're on the motorway, it's a good idea to use a sat-nav.
  </voice>
</speak>
```

次に、XML ファイルを参照するように音声合成要求を変更する必要があります。 要求はほとんど同じですが、`SpeakTextAsync()` 関数を使用する代わりに、`SpeakSsmlAsync()` を使用します。 この関数には XML 文字列が必要なので、最初に `File.ReadAllText()` を使用して SSML 構成を文字列として読み込みます。 ここからは、結果のオブジェクトは前の例とまったく同じです。

> [!NOTE]
> Visual Studio を使用している場合、既定では、ビルド構成で XML ファイルが見つからない可能性があります。 この問題を解決するには、該当する XML ファイルを右クリックし、 **[プロパティ]** を選択します。 **[ビルド アクション]** を *[コンテンツ]* に、 **[出力ディレクトリにコピー]** を *[常にコピーする]* に変更します。

```csharp
public static async Task SynthesizeAudioAsync() 
{
    var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
    using var synthesizer = new SpeechSynthesizer(config, null);
    
    var ssml = File.ReadAllText("./ssml.xml");
    var result = await synthesizer.SpeakSsmlAsync(ssml);

    using var stream = AudioDataStream.FromResult(result);
    await stream.SaveToWaveFileAsync("path/to/write/file.wav");
}
```

出力は機能しますが、さらに自然に聞こえるように、いくつかの簡単な変更を加えることができます。 話す速度が全体的に少し速すぎるため、`<prosody>` タグを追加して、速度を既定速度の **90%** に下げます。 また、文中のコンマの後の一時停止が少し短すぎて、不自然に聞こえます。 この問題を解決するには、`<break>` タグを追加して音声を遅らせ、time パラメーターを **200ms** に設定します。 合成を再実行して、これらのカスタマイズが出力にどのように影響したかを確認します。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
  <voice name="en-GB-George-Apollo">
    <prosody rate="0.9">
      When you're on the motorway,<break time="200ms"/> it's a good idea to use a sat-nav.
    </prosody>
  </voice>
</speak>
```

## <a name="neural-voices"></a>ニューラル音声

ニューラル音声とは、ディープ ニューラル ネットワークを使用した音声合成アルゴリズムです。 ニューラル音声を使用した場合、合成音声は人間の録音とほとんど区別がつきません。 ニューラル音声では、人間のような自然な韻律と明瞭な発音により、ユーザーが AI システムと対話する際のリスニング疲労が大幅に軽減されます。

ニューラル音声に切り替えるには、`name` を[ニューラル音声オプション](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)のいずれかに変更します。 次に、`mstts` の XML 名前空間を追加し、テキストを `<mstts:express-as>` タグ内にラップします。 読み上げスタイルをカスタマイズするには、`style` パラメーターを使用します。 この例では `cheerful` を使用していますが、`customerservice` または `chat` に設定して、読み上げスタイルの違いを確認してみてください。

> [!IMPORTANT]
> ニューラル音声は、"*米国東部*"、"*東南アジア*"、"*西ヨーロッパ*" のリージョンで作成された音声リソースで**のみ**サポートされています。

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
  <voice name="en-US-AriaNeural">
    <mstts:express-as style="cheerful">
      This is awesome!
    </mstts:express-as>
  </voice>
</speak>
```
