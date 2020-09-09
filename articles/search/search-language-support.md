---
title: 英語以外の検索クエリの多言語インデックス作成
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search では 56 の言語がサポートされており、Lucene の言語アナライザーや Microsoft の自然言語処理テクノロジが利用されています。
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: c7d574bf172a792c59e4b00ea9ad0366ad1f17ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922856"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Cognitive Search で複数の言語のインデックスを作成する方法

言語固有の文字列用に個別のフィールドを作成するなど、複数の言語のコンテンツを含むフィールドをインデックスに含めることができます。 インデックス作成およびクエリ中に最適な結果を得るには、適切な言語規則を提供する言語アナライザーを割り当てます。 

Azure Cognitive Search では、多彩な言語アナライザーが Lucene と Microsoft の両方から提供され、Analyzer プロパティを使用してこれらのアナライザーを個々のフィールドに割り当てることができます。 この記事で説明するように、ポータルで言語アナライザーを指定することもできます。

## <a name="add-analyzers-to-fields"></a>フィールドにアナライザーを追加する

言語アナライザーは、フィールドを作成するときに指定します。 既存のフィールド定義にアナライザーを追加するには、インデックスを上書き (して再読み込み) するか、または、元のものと同じだがアナライザーの割り当てがある新しいフィールドを作成する必要があります。 その後、使用されていないフィールドは都合の良いときに削除できます。

1. [Azure portal](https://portal.azure.com) にサインインし、ご利用の検索サービスを探します。
1. サービス ダッシュボードの上部にあるコマンド バーの **[インデックスの追加]** をクリックして新しいインデックスを開始するか、既存のインデックスを開いて、既存のインデックスに追加する新しいフィールドでアナライザーを設定します。
1. 名前を指定して、フィールド定義を開始します。
1. Edm.String データ型を選択します。 フルテキスト検索が可能なのは、文字列フィールドだけです。
1. Analyzer プロパティを有効にするには、**Searchable** 属性を設定します。 言語アナライザーを使用するには、フィールドがテキストベースである必要があります。
1. 使用可能なアナライザーのいずれかを選択します。 

![フィールド定義中に言語アナライザーを割り当てる](media/search-language-support/select-analyzer.png "フィールド定義中に言語アナライザーを割り当てる")

既定では、検索可能なすべてのフィールドで、言語に依存しない [標準の Lucene アナライザー](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) を使用します。 サポートされているアナライザーの詳細な一覧を参照するには、「[Azure Cognitive Search のインデックスに言語アナライザーを追加する](index-add-language-analyzers.md)」を参照してください。

ポータルでは、アナライザーはそのままで使用することが意図されています。 カスタマイズや、フィルターとトークナイザーの特定の構成が必要な場合は、コードで[カスタム アナライザーを作成する](index-add-custom-analyzers.md)必要があります。 ポータルでは、カスタム アナライザーの選択や構成はサポートされていません。

## <a name="query-language-specific-fields"></a>言語固有フィールドのクエリを実行する

フィールドに言語アナライザーが選択されると、そのフィールドのインデックス作成と検索要求それぞれに、選択した言語アナライザーが使用されます。 さまざまなアナライザーを使用して複数のフィールドに対してクエリを実行すると、そのクエリは各フィールドに割り当てられたアナライザーによって個別に処理されます。

クエリを発行するエージェントの言語がわかっている場合は、 **searchFields** クエリ パラメーターを使用して、検索要求を特定のフィールドに制限できます。 次のクエリは、ポーランド語の説明に対してのみ発行されます。

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

ポータルでは、インデックスをクエリし、[**検索エクスプローラー**](search-explorer.md)を使用して上記のようなクエリに貼り付けることができます。

## <a name="boost-language-specific-fields"></a>言語固有フィールドを強化する

クエリを発行するエージェントの言語が不明な場合もありますが、その場合は、すべてのフィールドに対して同時にクエリを発行できます。 必要に応じて、 [スコアリング プロファイル](index-add-scoring-profiles.md)を使用すると、特定言語の結果に対する優先度を定義できます。 次の例では、英語の説明で見つかった一致項目には、ポーランド語とフランス語の一致項目に比べて高いスコアが付けられます。

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>次のステップ

.NET 開発者の場合は、[Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) と [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) プロパティを使用して言語アナライザーを構成できます。