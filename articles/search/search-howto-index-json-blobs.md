---
title: JSON BLOB による検索
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search Blob インデクサーを使用してテキスト コンテンツのために Azure JSON BLOB をクロールします。 インデクサーにより、選択したデータ ソース (Azure Blob Storage など) のデータ インジェストが自動化されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 6a5a3634f1d5ddc7af2af2e27c2b1d7c8ce9a2af
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88917994"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Azure Cognitive Search で BLOB インデクサーを使用して JSON BLOB のインデックスを作成する方法

この記事では、Azure Blob Storage 内の JSON ドキュメントから構造化コンテンツを抽出するように Azure Cognitive Search BLOB [インデクサー](search-indexer-overview.md)を構成し、Azure Cognitive Search で検索できるようにする方法について説明します。 このワークフローでは、Azure Cognitive Search インデックスを作成し、JSON BLOB から抽出された既存のテキストと共に読み込みます。 

[ポータル](#json-indexer-portal)、[REST API](#json-indexer-rest)、または [.NET SDK](#json-indexer-dotnet) を使用して、JSON コンテンツのインデックスを作成できます。 すべての方法に共通するのは、JSON ドキュメントが Azure Storage アカウントの BLOB コンテナー内にあるということです。 他の Azure 以外のプラットフォームから JSON ドキュメントをプッシュする方法については、[Azure Cognitive Search でのデータのインポート](search-what-is-data-import.md)に関する記事をご覧ください。

Azure Blob Storage 内の JSON BLOB は、通常は、単一の JSON ドキュメント (解析モードは `json`) または JSON エンティティのコレクションのいずれかです。 コレクションの場合、BLOB には整形式の JSON 要素 (解析モードは `jsonArray`) の**配列**が格納されている可能性があります。 BLOB は、改行で区切られた複数の JSON エンティティ (解析モードは `jsonLines`) で構成されていることもあります。 要求の **parsingMode** パラメーターによって、出力構造が決定されます。

> [!NOTE]
> 1つの BLOB から複数の検索ドキュメントにインデックスを作成する方法の詳細については、[一対多のインデックス作成](search-howto-index-one-to-many-blobs.md)に関する記事を参照してください。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>ポータルの使用

JSON ドキュメントのインデックスを作成する最も簡単な方法は、[Azure portal](https://portal.azure.com/) のウィザードを使用することです。 Azure BLOB コンテナー内のメタデータを解析することにより、[**データのインポート**](search-import-data-portal.md) ウィザードでは、既定のインデックスを作成し、ソース フィールドをターゲット インデックス フィールドにマップし、1 回の操作でインデックスを読み込むことができます。 ソース データのサイズと複雑さによっては、数分で運用可能なフルテキスト検索インデックスを作成できます。

待機時間を短くし、帯域幅の課金を回避するために、Azure Cognitive Search と Azure Storage の両方に同じリージョンまたは場所を使用することをお勧めします。

### <a name="1---prepare-source-data"></a>1 - ソース データを準備する

[Azure portal にサインイン](https://portal.azure.com/)し、データを格納する [BLOB コンテナーを作成](../storage/blobs/storage-quickstart-blobs-portal.md)します。 パブリック アクセス レベルは、有効な任意の値に設定できます。

**インポート データ** ウィザードでデータを取得するには、ストレージ アカウント名、コンテナー名、アクセス キーが必要です。

### <a name="2---start-import-data-wizard"></a>2 - データのインポート ウィザードを開始する

検索サービスの [概要] ページでは、コマンド バーから[ウィザードを開始](search-import-data-portal.md)できます。

   ![ポータルの [データのインポート] コマンド](./media/search-import-data-portal/import-data-cmd2.png "データのインポート ウィザードを開始する")

### <a name="3---set-the-data-source"></a>3 - データ ソースを設定する

**データソース**のページでは、ソースが次のように指定された **Azure BLOB ストレージ**になっている必要があります。

+ **[抽出されるデータ]** は、 *[コンテンツとメタデータ]* になっている必要があります。 このオプションを選択すると、ウィザードでインデックス スキーマを推測して、インポート用のフィールドをマップすることができます。
   
+ **[解析モード]** を、 *[JSON]* 、" *[JSON 配列]* "、または " *[JSON 行]* " に設定する必要があります。 

  *[JSON]* は、各 BLOB が 1 つの検索ドキュメントであり、検索結果で独立した項目として表示されることを示します。 

  "*JSON 配列*" は、整形式の JSON データを含む BLOB 用です。この整形式の JSON は、オブジェクトの配列またはオブジェクトの配列であるプロパティに対応し、各要素をスタンドアロンの独立した検索ドキュメントとして使用できます。 BLOB が複雑な場合に、 *[JSON 配列]* を選択しないと、BLOB 全体が 1 つのドキュメントとして取り込まれます。

  " *[JSON 行]* " は、改行で区切られた複数の JSON 要素で構成されている BLOB 用であり、各エンティティをスタンドアロンの独立した検索ドキュメントとして使用できます。 BLOB が複雑なときに、" *[JSON 行]* " 解析モードが選択されていない場合は、BLOB 全体が単一のドキュメントとして取り込まれます。
   
+ **[ストレージ コンテナー]** では、使用するストレージ アカウントとコンテナー、またはコンテナーとして解決される接続文字列を指定する必要があります。 接続文字列は、Blob service のポータル ページで取得できます。

   ![BLOB データ ソースの定義](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - ウィザードの [コンテンツのエンリッチ] ページをスキップする

コグニティブ スキル (またはエンリッチメント) の追加は、インポート要件ではありません。 特別に [AI エンリッチメントを追加する](cognitive-search-concept-intro.md)必要がない場合は、この手順を省略してください。

手順をスキップするには、ページの下部にある [次へ] と [スキップ] の青いボタンをクリックします。

### <a name="5---set-index-attributes"></a>5 - インデックスの属性を設定する

**[インデックス]** ページには、フィールドとデータ型およびインデックスの属性を設定するための一連のチェック ボックスが一覧表示されます。 このウィザードで、メタデータに基づいてソース データをサンプリングすることで、フィールドの一覧を生成できます。 

属性列の上部にあるチェック ボックスをクリックすることで、属性を一括選択できます。 クライアント アプリに返してフル検索処理の対象にする必要があるすべてのフィールドで、 **[取得可能]** と **[検索可能]** を選択します。 整数はフルテキスト検索もあいまい検索もできないことに注意してください (数値は逐語的に評価され、多くの場合フィルターで役立ちます)。

詳細については、[インデックス属性](/rest/api/searchservice/create-index#bkmk_indexAttrib)と[言語アナライザー](/rest/api/searchservice/language-support)の説明を参照してください。 

時間をかけて選択内容を確認します。 ウィザードを実行すると、物理データ構造が作成されて、すべてのオブジェクトを削除して再作成しない限り、これらのフィールドを編集することはできません。

   ![BLOB のインデックス定義](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - インデクサーを作成する

完全に指定すると、ウィザードによって検索サービスに 3 つの異なるオブジェクトが作成されます。 データ ソース オブジェクトとインデックス オブジェクトは、Azure Cognitive Search サービスに名前付きリソースとして保存されます。 最後のステップでは、インデクサー オブジェクトが作成されます。 インデクサーに名前を付けると、インデクサーはスタンドアロン リソースとして存在することができ、同じウィザードのシーケンスで作成されるインデックス オブジェクトやデータ ソース オブジェクトとは独立して、スケジュールを設定したり管理したりできます。

インデクサーに慣れていないユーザーのために説明すると、"*インデクサー*" とは Azure Cognitive Search のリソースであり、外部データ ソースで検索可能なコンテンツのクロールを行います。 **データのインポート** ウィザードで出力されるインデクサーでは、JSON データ ソースがクロールされ、検索可能なコンテンツが抽出されて、Azure Cognitive Search のインデックスにインポートされます。

   ![BLOB インデクサーの定義](media/search-howto-index-json/import-wizard-json-indexer.png)

**[OK]** をクリックしてウィザードを実行し、すべてのオブジェクトを作成します。 インデックスの作成はすぐに開始されます。

ポータルのページでデータのインポートを監視することができます。 進行状況の通知では、インデックス作成の状態と、アップロードされたドキュメントの数が示されます。 

インデックスの作成が完了したら、[Search エクスプローラー](search-explorer.md)を使用してインデックスのクエリを実行できます。

> [!NOTE]
> 期待どおりのデータが表示されない場合は、その他のフィールドにその他の属性を設定する必要があります。 今作成したインデックスとインデクサーを削除し、もう一度ウィザードの手順に従って、手順 5 でインデックス属性の選択内容を変更してください。 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API の使用

REST API を使用して、Azure Cognitive Search のすべてのインデクサーに共通する 3 部構成のワークフロー (データ ソースを作成し、インデックスを作成し、インデクサーを作成する) で、JSON BLOB データのインデックスを作成できます。 BLOB ストレージからのデータ抽出は、インデクサー作成要求を送信した時点で発生します。 この要求が完了すると、クエリ可能なインデックスが作成されます。 

このセクションの最後にある [REST サンプル コード](#rest-example)で、3 つのオブジェクトをすべて作成する方法を確認できます。 このセクションには、[JSON 解析モード](#parsing-modes)、[単一 BLOB](#parsing-single-blobs)、[JSON 配列](#parsing-arrays)、および[入れ子になった配列](#nested-json-arrays)の詳細も含まれています。

コードベースの JSON インデックスでは、[Postman](search-get-started-postman.md) と REST API を使用して、次のオブジェクトが作成されます。

+ [インデックス](/rest/api/searchservice/create-index)
+ [データ ソース](/rest/api/searchservice/create-data-source)
+ [インデクサー](/rest/api/searchservice/create-indexer)

オブジェクトを作成して呼び出す操作は、この順序で実行する必要があります。 ポータルを使用するワークフローとは異なり、コードを使用する方法では、**インデックス作成**要求を通して送信された JSON ドキュメントを受け取るために利用できるインデックスが必要です。

Azure Blob Storage 内の JSON BLOB は、通常は、単一の JSON ドキュメントまたは JSON "配列" のいずれかです。 Azure Cognitive Search の BLOB インデクサーでは、要求で **parsingMode** パラメーターを設定する方法に応じて、構成を解析できます。

| JSON ドキュメント | parsingMode | 説明 | 可用性 |
|--------------|-------------|--------------|--------------|
| BLOB あたり 1 つ | `json` | JSON BLOB を 1 つのテキスト チャンクとして解析します。 各 JSON BLOB は、1 つの Azure Cognitive Search ドキュメントになります。 | [REST](/rest/api/searchservice/indexer-operations) API と [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK の両方で一般公開されています。 |
| BLOB あたり複数 | `jsonArray` | 配列の各要素が別々の Azure Cognitive Search ドキュメントになる、BLOB 内の JSON 配列を解析します。  | [REST](/rest/api/searchservice/indexer-operations) API と [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK の両方で一般公開されています。 |
| BLOB あたり複数 | `jsonLines` | 改行によって分離された複数の JSON エンティティ ("配列") を含む BLOB を解析します。各エンティティが独立した Azure Cognitive Search ドキュメントになります。 | [REST](/rest/api/searchservice/indexer-operations) API と [.NET](/dotnet/api/microsoft.azure.search.models.indexer) SDK の両方で一般公開されています。 |

### <a name="1---assemble-inputs-for-the-request"></a>1 - 要求に対する入力をアセンブルする

要求ごとに、Azure Cognitive Search サービス名と管理者キーを (POST ヘッダーに) 指定し、BLOB ストレージのストレージ アカウント名とキーを指定する必要があります。 [Postman](search-get-started-postman.md) を使用して、Azure Cognitive Search に HTTP 要求を送信できます。

次の 4 つの値をメモ帳にコピーして、要求に貼り付けることができるようにします。

+ Azure Cognitive Search サービス名
+ Azure Cognitive Search の管理者キー
+ Azure ストレージ アカウント名
+ Azure ストレージ アカウント キー

これらの値は、ポータルで調べることができます。

1. Azure Cognitive Search のポータル ページで、[概要] ページから Search サービスの URL をコピーします。

2. 左側のナビゲーション ウィンドウで **[キー]** をクリックし、プライマリまたはセカンダリのいずれかのキー (どちらも働きは同じです) をコピーします。

3. ストレージ アカウントのポータル ページに切り替えます。 左側のナビゲーション ウィンドウで、 **[設定]** の **[アクセス キー]** をクリックします。 このページには、アカウント名とキーの両方が表示されます。 ストレージ アカウント名といずれかのキーをメモ帳にコピーします。

### <a name="2---create-a-data-source"></a>2 - データ ソースを作成する

この手順では、インデクサーで使用されるデータ ソース接続情報を指定します。 データ ソースは、接続情報を保持する Azure Cognitive Search 内の名前付きオブジェクトです。 `azureblob` というデータ ソースの種類によって、インデクサーによって呼び出されるデータ抽出動作が決まります。 

サービス名、管理者キー、ストレージ アカウント、およびアカウント キーのプレースホルダーを有効な値を置き換えてください。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3 - ターゲット検索インデックスを作成する 

インデクサーは、インデックス スキーマとペアになります。 (ポータルではなく) API を使用する場合は、インデクサー操作で指定できるようにインデックスを事前に準備します。

インデックスには、Azure Cognitive Search の検索可能なコンテンツが格納されます。 インデックスを作成するには、検索エクスペリエンスを構成するドキュメント内のフィールド、属性、およびその他の構造が指定されているスキーマを提供します。 ソースと同じフィールド名とデータ型を持つインデックスを作成すると、インデクサーによってソースのフィールドとインデックスのフィールドが照合されるので、フィールドを明示的にマップする作業をしなくて済みます。

[インデックス作成](/rest/api/searchservice/create-index)要求の例を次に示します。 インデックスには、BLOB から抽出されたテキストを格納するための、検索可能な `content` フィールドが含まれます。   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4 - インデクサーを構成して実行する

インデックスおよびデータ ソースと同様に、インデクサーも Azure Cognitive Search サービス上に作成して再利用する名前付きオブジェクトです。 インデクサーを作成するための完全に指定された要求は次のようになります。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

インデクサーの構成は、要求の本文内にあります。 データ ソースと、Azure Cognitive Search に既に存在する空のターゲット インデックスが必要です。 

スケジュールとパラメーターは省略可能です。 これらを省略した場合、インデクサーは、解析モードとして `json` を使用してすぐに実行されます。

この特定のインデクサーには、フィールド マッピングは含まれていません。 インデクサーの定義では、ソース JSON ドキュメントのプロパティとターゲット検索インデックスのフィールドが一致する場合は、**フィールド マッピング**を省略できます。 


### <a name="rest-example"></a>REST の例

このセクションは、オブジェクトを作成するために使用されるすべての要求の要約です。 コンポーネント パーツの詳細については、この記事の前のセクションを参照してください。

### <a name="data-source-request"></a>データ ソース要求

すべてのインデクサーには、既存のデータへの接続情報を提供するデータ ソース オブジェクトが必要です。 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>インデックス要求

すべてのインデクサーには、データを受信するターゲット インデックスが必要です。 要求の本文には、検索可能なインデックスで目的の動作をサポートする属性が設定されたフィールドで構成されるインデックス スキーマを定義します。 インデクサーが実行される時点で、このインデックスは空である必要があります。 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>インデクサー要求

次の要求は、完全に指定されたインデクサーを示しています。 これには、前の例では省略されていたフィールド マッピングが含まれています。 使用可能な既定値がある限り、"schedule"、"parameters"、および "fieldMappings" は省略できます。 "schedule" を省略すると、インデクサーはすぐに実行されます。 "parsingMode" を省略すると、インデックスでは 既定の "json" が使用されます。

Azure Cognitive Search 上にインデクサーを作成すると、データのインポートがトリガーされます。 それはすぐに実行されます。スケジュールが指定されている場合は、スケジュールに従って実行されます。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK の使用

.NET SDK は REST API と完全に同等です。 前の REST API セクションを確認し、概念、ワークフロー、要件を理解することをお勧めします。 その後は、次の .NET API リファレンス ドキュメントを参照して、マネージド コードで JSON インデクサーを実装できます。

+ [microsoft.azure.search.models.datasource](/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>解析モード

JSON BLOB では、複数の形式を想定できます。 JSON インデクサーの **parsingMode** パラメーターによって、JSON BLOB のコンテンツをどのように解析し、Azure Cognitive Search インデックスをどのように構成するかが決まります。

| parsingMode | 説明 |
|-------------|-------------|
| `json`  | 各 BLOB を単一のドキュメントとしてインデックス化します。 これは既定値です。 |
| `jsonArray` | BLOB が JSON 配列で構成され、配列の各要素を Azure Cognitive Search で独立したドキュメントにする必要がある場合は、このモードを選択します。 |
|`jsonLines` | BLOB が、改行で分離されている複数の JSON エンティティで構成され、各エンティティを Azure Cognitive Search で独立したドキュメントにする必要がある場合は、このモードを選択します。 |

ドキュメントは、検索結果の 1 つの項目として考えることができます。 配列の各要素を、検索結果に独立した項目として表示する場合は、`jsonArray` または `jsonLines` オプションを使用します。

インデクサーの定義では、必要に応じて、[フィールド マッピング](search-indexer-field-mappings.md)を使用して、ターゲットの検索インデックスの設定に使用されるソース JSON ドキュメントのプロパティを選択できます。 `jsonArray` 解析モードでは、配列が下位レベルのプロパティとして存在する場合は、BLOB 内での配列の配置場所を示すドキュメント ルートを設定できます。

> [!IMPORTANT]
> `json`、`jsonArray`、または `jsonLines` 解析モードを使用した場合、Azure Cognitive Search では、データ ソース内のすべての BLOB に JSON が含まれていると想定されます。 JSON BLOB と JSON 以外の BLOB が混在するデータ ソースをサポートする必要がある場合は、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search) でお知らせください。


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>単一の JSON BLOB を解析する

[Azure Cognitive Search BLOB インデクサー](search-howto-indexing-azure-blob-storage.md) は、既定では JSON BLOB を 1 つのテキスト チャンクとして解析します。 多くの場合、JSON ドキュメントの構造はそのままに維持する必要があります。 たとえば、Azure Blob Storage に次の JSON ドキュメントがあると仮定します。

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

BLOB インデクサーでは、JSON ドキュメントが 1 つの Azure Cognitive Search ドキュメントとして解析されます。 インデクサーでは、ソースの "text"、"datePublished"、"tags" が、同じ名前と型のターゲット インデックス フィールドと照合されて、インデックスが読み込まれます。

前述のように、フィールド マッピングは必要ありません。 "text"、"datePublished、"tags" のフィールドでインデックスを指定すると、BLOB インデクサーは、要求にフィールド マッピングがない場合でも正しいマッピングを推測することができます。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON 配列を解析する

別の方法として、[JSON 配列] オプションを使用できます。 このオプションは、BLOB に "*JSON オブジェクトの配列*" が含まれ、各要素を個別の Azure Cognitive Search ドキュメントにする場合に役立ちます。 たとえば、次の JSON BLOB では、それぞれ "id" および "text" フィールドを持つ 3 つの独立したドキュメントで Azure Cognitive Search インデックスを作成できます。  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

JSON 配列の場合、インデクサーの定義は次の例のようになります。 parsingMode パラメーターで `jsonArray` パーサーが指定されていることに注意してください。 JSON BLOB のインデックスを作成するための配列に固有の要件は、適切なパーサーを指定することと、適切なデータを入力することの 2 つだけです。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

この場合も、フィールドのマッピングを省略できることに注意してください。 インデックスに同じ名前のフィールド "id" と "text" があるものとすると、フィールド マッピング リストを明示的に指定しなくても、BLOB インデクサーで正しいマッピングを推測できます。

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>入れ子になった配列を解析する
入れ子になった要素を持つ JSON 配列では、`documentRoot` を指定して、複数レベルの構造であることを指示することができます。 たとえば、次のような BLOB があるとします。

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

この構成を使用して、`level2` プロパティに格納されている配列にインデックスを付けます。

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>改行で区切られた BLOB を解析する

BLOB に改行で分離された複数の JSON エンティティが含まれ、各要素を独立した Azure Cognitive Search ドキュメントにする場合は、[JSON 行] オプションを選択できます。 たとえば、次の BLOB (3 つの異なる JSON があります) では、それぞれが "id" フィールドと "text" フィールドを持つ 3 つの独立したドキュメントを含む Azure Cognitive Search インデックスを作成できます。

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

JSON 行では、インデクサーの定義は次の例のようになります。 parsingMode パラメーターで `jsonLines` パーサーが指定されていることに注意してください。 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

ここでも、`jsonArray` 解析モードのように、フィールド マッピングを省略できることに注意してください。

## <a name="add-field-mappings"></a>フィールド マッピングを追加する

ソースとターゲットのフィールドが完全には一致していない場合、要求本文のフィールド マッピング セクションでフィールド間の明示的なアソシエーションを定義できます。

現在、Azure Cognitive Search では、プリミティブ データ型、文字列配列、および GeoJSON ポイントのみをサポートしているため、任意の JSON ドキュメントに対して直接インデックス作成を行うことはできません。 ただし、 **フィールド マッピング** を使用すると、JSON ドキュメントの一部を選択して、それを検索ドキュメントの最上位レベルのフィールドに "引き上げる" ことができます。 フィールド マッピングの基礎については、[Azure Cognitive Search インデクサーのフィールド マッピング](search-indexer-field-mappings.md)に関する記事をご覧ください。

JSON ドキュメントの例に戻りましょう。

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

`Edm.String` 型の `text` フィールド、`Edm.DateTimeOffset` 型の `date` フィールド、`Collection(Edm.String)` 型の `tags` フィールドを持つ検索インデックスがあるとします。 ソースの "datePublished" とインデックスの `date` フィールド間の違いに注目してください。 JSON を必要な形式にマッピングするには、次のフィールド マッピングを使用します。

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

マッピング内のソース フィールド名は、 [JSON ポインター](https://tools.ietf.org/html/rfc6901) の表記を使用して指定されています。 スラッシュから始めて、JSON ドキュメントのルートを参照し、その後、スラッシュ区切りのパスを使用して、目的のプロパティ (任意の入れ子レベル) まで指定します。

0 から始まるインデックスを使用して個々の配列要素を参照することもできます。 たとえば、上の例から "tags" 配列の最初の要素を選択するには、次のようなフィールド マッピングを使用します。

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> フィールド マッピングのパス内のソース フィールド名が、JSON に存在しないプロパティを参照している場合、そのマッピングはエラーなしでスキップされます。 これは、異なるスキーマを使用したドキュメントをサポートできるようにするためです (一般的なユース ケースです)。 検証機能がないため、フィールド マッピングの仕様を入力するときは、入力ミスをしないように注意する必要があります。
>
>

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [Azure Blob Storage のインデックスを Azure Cognitive Search で作成する](search-howto-index-json-blobs.md)
+ [Azure Cognitive Search BLOB インデクサーを使用して CSV BLOB のインデックスを作成する](search-howto-index-csv-blobs.md)
+ [チュートリアル:Azure Blob Storage で半構造化データを検索する](search-semi-structured-data.md)