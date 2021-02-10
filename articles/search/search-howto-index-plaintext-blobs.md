---
title: プレーンテキスト BLOB を検索する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でのフルテキスト検索のために、Azure の BLOB からプレーンテキストを抽出する検索インデクサーを構成します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 422346430e32ccb8745d5a5d829c5d61089a99c6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430430"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Azure Cognitive Search でプレーンテキスト BLOB のインデックスを作成する方法

フルテキスト検索のために [BLOB インデクサー](search-howto-indexing-azure-blob-storage.md)を使用して検索可能なテキストを抽出するときには、インデックス作成でより良い結果を得るため、さまざまな解析モードを呼び出ことができます。 既定では、インデクサーによって BLOB コンテンツが 1 つのテキスト チャンクとして解析されます。 ただし、すべての BLOB に同じエンコードのプレーンテキストが含まれている場合、`text` 解析モードを使用してインデックス作成のパフォーマンスを大幅に改善できます。

次の場合は、`text` 解析モードを使用する必要があります。

+ ファイルの種類が .txt の場合
+ どの種類のファイルでも、コンテンツそのものがテキストの場合 (プログラムのソース コード、HTML、XML など)。 マークアップ言語のファイルの場合、すべての構文文字が静的テキストとして送信されます。

インデクサーは JSON にシリアル化されることを思い出してください。 テキスト ファイル全体のコンテンツが、1 つの大きなフィールド内で `"content": "<file-contents>"` としてインデックスが作成されます。 改行とリターンの命令は `\r\n\` として表されます。

より詳細な結果が必要な場合は、次の解決策を検討してください。

+ [`delimitedText`](search-howto-index-csv-blobs.md) 解析モード (ソースが CSV の場合)
+ [`jsonArray` または `jsonLines`](search-howto-index-json-blobs.md) (ソースが JSON の場合)

コンテンツを複数の部分に分割するための 3 つ目のオプションには、[AI エンリッチメント](cognitive-search-concept-intro.md)形式の高度な機能が必要です。 これにより、ファイルのチャンクを識別して、別の検索フィールドに割り当てる分析が追加されます。 [組み込みのスキル](cognitive-search-predefined-skills.md)を通じて完全または部分的な解決策を見つけることができますが、より可能性の高い解決策は、カスタム学習モデルで明確に示され、[カスタム スキル](cognitive-search-custom-skill-interface.md)でラップされたコンテンツを理解する学習モデルです。

## <a name="set-up-plain-text-indexing"></a>プレーンテキストのインデックス作成を設定する

プレーンテキスト BLOB のインデックスを作成するには、[インデクサーの作成](/rest/api/searchservice/create-indexer)要求で `parsingMode` 構成プロパティを `text` に設定して、インデクサーの定義を作成または更新します。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

既定では、`UTF-8` エンコードが想定されます。 別のエンコードを指定するには、`encoding` 構成プロパティを使用します。 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>要求の例

解析モードは、インデクサーの定義で指定されています。

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>次のステップ

+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [BLOB インデクサーを構成する方法](search-howto-indexing-azure-blob-storage.md)
+ [BLOB のインデックス作成の概要](search-blob-storage-integration.md)