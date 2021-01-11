---
title: Azure Cosmos DB でマルチマスターを構成する方法
description: Azure Cosmos DB で複数の SDK を使用して、お使いのアプリケーション用のマルチマスターを構成する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 94465e44a6cc2c4fa94ec8ea4504cbed80742f0f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019149"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Azure Cosmos DB を使用するアプリケーションでマルチマスターを構成する

複数の書き込みリージョンが有効なアカウントが作成されたら、お使いのアプリケーションで DocumentClient 用の ConnectionPolicy に対して 2 つの変更を行って、Azure Cosmos DB でマルチマスター機能とマルチホーミング機能を有効にする必要があります。 ConnectionPolicy の中で、UseMultipleWriteLocations を true に設定し、アプリケーションのデプロイ先となるリージョンの名前を SetCurrentLocation に渡します。 これにより、渡された場所との地理的な近接性に基づいて PreferredLocations プロパティが設定されます。 後で新しいリージョンがアカウントに追加された場合でも、アプリケーションの更新や再デプロイを行う必要はなく、近接するリージョンが自動的に検出され、リージョンのイベントが発生した場合は自動ホーミングが実行されます。

> [!Note]
> 初期構成が単一書き込みリージョンである Cosmos アカウントを、ダウンタイムなしで複数書き込みリージョン (つまりマルチマスター) に構成できます。 詳細については、[複数書き込みリージョンの構成](how-to-manage-database-account.md#configure-multiple-write-regions)に関する記事を参照してください。

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

アプリケーションでマルチマスターを有効にするには、`UseMultipleWriteLocations` を `true` に設定します。 また、`SetCurrentLocation` は、アプリケーションがデプロイされ、Azure Cosmos DB がレプリケートされているリージョンに設定します。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

アプリケーションでマルチマスターを有効にするには、`ApplicationRegion` を、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

必要な場合は、`CosmosClientBuilder` と `WithApplicationRegion` を使用して同じ結果を得ることもできます。

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a> Java V4 SDK

アプリケーションでマルチマスターを有効にするには、クライアント ビルダーで `.multipleWriteRegionsEnabled(true)` と `.preferredRegions(preferredRegions)` を呼び出します。このとき、`preferredRegions` は 1 つの要素を含む `List` です。つまり、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンです。

# <a name="async"></a>[非同期](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 非同期 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[同期](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md) (Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) 同期 API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a> Async Java V2 SDK

Java V2 SDK では Maven [com.microsoft.azure::azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) が使用されていました。 アプリケーションでマルチマスターを有効にするには、`policy.setUsingMultipleWriteLocations(true)` を設定し、`policy.setPreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js、JavaScript、および TypeScript SDK

アプリケーションでマルチマスターを有効にするには、`connectionPolicy.UseMultipleWriteLocations` を `true` に設定します。 また、`connectionPolicy.PreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

アプリケーションでマルチマスターを有効にするには、`connection_policy.UseMultipleWriteLocations` を `true` に設定します。 また、`connection_policy.PreferredLocations` は、アプリケーションがデプロイされ、Cosmos DB がレプリケートされているリージョンに設定します。

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>次のステップ

次の記事を参照してください。

* [セッション トークンを使用して Azure Cosmos DB で一貫性を管理する](how-to-manage-consistency.md#utilize-session-tokens)
* [Azure Cosmos DB の競合の種類と解決ポリシー](conflict-resolution-policies.md)
* [Azure Cosmos DB での高可用性](high-availability.md)
* [Azure Cosmos DB の整合性レベル](consistency-levels.md)
* [Azure Cosmos DB で適切な一貫性レベルを選択する](consistency-levels-choosing.md)
* [Azure Cosmos DB の一貫性、可用性、パフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [さまざまな整合性レベルでの可用性およびパフォーマンスのトレードオフ](consistency-levels-tradeoffs.md)
* [プロビジョニング スループットのグローバルなスケーリング](scaling-throughput.md)
* [グローバル配信: しくみ](global-dist-under-the-hood.md)
