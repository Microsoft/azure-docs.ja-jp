---
title: 音声合成マークアップ言語 (SSML) - Speech Service
titleSuffix: Azure Cognitive Services
description: 音声合成マークアップ言語を使用して、テキスト読み上げの発音と韻律を制御します。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: dapine
ms.openlocfilehash: ac891e96e350f73b7728ee4b572d9e16a8794ff7
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77460996"
---
# <a name="speech-synthesis-markup-language-ssml"></a>音声合成マークアップ言語 (SSML)

音声合成マークアップ言語 (SSML) は、XML ベースのマークアップ言語であり、これにより、開発者はテキスト読み上げサービスを使用して、入力テキストを合成音声に変換する方法を指定することができます。 プレーンテキストと比較して、SSML では、開発者が音声合成出力のピッチ、読み方、読み上げ速度、音量などを微調整できます。 通常の句読点は、ピリオドの後の一時停止、または文が疑問符で終わるときの正しいイントネーションの使用など、自動的に処理されます。

SSML の Speech Service の実装は、World Wide Web コンソーシアムの[音声合成マークアップ言語バージョン 1.0](https://www.w3.org/TR/speech-synthesis) に基づいています。

> [!IMPORTANT]
> 中国語、日本語、韓国語の文字は、2 文字としてカウントされ課金されます。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)に関するページをご覧ください。

## <a name="standard-neural-and-custom-voices"></a>標準音声、ニューラル音声、およびカスタム音声

音声は、標準音声およびニューラル音声から選択できますが、製品やブランドに固有のカスタム音声を独自に作成することもできます。 標準音声は、45 を超える言語およびロケールで 75 種類以上が用意されています。ニューラル音声は、4 つの言語およびロケールで 5 種類が用意されています。 サポートされている言語、ロケール、および音声 (ニューラルと標準) の完全な一覧については、[言語のサポート](language-support.md)に関するページを参照してください。

標準音声、ニューラル音声、およびカスタム音声の詳細については、[テキスト読み上げの概要](text-to-speech.md)に関するページを参照してください。

## <a name="special-characters"></a>特殊文字

SSML を使用するとき、引用符、アポストロフィ、角かっこなどの特殊文字をエスケープする必要があることに注意してください。 詳細については、「[Extensible Markup Language (XML) 1.0: Appendix D](https://www.w3.org/TR/xml/#sec-entexpand)」 (拡張マークアップ言語 (XML) 1.0: 付録 D) を参照してください。

## <a name="supported-ssml-elements"></a>サポートされている SSML 要素

SSML の各ドキュメントは、SSML 要素 (またはタグ) を使用して作成されます。 これらの要素は、ピッチ、韻律、音量などの調整に使用されます。 次のセクションでは、各要素の使用方法と、要素がいつ必須か省略可能かについて説明します。  

> [!IMPORTANT]
> 必ず二重引用符を使用して属性値を囲んでください。 適切な形式の有効な XML の標準では、属性値を二重引用符で囲む必要があります。 たとえば、`<prosody volume="90">` は適切な形式で有効な要素ですが、`<prosody volume=90>` はそうではありません。 SSML は、引用符で囲まれていない属性値を認識しない場合があります。

## <a name="create-an-ssml-document"></a>SSML ドキュメントを作成する

`speak` はルート要素であり、SSML のすべてのドキュメントで**必須**です。 `speak` 要素には、バージョン、言語、およびマークアップのボキャブラリ定義などの重要な情報が含まれています。

**構文**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `version` | ドキュメント マークアップの解釈に使用される SSML 仕様のバージョンを指定します。 現行バージョンは 1.0 です。 | Required |
| `xml:lang` | ルート ドキュメントの言語を指定します。 この値には、小文字、2 文字の言語コード (`en` など)、または言語コードと大文字の国/地域 (`en-US` など) を含めることができます。 | Required |
| `xmlns` | SSML のドキュメントのマークアップ ボキャブラリ (要素型と属性名) を定義するドキュメントへの URI を指定します。 現在の URI は https://www.w3.org/2001/10/synthesis です。 | Required |

## <a name="choose-a-voice-for-text-to-speech"></a>テキスト読み上げのための音声を選択する

`voice` 要素は必須です。 テキスト読み上げに使用される音声を指定するために使用されます。

**構文**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `name` | テキスト読み上げの出力に使用される音声を識別します。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。 | Required |

**例**

> [!NOTE]
> この例では、`en-US-Jessa24kRUS` 音声を使用します。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>複数の音声の使用

`speak` 要素内には、テキスト読み上げの出力用の複数の音声を指定することができます。 これらの音声はさまざまな言語で指定できます。 音声ごとに、テキストを `voice` 要素にラップする必要があります。 

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `name` | テキスト読み上げの出力に使用される音声を識別します。 サポートされている声の全一覧については、[言語のサポート](language-support.md#text-to-speech)に関するページを参照してください。 | Required |

> [!IMPORTANT]
> 複数の音声はワード境界機能に対応していません。 複数の音声を使用するには、ワード境界機能を無効にする必要があります。

### <a name="disable-word-boundary"></a>ワード境界を無効にする

Speech SDK 言語に基づき、`SpeechConfig` オブジェクトのインスタンスで `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` プロパティを `false` に設定します。

# <a name="c"></a>[C#](#tab/csharp)

詳細については、<a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

詳細については、<a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

詳細については、<a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

詳細については、<a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

詳細については、<a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

詳細については、<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

詳細については、<a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a> を参照してください。

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>話し方を調整する

> [!IMPORTANT]
> ニューラル音声でのみ、話し方を調整できます。

既定では、テキスト読み上げサービスは、標準の音声とニューラル音声の両方のニュートラルな話し方を使用してテキストを合成します。 ニューラル音声では、話し方を調整して、`<mstts:express-as>` 要素を使用して陽気さ、共感、または感情を表現することができます。 これは、Speech Service に固有の省略可能な要素です。

現在、これらのニューラル音声では話し方の調整がサポートされています。
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

変更は文章レベルで適用され、スタイルは音声によって異なります。 スタイルがサポートされていない場合、サービスは既定のニュートラルな話し方の音声を返します。

**構文**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `type` | 話し方を指定します。 現在のところ、話し方は音声に固有です。 | ニューラル音声の話し方を調整する場合は、必須です。 `mstts:express-as` を使用する場合は、型を指定する必要があります。 無効な値を指定すると、この要素は無視されます。 |

各ニューラル音声でサポートされている話し方を確認するには、次の表を使用してください。

| 音声 | Type | 説明 |
|-------|------|-------------|
| `en-US-JessaNeural` | `type="cheerful"` | ポジティブで幸せな感情を示します |
| | `type="empathy"` | 思いやりと理解を示します |
| | `type="chat"` | カジュアルで緩やかな語調で話します |
| | `type="newscast"` | ニュース放送に似たフォーマルなトーンを示します |
| | `type="customerservice"` | 顧客サービスとして友好的で忍耐強く話します |
| `zh-CN-XiaoxiaoNeural` | `type="newscast"` | ニュース放送に似たフォーマルなトーンを示します |
| | `type="sentiment"` | 感動的なメッセージやストーリーを伝えます |

**例**

この SSML スニペットは、`<mstts:express-as>` 要素を使用して話し方を `cheerful` に変更する方法を示しています。

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>中断/一時停止を追加または削除する

`break` を使用して、単語間に一時停止 (または中断) を挿入したり、テキスト読み上げサービスによって一時停止が自動的に追加されないようにしたりします。

> [!NOTE]
> この要素を使用して、単語または語句の合成された音声が不自然に聞こえる場合に、その単語または語句のテキスト読み上げ (TTS) の既定の動作をオーバーライドします。 `strength` を `none` に設定して、テキスト読み上げサービスによって自動的に挿入される韻律的な中断を防ぎます。

**構文**

```xml
<break strength="string" />
<break time="string" />
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `strength` | 次のいずれかの値を使用して、一時停止の相対的な時間を指定します。<ul><li>なし</li><li>x-weak</li><li>weak</li><li>medium (既定値)</li><li>strong</li><li>x-strong</li></ul> | 省略可能 |
| `time` | 一時停止の絶対時間を秒またはミリ秒で指定します。 `2s` や `500` は有効な値の例です | 省略可能 |

| Strength | 説明 |
|----------|-------------|
| なし、または値が指定されていない場合 | 0 ミリ秒 |
| x-weak | 250 ミリ秒 |
| weak | 500 ミリ秒 |
| 中 | 750 ミリ秒 |
| strong | 1000 ミリ秒 |
| x-strong | 1250 ミリ秒 |


**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>段落や文を指定する

`p` 要素と `s` 要素は、それぞれ段落と文を表すために使用します。 これらの要素がない場合は、テキスト読み上げサービスで SSML ドキュメントの構造が自動的に決定されます。

`p` 要素には、テキストと、要素 `audio`、`break`、`phoneme`、`prosody`、`say-as`、`sub`、`mstts:express-as`、および `s` を含めることができます。

`s` 要素には、テキストと、要素 `audio`、`break`、`phoneme`、`prosody`、`say-as`、`mstts:express-as`、および `sub` を含めることができます。

**構文**

```XML
<p></p>
<s></s>
```

**例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>音素を使用して発音を向上させる

`ph` 要素は、SSML ドキュメントの発音のために使用します。 `ph` 要素に含めることができるのはテキストのみで、その他の要素を含めることはできません。 常に人間が判読できる音声をフォールバックとして提供します。

音標文字は音素で構成され、英字、数字、または文字から成り、場合によってはその組み合わせで構成されます。 それぞれの音素は、音声の一意の音を示します。 これは、文字が複数の話された音を表す場合があるラテン文字とは対照的です。 "candy" と "cease" という単語の文字 "c" の異なる発音や、"thing" と "those" の文字の組み合わせ "th" の異なる発音を考えてみてください。

**構文**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `alphabet` | `ph` 属性の文字列の発音を合成するときに使用する音標文字を指定します。 アルファベット順を指定する文字列は、小文字で指定する必要があります。 指定できる可能性のあるアルファベットは次のとおりです。<ul><li>ipa &ndash; 国際音標文字</li><li>sapi &ndash; Speech API 音素セット</li><li>ups &ndash; 汎用音素セット</li></ul>アルファベットは、要素内の音素にのみ適用されます。 詳細については、[音標文字リファレンス](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx)に関するページを参照してください。 | 省略可能 |
| `ph` | `phoneme` 要素内の単語の発音を指定する音素を含む文字列。 指定した文字列に認識されない音素が含まれている場合、テキスト読み上げ (TTS) サービスは SSML ドキュメント全体を拒否し、ドキュメントに指定されている音声出力を生成しません。 | 音素を使用する場合は必須です。 |

**使用例**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>韻律を調整する

`prosody` 要素を使用して、テキスト読み上げ出力のピッチ、音調、範囲、速度、期間、および音量に対する変更を指定します。 `prosody` 要素には、テキストと、要素 `audio`、`break`、`p`、`phoneme`、`prosody`、`say-as`、`sub`、および `s` を含めることができます。

韻律属性値は広範囲にわたって変化する可能性があるため、音声認識エンジンは、割り当てられた値を、選択された音声の実際の韻律値を連想するものとして解釈します。 テキスト読み上げサービスは、サポートされていない値を制限するか、置き換えます。 サポートされていない値の例として、1 MHz のピッチまたは 120 の音量があります。

**構文**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `pitch` | テキストのベースラインのピッチを示します。 ピッチは次のように表されます。<ul><li>絶対値。数字の後に "Hz" (ヘルツ) が付いて表されます。 たとえば、600 Hz。</li><li>相対値。前に "+" または "-" が付き、後にピッチの変更量を指定する "Hz" または "st" が付いた数字として表されます。 たとえば、+80 Hz、-2st。 "st" は、変更単位が半音 (標準の全音階での全音の半分) であることを示します。</li><li>定数値:<ul><li>x-low</li><li>low</li><li>中</li><li>high</li><li>x-high</li><li>既定値 (default)</li></ul></li></ul>。 | 省略可能 |
| `contour` | ニューラル音声では、音調はサポートされていません。 音調とは音の高さの変化です。 この変化は、音声出力において指定の時間位置にあるターゲットの配列として表わされます。 各ターゲットは、パラメーターのペアのセットによって定義されます。 次に例を示します。 <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>パラメーターの各セットの最初の値は、ピッチの変更位置をテキストの継続時間の割合として指定します。 2 番目の値は、ピッチの相対値または列挙値を使用して、ピッチを増減する量を指定します (`pitch` を参照)。 | 省略可能 |
| `range` | テキストのピッチの範囲を表す値。 `range` は、`pitch` の記述に使用されるものと同じ絶対値、相対値、または列挙値を使用して表すことができます。 | 省略可能 |
| `rate` | テキストの読み上げ速度を示します。 `rate` は次のように表されます。<ul><li>相対値。既定値の乗数として機能する数字で表されます。 たとえば、値 *1* では速度は変更されません。 値 *0.5* では、速度が半分になります。 値 *3* では、速度が 3 倍になります。</li><li>定数値:<ul><li>x-slow</li><li>slow</li><li>中</li><li>fast</li><li>x-fast</li><li>既定値 (default)</li></ul></li></ul> | 省略可能 |
| `duration` | 音声合成 (TTS) サービスがテキストを読んでいる間に経過する時間 (秒またはミリ秒)。 たとえば、*2s* または *1800ms* です。 | 省略可能 |
| `volume` | 読み上げている音声の音量レベルを示します。 音量は次のように表されます。<ul><li>絶対値。0.0 から 100.0 (*quietest* から *loudest* まで) の範囲の数字として表されます。 たとえば、75 です。 既定値は 100.0 です。</li><li>相対値。音量の変更量を指定する、前に "+" または "-" が付いた数字として表されます。 たとえば、+10、-5.5。</li><li>定数値:<ul><li>silent</li><li>x-soft</li><li>soft</li><li>中</li><li>loud</li><li>x-loud</li><li>既定値 (default)</li></ul></li></ul> | 省略可能 |

### <a name="change-speaking-rate"></a>読み上げ速度を変更する

読み上げ速度は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、読み上げ速度は文章レベルでのみ適用できます。

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>音量を変更する

音量変更は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、音量変更は文章レベルでのみ適用できます。

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>ピッチを変更する

ピッチ変更は、標準の音声に対して単語または文章レベルで適用できます。 ただし、ニューラル音声の場合、ピッチ変更は文章レベルでのみ適用できます。

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>ピッチ曲線を変更する

> [!IMPORTANT]
> ピッチ曲線の変更は、ニューラル音声についてはサポートされていません。

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as 要素

`say-as` は、要素のテキストのコンテンツの種類 (数値や日付など) を示す省略可能な要素です。 これにより、音声合成エンジンにテキストを発音する方法に関するガイダンスが提供されます。

**構文**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `interpret-as` | 要素のテキストのコンテンツの種類を示します。 種類の一覧については、次の表を参照してください。 | Required |
| `format` | あいまいな形式を持つ可能性のあるコンテンツの種類に対して、要素のテキストの正確な書式設定に関する追加情報を提供します。 SSML では、それらを使用するコンテンツの種類の形式が定義されます (次の表を参照)。 | 省略可能 |
| `detail` | 読み上げられる詳細のレベルを示します。 たとえば、この属性では、音声合成エンジンが句読点を発音するように要求する場合があります。 `detail` に対して定義されている標準値はありません。 | 省略可能 |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

`interpret-as` および `format` 属性でサポートされているコンテンツの種類は、次のとおりです。 `interpret-as` が日付と時刻に設定されている場合にのみ、`format` 属性を含めます。

| interpret-as | format | 解釈 |
|--------------|--------|----------------|
| `address` | | テキストはアドレスとして読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"I'm at 150th court north east redmond washington" (私はワシントン州レドモンド 150th コート ノースイーストにいます)。 |
| `cardinal`, `number` | | テキストは基数として読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"There are three alternatives" (代替手段は 3 つあります)。 |
| `characters`, `spell-out` | | テキストは、個別の文字 (綴り) として読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />"T E S T"。 |
| `date` | dmy、mdy、ymd、ydm、ym、my、md、dm、d、m、y | テキストは日付として読み上げられます。 `format` 属性では、日付の形式を指定します (*d=日、m=月、y=年*)。 音声合成エンジンでは次のように発音されます。<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Today is October nineteenth two thousand sixteen" (今日は 2016 年 10 月 19 日です)。 |
| `digits`, `number_digit` | | テキストは、個別の数字のシーケンスとして読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />"1 2 3 4 5 6 7 8 9"。 |
| `fraction` | | テキストは分数として読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"three eighths of an inch" (1 インチの 8 分の 3)。 |
| `ordinal` | | テキストは序数として読み上げられます。 音声合成エンジンでは次のように発音されます。<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />"3 つ目のオプションを選択します"。 |
| `telephone` | | テキストは電話番号として読み上げられます。 `format` 属性には、国番号を表す数字を含めることができます。 たとえば、米国の場合は "1"、イタリアの場合は "39" になります。 音声合成エンジンでは、この情報を使用して、電話番号の発音するガイドにすることができます。 電話番号には、国番号を含めることもできます。その場合は、`format` の国番号よりも優先されます。 音声合成エンジンでは次のように発音されます。<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"My number is area code eight eight eight five five five one two one two" (私の番号は市外局番 888 555 1212 です)。 |
| `time` | hms12、hms24 | テキストは時刻として読み上げられます。 `format` 属性では、時刻が 12 時間形式 (hms12) または 24 時間形式 (hms24) のいずれを使用するかを指定します。 時間、分、秒を表す数字を区切るには、コロンを使用します。 有効な時刻の例を次に示します。12:35、1:14:32、08:15、02:50:45。 音声合成エンジンでは次のように発音されます。<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"The train departs at four A M" (この電車は午前 4 時に発車します)。 |

**使用方法**

`say-as` 要素にはテキストのみを含めることができます。

**例**

音声合成エンジンは、"Your first request was for one room on October nineteenth twenty ten with early arrival at twelve thirty five PM" (最初のリクエストは 2010 年 10 月 19 日に 1 部屋、午後 12 時 35 分にアーリー アライバル、というものでした) という例を読み上げます。
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>録音されたオーディオを追加する

`audio` は、MP3 オーディオを SSML ドキュメントに挿入できるようにする省略可能な要素です。 オーディオ要素の本文には、オーディオ ファイルが使用不可の場合や再生できない場合に読み上げられるプレーン テキストや SSML マークアップが含まれていることがあります。 さらに、`audio` 要素には、テキストと、要素 `audio`、`break`、`p`、`s`、`phoneme`、`prosody`、`say-as`、および `sub` を含めることができます。

SSML ドキュメントに含まれるオーディオは、次の要件を満たしている必要があります。

* MP3 は、インターネットからアクセス可能な HTTPS エンドポイントでホストされている必要があります。 HTTPS は必須であり、MP3 ファイルをホストしているドメインは信頼できる有効な SSL 証明書を提示する必要があります。
* MP3 は有効な MP3 ファイル (MPEG v2) である必要があります。
* ビット レートは 48 kbps である必要があります。
* サンプル レートは 16,000 Hz である必要があります。
* 1つの応答に含まれるすべてのテキスト ファイルとオーディオ ファイルの合計時間は、90 秒以下でなければなりません。
* MP3 には、顧客固有情報またはその他の機密情報を含めることはできません。

**構文**

```xml
<audio src="string"/></audio>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `src` | オーディオ ファイルの場所/URL を指定します。 | SSML ドキュメントで audio 要素を使用している場合は必須です。 |

**例**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>バックグラウンド オーディオを追加する

`mstts:backgroundaudio` 要素を使用すると、バックグラウンド オーディオを SSML ドキュメントに追加できます (またはオーディオ ファイルをテキスト読み上げと組み合わせることができます)。 `mstts:backgroundaudio` を使用すると、バックグラウンドでオーディオ ファイルをループ処理し、テキスト読み上げの開始時にフェードインし、テキスト読み上げの終了時にフェードアウトできます。

指定されたバックグラウンド オーディオがテキスト読み上げまたはフェードアウトより短い場合は、ループします。 テキスト読み上げより長い場合は、フェードアウトが終了したときに停止します。

1 つの SSML ドキュメントにつき 1 つのバックグラウンド オーディオ ファイルのみが許可されます。 ただし、`voice` 要素内に `audio` タグを配置することで、SSML ドキュメントにオーディオを追加できます。

**構文**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**属性**

| 属性 | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `src` | バックグラウンド オーディオ ファイルの場所/URL を指定します。 | SSML ドキュメントでバックグラウンド オーディオを使用している場合は必須です。 |
| `volume` | バックグラウンド オーディオ ファイルのボリュームを指定します。 **指定可能な値**: `0` から `100`。 既定値は `1` です。 | 省略可能 |
| `fadein` | バックグラウンド オーディオの "フェードイン" の期間をミリ秒単位で指定します。 既定値は `0` で、"フェードインなし" に相当します。 **指定可能な値**: `0` から `10000`。  | 省略可能 |
| `fadeout` | バックグラウンド オーディオのフェードアウトの期間をミリ秒単位で指定します。 既定値は `0` で、"フェードアウトなし" に相当します。**指定可能な値**: `0` から `10000` まで。  | 省略可能 |

**例**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>次のステップ

* [言語サポート: 音声、ロケール、言語](language-support.md)
