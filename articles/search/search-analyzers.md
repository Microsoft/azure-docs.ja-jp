---
title: 言語処理とテキスト処理のためのアナライザー - Azure Search
description: インデックスの検索可能なテキスト フィールドにアナライザーを割り当てて、既定の標準 Lucene をカスタムの定義済みの代替または言語固有の代替と置換します。
services: search
ms.service: search
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: 868658062a6407dce901b455cc92f95008df798c
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53631944"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Azure Search でのテキスト処理のためのアナライザー

*アナライザー*は、クエリ文字列内のテキストとインデックス付きドキュメントを処理する[フル テキスト検索](search-lucene-query-architecture.md)のコンポーネントです。 分析中は、次の変換が一般的です。

+ 重要ではない単語 (ストップワード) と感嘆符は削除されます。
+ フレーズとハイフンでつながれた単語は、構成要素に分割されます。
+ 大文字の単語は小文字に変換されます。
+ 単語は、時制に関係なく一致が見つかるように、原形に戻されます。

言語アナライザーは、テキスト入力を情報の保存と取得に効率的な原型または原形に変換します。 変換は、インデックスが構築されるときにインデックスの作成中に実行され、インデックスの読み取り時の検索中にも実行されます。 両方の操作に同じテキスト アナライザーを使用すると、期待する検索結果が得られる可能性が高くなります。

Azure Search で既定で使用されるのは、[Lucene の標準アナライザー](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)です。 フィールド単位で既定値をオーバーライドすることができます。 この記事では、さまざまな選択肢と、カスタム分析のベスト プラクティスについて説明しています。 また、主なシナリオの構成例も示します。

## <a name="supported-analyzers"></a>サポートされているアナライザー

次の一覧では、Azure Search でサポートされているアナライザーについて説明しています。

| Category | 説明 |
|----------|-------------|
| [標準 Lucene のアナライザー](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | [既定]。 指定や構成は必要ありません。 この汎用アナライザーは、ほとんどの言語とシナリオで適切に実行されます。|
| 定義済みアナライザー | そのまま使用するように完成した製品として提供されます。カスタマイズは制限されています。 <br/>特殊と言語という 2 種類があります。 "定義済み" とは、カスタマイズなしで、名前で参照するためです。 <br/><br/>[特殊 (言語を選ばない) アナライザー](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)は、特殊な処理または最小限の処理が必要なテキスト入力に使用します。 非言語の定義済みアナライザーには、**Asciifolding**、**Keyword**、**Pattern**、**Simple**、**Stop**、**Whitespace** などがあります。<br/><br/>[言語アナライザー](https://docs.microsoft.com/rest/api/searchservice/language-support)は、各言語に合わせて高度の言語サポートが必要な場合に使用されます。 Azure Search は、35 個の Lucene 言語アナライザーと 50 個の Microsoft 自然言語処理アナライザーをサポートしています。 |
|[カスタム アナライザー](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 1 つのトークナイザー (必須) と省略可能なフィルター (文字またはトークン) から構成される既存の要素を組み合わせたユーザー定義の構成。|

**Pattern** や **Stop** などの定義済みアナライザーをカスタマイズして、「[Predefined Analyzer Reference](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search#AnalyzerTable)」(定義済みアナライザー リファレンス) で説明されている代替オプションを使用できます。 設定できるオプションがあるのは、定義済みアナライザーのごく一部です。 他のカスタマイズと同様に、新しい構成に *myPatternAnalyzer* などの名前を付けて、Lucene パターン アナライザーの名前を区別できるようにします。

## <a name="how-to-specify-analyzers"></a>アナライザーを指定する方法

1. (カスタム アナライザーの場合のみ) インデックス定義に **アナライザー** セクションを作成します。 詳細については、「[Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index)」(インデックスの作成) と[「Custom Analyzers」(カスタム アナライザー) の「Create」(作成)](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search#create-a-custom-analyzer) も参照してください。

2. インデックスの[フィールド定義](https://docs.microsoft.com/rest/api/searchservice/create-index)で、ターゲット アナライザー名に**アナライザー** プロパティを設定します。(たとえば、`"analyzer" = "keyword"` 有効な値には、定義済みアナライザー名、言語アナライザーまたは、これもインデックス スキーマで定義されるカスタム アナライザーが含まれます。

3. また、1 つの **アナライザー** プロパティの代わりに、**indexAnalyzer** および **searchAnalyzer** フィールド パラメーターを使用して、インデックスとクエリに異なるアナライザーを設定することができます。 

3. フィールドの定義に、アナライザーを追加すると、インデックスでの書き込み操作が発生します。 既存のインデックスに **アナライザー**を追加する場合は、次の手順に注意してください。
 
 | シナリオ | 影響 | 手順 |
 |----------|--------|-------|
 | 新しいフィールドの追加 | 最小限 | フィールドがまだスキーマに存在しない場合、インデックスにもフィールドはまだ物理的に存在していないため、フィールドのリビジョンは実行されていません。 このタスクには、[Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) と [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) を使用してください。|
 | アナライザーを既存のインデックス付きフィールドに追加します。 | 再構築 | そのフィールドの転置インデックスは最初から再作成する必要があり、それらのフィールドの内容は再度インデックス作成する必要があります。 <br/> <br/>現在開発中のインデックスの場合は、新しいフィールド定義を取得するために、インデックスを[削除](https://docs.microsoft.com/rest/api/searchservice/delete-index)して[作成](https://docs.microsoft.com/rest/api/searchservice/create-index)します。 <br/> <br/>運用中のインデックスの場合は、改訂された定義を提供してその使用を開始するために、新しいフィールドを作成する必要があります。 新しいフィールドを組み込むには、[Update Index](https://docs.microsoft.com/rest/api/searchservice/update-index) と [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) を使用してください。 後で、計画的なインデックス サービスの一環として、インデックスをクリーンアップし、不要になったフィールドを削除することができます。 |

## <a name="tips-and-best-practices"></a>ヒントとベスト プラクティス

このセクションでは、アナライザーの使用方法についてのアドバイスを提供します。

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>特定の要件がない場合は読み取り/書き込みに 1 つのアナライザー

Azure Search では、追加の `indexAnalyzer` および `searchAnalyzer` フィールド パラメーターを使用して、インデックス作成と検索に別々のアナライザーを指定することができます。 指定しなかった場合、`analyzer` プロパティで設定されたアナライザーが、インデックス作成と検索の両方に使用されます。 `analyzer` が指定されなかった場合は、標準の Lucene アナライザーが使用されます。

一般的な規則は、特定の要件で別に指示がない場合は、インデックスとクエリの両方に同じアナライザーを使用することです。 テストは徹底的に行ってください。 検索時とインデックス作成時にテキスト処理が異なると、検索アナライザーの構成とインデックス作成アナライザーの構成が揃っていない場合、クエリ用語とインデックス用語に不一致が生じるおそれがあります。

### <a name="test-during-active-development"></a>アクティブな開発中のテスト

標準アナライザーをオーバーライドするには、インデックスの再構築が必要です。 可能であれば、アクティブな開発中に使用するアナライザーを決めてから、インデックスを運用環境に展開します。

### <a name="inspect-tokenized-terms"></a>トークン化された用語の検査

検索結果が期待した内容ではない場合、よくあるシナリオとして、クエリの用語入力とインデックス内のトークン化された用語間にトークンの違いがあることが考えられます。 トークンが同じではない場合、一致処理は具体化に失敗します。 トークナイザーの出力を検査する場合、調査ツールとして [Analyze API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) を使用することをお勧めします。 応答は、特定のアナライザーによって生成されるトークンで構成されます。

### <a name="compare-english-analyzers"></a>英語のアナライザーの比較

[Search Analyzer Demo](https://alice.unearth.ai/) はサードパーティ製のデモ アプリです。標準 Lucene アナライザー、Lucene の英語アナライザー、Microsoft の英語自然言語プロセッサの比較が横並びに表示されます。 インデックスは固定です。よくあるストーリーのテキストが含まれています。 検索用語を入力するごとに、隣接するウィンドウに各アナライザーの結果が表示され、同じ文字列を各アナライザーがどのように処理するかを把握できます。 

## <a name="examples"></a>例

いくつかの主なシナリオについて、アナライザーの定義例を示します。

<a name="Example1"></a>
### <a name="example-1-custom-options"></a>例 1:カスタム オプション

この例では、カスタム オプションを使用してアナライザー定義を示しています。 文字フィルター、トークナイザー、トークン フィルターのオム オプションは、名前付きコンストラクトとは別に指定され、アナライザー定義で参照されます。 定義済みの要素はそのまま使用され、単純に名前で参照されます。

この例について手順を説明します。

* アナライザーは、検索可能なフィールドのフィールド クラスのプロパティです。
* カスタム アナライザーは、インデックス定義の一部です。 軽くカスタマイズされているか (1 つのフィルターの 1 つのオプションがカスタマイズされているなど)、複数個所でカスタマイズされている可能性があります。
* この例で、カスタム アナライザーは "my_analyzer" です。このアナライザーはカスタマイズされた標準トークナイザー "my_standard_tokenizer" と、小文字とカスタマイズされた asciifolding フィルターである "my_asciifolding" という 2 つのトークン フィルターを使用しています。
* また、トークン化前にすべてのダッシュをアンダースコアに置換するカスタム "map_dash" 文字フィルターも定義します (標準のトークナイザーはダッシュで停止しますが、アンダースコアでは停止しません)。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Example2"></a>
### <a name="example-2-override-the-default-analyzer"></a>例 2:既定のアナライザーをオーバーライドする

標準アナライザーが既定です。 たとえば、既定のアナライザーを、パターン アナライザーなど、別の定義済みアナライザーで置き換えるとします。 カスタム オプションを設定していない場合、フィールド定義で名前を使用して指定する必要があります。

"analyzer" 要素は、フィールドごとに標準アナライザーをオーバーライドします。 グローバルなオーバーライドはありません。 この例で、`text1` はパターン アナライザーと `text2` を使用します。これはアナライザーを指定せず、既定値を使用します。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Example3"></a>
### <a name="example-3-different-analyzers-for-indexing-and-search-operations"></a>例 3:インデックス作成と検索操作に別のアナライザーを指定する

API には、インデックス作成と検索に別のアナライザーを指定するための追加のインデックス属性が含まれています。 ペアとして `searchAnalyzer` 属性と `indexAnalyzer` 属性を指定して、1 つの `analyzer` 属性を置き換える必要があります。


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Example4"></a>
### <a name="example-4-language-analyzer"></a>例 4:言語アナライザー

複数言語の文字列を含むフィールドでは、言語アナライザーを使用できますが、他のフィールドは既定値のままです (または、他の定義済みまたはカスタム アナライザーを使用します)。 言語アナライザーを使用する場合、インデックス作成と検索操作の両方に使用する必要があります。 言語アナライザーを使用するフィールドでは、インデックス作成と検索に別のアナライザーを使用することはできません。

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="next-steps"></a>次の手順

+ 包括的な説明については、「[Azure Search のフルテキスト検索のしくみ](search-lucene-query-architecture.md)」を参照してください。 この記事では、例を使って、表面上は直感的ではないと思われるような動作について説明しています。

+ [Search Documents](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) の例に関するセクションや[単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)で紹介されているさまざまなクエリ構文をポータルの Search エクスプローラーで試します。

+ [言語に固有の字句解析器](https://docs.microsoft.com/rest/api/searchservice/language-support)を適用する方法について書かれた記事を参照します。

+ 個々のフィールドに対して最小限の処理または特殊な処理を適用するための[カスタム アナライザーを構成](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)します。

+ このデモ Web サイトで隣接する列の[標準と英語のアナライザーを比較](https://alice.unearth.ai/)します。 

## <a name="see-also"></a>関連項目

 [Search Documents REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [単純なクエリ構文](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 

 [Full Lucene クエリ構文](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 
 [検索結果の処理方法](https://docs.microsoft.com/azure/search/search-pagination-page-layout)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
