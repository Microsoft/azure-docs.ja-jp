---
title: Functions 2.x の Azure Cosmos DB バインド
description: Azure Functions で Azure Cosmos DB のトリガーとバインドを使用する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 3ef2fdcaefeedb0769eac34d292e67a99524a6f2
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168075"
---
# <a name="azure-cosmos-db-bindings-for-azure-functions-2x"></a>Azure Functions 2.x の Azure Cosmos DB バインド

> [!div class="op_single_selector" title1="使用している Azure Functions ランタイムのバージョンを選択します。 "]
> * [Version 1](functions-bindings-cosmosdb.md)
> * [Version 2](functions-bindings-cosmosdb-v2.md)

この記事では、Azure Functions 2.x で [Azure Cosmos DB](../cosmos-db/serverless-computing-database.md) のバインドを操作する方法について説明します。 Azure Functions は、Azure Cosmos DB のトリガー、入力、出力のバインドをサポートしています。

> [!NOTE]
> この記事は、[Azure Functions version 2.x](functions-versions.md) を対象としています。  Functions 1.x でこれらのバインドを使用する方法については、[Azure Functions 1.x の Azure Cosmos DB のバインド](functions-bindings-cosmosdb.md)に関するページを参照してください。
>
> このバインドの元の名称は DocumentDB でした。 Functions バージョン 2.x では、トリガー、バインド、およびパッケージの名称はすべて、Cosmos DB になっています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="supported-apis"></a>サポート対象 API

[!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="packages---functions-2x"></a>パッケージ - Functions 2.x

Functions バージョン 2.x の Azure Cosmos DB バインドは、[Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) NuGet パッケージのバージョン 3.x で提供されています。 バインドのソース コードは、[azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/) GitHub リポジトリにあります。

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>トリガー

Azure Cosmos DB のトリガーは [Azure Cosmos DB 変更フィード](../cosmos-db/change-feed.md)を使用して、パーティション間の挿入と更新をリッスンします。 変更フィードは、削除ではなく挿入と更新を発行します。

# <a name="c"></a>[C#](#tab/csharp)

次の例では、指定されたデータベースとコレクション内に挿入または更新がある場合に呼び出される [C# 関数](functions-dotnet-class-library.md)を示します。

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、Cosmos DB レコードが追加または変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

C# スクリプト コードを次に示します。

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、Cosmos DB レコードが追加または変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

JavaScript コードを次に示します。

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="python"></a>[Python](#tab/python)

次の例は、*function.json* ファイルの Cosmos DB トリガー バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、Cosmos DB レコードが変更されるとログ メッセージを書き込みます。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Python コードを次に示します。

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

# <a name="java"></a>[Java](#tab/java)

指定されたデータベースとコレクション内に挿入または更新がある場合に、この関数が呼び出されます。

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に由来するパラメーター上で `@CosmosDBTrigger` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

---

## <a name="trigger---attributes-and-annotations"></a>トリガー - 属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[トリガー - 構成](#trigger---configuration)」をご覧ください。 メソッド シグネチャでの `CosmosDBTrigger` 属性の例を次に示します。

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run(
        [CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
    IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

完全な例については、「[トリガー](#trigger)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)から、`@CosmosDBInput` 注釈を Cosmos DB からデータを読み取るパラメーターに使用します。

---

## <a name="trigger---configuration"></a>トリガー - 構成

次の表は、*function.json* ファイルと `CosmosDBTrigger` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 300 | `cosmosDBTrigger` に設定する必要があります。 |
|**direction** | 300 | `in` に設定する必要があります。 このパラメーターは、Azure Portal でトリガーを作成するときに自動で設定されます。 |
|**name** | 300 | 変更されるドキュメントの一覧を表す、関数コードで使用する変数の名前。 |
|**connectionStringSetting**|**ConnectionStringSetting** | 監視されている Azure Cosmos DB アカウントに接続するために使用される接続文字列を含めたアプリ設定の名前。 |
|**databaseName**|**DatabaseName**  | 監視されているコレクションを使用する Azure Cosmos DB データベースの名前。 |
|**collectionName** |**CollectionName** | 監視されているコレクションの名前。 |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | (省略可能) リース コレクションを保持する Azure Cosmos DB アカウントへの接続文字列を含むアプリ設定の名前。 この値を設定しない場合、`connectionStringSetting` という値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 リース コレクションの接続文字列には書き込み権限が必要です。|
|**leaseDatabaseName** |**LeaseDatabaseName** | (省略可能) リースの保存のために使用するコレクションを保持しているデータベースの名前。 この値を設定しない場合、`databaseName` の設定の値が使用されます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。 |
|**leaseCollectionName** | **LeaseCollectionName** | (省略可能) リースの保存のために使用するコレクションの名前。 この値を設定しない場合、`leases` という値が使用されます。 |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | (省略可能) `true` を設定すると、リース コレクションが存在していない場合に自動でリース コレクションを作成します。 既定値は `false` です。 |
|**leasesCollectionThroughput**| **leasesCollectionThroughput**| (省略可能) リース コレクションの作成に割り当てる要求ユニットの数を定義します。 この設定は、`createLeaseCollectionIfNotExists` が `true` に設定されている場合のみ使用できます。 このパラメーターは、ポータルでバインドが作成されるときに自動で設定されます。
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| (省略可能) 設定すると、この関数のリース コレクションで作成されたリースへのプレフィックスとして値が追加されます。 プレフィックスを使用すると、異なるプレフィックスを使用して、2 つの別の Azure Functions が同じリース コレクションを共有できるようになります。
|**feedPollDelay**| **FeedPollDelay**| (省略可能) 現在のすべての変更がドレインされた後、フィードの新しい変更についてパーティションをポーリングする間の遅延時間 (ミリ秒)。 既定値は 5,000 ミリ秒 (5 秒) です。
|**leaseAcquireInterval**| **LeaseAcquireInterval**| (省略可能) 設定すると、パーティションが既知のホスト インスタンス間で均等に分散されているかどうかを計算するタスクを開始する間隔がミリ秒単位で定義されます。 既定値は 13,000 (13 秒) です。
|**leaseExpirationInterval**| **LeaseExpirationInterval**| (省略可能) 設定すると、パーティションを表すリースでリースを取得する間隔がミリ秒単位で定義されます。 この間隔内にリースが更新されない場合、リースは期限切れとなり、パーティションの所有権は別のインスタンスに移動します。 既定値は 60,000 (60 秒) です。
|**leaseRenewInterval**| **LeaseRenewInterval**| (省略可能) 設定すると、インスタンスが現在保持しているパーティションのすべてのリースの更新間隔がミリ秒単位で定義されます。 既定値は 17,000 (17 秒) です。
|**checkpointFrequency**| **CheckpointFrequency**| (省略可能) 設定すると、リース チェックポイントの間隔がミリ秒単位で定義されます。 既定値は、常に各関数呼び出しの後です。
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| (省略可能) 設定すると、関数呼び出しごとに、受信するアイテムの最大数がこのプロパティによって設定されます。 監視対象のコレクションでストアド プロシージャによって操作が実行されると、変更フィードから項目が読み取られるとき、[トランザクション スコープ](../cosmos-db/stored-procedures-triggers-udfs.md#transactions)が保持されます。 その結果、受信した項目数が指定した値よりも多くなり、同じトランザクションで変更された項目が 1 つのアトミック バッチの一部として返される可能性があります。
|**startFromBeginning**| **StartFromBeginning**| (省略可能) このオプションを指定すると、現在の時刻から開始するのではなく、コレクションの変更履歴の先頭から変更を読み取るようにトリガーに指示できます。 以降の実行ではチェックポイントが既に保存されているため、先頭からの読み取りが機能するのは、トリガーが初めて開始されたときのみです。 既にリースが作成されているときにこのオプションを `true` に設定しても効果はありません。

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---usage"></a>トリガー - 使用方法

トリガーは、パーティションに _リース_ を保存するために使用する 2 つ目のコレクションを必要とします。 トリガーが機能するためには、監視されるコレクションと、リースを含むコレクションの両方が使用できる必要があります。

>[!IMPORTANT]
> 複数の関数が同じコレクションの Cosmos DB トリガーを使用するように構成している場合、各関数では専用のリース コレクションを使用するか、または各関数に対して異なる `LeaseCollectionPrefix`を指定する必要があります。 専用のリース コレクションを使用していない場合は、関数のうちの 1 つしかトリガーされません。 プレフィックスの詳細については、「[構成](#trigger---configuration)」セクションを参照してください。

トリガーは、ドキュメントが更新または挿入されたかどうかを示すわけではなく、単にドキュメント自体を提供します。 更新と挿入を別の方法で処理する必要がある場合は、挿入または更新のタイムスタンプを実装することでこれを実現できます。

## <a name="input"></a>入力

Azure Cosmos DB 入力バインドでは、SQL API を使用して 1 つ以上の Azure Cosmos DB ドキュメントを取得して関数の入力パラメーターに渡します。 ドキュメント ID またはクエリ パラメーターは、関数を呼び出したトリガーに基づいて決定することができます。

> [!NOTE]
> コレクションが[パーティション分割](../cosmos-db/partition-data.md#logical-partitions)されている場合、参照操作でも、パーティション キーの値を指定する必要があります。
>

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-c)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-c)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-c)
* [HTTP トリガー、SqlQuery を使用したルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-using-sqlquery-c)
* [HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-sqlquery-c)
* [HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-documentclient-c)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string PartitionKey { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-json-c"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索 

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、JSON オブジェクトを含むキュー メッセージによってトリガーされます。 キュー トリガーにより、検索する ID とパーティション キー値を含む、`ToDoItemLookup` タイプのオブジェクトに JSON が解析されます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItemLookup
    {
        public string ToDoItemId { get; set; }

        public string ToDoItemPartitionKeyValue { get; set; }
    }
}
```

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromJSON
    {
        [FunctionName("DocByIdFromJSON")]
        public static void Run(
            [QueueTrigger("todoqueueforlookup")] ToDoItemLookup toDoItemLookup,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{ToDoItemId}",
                PartitionKey = "{ToDoItemPartitionKeyValue}")]ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed Id={toDoItemLookup?.ToDoItemId} Key={toDoItemLookup?.ToDoItemPartitionKeyValue}");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
        }
    }
}
```

<a id="http-trigger-look-up-id-from-query-string-c"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

>[!NOTE]
>HTTP クエリ文字列パラメーターは大文字と小文字が区別されます。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromQueryString
    {
        [FunctionName("DocByIdFromQueryString")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{Query.id}",
                PartitionKey = "{Query.partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-c"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteData
    {
        [FunctionName("DocByIdFromRouteData")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems/{partitionKey}/{id}")]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                Id = "{id}",
                PartitionKey = "{partitionKey}")] ToDoItem toDoItem,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            if (toDoItem == null)
            {
                log.LogInformation($"ToDo item not found");
            }
            else
            {
                log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-look-up-id-from-route-data-using-sqlquery-c"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用したルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、ルート データを使用して検索のための ID を指定する HTTP 要求によってトリガーされます。 該当の ID は、指定されたデータベースとコレクションからの `ToDoItem` ドキュメントの取得に使用されます。

例では、`SqlQuery` パラメーターでバインド式を使用する方法を示しています。 ここに示したようにルート データを `SqlQuery` パラメーターに渡すことはできますが、現在、[クエリ文字列の値を渡すことはできません](https://github.com/Azure/azure-functions-host/issues/2554#issuecomment-392084583)。

> [!NOTE]
> ID だけでクエリを実行する必要がある場合、[前の例](#http-trigger-look-up-id-from-query-string-c)のように、参照を使用することをお勧めします。[要求ユニット](../cosmos-db/request-units.md)の消費が少なくなります。 ポイント読み取り操作 (GET) は ID によるクエリより[効率性に優れています](../cosmos-db/optimize-cost-queries.md)。
>

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocByIdFromRouteDataUsingSqlQuery
    {
        [FunctionName("DocByIdFromRouteDataUsingSqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = "todoitems2/{id}")]HttpRequest req,
            [CosmosDB("ToDoItems", "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "select * from ToDoItems r where r.id = {id}")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 クエリは、`SqlQuery` 属性プロパティで指定されます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class DocsBySqlQuery
    {
        [FunctionName("DocsBySqlQuery")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
                HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection",
                SqlQuery = "SELECT top 2 * FROM c order by c._ts desc")]
                IEnumerable<ToDoItem> toDoItems,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            foreach (ToDoItem toDoItem in toDoItems)
            {
                log.LogInformation(toDoItem.Description);
            }
            return new OkResult();
        }
    }
}

```

<a id="http-trigger-get-multiple-docs-using-documentclient-c"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# 関数](functions-dotnet-class-library.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 コードでは、Azure Cosmos DB バインドによって提供される `DocumentClient` インスタンスを使用して、ドキュメントの一覧を読み取ります。 また、`DocumentClient` インスタンスは、書き込み操作に使用できます。

> [!NOTE]
> [IDocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.idocumentclient?view=azure-dotnet) インターフェイスを使用してテストを容易にすることもできます。

```cs
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace CosmosDBSamplesV2
{
    public static class DocsByUsingDocumentClient
    {
        [FunctionName("DocsByUsingDocumentClient")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post",
                Route = null)]HttpRequest req,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")] DocumentClient client,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            var searchterm = req.Query["searchterm"];
            if (string.IsNullOrWhiteSpace(searchterm))
            {
                return (ActionResult)new NotFoundResult();
            }

            Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");

            log.LogInformation($"Searching for: {searchterm}");

            IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
                .Where(p => p.Description.Contains(searchterm))
                .AsDocumentQuery();

            while (query.HasMoreResults)
            {
                foreach (ToDoItem result in await query.ExecuteNextAsync())
                {
                    log.LogInformation(result.Description);
                }
            }
            return new OkResult();
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

このセクションには、次の例が含まれています。

* [キュー トリガー、文字列からの ID の検索](#queue-trigger-look-up-id-from-string-c-script)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-c-script)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-c-script)
* [HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-sqlquery-c-script)
* [HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得](#http-trigger-get-multiple-docs-using-documentclient-c-script)

HTTP トリガーの例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-look-up-id-from-string-c-script"></a>

### <a name="queue-trigger-look-up-id-from-string"></a>キュー トリガー、文字列からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger}",
    "partitionKey": "{partition key value}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
}
```
これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    using System;

    // Change input document contents using Azure Cosmos DB input binding
    public static void Run(string myQueueItem, dynamic inputDocument)
    {
      inputDocument.text = "This has changed.";
    }
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```csharp
    public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
    {
        foreach (var doc in documents)
        {
            // operate on each document
        }
    }

    public class QueuePayload
    {
        public string departmentId { get; set; }
    }
```

<a id="http-trigger-look-up-id-from-query-string-c-script"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey" : "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-look-up-id-from-route-data-c-script"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, ToDoItem toDoItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    if (toDoItem == null)
    {
         log.LogInformation($"ToDo item not found");
    }
    else
    {
        log.LogInformation($"Found ToDo item, Description={toDoItem.Description}");
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-sqlquery-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 クエリは、`SqlQuery` 属性プロパティで指定されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "sqlQuery": "SELECT top 2 * FROM c order by c._ts desc"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage Run(HttpRequestMessage req, IEnumerable<ToDoItem> toDoItems, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    foreach (ToDoItem toDoItem in toDoItems)
    {
        log.LogInformation(toDoItem.Description);
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

<a id="http-trigger-get-multiple-docs-using-documentclient-c-script"></a>

### <a name="http-trigger-get-multiple-docs-using-documentclient"></a>HTTP トリガー、DocumentClient を使用した複数のドキュメントの取得

次の例は、ドキュメントの一覧を取得する [C# スクリプト関数](functions-reference-csharp.md)を示しています。 関数は、HTTP 要求によってトリガーされます。 コードでは、Azure Cosmos DB バインドによって提供される `DocumentClient` インスタンスを使用して、ドキュメントの一覧を読み取ります。 また、`DocumentClient` インスタンスは、書き込み操作に使用できます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "client",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "inout"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.Documents.Client"

using System.Net;
using Microsoft.Azure.Documents.Client;
using Microsoft.Azure.Documents.Linq;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, DocumentClient client, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("ToDoItems", "Items");
    string searchterm = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "searchterm", true) == 0)
        .Value;

    if (searchterm == null)
    {
        return req.CreateResponse(HttpStatusCode.NotFound);
    }

    log.LogInformation($"Searching for word: {searchterm} using Uri: {collectionUri.ToString()}");
    IDocumentQuery<ToDoItem> query = client.CreateDocumentQuery<ToDoItem>(collectionUri)
        .Where(p => p.Description.Contains(searchterm))
        .AsDocumentQuery();

    while (query.HasMoreResults)
    {
        foreach (ToDoItem result in await query.ExecuteNextAsync())
        {
            log.LogInformation(result.Description);
        }
    }
    return req.CreateResponse(HttpStatusCode.OK);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

このセクションには、次の例が含まれています。各例では、さまざまなソースから ID 値を指定して単一のドキュメントを読み取ります。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-javascript)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-javascript)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-javascript)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-javascript)

<a id="queue-trigger-look-up-id-from-json-javascript"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "inputDocumentIn",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "inputDocumentOut",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    // Change input document contents using Azure Cosmos DB input binding, using context.bindings.inputDocumentOut
    module.exports = function (context) {
    context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
    context.bindings.inputDocumentOut.text = "This was updated!";
    context.done();
    };
```

<a id="http-trigger-look-up-id-from-query-string-javascript"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [JavaScript 関数](functions-reference-node.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="http-trigger-look-up-id-from-route-data-javascript"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [JavaScript 関数](functions-reference-node.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItem",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false
}
```

JavaScript コードを次に示します。

```javascript
module.exports = function (context, req, toDoItem) {
    context.log('JavaScript queue trigger function processed work item');
    if (!toDoItem)
    {
        context.log("ToDo item not found");
    }
    else
    {
        context.log("Found ToDo item, Description=" + toDoItem.Description);
    }

    context.done();
};
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-javascript"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    module.exports = function (context, input) {
        var documents = context.bindings.documents;
        for (var i = 0; i < documents.length; i++) {
            var document = documents[i];
            // operate on each document
        }
        context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

このセクションには、次の例が含まれています。各例では、さまざまなソースから ID 値を指定して単一のドキュメントを読み取ります。

* [キュー トリガー、JSON からの ID の検索](#queue-trigger-look-up-id-from-json-python)
* [HTTP トリガー、クエリ文字列からの ID の検索](#http-trigger-look-up-id-from-query-string-python)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-python)
* [キュー トリガー、SqlQuery を使用した複数のドキュメントの取得](#queue-trigger-get-multiple-docs-using-sqlquery-python)

<a id="queue-trigger-look-up-id-from-json-python"></a>

### <a name="queue-trigger-look-up-id-from-json"></a>キュー トリガー、JSON からの ID の検索

次の例は、*function.json* ファイルの Cosmos DB 入力バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、1 つのドキュメントを読み取って、そのドキュメントのテキスト値を更新します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "id" : "{queueTrigger_payload_property}",
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "in"
},
{
    "name": "$return",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": false,
    "partitionKey": "{queueTrigger_payload_property}",
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import azure.functions as func


def main(queuemsg: func.QueueMessage, documents: func.DocumentList) -> func.Document:
    if documents:
        document = documents[0]
        document['text'] = 'This was updated!'
        return document
```

<a id="http-trigger-look-up-id-from-query-string-python"></a>

### <a name="http-trigger-look-up-id-from-query-string"></a>HTTP トリガー、クエリ文字列からの ID の検索

次の例は、単一のドキュメントを取得する [Python 関数](functions-reference-python.md)を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "in",
      "Id": "{Query.id}",
      "PartitionKey": "{Query.partitionKeyValue}"
    }
  ],
  "disabled": true,
  "scriptFile": "__init__.py"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])

    return 'OK'
```

<a id="http-trigger-look-up-id-from-route-data-python"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する [Python 関数](functions-reference-python.md)を示しています。 関数は、ルート データを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションから `ToDoItem` ドキュメントを取得するために使用されます。

*function.json* ファイルを次に示します。

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get",
        "post"
      ],
      "route":"todoitems/{partitionKeyValue}/{id}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    },
    {
      "type": "cosmosDB",
      "name": "todoitems",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connection": "CosmosDBConnection",
      "direction": "in",
      "Id": "{id}",
      "PartitionKey": "{partitionKeyValue}"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Python コードを次に示します。

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest, todoitems: func.DocumentList) -> str:
    if not todoitems:
        logging.warning("ToDo item not found")
    else:
        logging.info("Found ToDo item, Description=%s",
                     todoitems[0]['description'])
    return 'OK'
```

<a id="queue-trigger-get-multiple-docs-using-sqlquery-python"></a>

### <a name="queue-trigger-get-multiple-docs-using-sqlquery"></a>キュー トリガー、SqlQuery を使用した複数のドキュメントの取得

次の例は、*function.json* ファイルの Azure Cosmos DB 入力バインドと、そのバインドが使用される [Python 関数](functions-reference-python.md)を示しています。 この関数は、SQL クエリで指定されている複数のドキュメントを、キュー トリガーを使用してクエリ パラメーターをカスタマイズすることで取得します。

キュー トリガーがパラメーター `departmentId` を提供します。 `{ "departmentId" : "Finance" }` のキュー メッセージは、金融部門のすべてのレコードを返します。

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "documents",
    "type": "cosmosDB",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}",
    "connectionStringSetting": "CosmosDBConnection"
}
```

これらのプロパティについては、「[構成](#input---configuration)」セクションを参照してください。

Python コードを次に示します。

```python
import azure.functions as func

def main(queuemsg: func.QueueMessage, documents: func.DocumentList):
    for document in documents:
        # operate on each document
```

# <a name="java"></a>[Java](#tab/java)

このセクションには、次の例が含まれています。

* [HTTP トリガー、クエリ文字列からの ID の検索 - String パラメーター](#http-trigger-look-up-id-from-query-string---string-parameter-java)
* [HTTP トリガー、クエリ文字列からの ID の検索 - POJO パラメーター](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)
* [HTTP トリガー、ルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-java)
* [HTTP トリガー、SqlQuery を使用したルート データからの ID の検索](#http-trigger-look-up-id-from-route-data-using-sqlquery-java)
* [HTTP トリガー、ルート データからの複数のドキュメントの取得、SqlQuery を使用](#http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```java
public class ToDoItem {

  private String id;
  private String description;

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }

  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}
```

<a id="http-trigger-look-up-id-from-query-string---string-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---string-parameter"></a>HTTP トリガー、クエリ文字列からの ID の検索 - String パラメーター

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを String 形式で取得するために使用されます。

```java
public class DocByIdFromQueryString {

    @FunctionName("DocByIdFromQueryString")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に由来する関数パラメーター上で `@CosmosDBInput` 注釈を使用します。  この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

<a id="http-trigger-look-up-id-from-query-string---pojo-parameter-java"></a>

### <a name="http-trigger-look-up-id-from-query-string---pojo-parameter"></a>HTTP トリガー、クエリ文字列からの ID の検索 - POJO パラメーター

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、クエリ文字列を使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを取得するために使用されます。 このドキュメントはその後、前に作成した ```ToDoItem``` POJO のインスタンスに変換され、引数として関数に渡されます。

```java
public class DocByIdFromQueryStringPojo {

    @FunctionName("DocByIdFromQueryStringPojo")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{Query.id}",
              partitionKey = "{Query.partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Item from the database is " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

<a id="http-trigger-look-up-id-from-route-data-java"></a>

### <a name="http-trigger-look-up-id-from-route-data"></a>HTTP トリガー、ルート データからの ID の検索

次の例は、単一のドキュメントを取得する Java 関数を示しています。 関数は、ルート パラメーターを使用して検索のための ID とパーティション キー値を指定する HTTP 要求によってトリガーされます。 その ID とパーティション キー値は、指定されたデータベースとコレクションからドキュメントを取得し、```Optional<String>``` として返すために使用されます。

```java
public class DocByIdFromRoute {

    @FunctionName("DocByIdFromRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems/{partitionKeyValue}/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              id = "{id}",
              partitionKey = "{partitionKeyValue}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            Optional<String> item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("String from the database is " + (item.isPresent() ? item.get() : null));

        // Convert and display
        if (!item.isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            // return JSON from Cosmos. Alternatively, we can parse the JSON string
            // and return an enriched JSON object.
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item.get())
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-look-up-id-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-look-up-id-from-route-data-using-sqlquery"></a>HTTP トリガー、SqlQuery を使用したルート データからの ID の検索

次の例は、単一のドキュメントを取得する Java 関数を示しています。 この関数は、ルート パラメーターを使用して検索する ID を指定する HTTP 要求によってトリガーされます。 クエリ条件によっては多数のドキュメントが返される可能性があるため、その ID は、指定されたデータベースとコレクションからドキュメントを取得し、結果セットを ```ToDoItem[]``` に変換するために使用されます。

> [!NOTE]
> ID だけでクエリを実行する必要がある場合、[前の例](#http-trigger-look-up-id-from-query-string---pojo-parameter-java)のように、参照を使用することをお勧めします。[要求ユニット](../cosmos-db/request-units.md)の消費が少なくなります。 ポイント読み取り操作 (GET) は ID によるクエリより[効率性に優れています](../cosmos-db/optimize-cost-queries.md)。
>

```java
public class DocByIdFromRouteSqlQuery {

    @FunctionName("DocByIdFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems2/{id}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where r.id = {id}",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] item,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Items from the database are " + item);

        // Convert and display
        if (item == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(item)
                          .build();
        }
    }
}
 ```

 <a id="http-trigger-get-multiple-docs-from-route-data-using-sqlquery-java"></a>

### <a name="http-trigger-get-multiple-docs-from-route-data-using-sqlquery"></a>HTTP トリガー、ルート データからの複数のドキュメントの取得、SqlQuery を使用

次の例は、複数のドキュメントを取得する Java 関数を示しています。 この関数は、ルート パラメーター ```desc``` を使用して ```description``` フィールドで検索する文字列を指定する HTTP 要求によってトリガーされます。 この検索用語は、指定されたデータベースとコレクションからドキュメントのコレクションを取得し、結果セットを ```ToDoItem[]``` に変換して、それを引数として関数に渡すために使用されます。

```java
public class DocsFromRouteSqlQuery {

    @FunctionName("DocsFromRouteSqlQuery")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET},
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "todoitems3/{desc}")
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBInput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              sqlQuery = "select * from Items r where contains(r.description, {desc})",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            ToDoItem[] items,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());
        context.getLogger().info("Number of items from the database is " + (items == null ? 0 : items.length));

        // Convert and display
        if (items == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("No documents found.")
                          .build();
        }
        else {
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(items)
                          .build();
        }
    }
}
 ```

 ---

## <a name="input---attributes-and-annotations"></a>入力 - 属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、[この後の構成に関するセクション](#input---configuration)をご覧ください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)から、Cosmos DB に書き込むパラメーターに `@CosmosDBOutput` 注釈を使用します。 注釈パラメーターの型は `OutputBinding<T>` にするようにします。ここで、`T` は Java のネイティブ型または POJO のどちらかです。

---

## <a name="input---configuration"></a>入力 - 構成

次の表は、*function.json* ファイルと `CosmosDB` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**     | 300 | `cosmosDB` に設定する必要があります。        |
|**direction**     | 300 | `in` に設定する必要があります。         |
|**name**     | 300 | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** |**DatabaseName** |ドキュメントを含むデータベース。        |
|**collectionName** |**CollectionName** | ドキュメントを含むコレクションの名前。 |
|**id**    | **Id** | 取得するドキュメントの ID。 このプロパティは、[バインド式](./functions-bindings-expressions-patterns.md)をサポートしています。 `id` プロパティと **sqlQuery** プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。 |
|**sqlQuery**  |**SqlQuery**  | 複数のドキュメントを取得するときに使用する Azure Cosmos DB SQL クエリ。 このプロパティは、次の例のように実行時のバインドをサポートします。`SELECT * FROM c where c.departmentId = {departmentId}` `id` と `sqlQuery` プロパティの両方は設定しないでください。 いずれも設定しない場合は、コレクション全体が取得されます。|
|**connectionStringSetting**     |**ConnectionStringSetting**|Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |
|**partitionKey**|**PartitionKey**|参照用のパーティション キー値を指定します。 バインディング パラメーターを含めることもできます。 [パーティション分割](../cosmos-db/partition-data.md#logical-partitions)されたコレクションの参照で必須です。|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="input---usage"></a>入力 - 使用方法

# <a name="c"></a>[C#](#tab/csharp)

関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

関数が正常に終了したときに、名前付き入力パラメーターを介した入力ドキュメントへの変更がすべて自動的に保持されます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

関数の終了時に更新が自動的に行われることはありません。 代わりに、`context.bindings.<documentName>In` と `context.bindings.<documentName>Out` を使用して、更新を行います。 JavaScript の例を参照してください。

# <a name="python"></a>[Python](#tab/python)

データは、`DocumentList` パラメーターを介して関数から使用できるようになります。 ドキュメントに加えられた変更は、自動的には保存されません。

# <a name="java"></a>[Java](#tab/java)

[Java 関数ランタイム ライブラリ](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)の [@CosmosDBInput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.cosmosdbinput) 注釈によって Cosmos DB データは関数に公開されます。 この注釈は、Java のネイティブ型、POJO、または `Optional<T>` を使用した null 許容値で使用できます。

---

## <a name="output"></a>Output

Azure Cosmos DB 出力バインドを使用すると、SQL API を使って Azure Cosmos DB データベースに新しいドキュメントを記述できます。

# <a name="c"></a>[C#](#tab/csharp)

このセクションには、次の例が含まれています。

* [キュー トリガー、1 つのドキュメントの書き込み](#queue-trigger-write-one-doc-c)
* [キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み](#queue-trigger-write-docs-using-iasynccollector-c)

例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>キュー トリガー、1 つのドキュメントの書き込み

次の例は、キュー ストレージからのメッセージで提供されるデータを使用して、ドキュメントをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み

次の例は、キュー メッセージ JSON で提供されるデータを使用して、ドキュメントのコレクションをデータベースに追加する[ C# 関数](functions-dotnet-class-library.md)を示しています。

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

このセクションには、次の例が含まれています。

* [キュー トリガー、1 つのドキュメントの書き込み](#queue-trigger-write-one-doc-c-script)
* [キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>キュー トリガー、1 つのドキュメントの書き込み

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [C# スクリプト関数](functions-reference-csharp.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

C# スクリプト コードを次に示します。

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>キュー トリガー、IAsyncCollector を使用したドキュメントの書き込み

複数のドキュメントを作成するために `ICollector<T>` または `IAsyncCollector<T>` にバインドできます。`T` は、サポートされている型のいずれかです。

この例では、次のようなシンプルな `ToDoItem` タイプを参照します。

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

function.json ファイルを次に示します。

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# スクリプト コードを次に示します。

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイルの Azure Cosmos DB 出力バインドと、そのバインドが使用される [JavaScript 関数](functions-reference-node.md)を示しています。 この関数は、次の形式で JSON を受信するキューのキュー入力バインドを使用します。

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

この関数は、各レコードに対して次の形式の Azure Cosmos DB ドキュメントを作成します。

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* ファイルのバインディング データを次に示します。

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

これらのプロパティについては、「[構成](#output---configuration)」セクションを参照してください。

JavaScript コードを次に示します。

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

次の例は、関数の出力としてドキュメントを Azure CosmosDB データベースに書き込む方法を示しています。

バインディング定義は *function.json* で定義され、そこで *type* は `cosmosDB` に設定されます。

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

データベースに書き込むには、ドキュメント オブジェクトをデータベース パラメーターの `set` メソッドのに渡します。

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [キュー トリガー、戻り値を使用したメッセージのデータベースへの保存](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP トリガー、戻り値を使用した 1 つのドキュメントのデータベースへの保存](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP トリガー、OutputBinding を使用した 1 つのドキュメントのデータベースへの保存](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP トリガー、OutputBinding を使用した複数のドキュメントのデータベースへの保存](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>キュー トリガー、戻り値を使用したメッセージのデータベースへの保存

次の例は、キュー ストレージのメッセージからのデータを使用して、ドキュメントをデータベースに追加する Java 関数を示しています。

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP トリガー、戻り値を使用した 1 つのドキュメントのデータベースへの保存

次の例は、シグネチャに ```@CosmosDBOutput``` の注釈が付けられ、型 ```String``` の戻り値を持つ Java 関数を示しています。 この関数によって返された JSON ドキュメントは、対応する CosmosDB コレクションに自動的に書き込まれます。

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP トリガー、OutputBinding を使用した 1 つのドキュメントのデータベースへの保存

次の例は、```OutputBinding<T>``` 出力パラメーターを使用して CosmosDB にドキュメントを書き込む Java 関数を示しています。 この例では、```outputItem``` パラメーターに関数シグネチャではなく ```@CosmosDBOutput``` の注釈を付ける必要があります。 ```OutputBinding<T>``` を使用すると、関数で CosmosDB にドキュメントを書き込むためのバインディングを利用しながら、関数の呼び出し元に JSON または XML ドキュメントなどの異なる値を返すこともできるようになります。

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP トリガー、OutputBinding を使用した複数のドキュメントのデータベースへの保存

次の例は、```OutputBinding<T>``` 出力パラメーターを使用して CosmosDB に複数のドキュメントを書き込む Java 関数を示しています。 この例では、```outputItem``` パラメーターには関数シグネチャではなく ```@CosmosDBOutput``` の注釈が付けられています。 出力パラメーター ```outputItem``` には、そのテンプレート パラメーターの型として ```ToDoItem``` オブジェクトの一覧が含まれています。 ```OutputBinding<T>``` を使用すると、関数で CosmosDB にドキュメントを書き込むためのバインディングを利用しながら、関数の呼び出し元に JSON または XML ドキュメントなどの異なる値を返すこともできるようになります。

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

[Java 関数ランタイム ライブラリ](/java/api/overview/azure/functions/runtime)で、その値が Cosmos DB に書き込まれるパラメーター上で `@CosmosDBOutput` 注釈を使用します。  注釈パラメーターの型は ```OutputBinding<T>``` である必要があります。ここで、T は Java のネイティブ型または POJO のどちらかです。

---

## <a name="output---attributes-and-annotations"></a>出力 - 属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 属性を使用します。

この属性のコンストラクターにはデータベース名とコレクションを指定します。 これらの設定や構成できる他のプロパティについて詳しくは、「[出力 - 構成](#output---configuration)」をご覧ください。 メソッド シグネチャでの `CosmosDB` 属性の例を次に示します。

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

属性は、Python ではサポートされていません。

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput` 注釈は、Cosmos DB にデータを書き込むために使用できます。 関数または個々の関数パラメーターに注釈を適用できます。 関数のメソッドで使用する場合、関数の戻り値は Cosmos DB に書き込まれるものです。 パラメーターで注釈を使用する場合、パラメーターの型は `OutputBinding<T>` として宣言する必要があります。ここで `T` はネイティブ Java 型または POJO です。

---

## <a name="output---configuration"></a>出力 - 構成

次の表は、*function.json* ファイルと `CosmosDB` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type**     | 300 | `cosmosDB` に設定する必要があります。        |
|**direction**     | 300 | `out` に設定する必要があります。         |
|**name**     | 300 | 関数のドキュメントを表すバインド パラメーターの名前。  |
|**databaseName** | **DatabaseName**|ドキュメントが作成されたコレクションを含むデータベース。     |
|**collectionName** |**CollectionName**  | ドキュメントが作成されたコレクションの名前。 |
|**createIfNotExists**  |**CreateIfNotExists**    | コレクションが存在しないときに作成するかどうかを示すブール値。 既定値は *false* です。新しいコレクションは予約済みのスループットで作成され、これが価格に影響を及ぼすためです。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。  |
|**partitionKey**|**PartitionKey** |`CreateIfNotExists` が true の場合に、作成されるコレクションのパーティション キー パスを定義します。|
|**collectionThroughput**|**CollectionThroughput**| `CreateIfNotExists` が true の場合に、作成されるコレクションの[スループット](../cosmos-db/set-throughput.md)を定義します。|
|**connectionStringSetting**    |**ConnectionStringSetting** |Azure Cosmos DB 接続文字列を含むアプリ設定の名前。        |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>出力 - 使用方法

既定では、関数の出力パラメーターに書き込むと、ドキュメントがデータベースに作成されます。 このドキュメントには、自動的に生成された GUID がドキュメント ID として割り当てられます。 出力ドキュメントのドキュメント ID を指定するには、出力パラメーターに渡された JSON オブジェクトで `id` プロパティを指定します。

> [!Note]
> 既存のドキュメントの ID を指定した場合、既存のドキュメントは新しい出力ドキュメントによって上書きされます。

## <a name="exceptions-and-return-codes"></a>例外とリターン コード

| バインド | リファレンス |
|---|---|
| Cosmos DB | [CosmosDB のエラー コード](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 設定

このセクションでは、バージョン 2.x でこのバインディングに使用可能なグローバル構成設定について説明します。 バージョン 2.x でのグローバル構成設定の詳細については、[Azure Functions バージョン 2.x の host.json のリファレンス](functions-host-json.md)を参照してください。

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|GatewayMode|Gateway|Azure Cosmos DB サービスに接続する際に関数で使用される接続モード。 オプションは `Direct` と `Gateway` です|
|Protocol|Https|Azure Cosmos DB サービスに接続する際に関数で使用される接続プロトコル。  両方のモードの説明については[こちら](../cosmos-db/performance-tips.md#networking)を参照してください|
|leasePrefix|300|アプリ内のすべての関数で使用するプレフィックスをリースします。|

## <a name="next-steps"></a>次のステップ

* [Cosmos DB のサーバーレス データベース コンピューティングの詳細情報](../cosmos-db/serverless-computing-database.md)
* [Azure Functions のトリガーとバインドの詳細情報](functions-triggers-bindings.md)

<!---
> [!div class="nextstepaction"]
> [Go to a quickstart that uses a Cosmos DB trigger](functions-create-cosmos-db-triggered-function.md)
--->
