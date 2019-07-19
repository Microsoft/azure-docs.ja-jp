---
title: Text Analytics REST API を使用した言語の検出 | Microsoft Docs
description: Azure Cognitive Services の Text Analytics REST API を使用して言語を検出する方法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: 6f1e71b75aa68c8f4ea1fa8ed373da25dbb3c24b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304042"
---
# <a name="example-how-to-detect-language-with-text-analytics"></a>例:Text Analytics で言語を検出する方法

この API の[言語検出](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)機能では、テキスト入力が評価され、各ドキュメントについて言語識別子と、分析の強度を示すスコアが返されます。

この機能は、言語が不明な任意のテキストを取集するコンテンツ ストアに役立ちます。 この分析の結果を解析して、入力ドキュメントでいずれの言語が使用されるかを特定できます。 応答では、モデルの信頼度 (0 から 1 の値) が反映されたスコアも返されます。

この機能の言語の正確な一覧は公開されていませんが、さまざまな言語、異形、方言、一部の地方言語や文化言語を検出できます。 

使用頻度の低い言語で表されるコンテンツがある場合は、言語検出を試して、コードが返されるかどうかを確認できます。 検出できない言語の応答は `unknown` です。

> [!TIP]
> Text Analytics には言語検出用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

## <a name="preparation"></a>準備

JSON ドキュメントは、次の形式である必要があります: ID、テキスト

ドキュメントのサイズは、ドキュメントあたり 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 言語検出のために送信するコンテンツの例を次に示します。

   ```
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },                
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>手順 1:要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください。[Language Detection API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)

+ Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、言語検出用の HTTP エンドポイントを設定します。 そこには、`/languages` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)についてのページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[データ制限](../overview.md#data-limits)」セクションを参照してください。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。


## <a name="step-3-view-results"></a>手順 3:結果の表示

すべての POST 要求で、ID と検出されたプロパティを含む JSON 形式の応答が返されます。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

要求の例の結果は、次の JSON のようになります。 複数の項目を含む 1 つのドキュメントであることに注意してください。 出力は英語です。 言語識別子には、[ISO 639-1](https://www.iso.org/standard/22109.html) 形式のフレンドリ名と言語コードが含まれます。

正のスコア 1.0 は、分析の可能な最も高い信頼レベルを表します。



```
{
    "documents": [
        {
            "id": "1",
            "detectedLanguages": [
                {
                    "name": "English",
                    "iso6391Name": "en",
                    "score": 1
                }
            ]
        },
        {
            "id": "2",
            "detectedLanguages": [
                {
                    "name": "Spanish",
                    "iso6391Name": "es",
                    "score": 1
                }
            ]
        },
        {
            "id": "3",
            "detectedLanguages": [
                {
                    "name": "French",
                    "iso6391Name": "fr",
                    "score": 1
                }
            ]
        },
        {
            "id": "4",
            "detectedLanguages": [
                {
                    "name": "Chinese_Simplified",
                    "iso6391Name": "zh_chs",
                    "score": 1
                }
            ]
        },
        {
            "id": "5",
            "detectedLanguages": [
                {
                    "name": "Russian",
                    "iso6391Name": "ru",
                    "score": 1
                }
            ]
        }
    ],
```

### <a name="ambiguous-content"></a>あいまいなコンテンツ

アナライザーで入力を解析できない場合 (たとえば、アラビア数字のみで構成されるテキスト ブロックを送信した場合など)、`(Unknown)` が返されます。

```
    {
      "id": "5",
      "detectedLanguages": [
        {
          "name": "(Unknown)",
          "iso6391Name": "(Unknown)",
          "score": "NaN"
        }
      ]
```
### <a name="mixed-language-content"></a>混合言語のコンテンツ

同じドキュメント内の言語が混在したコンテンツでは、コンテンツの最大の表現で言語が返されますが、評価の肯定度は低くなります。これは、その評価の限界強度が反映されるためです。 次の例では、入力は英語、スペイン語、フランス語の混在です。 アナライザーは各セグメント内の文字をカウントして、主要な言語を特定します。

**Input (入力)**

```
{
  "documents": [
    {
      "id": "1",
      "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
    }
  ]
}
```

**Output**

結果の出力は、主要な言語で構成され、スコアは 1.0 未満で低い信頼度レベルが示されます。

```
{
  "documents": [
    {
      "id": "1",
      "detectedLanguages": [
        {
          "name": "Spanish",
          "iso6391Name": "es",
          "score": 0.9375
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用する言語検出の概念とワークフローについて説明しました。 これまでに説明してデモを示した主要なポイントの参照先は以下のとおりです。

+ [言語検出](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)は、さまざまな言語、異形、方言、および一部の地方言語や文化言語でも使用できます。
+ 要求本文内の JSON ドキュメントには、ID とテキストが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/languages` エンドポイントに対して行われます。
+ ドキュメント ID ごとの言語識別子で構成される応答出力は、Excel や Power BI を含む JSON を受け取るすべてのアプリにストリーミングすることができます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [感情を分析する](text-analytics-how-to-sentiment-analysis.md)
