---
title: Speech to Text API リファレンス (REST) - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech to Text REST API の使用方法について説明します。 この記事では、認可のオプションとクエリのオプション、さらに要求を構築する方法と応答を受信する方法について説明します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: 26fe995f45a97a5863bfc20fd1564df89124ed88
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168311"
---
# <a name="speech-to-text-rest-api"></a>Speech to Text REST API

[Speech SDK](speech-sdk.md) の代替手段となる Speech Services では、REST API を使用して音声テキスト変換を実行できます。 アクセス可能な各エンドポイントは、リージョンに関連付けられています。 アプリケーションには、使用を検討しているエンドポイントのサブスクリプション キーが必要となります。

Speech to Text REST API を使用する前に、次のことを理解してください。

* REST API を使用して音声を直接送信する要求には、最大 60 秒の音声のみを含めることができます。
* Speech to Text REST API から返すことができるのは最終的な結果だけです。 結果を部分的に得ることはできません。

それを超える長さの音声をアプリケーションで送信する必要がある場合は、[Speech SDK](speech-sdk.md) か、[バッチ文字起こし](batch-transcription.md)など、ファイルベースの REST API の使用を検討してください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>リージョンとエンドポイント

REST API のエンドポイントの形式は次のとおりです。

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

次の表に示す、ご利用のサブスクリプションのリージョンと一致する識別子で `<REGION_IDENTIFIER>` を置き換えてください。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 4xx HTTP エラーを受け取らないためには、URL に言語パラメーターを付加する必要があります。 たとえば、米国西部エンドポイントを使用する米国英語に設定される言語は `https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US` です。

## <a name="query-parameters"></a>クエリ パラメーター

REST 要求のクエリ文字列には、次のパラメーターを含めることができます。

| パラメーター | 説明 | 必須/省略可能 |
|-----------|-------------|---------------------|
| `language` | 認識の対象として発話された言語を識別します。 [サポートされている言語](language-support.md#speech-to-text)を参照してください。 | Required |
| `format` | 結果の形式を指定します。 指定できる値は、`simple` と `detailed` です。 単純な結果には `RecognitionStatus`、`DisplayText`、`Offset`、`Duration`が含まれます。 詳細な応答には、信頼度の値と 4 つの異なる表現を持った複数の結果が含まれます。 既定の設定は `simple` です。 | 省略可能 |
| `profanity` | 認識結果内の不適切な表現をどう扱うかを指定します。 指定できる値は、`masked` (不適切な表現をアスタリスクに置き換える)、`removed` (すべての不適切な表現を結果から除去する)、または `raw` (不適切な表現を結果に含める) です。 既定の設定は `masked` です。 | 省略可能 |

## <a name="request-headers"></a>要求ヘッダー

この表は、音声テキスト変換要求の必須のヘッダーと省略可能なヘッダーの一覧です。

|ヘッダー| 説明 | 必須/省略可能 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 音声サービスのサブスクリプション キー。 | このヘッダーと `Authorization` のどちらかが必須となります。 |
| `Authorization` | 単語 `Bearer` が前に付いた認証トークン。 詳細については、[認証](#authentication)に関するページをご覧ください。 | このヘッダーと `Ocp-Apim-Subscription-Key` のどちらかが必須となります。 |
| `Content-type` | 指定したオーディオ データの形式とコーデックを記述します。 指定できる値は、`audio/wav; codecs=audio/pcm; samplerate=16000` と `audio/ogg; codecs=opus` です。 | Required |
| `Transfer-Encoding` | オーディオを個別のファイルとしてではなくチャンク データとして送信することを指定します。 このヘッダーは、オーディオ データをチャンクにする場合にのみ使用してください。 | 省略可能 |
| `Expect` | チャンク転送を使用する場合、`Expect: 100-continue` を送信します。 音声サービスは最初の要求を確認し、追加のデータを待ちます。| オーディオのチャンク データを送信する場合は必須となります。 |
| `Accept` | 指定する場合は、`application/json` とする必要があります。 Speech Service からは、結果が JSON 形式で返されます。Speech Service からは、結果が JSON 形式で返されます。 一部の要求フレームワークでは、互換性のない既定値が提供されます。 常に `Accept` を含めることをお勧めします。 | 省略可能ですが、指定することをお勧めします。 |

## <a name="audio-formats"></a>オーディオの形式

オーディオは HTTP `POST` 要求の本文で送信されます。 この表内のいずれかの形式にする必要があります。

| Format | コーデック | Bitrate | サンプル レート |
|--------|-------|---------|-------------|
| WAV | PCM 0 | 16 ビット | 16 kHz、モノラル |
| OGG | OPUS | 16 ビット | 16 kHz、モノラル |

>[!NOTE]
>上の形式は、Speech Service の REST API と WebSocket を介してサポートされます。 現在、[Speech SDK](speech-sdk.md) では PCM コーデックの WAV 形式と、[その他の形式](how-to-use-codec-compressed-audio-input-streams.md)がサポートされています。

## <a name="sample-request"></a>要求のサンプル

以下のサンプルには、ホスト名と必須のヘッダーが含まれています。 サービスにはオーディオ データも必要であることに注意してください。このサンプルにオーディオ データは含まれていません。 前述のようにチャンクにすることをお勧めしますが、必須ではありません。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

## <a name="http-status-codes"></a>HTTP 状態コード

各応答の HTTP 状態コードは、成功または一般的なエラーを示します。

| HTTP 状態コード | 説明 | 考えられる理由 |
|------------------|-------------|-----------------|
| 100 | Continue | 最初の要求が受け付けられました。 残りのデータの送信を続行します。 (チャンク転送で使用されます。) |
| 200 | [OK] | 要求は成功しました。応答本文は JSON オブジェクトです。 |
| 400 | 正しくない要求 | 言語コードが提供されていない、サポートされていない言語、無効な音声ファイルなどです。 |
| 401 | 権限がありません | サブスクリプション キーまたは認証トークンが指定のリージョンで無効であるか、または無効なエンドポイントです。 |
| 403 | Forbidden | サブスクリプション キーまたは認証トークンがありません。 |

## <a name="chunked-transfer"></a>チャンク転送

チャンク転送 (`Transfer-Encoding: chunked`) は、認識の待ち時間を短縮するのに役立ちます。 これにより、Speech Services では、音声ファイルを転送中にファイルの処理を開始できます。 REST API は部分的または中間的な結果を提供しません。

このコード サンプルは、オーディオをチャンクで送信する方法を示しています。 最初のチャンクだけに、オーディオ ファイルのヘッダーが含まれている必要があります。 `request` は、適切な REST エンドポイントに接続された HTTPWebRequest オブジェクトです。 `audioFile` はディスク上のオーディオ ファイルのパスです。

```csharp

    HttpWebRequest request = null;
    request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
    request.SendChunked = true;
    request.Accept = @"application/json;text/xml";
    request.Method = "POST";
    request.ProtocolVersion = HttpVersion.Version11;
    request.Host = host;
    request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
    request.Headers["Ocp-Apim-Subscription-Key"] = args[1];
    request.AllowWriteStreamBuffering = false;

using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>応答パラメーター

結果は JSON 形式で返されます。 `simple` 形式には、次の最上位レベル フィールドが含まれます。

| パラメーター | 説明  |
|-----------|--------------|
|`RecognitionStatus`|認識成功を表す `Success` などのステータス。 次の表を参照してください。|
|`DisplayText`|大文字化、句読点、逆テキスト正規化 (「two hundred」から「200」に、または「doctor smith」から「Dr. Smith」のように、音声テキストをより短い形式に変換すること)、および不適切な表現のマスキングの後に認識されたテキスト。 成功時にのみ存在します。|
|`Offset`|認識された音声がオーディオ ストリーム内で開始する時間 (100 ナノ秒単位)。|
|`Duration`|認識された音声のオーディオ ストリーム内での持続時間 (100 ナノ秒単位)。|

`RecognitionStatus` フィールドには、次の値が格納されます。

| Status | 説明 |
|--------|-------------|
| `Success` | 認識が成功し、`DisplayText` フィールドが存在する。 |
| `NoMatch` | 音声ストリーム内に音声が検出されたが、ターゲット言語の単語は見つからなかった。 通常は、認識言語がユーザーが話している言語とは異なる言語であることを意味します。 |
| `InitialSilenceTimeout` | 音声ストリームの先頭に無音状態しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `BabbleTimeout` | 音声ストリームの先頭に雑音しか含まれておらず、音声の待機中にサービスがタイムアウトした。 |
| `Error` | 認識サービスで内部エラーが発生し、処理を継続できない。 可能であれば再試行してください。 |

> [!NOTE]
> オーディオが不適切な表現のみで構成されており、`profanity` クエリ パラメーターが `remove` に設定されている場合、サービスは音声結果を返しません。

`detailed` 形式には、`simple` 形式と同じデータに加え、`NBest` が含まれます。これは、同じ認識結果に対する他の解釈のリストです。 これらの結果は、最も可能性の高いものから最も可能性の低いものへと順位付けされます。 最初のエントリはメイン認識結果と同じです。  `detailed` 形式を使用しているときは、`NBest` リストに含まれるそれぞれの結果について、`DisplayText` が `Display` として返されます。

`NBest` リスト内の各オブジェクトには以下のものが含まれます。

| パラメーター | 説明 |
|-----------|-------------|
| `Confidence` | 0\.0 (信頼度なし) から1.0 (完全信頼) までのエントリの信頼度スコア |
| `Lexical` | 認識されたテキストの語彙形式: 認識された実際の単語。 |
| `ITN` | 認識されたテキストの逆テキスト正規化 ("カノニカル") 形式。電話番号、数字、略語 (「doctor smith」から「dr smith」)、およびその他の変換を適用したものです。 |
| `MaskedITN` | 要求された場合、不適切な表現のマスキングを適用した ITN 形式。 |
| `Display` | 認識されたテキストの表示形式。句読点と大文字化を追加したものです。 このパラメーターは、形式を `simple` に設定したときに返される `DisplayText` と同じです。 |

## <a name="sample-responses"></a>応答のサンプル

`simple` 認識の代表的な応答:

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed` 認識の代表的な応答:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>次のステップ

- [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
- [音響モデルをカスタマイズする](how-to-customize-acoustic-models.md)
- [言語モデルをカスタマイズする](how-to-customize-language-model.md)
