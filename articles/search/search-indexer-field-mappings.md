---
title: インデクサーのフィールド マッピング
titleSuffix: Azure Cognitive Search
description: フィールド名とデータ表現の間の違いを調整するようにインデクサーでフィールド マッピングを構成します。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72623787cdb27c568fe2b4ec075010674a3996ef
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74124002"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Cognitive Search インデクサーを使用したフィールドのマッピングと変換

Azure Cognitive Search インデクサーを使用すると、入力データがターゲット インデックスのスキーマと完全に一致しないことがあります。 そのような場合は、インデックス作成処理中に**フィールド マッピング**を使用してデータを変形できます。

フィールド マッピングが役立つシナリオは次のとおりです。

* データ ソースに `_id` というフィールドがあるが、Azure Cognitive Search でアンダースコアで始まるフィールド名が許可されていない場合。フィールド マッピングを使用すると、フィールドの名前を効果的に変更できます。
* 同じデータ ソース データからインデックス内のいくつかのフィールドにデータを入力します。 たとえば、このようなフィールドにさまざまなアナライザーを適用することができます。
* インデックス フィールドに複数のデータ ソースのデータを入力し、データ ソースはそれぞれ異なるフィールド名が使用されています。
* Base64 エンコードまたはデータのデコードが必要な場合。 フィールド マッピングは、Base64 エンコードおよびデコードの関数など、**マッピング関数**をいくつかサポートしています。

> [!NOTE]
> Azure Cognitive Search インデクサーのフィールド マッピング機能には、データ変換のためのいくつかのオプションを使用して、データ フィールドをインデックス フィールドにマップする簡単な方法が用意されています。より複雑なデータでは、インデックス付けが簡単な形式に変形する前処理が必要な場合があります。
>
> Microsoft Azure Data Factory は、データをインポートおよび変換するための強力なクラウドベースのソリューションです。 インデックスを付ける前にソース データを変換するコードを書くこともできます。 コード例については、「[リレーショナル データをモデル化する](search-example-adventureworks-modeling.md)」と「[多層構造ファセットをモデル化する](search-example-adventureworks-multilevel-faceting.md)」を参照してください。
>

## <a name="set-up-field-mappings"></a>フィールド マッピングの設定

フィールド マッピングは、3 つの部分で構成されます。

1. `sourceFieldName`。データ ソース内のフィールドを表します。 このプロパティは必須です。
2. `targetFieldName` (省略可能)。Search インデックス内のフィールドを表します。 省略すると、データ ソースと同じ名前が使用されます。
3. `mappingFunction` (省略可能)。定義済みのいずれかの関数を使用してデータを変換できます。 関数の完全な一覧については、[以下](#mappingFunctions)をご覧ください。

フィールド マッピングは、インデクサー定義の `fieldMappings` 配列に追加されます。

## <a name="map-fields-using-the-rest-api"></a>REST API を使用してフィールドをマップする

フィールド マッピングは、[インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) API 要求を使用して新しいインデクサーを作成するときに追加できます。 [インデクサーの更新](https://docs.microsoft.com/rest/api/searchservice/update-indexer) API 要求を使用して、既存のインデクサーのフィールド マッピングを管理できます。

たとえば、ソース フィールドを別の名前のターゲット フィールドにマップする方法は次のとおりです。

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

ソース フィールドは複数のフィールド マッピングで参照できます。 次の例は、同じソース フィールドを 2 つの異なるインデックス フィールドにコピーしてフィールドを "分岐させる" 方法を示しています。

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search では、大文字と小文字が区別されない比較を使用して、フィールド マッピングのフィールドと関数名を解決します。これは便利ですが (大文字と小文字を区別する必要がないため)、同時に、データ ソースまたはインデックスが、大文字と小文字のみが異なるフィールドを持つことができないことを意味します。  
>
>

## <a name="map-fields-using-the-net-sdk"></a>.NET SDK を使用してフィールドをマップする

プロパティ `SourceFieldName` と `TargetFieldName` を持つ [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) クラスと、オプションの `MappingFunction` 参照を使用して、.NET SDK でフィールド マッピングを定義します。

インデクサーを作成するとき、または後で `Indexer.FieldMappings` プロパティを直接設定することによって、フィールド マッピングを指定できます。

次の C# の例では、インデクサーを作成するときにフィールド マッピングを設定します。

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>フィールド マッピング関数

フィールド マッピング関数によって、インデックスに格納される前にフィールドのコンテンツが変換されます。 現在、以下のマッピング関数がサポートされています。

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 関数

入力文字列の *URL の安全な* Base64 エンコードを実行します。 入力は UTF-8 でエンコードされていることを前提としています。

#### <a name="example---document-key-lookup"></a>例 - ドキュメント キーの検索

Azure Cognitive Search ドキュメント キーには、URL で使用できる文字のみを使用できます ([Lookup API](https://docs.microsoft.com/rest/api/searchservice/lookup-document) を使用してドキュメントのアドレスを指定できるようにする必要があるため)。URL の安全ではない文字がキーのソース フィールドに含まれている場合は、インデックス作成時に `base64Encode` 関数を使用して変換できます。

検索時にエンコードされたキーを取得すると、`base64Decode` 関数を使用して元のキー値を取得し、それを使用してソース ドキュメントを取得できます。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

マッピング関数に parameters プロパティを含めない場合、既定で値 `{"useHttpServerUtilityUrlTokenEncode" : true}` になります。

Azure Cognitive Search では、2 つの異なる Base64 エンコードがサポートされています。 同じフィールドをエンコードおよびデコードするときは、同じパラメーターを使用する必要があります。 詳細については、[base64 エンコード オプション](#base64details)に関するページを参照して使用するパラメーターを決定します。

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 関数

入力文字列の Base64 デコードを実行します。 入力値は *URL 対応*の Base64 でエンコードされた文字列と想定されます。

#### <a name="example---decode-blob-metadata-or-urls"></a>例 - BLOB メタデータまたは URL をデコードする

ソース データには、BLOB メタデータ文字列や Web URL など、プレーン テキストとして検索できるようにする Base64 エンコード文字列が含まれている場合があります。 検索インデックスを設定するときに、`base64Decode` 関数を使用して、エンコードされたデータを通常の文字列に戻すことができます。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

parameters プロパティを含めない場合、既定で値 `{"useHttpServerUtilityUrlTokenEncode" : true}` になります。

Azure Cognitive Search では、2 つの異なる Base64 エンコードがサポートされています。 同じフィールドをエンコードおよびデコードするときは、同じパラメーターを使用する必要があります。 詳細については、「[base64 エンコード オプション](#base64details)」を参照して使用するパラメーターを決定します。

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 エンコード オプション

Azure Cognitive Search では、URL セーフな base64 エンコードと通常の base64 エンコードがサポートされています。 インデックス作成中に base64 でエンコードされた文字列は、後で同じエンコード オプションでデコードする必要があります。そうしないと、結果が元の文字列と一致しなくなります。

それぞれがエンコードとデコードに対応する `useHttpServerUtilityUrlTokenEncode` パラメーターまたは `useHttpServerUtilityUrlTokenDecode` パラメーターが `true` に設定されると、`base64Encode` は [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) のように、`base64Decode` は [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx) のように動作します。

> [!WARNING]
> キー値を生成するために `base64Encode` を使用する場合は、`useHttpServerUtilityUrlTokenEncode` を true に設定する必要があります。 キー値に使用できるのは、URL セーフな base64 エンコードのみです。 キー値の文字に関するすべての制限事項については、「[名前付け規則 &#40;Azure Cognitive Search&#41;](https://docs.microsoft.com/rest/api/searchservice/naming-rules)」を参照してください。

Azure Cognitive Search の .NET ライブラリでは、組み込みのエンコードを提供する完全な .NET Framework が前提になっています。 `useHttpServerUtilityUrlTokenEncode` オプションと `useHttpServerUtilityUrlTokenDecode` オプションでは、この組み込みの機能が活用されます。 .NET Core または別のフレームワークを使用している場合は、これらのオプションを `false` に設定し、フレームワークのエンコードおよびデコード関数を直接呼び出すことをお勧めします。

次の表では、文字列 `00>00?00` の異なる base64 エンコードを比較しています。 base64 関数に必要な追加処理 (ある場合) を判断するには、ライブラリ エンコード関数を文字列 `00>00?00` に適用し、出力を想定出力 `MDA-MDA_MDA` と比較します。

| エンコード | base64 エンコード出力 | ライブラリ エンコード後の追加処理 | ライブラリ デコード前の追加処理 |
| --- | --- | --- | --- |
| パディング付きの base64 | `MDA+MDA/MDA=` | URL で使用できる文字を使用し、パディングを削除する | 標準 base64 文字を使用し、パディングを追加する |
| パディングなしの base64 | `MDA+MDA/MDA` | URL で使用できる文字を使用する | 標準 base64 文字を使用する |
| パディング付きの URL 対応 base64 | `MDA-MDA_MDA=` | パディングを削除する | パディングを追加する |
| パディングなしの URL 対応 base64 | `MDA-MDA_MDA` | なし | なし |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 関数

指定された区切り記号を使用して文字列フィールドを分割し、結果として得られる分割の指定位置でトークンを取得します。

この関数は以下のパラメーターを使用します。

* `delimiter`: 入力文字列を分割するときに区切り記号として使用する文字列。
* `position`: 入力文字列の分割後に取得するトークンの整数の 0 から始まる位置。

たとえば、入力が `Jane Doe`、`delimiter` が `" "` (空白)、`position` が 0 の場合、結果は `Jane` になり、`position` が 1 の場合、結果は `Doe` になります。 位置が、存在しないトークンを参照する場合、エラーが返されます。

#### <a name="example---extract-a-name"></a>例 - 名前を抽出する

データ ソースに `PersonName` フィールドが含まれ、それを 2 つの別々の `FirstName` および `LastName` フィールドとしてインデックスする必要がある場合、 この関数を使用して、空白文字を区切り記号として使って入力を分割できます。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 関数

文字列の JSON 配列として書式設定された文字列を、インデックス内の `Collection(Edm.String)` フィールドの入力に使用できる文字列配列に変換します。

たとえば、入力文字列が `["red", "white", "blue"]` の場合、`Collection(Edm.String)` 型のターゲット フィールドに `red`、`white`、`blue` の 3 つの値が設定されます。 JSON 文字列配列として解析できない入力値では、エラーが返されます。

#### <a name="example---populate-collection-from-relational-data"></a>例 - リレーショナル データからコレクションを設定する

Azure SQL Database には、Azure Cognitive Search の `Collection(Edm.String)` のフィールドに自然にマップされる組み込みのデータ型がありません。文字列収集フィールドを設定するには、ソース データを JSON 文字列配列として前処理してから、`jsonArrayToStringCollection` マッピング関数を使用することができます。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

リレーショナル データをインデックス コレクション フィールドに変換する詳細な例については、「[リレーショナル データをモデル化する](search-example-adventureworks-modeling.md)」を参照してください。

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 関数

この関数を使用すると、"URL セーフ" になるように文字列をエンコードできます。 URL 内で許可されていない文字を含む文字列と共に使用する場合、この関数では、これらの "安全でない" 文字が等価の文字エンティティに変換されます。 この関数では、UTF-8 エンコード形式が使用されます。

#### <a name="example---document-key-lookup"></a>例 - ドキュメント キーの検索

`urlEncode` 関数は、他の文字をそのままにして、URL の安全でない文字だけを変換する場合に、`base64Encode` 関数の代わりに使用できます。

たとえば、入力文字列が `<hello>` の場合、`(Edm.String)` 型のターゲット フィールドに値 `%3chello%3e`が設定されます

検索時にエンコードされたキーを取得すると、`urlDecode` 関数を使用して元のキー値を取得し、それを使用してソース ドキュメントを取得できます。

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode 関数

 この関数では、UTF-8 エンコード形式を使用して、URL エンコードされた文字列がデコードされた文字列に変換されます。

 ### <a name="example---decode-blob-metadata"></a>例 - BLOB メタデータをデコードする

 一部の Azure ストレージ クライアントでは、ASCII 以外の文字が含まれている場合に、BLOB メタデータが自動的に URL エンコードされます。 ただし、このようなメタデータを (プレーンテキストとして) 検索可能にする場合は、`urlDecode` 関数を使用して、検索インデックスを設定する際に、エンコードされたデータを通常の文字列に戻すことがきます。

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
