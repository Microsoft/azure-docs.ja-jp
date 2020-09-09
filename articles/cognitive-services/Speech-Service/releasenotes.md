---
title: リリース ノート - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech サービスの機能リリース、改善、バグ修正、既知の問題の時系列ログ。
services: cognitive-services
author: oliversc
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: oliversc
ms.custom: seodec18
ms.openlocfilehash: 8df54adf8f3aeaa58e30d6d9218cec8bec8c1121
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525923"
---
# <a name="speech-service-release-notes"></a>Speech Service リリース ノート

## <a name="text-to-speech-2020-august-release"></a>テキスト読み上げの 2020 年 8 月のリリース

### <a name="new-features"></a>新機能

* **ニューラル TTS: `en-US` Aria 音声の新しい読み上げスタイル**。 AriaNeural は、ニュースを読むときのニュースキャスターのように聞こえます。 'newscast-formal' スタイルの方がより真面目に聞こえるのに対し、'newscast-casual' スタイルの方はよりリラックスしてくだけた感じです。 [SSML での読み上げスタイルの使用方法](speech-synthesis-markup.md)を参照してください。

* **Custom Voice: トレーニング データの品質を自動的にチェックするための新機能がリリースされました**。 データをアップロードすると、システムによってオーディオとトランスクリプト データのさまざまな側面が調べられ、問題が自動的に修正またはフィルター処理されて、音声モデルの品質が向上します。 ここでは、オーディオとスクリプトの形式に加え、オーディオの音量、ノイズ レベル、音声の発音の正確さ、音声と正規化されたテキスト間の調整、オーディオのサイレント状態についても説明します。 

* **Audio Content Creation: より強力な音声チューニングとオーディオ管理機能を可能にする一連の新機能**。

    * 発音: 発音チューニング機能は、最新の音素セットに更新されています。 ライブラリから適切な音素要素を選択し、選択した単語の発音を洗練することができます。 

    * ダウンロード:オーディオの "ダウンロード" または "エクスポート" 機能は、段落によるオーディオ生成をサポートするように強化されています。 複数のオーディオ出力を生成しながら、同じファイルまたは SSML でコンテンツを編集することができます。 "ダウンロード" のファイル構造も改良されています。 すべてのオーディオを 1 つのフォルダーに簡単に取り込むことができるようになりました。 

    * タスクの状態: 複数ファイルのエクスポート エクスペリエンスが向上しました。 これまでは、複数のファイルをエクスポートするときに、いずれかのファイルにエラーが発生すると、タスク全体が失敗していました。 しかし今は、他のすべてのファイルが正常にエクスポートされます。 タスク レポートは、より詳細で構造化された情報で拡充されています。 すべての失敗したファイルと文のログをレポートで確認できるようになりました。 

    * SSML ドキュメント: すべてのチューニング機能の使用方法に関するルールを確認できるように、SSML ドキュメントにリンクされています。

* **Voice List API が更新され、ユーザー フレンドリな表示名と、ニューラル音声でサポートされている読み上げスタイルが含まれるようになりました**。

### <a name="general-tts-voice-quality-improvements"></a>一般的な TTS 音声品質の改善

* 単語レベルの発音エラー率が低減しました。`ru-RU` はエラーが 56% 減少し、`sv-SE` はエラーが 49% 減少しました。

* `en-US` ニューラル音声のポリフォニーの単語の読み上げが 40% 改良されました。 ポリフォニー単語の例には、"read"、"live"、"content"、"record"、"object" などがあります。 

* `fr-FR` での質問口調がより自然になりました。 MOS (Mean Opinion Score) の向上: +0.28

* 次の音声のボコーダーを更新したことで、忠実性が向上し、全体のパフォーマンスが 40% 向上しました。

    | Locale | 音声 |
    |---|---|    
    | `en-GB` | Mia |
    | `es-MX` | Dalia |
    | `fr-CA` | Sylvie |
    | `fr-FR` | Denise |
    | `ja-JP` | Nanami |
    | `ko-KR` | Sun-Hi |

### <a name="bug-fixes"></a>バグの修正

* Audio Content Creation ツールのいくつかのバグを修正しました 
    * 自動更新に関する問題を修正しました。 
    * 東南アジア リージョンの zh-CN の音声スタイルに関する問題を修正しました。
    * "break" タグによるエクスポート エラー、句読点のエラーなど、安定性の問題を修正しました。    

## <a name="new-speech-to-text-locales-2020-august-release"></a>新しい音声テキスト変換ロケール: 2020 年 8 月のリリース
8 月にリリースされた 26 個の音声テキスト変換の新しいロケール:2 つのヨーロッパ言語 (`cs-CZ` と `hu-HU`)、5 つの英語ロケール、および南米のほとんどの国をカバーする 19 のスペイン語のロケール。 新しいロケールの一覧を次に示します。 言語の完全な一覧については、[こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support)を参照してください。

| Locale  | Language                          |
|---------|-----------------------------------|
| `cs-CZ` | チェコ語 (チェコ共和国)            | 
| `en-HK` | 英語 (香港)               | 
| `en-IE` | 英語 (アイルランド)                 | 
| `en-PH` | 英語 (フィリピン)             | 
| `en-SG` | 英語 (シンガポール)               | 
| `en-ZA` | 英語 (南アフリカ)            | 
| `es-AR` | スペイン語 (アルゼンチン)               | 
| `es-BO` | スペイン語 (ボリビア)                 | 
| `es-CL` | スペイン語 (チリ)                   | 
| `es-CO` | スペイン語 (コロンビア)                | 
| `es-CR` | スペイン語 (コスタリカ)              | 
| `es-CU` | スペイン語 (キューバ)                    | 
| `es-DO` | スペイン語 (ドミニカ共和国)      | 
| `es-EC` | スペイン語 (エクアドル)                 | 
| `es-GT` | スペイン語 (グアテマラ)               | 
| `es-HN` | スペイン語 (ホンジュラス)                | 
| `es-NI` | スペイン語 (ニカラグア)               | 
| `es-PA` | スペイン語 (パナマ)                  | 
| `es-PE` | スペイン語 (ペルー)                    | 
| `es-PR` | スペイン語 (プエルトリコ)             | 
| `es-PY` | スペイン語 (パラグアイ)                | 
| `es-SV` | スペイン語 (エルサルバドル)             | 
| `es-US` | スペイン語 (米国)                     | 
| `es-UY` | スペイン語 (ウルグアイ)                 | 
| `es-VE` | スペイン語 (ベネズエラ)               | 
| `hu-HU` | ハンガリー語 (ハンガリー)               | 


## <a name="speech-sdk-1130-2020-july-release"></a>Speech SDK 1.13.0:2020 年 7 月リリース

**注**:Windows の音声 SDK は、Visual Studio 2015、2017、および 2019 の Microsoft Visual C++ 再配布可能パッケージに依存します。 [こちら](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)からダウンロードしてインストールします。

**新機能**
- **C#** : 非同期での会話の文字起こしのサポートが追加されました。 [こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-async-conversation-transcription)のドキュメントを参照してください。  
- **JavaScript**:[ブラウザー](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/speaker-recognition) と [node.js](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/speaker-recognition) の両方で、話者認識のサポートが追加されました。
- **JavaScript**:言語/言語 ID の自動検出のサポートが追加されました。 [こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-javascript)のドキュメントを参照してください。
- **Objective-C**:[マルチデバイスの会話](https://docs.microsoft.com/azure/cognitive-services/speech-service/multi-device-conversation)と[会話の文字起こし](https://docs.microsoft.com/azure/cognitive-services/speech-service/conversation-transcription)のサポートが追加されました。 
- **Python**: Windows および Linux での Python 用圧縮オーディオ サポートが追加されました。 [こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams)のドキュメントを参照してください。 

**バグの修正**
- **[すべて]** : KeywordRecognizer で認識後にストリームが送られない原因となっていた問題が修正されました。
- **[すべて]** : KeywordRecognitionResult から取得したストリームにキーワードが含まれていない原因となっていた問題が修正されました。
- **[すべて]** : SendMessageAsync で、ユーザーがその待機を終えた後に、実際にネットワーク経由でメッセージが送信されないという問題が修正されました。
- **[すべて]** : ユーザーが VoiceProfileClient::SpeakerRecEnrollProfileAsync メソッドを複数回呼び出し、呼び出しの完了を待機しなかった場合の、Speaker Recognition API のクラッシュが修正されました。
- **[すべて]** : VoiceProfileClient クラスと SpeakerRecognizer クラスでのファイル ログの有効化が修正されました。
- **JavaScript**:ブラウザーが最小化されたときの調整の[問題](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/74)が修正されました。
- **JavaScript**:ストリームでのメモリ リークの[問題](https://github.com/microsoft/cognitive-services-speech-sdk-js/issues/78)が修正されました。
- **JavaScript**:NodeJS からの OCSP 応答のキャッシュが追加されました。
- **Java**: BigInteger フィールドが常に 0 を返す原因となっていた問題が修正されました。
- **iOS**: iOS App Store で Speech SDK ベースのアプリを発行する場合の[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/702)が修正されました。

**サンプル**
- **C++** :話者認識のサンプル コードが[ここ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/cpp/windows/console/samples/speaker_recognition_samples.cpp)に追加されました。

**COVID-19 の影響によるテストの短縮:** 過去数週間にわたってリモートにて作業を行っているため、通常ならば実施するはずの手動による検証テストを行うことができませんでした。 問題発生の可能性が想定される変更はいっさい行っていません。また、自動テストはすべて成功しました。 しかし、もし仮に何らかの問題が発生した場合には、[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) にてお知らせください。<br>
皆様の健康をお祈りします！

## <a name="text-to-speech-2020-july-release"></a>テキスト読み上げ 2020 年 7 月リリース

### <a name="new-features"></a>新機能

* **ニューラル TTS、15 の新しいニューラル音声**: ニューラル TTS ポートフォリオに追加された新しい音声は、Salma (`ar-EG` アラビア語 (エジプト))、Zariyah (`ar-SA` アラビア語 (サウジアラビア))、Alba (`ca-ES` カタルニア語 (スペイン))、Christel (`da-DK` デンマーク語 (デンマーク))、Neerja (`es-IN` 英語 (インド))、Noora (`fi-FI` フィンランド語 (フィンランド))、Swara (`hi-IN` ヒンディー語 (インド))、Colette (`nl-NL` オランダ語 (オランダ))、Zofia (`pl-PL` ポーランド語 (ポーランド))、Fernanda (`pt-PT` ポルトガル (ポルトガル))、Dariya (`ru-RU` ロシア語 (ロシア))、Hillevi (`sv-SE` スウェーデン語 (スウェーデン))、Achara (`th-TH` タイ語 (タイ))、HiuGaai (`zh-HK` 中国語 (広東語、繁体中国語))、HsiaoYu (`zh-TW` 中国語 (台湾標準中国語)) です。 すべての[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices)を確認してください。  

* **Custom Voice、ユーザー エクスペリエンスを簡単にするトレーニング フローで合理化された音声テスト**: 新しいテスト機能を使用すると、一般的なシナリオや音声アシスタントのシナリオに対応するために、各言語用に最適化された定義済みのテスト セットを使用して、各音声が自動的にテストされます。 これらのテスト セットは慎重に選択され、一般的なユース ケースと音素が言語に含まれるようにテストされています。 さらに、ユーザーは、モデルのトレーニング時に独自のテスト スクリプトをアップロードできます。

* **オーディオ コンテンツの作成: 新しい機能のセットがリリースされ、より強力な音声チューニングとオーディオ管理機能が可能になりました**

    * `Pitch`、`rate`、`volume` は、低、中、高などの定義済みの値を使用したチューニングをサポートするように強化されています。 ユーザーは、オーディオ編集の "定数" 値を簡単に選択できるようになりました。

    ![オーディオ チューニング](media/release-notes/audio-tuning.png)

    * ユーザーは、作業ファイルの `Audio history` を確認できるようになりました。 この機能を使用すると、ユーザーは作業ファイルに関連するすべての生成されたオーディオを簡単に追跡できます。 履歴バージョンを確認し、同時にチューニングしながら品質を比較することができます。 

    ![オーディオ履歴](media/release-notes/audio-history.png)

    * `Clear` 機能の柔軟性が向上しました。 ユーザーは、選択したコンテンツで使用可能な他のパラメーターを維持したまま、特定のチューニング パラメーターをクリアできます。  

    * ユーザーが TTS 音声チューニングとオーディオ管理をすぐに開始できるようにするためのチュートリアル ビデオが、[ランディング ページ](https://speech.microsoft.com/audiocontentcreation)に追加されました。 

### <a name="general-tts-voice-quality-improvements"></a>一般的な TTS 音声品質の改善

* 再現性が高く、待機時間が短くなるように、TTS ボコーダーが改善されました。

    * `it-IT` の Elsa が新しいボコーダーに更新されました。これは、音声品質で +0.464 CMOS (Comparative Mean Opinion Score) の向上、合成で 40% の高速化、最初のバイトの待機時間で 30% の短縮を実現するものです。 
    * `zh-CN` の Xiaoxiao が新しいボコーダーに更新されました。これは、一般的なドメインで +0148 CMOS、ニュース放送スタイルで +0.348、叙情スタイルで +0.195 の向上を実現するものです。 

* TTS 出力をより自然にするために、`de-DE` および `ja-JP` 音声モデルが更新されました。
    
    * 最新の韻律モデリング方法を使用して `de-DE` の Katja が更新されました。MOS (Mean Opinion Score) の向上は +0.13 です。 
    * 新しいピッチ アクセント韻律モデルを使用して `ja-JP` の Nanami が更新されました。MOS (Mean Opinion Score) の向上は +0.19 です。  

* 5 つの言語の単語レベルの発音精度が向上しました。

    | Language | 発音エラーの除去 |
    |---|---|
    | `en-GB` | 51% |
    | `ko-KR` | 17% |
    | `pt-BR` | 39% |
    | `pt-PT` | 77% |
    | `id-ID` | 46% |

### <a name="bug-fixes"></a>バグの修正

* 通貨の読み取り
    * `es-ES` と `es-MX` での通貨の読み取りに関する問題が修正されました
     
    | Language | 入力 | 改善後の読み上げ |
    |---|---|---|
    | `es-MX` | $1.58 | un peso cincuenta y ocho centavos |
    | `es-ES` | $1.58 | un dólar cincuenta y ocho centavos |

    * 次のロケールでの負の通貨 ("-325 €" など) のサポート: `en-US`、`en-GB`、`fr-FR`、`it-IT`、`en-AU`、`en-CA`。

* `pt-PT` での住所の読み取りが改善されました。
* 単語 "for" と "four" について、Natasha (`en-AU`) と Libby (`en-UK`) の発音に関する問題を修正しました。  
* オーディオ コンテンツ作成ツールのバグを修正しました
    * 2 番目の段落の後の追加および予期しない一時停止が修正されています。  
    * "休止なし" 機能が回帰バグから再び追加されています。 
    * Speech Studio のランダム更新の問題が修正されています。  

### <a name="samplessdk"></a>サンプル/SDK

* JavaScript:Firefox および macOS と iOS での Safari における再生の問題が修正されました。 

## <a name="speech-sdk-1121-2020-june-release"></a>Speech SDK 1.12.1:2020 年 6 月リリース
**Speech CLI (SPX とも呼ばれます)**
-   CLI 内ヘルプ検索機能が追加されました。
    -   `spx help find --text TEXT`
    -   `spx help find --topic NAME`
-   新しくデプロイされた v3.0 Batch および Custom Speech API と連携するように更新されました。
    -   `spx help batch examples`
    -   `spx help csr examples`

**新機能**
-   **C\#、C++** :Speaker Recognition プレビュー:この機能により、話者識別 (だれが話しているか) と話者認証 (要求した本人か) が可能になります。 [概要](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/speaker-recognition-overview)から始めて、[話者認識の基本の記事](https://docs.microsoft.com/azure/cognitive-services/speech-service/speaker-recognition-basics)、または [API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/speakerrecognition/)を参照してください。

**バグの修正**
-   **C\#、C++** :話者認識の 1.12 で機能していなかったマイクの録音を修正しました。
-   **JavaScript**:FireFox および macOS と iOS 上の Safari でのテキスト読み上げの修正。
-   8 チャネル ストリーム使用時の、会話の文字起こしでの Windows アプリケーション検証ツールのアクセス違反クラッシュの修正。
-   マルチデバイス会話翻訳での Windows アプリケーション検証ツールのアクセス違反クラッシュの修正。

**サンプル**
-   **C#** : 話者認識の[コード サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/speaker-recognition)。
-   **C++** :話者認識の[コード サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp/windows/speaker-recognition)。
-   **Java**: Android での意図認識の[コード サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/intent-recognition)。 

**COVID-19 の影響によるテストの短縮:** 過去数週間にわたってリモートにて作業を行っているため、通常ならば実施するはずの手動による検証テストを行うことができませんでした。 問題発生の可能性が想定される変更はいっさい行っていません。また、自動テストはすべて成功しました。 しかし、もし仮に何らかの問題が発生した場合には、[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) にてお知らせください。<br>
皆様の健康をお祈りします！


## <a name="speech-sdk-1120-2020-may-release"></a>Speech SDK 1.12.0:2020-May リリース
**Speech CLI (SPX とも呼ばれます)**
- **SPX** は、コマンド ラインから認識、合成、翻訳、バッチ文字起こし、およびカスタム音声管理を実行するための新しいコマンド ライン ツールです。 これを使用して、音声サービスをテストしたり、実行する必要がある音声サービス タスクをスクリプト化したりできます。 ツールをダウンロードし、[こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/spx-overview)のドキュメントを参照してください。

**新機能**
- **Go**: [音声認識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go)と[カスタム音声アシスタント](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)のための新しい Go 言語サポート。 開発環境を[ここで](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-go)セットアップします。 サンプル コードについては、以下の「サンプル」セクションを参照してください。 
- **JavaScript**:音声合成のためのブラウザー サポートを追加しました。 [こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech-audio-file?pivots=programming-language-JavaScript)のドキュメントを参照してください。
- **C++、C#、Java**:新しい `KeywordRecognizer` オブジェクトと API が Windows、Android、Linux、および iOS プラットフォームでサポートされました。 [こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/custom-keyword-overview)のドキュメントをお読みください。 サンプル コードについては、以下の「サンプル」セクションを参照してください。 
- **Java**: 翻訳サポートを含むマルチデバイスの会話機能を追加しました。 [こちら](https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.transcription)の参照ドキュメントを参照してください。

**強化および最適化**
- **JavaScript**:ブラウザー マイクの実装を最適化し、音声認識の精度が向上しています。
- **Java**: SWIG なしで直接 JNI 実装を使用するバインディングをリファクターしました。 Windows、Android、Linux、Mac で使用されるすべての Java パッケージのバインディング サイズが 10 分の 1 に縮小され、Speech SDK Java 実装のさらなる開発が容易になります。
- **Linux**:最新の RHEL 7 固有の記載を含むようにサポート [ドキュメント](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)を更新しました。
- サービス エラーやネットワーク エラーが発生した場合に接続を複数回試行するための接続ロジックを改善しました。
- [portal.azure.com](https://portal.azure.com) の音声機能のクイックスタート ページを更新しました。開発者は Azure の音声体験の次のステップに進むことができます。

**バグの修正**
- **C#、Java**:Linux ARM への SDK ライブラリの読み込みに関する[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/587)を修正しました (32 ビットと 64 ビットの両方)。
- **C#** : TranslationRecognizer、IntentRecognizer、および Connection オブジェクトのネイティブ ハンドルの明示的な破棄を修正しました。
- **C#** : ConversationTranscriber オブジェクトのオーディオ入力の有効期間の管理を修正しました。
- 単純な語句から意図を認識するときに、`IntentRecognizer` の結果の理由が適切に設定されない問題を修正しました。
- `SpeechRecognitionEventArgs` 結果のオフセットが正しく設定されない問題を修正しました。
- WebSocket 接続を開く前に SDK がネットワーク メッセージを送信しようとする際の競合状態を修正しました。 参加者の追加中に `TranslationRecognizer` で再現されていました。
- キーワード認識エンジンのメモリ リークを修正しました。

**サンプル**
- **Go**: [音声認識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/speech-to-text-from-microphone?pivots=programming-language-go)と[カスタム音声アシスタント](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/voice-assistants?pivots=programming-language-go)のクイックスタートを追加しました。 サンプル コードは[こちら](https://github.com/microsoft/cognitive-services-speech-sdk-go/tree/master/samples)を参照してください。 
- **JavaScript**:[音声合成](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/text-to-speech?pivots=programming-language-javascript)、[翻訳](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/translate-speech-to-text?pivots=programming-language-javascript)、および[意図認識](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition?pivots=programming-language-javascript)のクイックスタートを追加しました。
- [C\#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer) および [Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer) (Android) のキーワード認識サンプル。  

**COVID-19 の影響によるテストの短縮:** 過去数週間にわたってリモートにて作業を行っているため、通常ならば実施するはずの手動による検証テストを行うことができませんでした。 問題発生の可能性が想定される変更はいっさい行っていません。また、自動テストはすべて成功しました。 しかし、もし仮に何らかの問題が発生した場合には、[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) にてお知らせください。<br>
皆様の健康をお祈りします！

## <a name="speech-sdk-1110-2020-march-release"></a>Speech SDK 1.11.0:2020年 3月 リリース
**新機能**
- Linux: Red Hat Enterprise Linux (RHEL)/CentOS 7 x64 のサポートを追加しました。Speech SDK 向けのシステム構成方法の[インストラクション](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-rhel-centos-7)も付いています。
- Linux: Linux ARM32 と ARM64 での C.NET Core C# のサポートを追加しました。 詳細については、[こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=linux)をご覧ください。 
- C# および C++:`ConversationTranscriptionResult` で `UtteranceId` を追加しました。これは、すべての中間および最終的な音声認識の結果にわたる一貫性のある ID です。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.transcription.conversationtranscriptionresult?view=azure-dotnet) および [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/transcription-conversationtranscriptionresult) の詳細。
- Python: `Language ID` のサポートを追加しました。 [GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/python/console) の speech_sample を参照してください。
- Windows: すべての win32 コンソール アプリケーション向けに、Windows プラットフォームでの圧縮されたオーディオ入力形式のサポートを追加しました。 詳細については、[こちら](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams) を参照してください。 
- JavaScript:NodeJS で音声合成 (テキスト読み上げ) をサポートします。 [こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node/text-to-speech)をご覧ください。 
- JavaScript:新しい API を追加して、すべての送受信メッセージを検査できるようにします。 [こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript)をご覧ください。 
        
**バグの修正**
- C# および C++:`SendMessageAsync` がバイナリ型としてバイナリ メッセージを送信するよう、問題を修正しました。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.sendmessageasync?view=azure-dotnet#Microsoft_CognitiveServices_Speech_Connection_SendMessageAsync_System_String_System_Byte___System_UInt32_) および [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection) の詳細。
- C# および C++:`Connection` オブジェクトの前に `Recognizer` が破棄された場合に `Connection MessageReceived` イベントを使用するとクラッシュが発生する問題を修正しました。 [C#](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.connection.messagereceived?view=azure-dotnet) および [C++](https://docs.microsoft.com/cpp/cognitive-services/speech/connection#messagereceived) の詳細。
- Android:マイクからのオーディオ バッファーサイズが 800ms から 100ms に減少し、待機時間が短縮されます。
- Android:Android Studio の x86 Android emulator に関する[問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/563)を修正しました。
- JavaScript:`fromSubscription` API の中国のリージョン向けのサポートが追加されました。 詳細については、[こちら](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#fromsubscription-string--string-) を参照してください。 
- JavaScript:NodeJS からの接続失敗のエラー情報を追加します。
        
**サンプル**
- Unity:意図認識パブリック サンプルで LUIS json インポートに失敗する問題が修正されました。 詳細については、[こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/369) を参照してください。
- Python: `Language ID` 向けに追加されたサンプル。 詳細については、[こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py) を参照してください。
    
**Covid19 により、テストの短縮を余儀なくされました**過去数週間にわたってリモートにて作業を行っているため、通常ならば実施するはずの手動によるデバイス検証テストを行うことができませんでした。 この例としては、Linux、iOS、macOS でのマイク入力とスピーカー出力のテストなどがあります。 これらのプラットフォームにおいては、問題発生の可能性が想定される変更はいっさい行っていません。また、自動テストはすべて成功しました。 しかし、もし仮に何らかの問題が発生した場合には、[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) にてお知らせください。<br> 今後とも、どうぞよろしくお願いいたします。 これまでと同様、[GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues?q=is%3Aissue+is%3Aopen) や [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/731) での質問やフィードバックの投稿をお待ちしています。<br>
皆様の健康をお祈りします！

## <a name="speech-sdk-1100-2020-february-release"></a>Speech SDK 1.10.0: 2020 年 2 月リリース

**新機能**

 - Python の新しい 3.8 リリースをサポートするために Python パッケージが追加されしました。
 - Red Hat Enterprise Linux (RHEL)/CentOS 8 x64 のサポート (C++、C#、Java、Python)。
   > [!NOTE] 
   > ユーザーは、[こちらの手順](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)に従って OpenSSL を構成する必要があり ます。
 - Debian および Ubuntu に対する Linux ARM32 のサポート。
 - DialogServiceConnector で、BotFrameworkConfig の省略可能な "ボット ID" パラメーターがサポートされるようになりました。 このパラメーターを使用すると、単一の Azure Speech リソースで複数の Direct Line Speech ボットを使用できます。 パラメーターの指定がない場合は、既定のボット ([Direct Line Speech チャネルの構成] ページによって決まります) が使用されます。
 - DialogServiceConnector に SpeechActivityTemplate プロパティが含まれるようになりました。 この JSON 文字列の内容は Direct Line Speech によって使用され、Direct Line Speech ボットに達するすべてのアクティビティでサポートされているさまざまなフィールドに事前設定されます。これには、音声認識などのイベントの応答で自動的に生成されるアクティビティも含まれます。
 - TTS で認証にサブスクリプション キーが使用されるようになり、シンセサイザーを作成した後の最初の合成結果の最初のバイト待機時間が短縮されました。
 - 19 のロケールの音声認識モデルが更新され、平均ワード エラー率が 18.6% 下がりました (es-ES、es-MX、fr-CA、fr-FR、it-IT、ja-JP、ko-KR、pt-BR、zh-CN、zh-HK、nb-NO、fi-FL、ru-RU、pl-PL、ca-ES、zh-TW、th-TH、pt-PT、tr-TR)。 新しいモデルでは、ディクテーション、コールセンターの文字起こし、ビデオのインデックス作成を含む複数の領域にわたる大幅な機能強化が行われています。

**バグの修正**

 - JAVA API で会話の文字起こし機能が適切に待機しないバグが修正されました 
 - Xamarin [GitHub 問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/363)に対する Android x86 エミュレーターの修正が行われました
 - 欠落していた (Get|Set)Property メソッドが AudioConfig に追加されました
 - 接続に失敗したときに audioDataStream を停止できない TTS バグが修正されました
 - リージョンなしでエンドポイントを使用すると、会話の翻訳で USP エラーが発生します
 - ユニバーサル Windows アプリケーションでの ID の生成で、適切に一意の GUID アルゴリズムが使用されるようになりました。これまでは、大規模なインタラクションでしばしば競合を発生させるスタブ実装に意図せずに既定で設定されていました。
 
 **サンプル**
 
 - [Unity マイクとプッシュ モード ストリーミング](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/unity/from-unitymicrophone)で Speech SDK を使用するための Unity サンプル

**その他の変更点**

 - [Linux 用の OpenSSL 構成ドキュメントが更新されました](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-configure-openssl-linux)

## <a name="speech-sdk-190-2020-january-release"></a>Speech SDK 1.9.0:2020 年 1 月のリリース

**新機能**

- マルチデバイスの会話: 複数のデバイスを同じ音声またはテキストベースの会話に接続し、それらの間で送信されるメッセージを必要に応じて翻訳します。 詳しくは、[こちらの記事](multi-device-conversation.md)をご覧ください。 
- Android の .aar パッケージ用のキーワード認識サポートが追加され、x86 と x64 のフレーバーのサポートが追加されました。 
- Objective-C: `SendMessage` および `SetMessageProperty` メソッドが `Connection` オブジェクトに追加されました。 [こちら](https://docs.microsoft.com/objectivec/cognitive-services/speech/spxconnection)のドキュメントを参照してください。
- TTS C++ api で、合成テキスト入力として `std::wstring` がサポートされるようになり、SDK に渡す前に wstring を string に変換する必要がなくなりました。 詳細については、[こちら](https://docs.microsoft.com/cpp/cognitive-services/speech/speechsynthesizer#speaktextasync)を参照してください。 
- C#:[言語 ID](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-automatic-language-detection?pivots=programming-language-csharp) と[ソース言語構成](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-specify-source-language?pivots=programming-language-csharp)を使用できるようになりました。
- JavaScript:カスタム メッセージをコールバック `receivedServiceMessage` として音声サービスからパススルーする機能を `Connection` オブジェクトに追加しました。
- JavaScript:オンプレミス コンテナーとソブリン クラウドでの使用を容易にするために、`FromHost API` のサポートが追加されました。 [こちら](speech-container-howto.md)のドキュメントを参照してください。
- JavaScript:[orgads](https://github.com/orgads) からの投稿により、`NODE_TLS_REJECT_UNAUTHORIZED` が適用されるようになりました。 詳細については、[こちら](https://github.com/microsoft/cognitive-services-speech-sdk-js/pull/75)を参照してください。

**重大な変更**

- `OpenSSL` はバージョン 1.1.1b に更新され、Linux の Speech SDK コア ライブラリに静的にリンクされています。 これにより、システムの `/usr/lib/ssl` ディレクトリに受信トレイ `OpenSSL` がインストールされていない場合に、中断が発生する可能性があります。 この問題を回避するには、Speech SDK ドキュメントにある[こちらのドキュメント](how-to-configure-openssl-linux.md)を確認してください。
- 音声データが 2 分を超えた場合に `WordLevelTimingResults` にアクセスできるように、C# `WordLevelTimingResult.Offset` に返されるデータ型を `int` から `long` に変更しました。
- `PushAudioInputStream` および `PullAudioInputStream` は、作成時にオプションで指定された`AudioStreamFormat` に基づいて wav ヘッダー情報を音声サービスに送信するようになりました。 お客様は、[サポートされるオーディオ入力の形式](how-to-use-audio-input-streams.md)を使用する必要があります。 他の形式では、最適な認識結果が得られないか、その他の問題が発生する可能性があります。 

**バグの修正**

- 上記の「重大な変更」にある `OpenSSL` の更新情報を参照してください。 Linux と Java の断続的なクラッシュとパフォーマンスの問題 (高負荷でのロックの競合) の両方を修正しました。 
- Java:コンカレンシーの高いシナリオでのオブジェクト クロージャが改善されました。
- NuGet パッケージを再構築しました。 lib フォルダーの下にある `Microsoft.CognitiveServices.Speech.core.dll` と `Microsoft.CognitiveServices.Speech.extension.kws.dll` の 3 つのコピーを削除し、NuGet パッケージを小さく、速くダウンロードできるようにし、いくつかの C++ ネイティブ アプリをコンパイルするために必要なヘッダーを追加しました。
- [こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp)のクイック スタート サンプルを修正しました。 これらは、Linux、macOS、Windows で「microphone not found」(マイクが見つかりません) という例外を表示せずに終了していました。
- [こちらのサンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/csharp/uwp/speechtotext-uwp)のような特定のコード パスでの長い音声認識結果による SDK のクラッシュを修正しました。
- [こちらのお客様の問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/396)に対処するために、Azure Web アプリ環境の SDK デプロイ エラーを修正しました。
- [こちらのお客様の問題](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/433)に対処するために、複数の `<voice>` タグまたは `<audio>` タグを使用したときの TTS エラーを修正しました。 
- SDK が中断から回復されたときの TTS 401 エラーを修正しました。
- JavaScript:[euirim](https://github.com/euirim) からの投稿により、オーディオ データの循環インポートを修正しました。 
- JavaScript: 1.7 で追加されたサービス プロパティの設定のサポートが追加されました。
- JavaScript: 接続エラーにより WebSocket の再接続試行が連続して失敗する可能性がある問題を修正しました。

**サンプル**

- Android 用のキーワード認識サンプルを[こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/java/android/sdkdemo)に追加しました。
- サーバー シナリオ用の TTS サンプルを[こちら](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_synthesis_server_scenario_sample.cs)に追加しました。
- C# と C++ 用のマルチデバイス会話のクイックスタートを[こちら](quickstarts/multi-device-conversation.md)に追加しました。

**その他の変更点**

- Android の SDK コア ライブラリのサイズが最適化されています。
- 1\.9.0 以降の SDK で、会話の文字起こしの声紋バージョン フィールドで `int` と `string` の両方の型がサポートされます。

## <a name="speech-sdk-180-2019-november-release"></a>Speech SDK 1.8.0: 2019-November リリース

**新機能**

- オンプレミス コンテナーとソブリン クラウドでの使用を容易にするために `FromHost()` API を追加しました。
- 音声認識の自動ソース言語検出機能を追加しました (Java および C++)。
- 音声認識でしかるべきソース言語を指定するための `SourceLanguageConfig` オブジェクトを追加しました (Java および C++)
- NuGet パッケージと Unity パッケージを通じて、Windows (UWP)、Android、iOS で新たに `KeywordRecognizer` を使用できるようになりました
- 非同期バッチで会話の文字起こしを行うための Remote Conversation Java API を追加しました。

**重大な変更**

- 会話の文字起こし機能が名前空間 `Microsoft.CognitiveServices.Speech.Transcription` の下に移動されました。
- 会話の文字起こし機能の一部のメソッドが、新しい `Conversation` クラスに移されました。
- 32 ビット (ARMv7 および x86) iOS のサポートが終了しました。

**バグの修正**

- 有効な音声サービスのサブスクリプション キーなしでローカル `KeywordRecognizer` を使用した場合に発生するクラッシュを修正しました

**サンプル**

- `KeywordRecognizer` の Xamarin サンプル
- `KeywordRecognizer` の Unity サンプル
- 自動ソース言語検出の C++ および Java サンプル

## <a name="speech-sdk-170-2019-september-release"></a>Speech SDK 1.7.0: 2019-September リリース

**新機能**

- ユニバーサル Windows プラットフォーム (UWP)、Android、iOS での Xamarin のベータ サポートが追加されました
- Unity の iOS サポートが追加されました
- Android、iOS、Linux での ALaw、Mulaw、FLAC に対する `Compressed` 入力のサポートが追加されました
- メッセージをサービスに送信するための `SendMessageAsync` が `Connection` クラスに追加されました
- メッセージのプロパティを設定するための `SetMessageProperty` が `Connection` クラスに追加されました
- TTS で Java (JRE と Android)、Python、Swift、Objective-C に対するバインドが追加されました
- TTS で macOS、iOS、Android に対する再生のサポートが追加されました。
- TTS に対する "ワード境界" の情報が追加されました。

**バグの修正**

- Unity 2019 for Android での IL2CPP のビルドの問題が修正されました
- wav ファイルの入力が正しく処理されない、間違った形式のヘッダーに関する問題が修正されました
- 一部の接続プロパティで UUID が一意ではない問題が修正されました
- Swift バインドでの NULL 値許容指定子に関するいくつかの警告が修正されました (小さなコード変更が必要な場合があります)
- ネットワークに負荷がかかると WebSocket 接続が異常終了する原因となっていたバグが修正されました
- `DialogServiceConnector` によって使用される印象 ID が重複することがある原因であった Android の問題が修正されました
- 複数ターン相互作用を通した接続の安定性と、`DialogServiceConnector` でエラーが発生したときの (`Canceled` イベント経由による) エラーの報告が向上しました
- `DialogServiceConnector` セッションの開始により、アクティブな `StartKeywordRecognitionAsync()` の間に `ListenOnceAsync()` を呼び出すなど、イベントが正しく提供されるようになりました
- `DialogServiceConnector` アクティビティの受信に関連するクラッシュが対処されました

**サンプル**

- Xamarin のクイックスタート
- Linux ARM64 の情報で CPP のクイックスタートを更新しました
- iOS の情報で Unity のクイックスタートを更新しました

## <a name="speech-sdk-160-2019-june-release"></a>Speech SDK 1.6.0: 2019-June リリース

**サンプル**

- UWP および Unity 上の Text to Speech 用のクイックスタート サンプル
- iOS 上の Swift 用のクイックスタート サンプル
- 音声および意図の認識と翻訳用の Unity サンプル
- `DialogServiceConnector` のクイック スタート サンプルが更新されました

**機能強化/変更**

- Dialog 名前空間:
  - `SpeechBotConnector` の名前が `DialogServiceConnector` に変更されました
  - `BotConfig` の名前が `DialogServiceConfig` に変更されました
  - `BotConfig::FromChannelSecret()` は `DialogServiceConfig::FromBotSecret()` に再マッピングされました
  - 既存のすべての Direct Line Speech クライアントは、名前の変更後も引き続きサポートされます
- TTS REST アダプターが更新され、プロキシ、固定接続がサポートされるようになりました
- 無効なリージョンが渡されたときのエラー メッセージを改善しました
- Swift/Objective-C:
  - エラー報告の改善: エラーが発生する可能性のあるメソッドが、2 つのバージョンで存在するようになりました。エラー処理のために `NSError` オブジェクトを公開するものと、例外を発生させるものです。 前者は Swift に公開されます。 この変更を既存の Swift コードに適応させる必要があります。
  - イベント処理を改善しました

**バグの修正**

- オーディオがレンダリングを完了するまで待たずに、`SpeakTextAsync` が制御を戻していた TTS の問題を修正しました
- 言語の完全サポートを可能にするために、C# での文字列のマーシャリングを修正しました
- サンプルで net461 ターゲット フレームワークを使用してコア ライブラリを読み込むときの .NET Core アプリの問題を修正しました
- サンプルでネイティブ ライブラリを出力フォルダーに展開するときに発生する場合がある問題を修正しました
- Web ソケットを確実に閉じるための修正を行いました
- Linux で高負荷時に接続を開いたときに発生する可能性があるクラッシュを修正しました
- macOS 用のフレームワーク バンドルの欠落しているメタデータを修正しました
- Windows での `pip install --user` に関する問題を修正しました

## <a name="speech-sdk-151"></a>Speech SDK 1.5.1

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

**バグの修正**

- 会話の文字起こしで使用する FromSubscription を修正しました。
- 音声アシスタントのキーワード スポッティング機能のバグを修正しました。

## <a name="speech-sdk-150-2019-may-release"></a>Speech SDK 1.5.0:2019-May リリース

**新機能**

- キーワード スポッティング機能 (KWS) が Windows と Linux で利用できるようになりました。 KWS の機能は任意の種類のマイクでも動作する可能性がありますが、公式の KWS サポートは、現時点では Azure Kinect DK ハードウェアまたは Speech Devices SDK 内のマイク アレイに限定されています。
- フレーズ ヒント機能は、この SDK を介して利用できます。 詳細については、[このページ](how-to-phrase-lists.md)を参照してください。
- 会話の文字起こし機能は、この SDK を介して利用できます。 [こちら](conversation-transcription-service.md)を参照してください。
- Direct Line Speech チャネルを使用して、音声アシスタントのサポートを追加します。

**サンプル**

- SDK でサポートされている新機能または新サービスのサンプルを追加しました。

**機能強化/変更**

- サービスの動作やサービスの結果を調整するさまざまなレコグナイザー プロパティを追加しました (冒涜的な表現やその他のマスクなど)。
- レコグナイザー `FromEndpoint` を作成した場合でも、標準の構成プロパティを使用してレコグナイザーを構成できるようになりました。
- Objective-C: `OutputFormat` プロパティが `SPXSpeechConfiguration` に追加されました。
- SDK は、Linux ディストリビューションとして Debian 9 をサポートするようになりました。

**バグの修正**

- テキスト読み上げでスピーカー リソースの破棄が早すぎる問題を修正しました。

## <a name="speech-sdk-142"></a>Speech SDK 1.4.2

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

## <a name="speech-sdk-141"></a>Speech SDK 1.4.1

これは、JavaScript のみのリリースです。 機能は追加されていません。 以下の修正が行われました。

- Web パックでHTTPS プロキシ エージェントが読み込まれないようにしました。

## <a name="speech-sdk-140-2019-april-release"></a>Speech SDK 1.4.0: 2019 年 4 月リリース

**新機能**

- SDK で、ベータ版としてテキスト読み上げサービスがサポートされるようになりました。 Windows および Linux デスクトップの C++ および C# からサポートされます。 詳細については、[テキスト読み上げの概要](text-to-speech.md#get-started)に関する記事を参照してください。
- SDK は、ストリーム入力ファイルとして MP3 および Opus/OGG オーディオ ファイルをサポートするようになりました。 この機能は、Linux の C++ と C# からのみ使用でき、現在はベータ版です (詳しくは[こちら](how-to-use-codec-compressed-audio-input-streams.md))。
- Java、.NET Core、C++、Objective-C 用の Speech SDK で、macOS がサポートされるようになりました。 Objective-C での MacOS のサポートは、現在ベータ版です。
- iOS:iOS (Objective-C) 用の Speech SDK が、CocoaPod としても公開されるようになりました。
- JavaScript:入力デバイスとしての既定以外のマイクのサポート。
- JavaScript:Node.js に対するプロキシのサポート。

**サンプル**

- macOS の C++ および Objective-C での Speech SDK の使用のサンプルが追加されました。
- テキスト読み上げサービスの使用方法を示すサンプルが追加されました。

**機能強化/変更**

- Python: 認識結果の追加のプロパティが、`properties` プロパティで公開されるようになりました。
- 開発とデバッグの追加サポートでは、SDK のログ記録と診断情報をログ ファイルにリダイレクトすることができます (詳細については[こちら](how-to-use-logging.md))。
- JavaScript:オーディオ処理のパフォーマンスが向上しました。

**バグの修正**

- Mac/iOS:Speech サービスへの接続を確立できないときに、長時間待機が発生するバグを修正しました。
- Python: Python コールバックでの引数のエラー処理が向上しました。
- JavaScript:RequestSession で終了した音声の正しくない状態レポートが修正されました。

## <a name="speech-sdk-131-2019-february-refresh"></a>Speech SDK 1.3.1:2019 年 2 月更新

これはバグ修正リリースで、ネイティブ/マネージド SDK にのみ影響します。 JavaScript バージョンの SDK には影響しません。

**バグ修正**

- マイク入力を使用する際のメモリ リークを修正しました。 ストリーム ベースの入力やファイル入力には影響しません。

## <a name="speech-sdk-130-2019-february-release"></a>Speech SDK 1.3.0: 2019-February リリース

**新機能**

- Speech SDK では、`AudioConfig` クラスによって入力マイクの選択がサポートされます。 これにより、既定以外のマイクから、Speech サービスに、音声 データをストリーミングできます。 詳しくは、[オーディオ入力デバイスの選択](how-to-select-audio-input-devices.md)に関する記事をご覧ください。 この機能は、JavaScript からはまだ使用できません。
- Speech SDK では、ベータ版で Unity がサポートされるようになりました。 [GitHub サンプル リポジトリ](https://aka.ms/csspeech/samples)の問題セクションでフィードバックをお送りください。 このリリースでは、Windows x86 と x64 (デスクトップまたはユニバーサル Windows プラットフォーム アプリケーション) および Android (ARM32/64、x86) での Unity がサポートされています。 詳しくは、[Unity のクイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=unity)に関する記事をご覧ください。
- (以前のリリースで提供されていた) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` ファイルは不要になりました。 この機能はコア SDK に統合されました。

**サンプル**

[サンプル リポジトリ](https://aka.ms/csspeech/samples)で次の新しいコンテンツを利用できます。

- `AudioConfig.FromMicrophoneInput` のその他のサンプル
- 意図認識と翻訳に関する追加の Python サンプル。
- iOS での `Connection` オブジェクトの使用に関する追加サンプル。
- オーディオ出力での翻訳に関する追加の Java サンプル。
- [Batch Transcription REST API](batch-transcription.md) の使用に関する新しいサンプル。

**機能強化/変更**

- Python
  - `SpeechConfig` でのパラメーター検証とエラー メッセージが強化されました。
  - `Connection` オブジェクトのサポートを追加します。
  - Windows での 32 ビット Python (x86) のサポート。
  - Python 用 Speech SDK がベータ版ではなくなりました。
- iOS
  - SDK のビルド対象が iOS SDK バージョン 12.1 になりました。
  - SDK が、iOS バージョン 9.2 以降をサポートするようになりました。
  - リファレンス ドキュメントが改善され、いくつかのプロパティ名が修正されました。
- JavaScript
  - `Connection` オブジェクトのサポートを追加します。
  - バンドルされている JavaScript の型定義ファイルが追加されました
  - 語句ヒントの最初のサポートと実装。
  - 認識に対するサービス JSON でプロパティのコレクションが返されます
- Windows の DLL に、バージョン リソースが含まれるようになりました。
- 認識エンジン `FromEndpoint` を作成した場合は、エンドポイント URL に直接パラメーターを追加できます。 `FromEndpoint` を使用した場合、標準の構成プロパティを通じて認識エンジンを構成することはできません。

**バグの修正**

- 空のプロキシ ユーザー名とプロキシ パスワードが、正しく処理されませんでした。 このリリースでは、プロキシ ユーザー名とプロキシ パスワードを空の文字列に設定した場合は、プロキシに接続したときに送信されません。
- SDK によって作成された SessionId が、一部の言語&nbsp;/環境で本当にランダムではないことがありました。 乱数ジェネレーターの初期化を追加してこの問題を解決しました。
- 認可トークンの処理が向上します。 認可トークンを使用する場合、`SpeechConfig` で指定し、サブスクリプション キーを空のままにします。 その後、通常どおり認識エンジンを作成します。
- 場合によっては、`Connection` オブジェクトが正しく解放されませんでした。 この問題は修正されています。
- 翻訳合成も Safari 上にある場合にオーディオ出力をサポートするよう、JavaScript のサンプルが修正されました。

## <a name="speech-sdk-121"></a>Speech SDK 1.2.1

これは、JavaScript のみのリリースです。 機能は追加されていません。 以下の修正が行われました。

- speech.end ではなく turn.end でストリームの終了が発生します。
- 現在の送信が失敗した場合に、次の送信がスケジュールされなかった、オーディオ ポンプのバグを修正しました。
- 認証トークンでの継続的な認識を修正しました。
- 別の認識エンジン/エンドポイントのバグ修正。
- ドキュメントの改善。

## <a name="speech-sdk-120-2018-december-release"></a>Speech SDK 1.2.0:2018-December リリース

**新機能**

- Python
  - このリリースで、Python サポート (3.5 以降) のベータ版を使用できるようになりました。 詳しくは、こちら (quickstart-python.md) をご覧ください。
- JavaScript
  - Speech SDK for JavaScript はオープンソースで提供されています。 ソース コードは [GitHub](https://github.com/Microsoft/cognitive-services-speech-sdk-js)から入手できます。
  - Node.js のサポートを開始しました。詳細については、[こちら](quickstart-js-node.md)を参照してください。
  - 音声セッションの長さの制限がなくなり、再接続は背後で自動的に実行されるようになります。
- `Connection` オブジェクト
  - `Recognizer` から、`Connection` オブジェクトにアクセスできます。 このオブジェクトを使用すると、サービスの接続を明示的に開始し、接続イベントと切断イベントをサブスクライブすることができます
    (この機能は、JavaScript と Python からはまだ使用できません)。
- Ubuntu 18.04 のサポート。
- Android
  - APK 生成時の ProGuard サポートが有効になりました。

**機能強化**

- 内部スレッドの使用方法を改善し、スレッド、ロック、相互排他の数を減らしました。
- エラー報告や情報を改善しました。 一部のケースでは、エラー メッセージがまったく伝達されていませんでした。
- 最新のモジュールを使用するように JavaScript の開発依存関係を更新しました。

**バグの修正**

- `RecognizeAsync` の型の不一致によるメモリ リークを修正しました。
- 場合によっては、例外がリークしていました。
- 翻訳イベント引数のメモリ リークを修正しました。
- 長時間実行中のセッションでの再接続に関するロックの問題を修正しました。
- 翻訳が失敗した場合に最終的な結果が失われる可能性がある問題を修正しました。
- C#:メイン スレッドで `async` 操作が待機されていない場合、非同期タスクが完了する前に認識機能が破棄される可能性がありました。
- Java:Java VM がクラッシュする原因となる問題を修正しました。
- Objective-C: `RecognizingIntent` ではなく RecognizedIntent が返されるという列挙型のマッピングを修正しました。
- JavaScript:`SpeechConfig` で既定の出力形式を 'simple' に設定します。
- JavaScript:JavaScript と他の言語の config オブジェクトのプロパティ間にある不整合を解消しました。

**サンプル**

- いくつかのサンプルを更新および修正しました (たとえば、翻訳のための出力音声など)。
- [サンプル リポジトリ](https://aka.ms/csspeech/samples)に Node.js サンプルを追加しました。

## <a name="speech-sdk-110"></a>Speech SDK 1.1.0

**新機能**

- Android x86 または x64 のサポート。
- プロキシのサポート: `SpeechConfig` オブジェクトで、プロキシ情報 (ホスト名、ポート、ユーザー名、パスワード) を設定する関数を呼び出せるようになりました。 この機能は iOS ではまだ利用できません。
- 強化されたエラー コードとメッセージ。 認識でエラーが返された場合、これは既に `Reason` (取り消されたイベントの場合) または`CancellationDetails` (認識結果) を `Error` に設定します。 取り消されたイベントに、2 つのメンバー `ErrorCode` と `ErrorDetails` が含まれるようになりました。 サーバーによって、報告されたエラーと一緒に追加のエラー情報が返された場合、これを新しいメンバーで使用できるようになります。

**機能強化**

- 認識エンジンの構成に検証が追加され、エラー メッセージが追加されました。
- オーディオ ファイル中の長時間のサイレント状態の処理が強化されました。
- NuGet パッケージ: .NET Framework プロジェクトの場合、AnyCPU 構成でビルドされることを防ぎます。

**バグの修正**

- 認識エンジンで見つかったいくつかの例外を修正しました。 さらに例外がキャッチされ、`Canceled` イベントに変換されます。
- プロパティ管理のメモリ リークを修正しました。
- オーディオ入力ファイルが認識エンジンをクラッシュする可能性があるバグを修正しました。
- セッションがイベントを停止した後でイベントが受信されることがあるバグを修正しました。
- スレッド処理の一部の競合状態を修正しました。
- クラッシュの原因となる iOS の互換性の問題を修正しました。
- Android のマイクのサポートのための安定性の向上。
- JavaScript の認識エンジンが認識言語を無視するバグを修正しました。
- JavaScript で (場合によっては) `EndpointId` の設定を妨げるバグを修正しました。
- JavaScript の AddIntent のパラメーターの順序を変更し、不足している `AddIntent` JavaScript 署名を追加します。

**サンプル**

- プルおよびプッシュ ストリームの使用のための C++ と C# のサンプルを[サンプル リポジトリ](https://aka.ms/csspeech/samples)に追加しました。

## <a name="speech-sdk-101"></a>Speech SDK 1.0.1

信頼性の向上とバグ修正

- 認識エンジンの破棄での競合状態によって致命的なエラーが発生する可能性を修正しました
- 未設定のプロパティによって致命的なエラーが発生する可能性を修正しました。
- 新しいエラーとパラメーターのチェックを追加しました。
- Objective-C: NSString での名前のオーバーライドによって致命的なエラーが発生する可能性を修正しました。
- Objective-C: API の可視性を調整しました
- JavaScript:イベントとそのペイロードに関して修正しました。
- ドキュメントの改善。

[サンプル リポジトリ](https://aka.ms/csspeech/samples)に、JavaScript の新しいサンプルを追加しました。

## <a name="cognitive-services-speech-sdk-100-2018-september-release"></a>Cognitive Services Speech SDK 1.0.0: 2018-September リリース

**新機能**

- iOS での Objective-C のサポート。 [iOS での Objective-C のクイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone-langs/objectivec-ios.md)に関するページをご覧ください。
- ブラウザーでの JavaScript のサポート。 [JavaScript のクイック スタート](quickstart-js-browser.md)に関するページをご覧ください。

**重大な変更**

- このリリースでは、重大な変更がいくつか行われています。
  詳細については、[こちらのページ](https://aka.ms/csspeech/breakingchanges_1_0_0)を参照してください。

## <a name="cognitive-services-speech-sdk-060-2018-august-release"></a>Cognitive Services Speech SDK 0.6.0: 2018-August リリース

**新機能**

- Speech SDK で構築された UWP アプリは、Windows アプリ認定キット (WACK) に合格できるようになりました。
  [UWP のクイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp&tabs=uwp)に関するページをご覧ください。
- Linux (Ubuntu 16.04 x 64) 上の .NET Standard 2.0 のサポート。
- 試験段階: Windows (64 ビット) および Linux (Ubuntu 16.04 x 64) での Java 8 サポート。
  [Java ランタイム環境のクイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=jre)に関するページをご覧ください。

**機能の変更点**

- 接続エラーに関する追加エラーの詳細情報が公開されました。

**重大な変更**

- Java (Android) で、`SpeechFactory.configureNativePlatformBindingWithDefaultCertificate` 関数にパス パラメーターが不要になりました。 サポートされているすべてのプラットフォームでパスが自動的に検出されるようになりました。
- Java および C# のプロパティ `EndpointUrl` の get-accessor が削除されました。

**バグの修正**

- Java で、翻訳認識エンジンの音声合成結果が実装されるようになりました。
- 非アクティブなスレッドの原因となったり、未使用の開いたソケット数の増加の原因となったりするバグが修正されました。
- 実行時間の長い認識が伝送の途中で終了する問題が修正されました。
- 認識エンジンのシャットダウン時の競合状態を修正しました。

## <a name="cognitive-services-speech-sdk-050-2018-july-release"></a>Cognitive Services Speech SDK 0.5.0: 2018-July リリース

**新機能**

- Android プラットフォーム (API 23: Android 6.0 Marshmallow 以降) をサポートします。 [Android クイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-java&tabs=android)をチェックアウトします。
- Windows 上の .NET Standard 2.0 をサポートします。 [.NET Core クイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore)をチェックアウトします。
- 試験段階: Windows 上での UWP のサポート (バージョン 1709 以降)。
  - [UWP のクイック スタート](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp)に関するページをご覧ください。
  - 注:Speech SDK で構築された UWP アプリは、まだ Windows アプリ認定キット (WACK) に合格していません。
- 自動再接続を使用して、実行時間の長い認識をサポートします。

**機能の変更点**

- `StartContinuousRecognitionAsync()` は、実行時間の長い認識をサポートします。
- 認識結果に含まれるフィールドが増えました。 認識されたテキストのオーディオの開始からのオフセットと期間 (どちらも単位はティック)、および認識状態を表す追加の値 (`InitialSilenceTimeout` や `InitialBabbleTimeout` など) です。
- ファクトリ インスタンスを作成するための AuthorizationToken をサポートします。

**重大な変更**

- 認識イベント: `NoMatch` のイベントの種類は、`Error` イベントにマージされました。
- C# での SpeechOutputFormat は、C++ との整合性を維持するために `OutputFormat` に名前が変更されました。
- `AudioInputStream` インターフェイスのいくつかのメソッドの戻り値の型が若干変更されました。
  - Java では、`read` メソッドが `int` の代わりに `long` を返すようになりました。
  - C# では、`Read` メソッドが `int` の代わりに `uint` を返すようになりました。
  - C++ では、`Read` および `GetFormat` メソッドが `int` の代わりに `size_t` を返すようになりました。
- C++: オーディオ入力ストリームのインスタンスを `shared_ptr` としてのみ渡すことができるようになりました。

**バグの修正**

- `RecognizeAsync()` がタイムアウトしたときの結果にある正しくない戻り値が修正されました。
- Windows 上のメディア ファンデーション ライブラリへの依存関係が削除されました。 SDK で Core Audio API が使用されるようになりました。
- ドキュメントの修正: サポートされるリージョンを説明するための[リージョン](regions.md) ページが追加されました。

**既知の問題**

- Android 用の Speech SDK では、翻訳のための音声合成の結果が報告されません。 この問題は次のリリースで修正される予定です。

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Cognitive Services Speech SDK 0.4.0: 2018-June リリース

**機能の変更点**

- AudioInputStream

  認識エンジンでは、オーディオ ソースとしてストリームを利用できるようになりました。 詳細については、関連する[ハウツー ガイド](how-to-use-audio-input-streams.md)を参照してください。

- 出力形式の詳細

  `SpeechRecognizer` を作成するときに、`Detailed` または `Simple` 出力形式を要求できます。 `DetailedSpeechRecognitionResult` には、信頼度スコア、認識されるテキスト、生の語彙形式、正規化形式、および不適切な内容がマスクされた正規化形式が含まれます。

**重大な変更**

- C# で `SpeechRecognitionResult.Text` から `SpeechRecognitionResult.RecognizedText` に変更されました。

**バグの修正**

- シャットダウン中に USP レイヤーで発生する可能性のあるコールバックの問題を修正しました。
- 認識エンジンでオーディオ入力ファイルが使用されると、必要以上に長くファイル ハンドルが保持されていました。
- メッセージ ポンプと認識エンジンの間の複数のデッドロックが削除されました。
- サービスからの応答がタイムアウトすると、結果は `NoMatch` になります。
- Windows のメディア ファンデーション ライブラリは、遅延読み込みされます。 このライブラリは、マイク入力の場合のみ必要です。
- オーディオ データのアップロードの速度が、元の音声速度の約 2 倍に制限されます。
- Windows では、C# .NET アセンブリには厳密な名前が指定されるようになりました。
- ドキュメントの修正: `Region` は、認識エンジンを作成するための必須情報です。

他のサンプルも追加されており、常に更新されます。 最新のサンプル セットについては、[Speech SDK のサンプルの GitHub リポジトリ](https://aka.ms/csspeech/samples)を参照してください。

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Cognitive Services Speech SDK 0.2.12733: 2018-May リリース

このリリースは、Cognitive Services Speech SDK の最初のパブリック プレビュー リリースです。
