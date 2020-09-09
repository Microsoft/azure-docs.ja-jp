---
title: Text Analytics API を呼び出す
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Text Analytics REST API と Postman を呼び出す方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: c6fbec35920c8afd08ab60fc380c9f816ae599b0
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561021"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Text Analytics REST API を呼び出す方法

**Text Analytics API** の呼び出しは HTTP POST/GET 呼び出しであり、任意の言語で作成できます。 この記事では、REST と [Postman](https://www.postman.com/downloads/) を使用して主要な概念を示します。

各要求には、アクセス キーと HTTP エンドポイントを含める必要があります。 エンドポイントは、サインアップ時に選択したリージョン、サービス URL、および要求: `sentiment`、`keyphrases`、`languages`、`entities` で使用したリソースを指定します。 

Text Analytics はステートレスであるため、管理するデータ資産がないことを忘れないでください。 テキストがアップロードされ、受信されると分析され、結果は呼び出し元のアプリケーションにすぐに返されます。

[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

## <a name="prerequisites"></a>前提条件

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="json-schema"></a>

## <a name="json-schema-definition"></a>JSON スキーマ定義

未加工の非構造化テキストでは、入力は JSON でなければなりません。 XML はサポートされていません。 スキーマは単純であり、次の一覧で説明する要素で構成されます。 

センチメント、キー フレーズ、言語検出、およびエンティティ識別のすべてのテキスト分析操作に対して、現在では同じドキュメントを送信できます。 (スキーマは今後、各分析に応じて変更される可能性があります。)

| 要素 | 有効な値 | 必須 | 使用法 |
|---------|--------------|-----------|-------|
|`id` |データ型は文字列ですが、実際にはドキュメント ID は整数になる傾向があります。 | 必須 | 構造体に提供する ID が出力に使用されます。 言語コード、キー フレーズ、およびセンチメント スコアが、要求の各 ID に対して生成されます。|
|`text` | 最大 5,120 文字の、構造化されていない生のテキスト。 | 必須 | 言語の検出では、任意の言語でテキストを表現できます。 センチメント分析、キー フレーズ抽出、およびエンティティ識別の場合、テキストは[サポートされている言語](../text-analytics-supported-languages.md)である必要があります。 |
|`language` | [サポートされている言語](../text-analytics-supported-languages.md)の 2 文字の [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) コード | 場合により異なる | センチメント分析、キー フレーズ抽出、およびエンティティ リンク設定には必須。言語検出には省略可能。 除外してもエラーにはなりませんが、それなしでは分析力は低下します。 言語コードは提供した `text` に対応する必要があります。 |

制限の詳細については、[テキスト分析の概要 > データ制限](../overview.md#data-limits)を参照してください。 

## <a name="set-up-a-request-in-postman"></a>Postman での要求の設定

このサービスは最大 1 MB のサイズの要求を受け入れます。 Postman (または別の Web API テスト ツール) を使用する場合、使用するリソースを含むようにエンドポイントを設定し、要求ヘッダーにアクセス キーを提供します。 各操作では、エンドポイントに適切なリソースを付加することが必要です。 

1. Postman の場合:

   + 要求の種類として **[投稿]** を選択します。
   + ポータル ページからコピーしたエンドポイントに貼り付けます。
   + リソースを追加します。

   リソースのエンドポイントは、次のようになります (地域により異なる場合があります)。

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/entities`

2. 次の 3 つの要求ヘッダーを設定します。

   + `Ocp-Apim-Subscription-Key`: Azure Portal から取得したアクセス キー。
   + `Content-Type`: application/json。
   + `Accept`: application/json。

   **/keyPhrases** リソースと想定すると、要求は次のスクリーン ショットと似たものになります。

   ![エンドポイントとヘッダーがある要求のスクリーン ショット](../media/postman-request-keyphrase-1.png)

4. **[本文]** をクリックして、フォーマットには **[未加工]** を選択します。

   ![本文の設定がある要求のスクリーン ショット](../media/postman-request-body-raw.png)

5. 目的とする分析に有効な形式で、いくつかの JSON ドキュメントに貼り付けます。 特定の分析の詳細については、以下のトピックを参照してください。

  + [言語検出](text-analytics-how-to-language-detection.md)  
  + [キー フレーズ抽出](text-analytics-how-to-keyword-extraction.md)  
  + [感情分析](text-analytics-how-to-sentiment-analysis.md)  
  + [エンティティの認識](text-analytics-how-to-entity-linking.md)  


6. **[保存]** をクリックして、要求を送信します。 分単位および秒単位で送信できる要求の数については、概要の「[データ制限](../overview.md#data-limits)」セクションを参照してください。

   Postman では、応答は、次のウィンドウに単一の JSON ドキュメントとして、要求に指定された各ドキュメント ID の項目とともに表示されます。

## <a name="see-also"></a>参照 

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [言語を検出する](text-analytics-how-to-language-detection.md)
