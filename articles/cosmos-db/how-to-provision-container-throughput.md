---
title: Azure Cosmos DB のコンテナーのスループットをプロビジョニングする
description: Azure portal、CLI、PowerShell および他のさまざまな SDK を使用して、Azure Cosmos DB のコンテナー レベルでスループットをプロビジョニングする方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: mjbrown
ms.openlocfilehash: 6d163609d505516514f078e5d5374c20b5a24e89
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77585585"
---
# <a name="provision-throughput-on-an-azure-cosmos-container"></a>Azure Cosmos コンテナー上でのスループットをプロビジョニングする

この記事では、Azure Cosmos DB のコンテナー (コレクション、グラフ、またはテーブル) のスループットをプロビジョニングする方法について説明します。 スループットは、単一のコンテナーを対象にプロビジョニングできるほか、[データベースを対象にプロビジョニング](how-to-provision-database-throughput.md)して、それをデータベース内の複数のコンテナーで共有することもできます。 コンテナーのスループットは、Azure portal、Azure CLI、Azure Cosmos DB SDK のいずれかを使用してプロビジョニングできます。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. [新しい Azure Cosmos アカウントを作成する](create-sql-api-dotnet.md#create-account)か、既存の Azure Cosmos アカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて **[新しいコレクション]** を選択します。 次に、以下の詳細を指定します。

   * 新しいデータベースを作成するか、既存のデータベースを使用するかを指定します。
   * コンテナー (またはテーブル、グラフ) ID を入力します。
   * パーティション キーの値 (`/userid` など) を入力します。
   * プロビジョニングするスループットを入力します (例: 1,000 RU)。
   * **[OK]** を選択します。

    ![[新しいコレクション] が強調表示されている [データ エクスプローラー] のスクリーンショット](./media/how-to-provision-container-throughput/provision-container-throughput-portal-all-api.png)

## <a name="azure-cli-or-powershell"></a>Azure CLI または PowerShell

専用スループットを持つコンテナーの作成については、以下を参照してください。

* [Azure CLI を使用してコンテナーを作成する](manage-with-cli.md#create-a-container)
* [PowerShell を使用してコンテナーを作成する](manage-with-powershell.md#create-container)

> [!Note]
> MongoDB 用 Azure Cosmos DB API を使用して構成された Azure Cosmos DB アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myShardKey` を使用します。 Cassandra API を使用して構成された Azure Cosmos アカウントのコンテナーに対するスループットをプロビジョニングする場合は、パーティション キーのパスとして `/myPrimaryKey` を使用します。

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Cassandra API を除くすべての Cosmos DB API シリーズに対するスループットをプロビジョニングするには、SQL API 用の Cosmos SDK を使用します。

### <a id="dotnet-most"></a>SQL API、MongoDB API、Gremlin API、Table API
### <a name="net-v2-sdk"></a>.Net V2 SDK

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

### <a name="net-v3-sdk"></a>.Net V3 SDK

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs" id="ContainerCreateWithThroughput":::

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

### <a id="dotnet-cassandra"></a>Cassandra API

同様のコマンドは、CQL に準拠している任意のドライバーを使用して発行できます。

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
### <a name="alter-or-change-throughput-for-cassandra-table"></a>Cassandra テーブルのスループットの変更

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```


## <a name="next-steps"></a>次のステップ

Azure Cosmos DB でのスループットのプロビジョニングについては、次の記事を参照してください。

* [データベースのスループットをプロビジョニングする方法](how-to-provision-database-throughput.md)
* [Azure Cosmos DB における要求ユニットとスループット](request-units.md)
