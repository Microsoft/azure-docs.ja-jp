---
title: 名刺 - Form Recognizer
titleSuffix: Azure Cognitive Services
description: Form Recognizer API を使用した名刺分析に関連する概念 (使用法と制限) について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 039f7343bcef64db9ad9eae558cd3e97f3678c59
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799283"
---
# <a name="business-card-concepts"></a>名刺の概念

Azure Form Recognizer では、事前構築済みモデルの 1 つを使用して、名刺からキーと値のペアを分析して抽出できます。 Business Card API は、強力な光学式文字認識 (OCR) 機能と名刺解釈モデルを組み合わせて、英語の名刺から重要な情報を抽出します。 抽出されるのは、個人の連絡先情報、会社名、役職などです。 Form Recognizer v2.1 プレビューで事前構築済み Business Card API が公開されています。 

## <a name="what-does-the-business-card-api-do"></a>Business Card API の機能

Business Card API は、名刺からキー フィールドを抽出し、それを構成済みの JSON 応答で返します。

![FOTT + JSON 出力からの Contoso の明細画像](./media/business-card-english.jpg)

### <a name="fields-extracted"></a>抽出されるフィールド: 
* 連絡先の名前 
* 名 
* 姓 
* 会社名 
* Departments 
* 役職 
* メール 
* Websites 
* アドレス 
* 電話番号 
  * 携帯電話 
  * Fax 
  * 勤務先の電話 
  * その他の電話 

また、Business Card API は、すべての認識されたテキストを名刺から返します。 この OCR 出力は JSON 応答に含まれています。  

### <a name="input-requirements"></a>入力要件 

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-business-card-operation"></a>名刺の分析操作

[名刺の分析](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)では、名刺の画像または PDF を入力として受け取り、目的の値やテキストを抽出します。 この呼び出しにより、`Operation-Location` という応答ヘッダー フィールドが返されます。 `Operation-Location` 値は、次の手順で使用される結果 ID を含む URL です。

|応答ヘッダー| 結果の URL |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-business-card-result-operation"></a>名刺の分析結果の取得操作

2 番目の手順では、[名刺の分析結果の取得](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeBusinessCardResult)操作を呼び出します。 この操作では、名刺の分析操作によって作成された結果 ID を入力として受け取ります。 これにより、次の設定可能な値を持つ **status** フィールドが含まれた JSON 応答が返されます。 **succeeded** の値が返されるまで、この操作を対話形式で呼び出します。 1 秒あたりの要求数 (RPS) を超えないようにするために、間隔は 3 - 5 秒あけてください。

|フィールド| 型 | 設定可能な値 |
|:-----|:----:|:----|
|status | string | notStarted: 分析操作は開始されていません。 |
| |  | running: 分析操作が進行中です。 |
| |  | failed: 分析操作は失敗しました。 |
| |  | succeeded: 分析操作は成功しました。 |

**status** フィールドの値が **succeeded** の場合、JSON 応答には名刺の解釈とテキスト認識の結果が含まれます。 名刺の解釈の結果は名前付きフィールド値のディクショナリとして編成され、各値には、抽出されたテキスト、正規化された値、境界ボックス、信頼および対応する単語要素が含まれます。 テキスト認識の結果は行と単語の階層として編成され、テキスト、境界ボックス、信頼情報が含まれます。

![サンプルの名刺の出力](./media/business-card-results.png)

### <a name="sample-json-output"></a>サンプル JSON 出力

成功した JSON 応答の例を次に示します。"readResults" ノードには、認識されたすべてのテキストが格納されます。 テキストは、まずページごとに整理され、そのうえで行ごと、さらに個々の単語ごとに整理されます。 "documentResults" ノードには、モデルによって検出された名刺固有の値が格納されます。 名、姓、会社名など、大切なキーと値のペアが存在する場所です。

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T17:41:19Z",
    "lastUpdatedDateTime": "2020-08-20T17:41:24Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel",
                "lines": 
                          {
                        "text": "Dr. Avery Smith",
                        "boundingBox": [
                            419.3,
                            1154.6,
                            1589.6,
                            877.9,
                            1618.9,
                            1001.7,
                            448.6,
                            1278.4
                        ],
                        "words": [
                            {
                                "text": "Dr.",
                                "boundingBox": [
                                    419,
                            ]
    
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                    },
                                    "LastName": {
                                        "type": "string",
                                        "valueString": "Smith",
                                        "text": "Smith",
                                        "boundingBox": [
                                            1186,
                                            976,
                                            1585,
                                            879,
                                            1618,
                                            998,
                                            1218,
                                            1096
                                        ],
                                        "page": 1
                                    }
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.97
                            }
                        ]
                    },
                    "JobTitles": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Senior Researcher",
                                "text": "Senior Researcher",
                                "boundingBox": [
                                    451.8,
                                    1301.9,
                                    1313.5,
                                    1099.9,
                                    1333.8,
                                    1186.7,
                                    472.2,
                                    1388.7
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Departments": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Cloud & Al Department",
                                "text": "Cloud & Al Department",
                                "boundingBox": [
                                    480.1,
                                    1403.3,
                                    1590.5,
                                    1129.6,
                                    1612.6,
                                    1219.6,
                                    502.3,
                                    1493.3
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    },
                    "MobilePhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 7911 123456",
                                "boundingBox": [
                                    2434.9,
                                    1033.3,
                                    3072,
                                    836,
                                    3096.2,
                                    914.3,
                                    2459.1,
                                    1111.6
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "OtherPhones": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 9876 5432",
                                "boundingBox": [
                                    2473.2,
                                    1115.4,
                                    3139.2,
                                    907.7,
                                    3163.2,
                                    984.7,
                                    2497.2,
                                    1192.4
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Faxes": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "phoneNumber",
                                "text": "+44 (0) 20 6789 2345",
                                "boundingBox": [
                                    2525,
                                    1185.4,
                                    3192.4,
                                    977.9,
                                    3217.9,
                                    1060,
                                    2550.5,
                                    1267.5
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Addresses": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "2 Kingdom Street Paddington, London, W2 6BD",
                                "text": "2 Kingdom Street Paddington, London, W2 6BD",
                                "boundingBox": [
                                    1230,
                                    2138,
                                    2535.2,
                                    1678.6,
                                    2614.2,
                                    1903.1,
                                    1309,
                                    2362.5
                                ],
                                "page": 1,
                                "confidence": 0.977
                            }
                        ]
                    },
                    "CompanyNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "Contoso",
                                "text": "Contoso",
                                "boundingBox": [
                                    1152,
                                    1916,
                                    2293,
                                    1552,
                                    2358,
                                    1733,
                                    1219,
                                    2105
                                ],
                                "page": 1,
                                "confidence": 0.97
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

[名刺データ抽出](./QuickStarts/python-business-cards.md)のクイックスタートに従って、Python と REST API を使用した名刺データ抽出を実装します。

## <a name="customer-scenarios"></a>顧客シナリオ  

Business Card API で抽出されたデータは、さまざまな作業を行うために使用できます。 このように連絡先情報を自動的に抽出すると、クライアントに対応するロールの時間を節約できます。 次に、顧客が Business Card API を使用して行った作業の例をいくつかご紹介します。

* 名刺から連絡先情報を抽出し、連絡先の電話番号をすばやく作成します。 
* CRM と統合して、名刺画像を使用して自動的に連絡先を作成します。 
* 潜在顧客を追跡します。  
* 既存の名刺画像から、連絡先情報を一括して抽出します。 

また、Business Card API では、[AIBuilder の名刺処理機能](https://docs.microsoft.com/ai-builder/prebuilt-business-card)も強化されています。

## <a name="next-steps"></a>次のステップ

- クイックスタートに従って、[Python での Business Card API のクイックスタート](./quickstarts/python-business-cards.md)を開始します。
- [Form Recognizer REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) について学習します。
- [Form Recognizer](overview.md) の詳細を確認します。


