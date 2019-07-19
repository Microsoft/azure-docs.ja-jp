---
title: Azure Cognitive Services の Text Analytics を使用した感情分析 | Microsoft Docs
description: Text Analytics REST API を使用したセンチメントの検出方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 02/26/2019
ms.author: aahi
ms.openlocfilehash: e17b68dfd63952d0c8c81415b090b047c5808e2e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797805"
---
# <a name="example-how-to-detect-sentiment-with-text-analytics"></a>例:Text Analytics でセンチメントを検出する方法

[Sentiment Analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) はテキスト入力を評価し、ドキュメントごとに 0 (負) から 1 (正) のセンチメント スコアを返します。

この機能は、ソーシャル メディア、顧客レビューおよびディスカッション フォーラムで肯定的および否定的センチメントを検出するために役立ちます。 コンテンツはユーザーが提供します。モデルとトレーニング データは、サービスによって提供されます。

現時点では、感情分析は、英語、ドイツ語、スペイン語、およびフランス語をサポートします。 その他の言語はプレビュー段階です。 詳細については、[サポートされている言語](../text-analytics-supported-languages.md)に関するページを参照してください。

> [!TIP]
> Text Analytics には感情分析用の Linux ベースの Docker コンテナー イメージも用意されているため、データの近くに [Text Analytics コンテナーをインストールして実行](text-analytics-how-to-install-containers.md)することができます。

## <a name="concepts"></a>概念

Text Analytics では、機械学習分類アルゴリズムを使用して、0 ～ 1 のセンチメント スコアを生成します。 1 に近いスコアは肯定的センチメント、0 に近いスコアは否定的センチメントを示します。 モデルは、センチメント関連付けの広範なテキスト本文を使って事前トレーニングされています。 現時点では、ユーザー独自のトレーニング データを提供することはできません。 モデルでは、テキスト分析中に、テキスト処理、品詞の分析、単語の配置、および単語の関連付けを含む手法を組み合わせて使用します。 アルゴリズムの詳細については、[Text Analytics の概要](https://blogs.technet.microsoft.com/machinelearning/2015/04/08/introducing-text-analytics-in-the-azure-ml-marketplace/)に関する記事を参照してください。

感情分析は、テキスト内の特定のエンティティのセンチメントを抽出するのではなく、ドキュメント全体に対し実行されます。 実際には、ドキュメントに大きなテキスト ブロックではなく、1 つまたは 2 つの文が含まれている場合に、スコア付けの正確性が向上する傾向があります。 客観性評価段階では、モデルは、ドキュメント全体として客観的か、またはセンチメントが含まれているかどうかを決定します。 大部分が客観的なドキュメントはセンチメント検出段階には進まず、.50 スコアが付けられ、それ以上は処理されません。 パイプラインを先に進むドキュメントの次の段階では、ドキュメントで検出されたセンチメントの程度にしたがって、 .50 より上または下のスコアが生成されます。

## <a name="preparation"></a>準備

感情分析では、作業するテキストが小さなチャンクである場合に、高い品質の結果を生成します。 これは、テキスト ブロックが大きい方がパフォーマンスが向上するキー フレーズ抽出とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

JSON ドキュメントは、次の形式である必要があります: ID、テキスト、言語

ドキュメントのサイズは、ドキュメントあたり 5,120 文字未満である必要があり、コレクションあたり最大 1,000 の項目 (ID) を含めることができます。 コレクションは、要求の本文で送信されます。 感情分析のために送信するコンテンツの例を次に示します。

```
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },                
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>手順 1:要求を構造化する

要求定義の詳細については、[Text Analytics API を呼び出す方法](text-analytics-how-to-call-api.md)に関するページを参照してください。 確認に便利なように、以下に再度、要点を示します。

+ **POST** 要求を作成します。 この要求については次の API ドキュメントを確認してください。[感情分析 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)

+ Azure 上の Text Analytics リソースまたはインスタンス化された [Text Analytics コンテナー](text-analytics-how-to-install-containers.md)を使用して、感情分析用の HTTP エンドポイントを設定します。 そこには、`/sentiment` リソースが含まれている必要があります: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`

+ Text Analytics 操作用のアクセス キーが含まれるように要求ヘッダーを設定します。 詳細については、[エンドポイントを見つけてアクセス キーにアクセスする方法](text-analytics-how-to-access-key.md)に関するページを参照してください。

+ 要求本文で、この分析のために準備した JSON ドキュメントのコレクションを提供します。

> [!Tip]
> [Postman](text-analytics-how-to-call-api.md) を使用するか、[ドキュメント](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)に記載されている **API テスト コンソール**を開き、要求を構造化して POST でサービスに投稿します。

## <a name="step-2-post-the-request"></a>手順 2:要求を投稿する

要求が受信されると分析が実行されます。 分単位および秒単位で送信できる要求のサイズと数については、概要の「[データ制限](../overview.md#data-limits)」セクションを参照してください。

サービスはステートレスであることを思い出してください。 ユーザーのアカウントに保存されるデータはありません。 結果はすぐに、応答で返されます。


## <a name="step-3-view-results"></a>手順 3:結果の表示

センチメント アナライザーは、0 ～ 1 の範囲のスコアを割り当てて、テキストを主に正または負に分類します。 0\.5 に近い値は、中立または不確定です。 0\.5 のスコアは、中立性を示します。 文字列の感情分析をすることができない場合、またはセンチメントがない場合、スコアは必ず正確に 0.5 になります。 たとえば、英語の言語コードでスペイン語の文字列を渡した場合、スコアは 0.5 になります。

出力はすぐに返されます。 結果は、JSON を受け付けるアプリケーションにストリームするか、ローカル システム上のファイルに出力を保存してから、そのファイルを、データの並べ替え、検索、および操作が可能なアプリケーションにインポートすることができます。

次の例では、この記事のドキュメント コレクションへの応答を示します。

```json
{
    "documents": [
        {
            "score": 0.9999237060546875,
            "id": "1"
        },
        {
            "score": 0.0000540316104888916,
            "id": "2"
        },
        {
            "score": 0.99990355968475342,
            "id": "3"
        },
        {
            "score": 0.980544924736023,
            "id": "4"
        },
        {
            "score": 0.99996328353881836,
            "id": "5"
        }
    ],
    "errors": []
}
```

## <a name="sentiment-analysis-v3-public-preview"></a>感情分析 V3 のパブリック プレビュー

現在、[感情分析の次期バージョン](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-preview/operations/56f30ceeeda5650db055a3c9)はパブリック プレビューで利用できます。API のテキスト分類とスコア付けの精度と詳細が向上しました。 

> [!NOTE]
> * 感情分析 v3 の要求の形式と[データ制限](../overview.md#data-limits)は以前のバージョンと同じです。
> * 現時点では、感情分析 V3 には以下の制限があります。 
>    * 現在、英語のみがサポートされています。  
>    * 以下のリージョンで利用できます: `Central US`、`Central Canada`、`East Asia` 

|機能 |説明  |
|---------|---------|
|精度の向上     | テキスト ドキュメントでの肯定的、中立、否定的、および混合センチメントの検出が以前のバージョンより大幅に向上しています。           |
|ドキュメントおよび文章レベルでのセンチメント スコア     | ドキュメントおよびその個々の文章の両方のセンチメントを検出します。 ドキュメントに複数の文が含まれる場合、各文にセンチメント スコアが割り当てられています。         |
|センチメント カテゴリ     | API で、センチメント スコアに加え、センチメント カテゴリ (`positive`、`negative`、`neutral`、および `mixed`) がテキストに対して返されるようになりました。        |
| 改善された出力 | 感情分析で、テキスト ドキュメント全体とその各文章の両方の情報が返されるようになりました。 |

### <a name="sentiment-labeling"></a>センチメント ラベル付け

感情分析 V3 では、文章およびドキュメント レベルでスコアとラベル (`positive`、`negative`、および`neutral`) が返されます。 ドキュメント レベルでは、`mixed` センチメント ラベル (スコアではなく) も返されます。 ドキュメントのセンチメントは、その文章のスコアを集計することによって決定されます。

| 文章のセンチメント                                                        | 返されるドキュメントのラベル |
|---------------------------------------------------------------------------|----------------|
| 少なくとも 1 つが肯定文で、残りの文は中立。 | `positive`     |
| 少なくとも 1 つが否定文で、残りの文は中立。  | `negative`     |
| 少なくとも 1 つが否定文で少なくとも 1 つが肯定文。         | `mixed`        |
| すべての文が中立。                                                 | `neutral`      |

### <a name="sentiment-analysis-v3-example-request"></a>感情分析 V3 の要求の例

次の JSON は、新しいバージョンの感情分析に行われた要求の例です。 要求の形式が、以前のバージョンと同じであることに注意してください。

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Hello world. This is some input text that I love."
    },
    {
      "language": "en",
      "id": "2",
      "text": "It's incredibly sunny outside! I'm so happy."
    }
  ]
}
```

### <a name="sentiment-analysis-v3-example-response"></a>感情分析 V3 の応答の例

要求の形式は以前のバージョンと同じですが、応答の形式は変わりました。 次の JSON は、新しいバージョンの API からの応答の例です。

```json
{
    "documents": [
        {
            "id": "1",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.98570585250854492,
                "neutral": 0.0001625834556762,
                "negative": 0.0141316400840878
            },
            "sentences": [
                {
                    "sentiment": "neutral",
                    "sentenceScores": {
                        "positive": 0.0785155147314072,
                        "neutral": 0.89702343940734863,
                        "negative": 0.0244610067456961
                    },
                    "offset": 0,
                    "length": 12
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.98570585250854492,
                        "neutral": 0.0001625834556762,
                        "negative": 0.0141316400840878
                    },
                    "offset": 13,
                    "length": 36
                }
            ]
        },
        {
            "id": "2",
            "sentiment": "positive",
            "documentScores": {
                "positive": 0.89198976755142212,
                "neutral": 0.103382371366024,
                "negative": 0.0046278294175863
            },
            "sentences": [
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.78401315212249756,
                        "neutral": 0.2067587077617645,
                        "negative": 0.0092281140387058
                    },
                    "offset": 0,
                    "length": 30
                },
                {
                    "sentiment": "positive",
                    "sentenceScores": {
                        "positive": 0.99996638298034668,
                        "neutral": 0.0000060341349126,
                        "negative": 0.0000275444017461
                    },
                    "offset": 31,
                    "length": 13
                }
            ]
        }
    ],
    "errors": []
}
```

### <a name="example-c-code"></a>C# のコード例

このバージョンの感情分析を呼び出す C# アプリケーションの例については、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/dotnet/Language/SentimentV3.cs) をご覧ください。

## <a name="summary"></a>まとめ

この記事では、Cognitive Services の Text Analytics を使用する感情分析の概念とワークフローについて説明しました。 要旨:

+ 選択した言語に対して、[Sentiment analysis API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) を使用できます。
+ 要求本文内の JSON ドキュメントには、ID、テキスト、および言語のコードが含まれます。
+ POST 要求は、ユーザーのサブスクリプションで有効な、個人用に設定された[アクセス キーとエンドポイント](text-analytics-how-to-access-key.md)を使用して `/sentiment` エンドポイントに対して行われます。
+ ドキュメント ID ごとのセンチメント スコアで構成される応答出力は、Excel や Power BI を含む JSON を受け取るすべてのアプリにストリーミングすることができます。

## <a name="see-also"></a>関連項目 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)</br>
 [Text Analytics 製品ページ](//go.microsoft.com/fwlink/?LinkID=759712) 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [キー フレーズを抽出する](text-analytics-how-to-keyword-extraction.md)
