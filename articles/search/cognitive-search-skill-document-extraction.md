---
title: ドキュメント抽出の認知技術 (プレビュー)
titleSuffix: Azure Cognitive Search
description: エンリッチメント パイプライン内のファイルからコンテンツを抽出します。 このスキルは現在、パブリック プレビューの段階です。
manager: nitinme
author: careyjmac
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: chalton
ms.openlocfilehash: 0f67caad03c4ebd1cf8f3721f377d8362219016a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837733"
---
# <a name="document-extraction-cognitive-skill"></a>ドキュメント抽出の認知技術

> [!IMPORTANT] 
> このスキルは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 現時点では、ポータルと .NET SDK によるサポートはありません。

**ドキュメント抽出**スキルでは、エンリッチメント パイプライン内のファイルからコンテンツが抽出されます。 これにより、通常であれば他のスキルによって生成される可能性のあるファイルでのスキルセット実行の前に発生する、ドキュメント抽出ステップを利用できます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、およびインデックス作成のドキュメント解析ステージの一部としての画像抽出に対しては、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格については、[価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。
## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.DocumentExtractionSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| 入力            | 使用できる値 | 説明 |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` <br/> `text` <br/> `json`  | テキストだけ、または JSON だけではないファイルからドキュメントを抽出する場合は、`default` に設定します。 プレーン テキスト ファイルでパフォーマンスを向上させるには、`text` に設定します。 JSON ファイルから構造化コンテンツを抽出するには、`json` に設定します。 `parsingMode` が明示的に定義されていない場合は、`default` に設定されます。 |
| `dataToExtract` | `contentAndMetadata` <br/> `allMetadata` | 各ファイルからすべてのメタデータとテキスト コンテンツを抽出するには、`contentAndMetadata` に設定します。 [コンテンツの種類に固有のメタデータ](search-howto-indexing-azure-blob-storage.md#ContentSpecificMetadata)のみを抽出するには、`allMetadata` に設定します (たとえば、.png ファイルだけに固有のメタデータ)。 `dataToExtract` が明示的に定義されていない場合は、`contentAndMetadata` に設定されます。 |
| `configuration` | 以下を参照してください。 | ドキュメント抽出の実行方法を調整する省略可能なパラメーターのディクショナリ。 サポートされている構成プロパティの説明については、以下の表を参照してください。 |

| 構成パラメーター   | 使用できる値 | 説明 |
|-------------------------|----------------|-------------|
| `imageAction`           | `none`<br/> `generateNormalizedImages`<br/> `generateNormalizedImagePerPage` | 埋め込み画像またはデータ セット内の画像ファイルを無視するには、`none` に設定します。 これは既定値です。 <br/>[認知技術を使用した画像分析](cognitive-search-concept-image-scenarios.md)の場合、ドキュメント解析の一部として正規化された画像の配列をスキルで作成するには、`generateNormalizedImages` に設定します。 このアクションを実行するには、`parsingMode` を `default` に設定し、`dataToExtract` を `contentAndMetadata` に設定する必要があります。 正規化された画像は、一様な画像出力をもたらす追加処理を参照します。画像出力は、ビジュアル検索の結果に画像を含めるときに一貫性のあるレンダリングを促進するために、サイズと回転を調整されます (たとえば、[JFK のデモ](https://github.com/Microsoft/AzureSearch_JFK_Files)で見られるような、グラフ コントロール内の同一サイズの写真)。 このオプションを使用すると、各画像に対してこの情報が生成されます。  <br/>`generateNormalizedImagePerPage` に設定した場合は、PDF ファイルの処理が異なり、埋め込み画像が抽出されるのではなく、各ページが画像としてレンダリングされ、それに応じて正規化されます。  PDF 以外のファイルの種類は、`generateNormalizedImages` が設定されている場合と同じように処理されます。
| `normalizedImageMaxWidth` | 50 - 10000 の範囲の整数 | 生成された正規化画像の最大幅 (ピクセル単位)。 既定値は 2000 です。 | 
| `normalizedImageMaxHeight` | 50 - 10000 の範囲の整数 | 生成された正規化画像の最大の高さ (ピクセル単位)。 既定値は 2000 です。 |

> [!NOTE]
> 正規化された画像の最大幅と最大高さの既定値 (2000 ピクセル) は、 [OCR スキル](cognitive-search-skill-ocr.md)と[画像分析スキル](cognitive-search-skill-image-analysis.md)でサポートされる最大サイズに基づいています。 [OCR のスキル](cognitive-search-skill-ocr.md)では、英語以外の言語の場合は最大の幅と高さ 4200、英語の場合は 10000 がサポートされます。  上限を引き上げると、スキルセットの定義とドキュメントの言語によっては、大きな画像で処理が失敗する可能性があります。 
## <a name="skill-inputs"></a>スキルの入力

| 入力名     | 説明 |
|--------------------|-------------|
| file_data | コンテンツを抽出する必要があるファイル。 |

"file_data" の入力では、次のように定義されたオブジェクトを指定する必要があります。

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

このファイル参照オブジェクトは、次の 3 つの方法のいずれかで生成できます。

 - インデクサーの定義で `allowSkillsetToReadFileData` パラメーターを "true" に設定します。  このようにすると、BLOB データ ソースからダウンロードされた元のファイル データを表すオブジェクトであるパス `/document/file_data` が作成されます。 このパラメーターは、BLOB ストレージのデータにのみ適用されます。

 - インデクサーの定義で `imageAction` パラメーターを `none` 以外の値に設定します。  このようにすると、画像の配列が作成されます。この配列は、個別に渡した場合にこのスキルに対する入力となるために必要な規則に従います (つまり `/document/normalized_images/*`)。

 - カスタム スキルで、上記のように EXACTLY と定義された JSON オブジェクトが返されるようにします。  `$type` パラメーターは厳密に `file` に設定する必要があり、`data` パラメーターはファイル コンテンツの Base 64 でエンコードされたバイト配列データである必要があります。

## <a name="skill-outputs"></a>スキルの出力

| 出力名    | 説明 |
|--------------|-------------|
| content | ドキュメントのテキスト コンテンツ。 |
| normalized_images | `imageAction` を `none` 以外の値に設定すると、新しい *normalized_images* フィールドに画像の配列が格納されます。 各画像の出力形式の詳細については、[画像抽出に関するドキュメント](cognitive-search-concept-image-scenarios.md)を参照してください。 |

##  <a name="sample-definition"></a>定義例

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "content"
      },
      {
        "name": "normalized_images",
        "targetName": "normalized_images"
      }
    ]
  }
```

##  <a name="sample-input"></a>サンプル入力

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```


##  <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## <a name="see-also"></a>参照

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [認知検索のシナリオで画像からの情報を処理し、抽出する方法](cognitive-search-concept-image-scenarios.md)