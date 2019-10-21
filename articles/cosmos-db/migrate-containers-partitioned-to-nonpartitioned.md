---
title: Azure Cosmos の非パーティション分割コンテナーをパーティション分割コンテナーに移行する
description: 既存のすべての非パーティション分割コンテナーをパーティション分割コンテナーに移行する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 3a13f8928ba243195c30200dae0525e72c1c161b
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71844397"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>非パーティション分割コンテナーをパーティション分割コンテナーに移行する

Azure Cosmos DB では、パーティション キーのないコンテナーの作成がサポートされています。 現在は、Azure CLI とバージョン 2.x 以下の Azure Cosmos DB SDK (.Net、Java、NodeJs) を使って、非パーティション分割コンテナーを作成できます。 Azure portal を使って非パーティション分割コンテナーを作成することはできません。 ただし、そのような非パーティション分割コンテナーはエラスティックではなく、ストレージ容量 10 GB およびスループット制限 10 K RU/秒の固定です。

非パーティション分割コンテナーはレガシであり、ストレージとスループットをスケーリングするには、既存の非パーティション分割コンテナーをパーティション分割コンテナーに移行する必要があります。 Azure Cosmos DB では、非パーティション分割コンテナーをパーティション分割コンテナーに移行するためのメカニズムが、システムで定義されています。 このドキュメントでは、既存のすべての非パーティション分割コンテナーをパーティション分割コンテナーに自動的に移行する方法について説明します。 自動移行機能は、どの言語でも V3 バージョンの SDK を使っている場合にのみ利用できます。

> [!NOTE]
> 現時点では、このドキュメントで説明されている手順を使って、Azure Cosmos DB の MongoDB および Gremlin API アカウントを移行することはできません。

## <a name="migrate-container-using-the-system-defined-partition-key"></a>システム定義のパーティション キーを使用してコンテナーを移行する

Azure Cosmos DB では、移行をサポートするため、パーティション キーを持たないすべてのコンテナーにおいて、`/_partitionkey` という名前のシステム定義のパーティション キーが用意されています。 コンテナーが移行された後で、パーティション キーの定義を変更することはできません。 たとえば、パーティション分割コンテナーに移行されたコンテナーの定義は次のようになります。

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

コンテナーが移行された後、`_partitionKey` プロパティと共にドキュメントの他のプロパティを設定することにより、ドキュメントを作成できます。 `_partitionKey` プロパティは、ドキュメントのパーティション キーを表します。

適切なパーティション キーを選択することは、プロビジョニング済みスループットを最適に利用するために重要です。 詳しくは、[パーティション キーの選択方法](partitioning-overview.md)に関する記事をご覧ください。

> [!NOTE]
> システム定義のパーティション キーは、どの言語でも最新/V3 バージョンの SDK を使っている場合にのみ利用できます。

次の例では、システム定義のパーティション キーでドキュメントを作成し、そのドキュメントを読み取るサンプル コードを示します。

**ドキュメントの JSON 表現**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

完全なサンプルについては、[.Net サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)の GitHub リポジトリをご覧ください。
                      
## <a name="migrate-the-documents"></a>ドキュメントを移行する

コンテナー定義がパーティション キー プロパティで拡張されている間は、コンテナー内のドキュメントは自動的に移行されません。 つまり、システム パーティション キー プロパティのパス `/_partitionKey` は、既存のドキュメントには自動的に追加されません。 パーティション キーなしで作成されたドキュメントを読み取り、`_partitionKey` プロパティを指定してドキュメントに書き戻すことによって、既存のドキュメントを再パーティション分割する必要があります。

## <a name="access-documents-that-dont-have-a-partition-key"></a>パーティション キーを持たないドキュメントにアクセスする

アプリケーションでは、"PartitionKey.None" という特殊なシステム プロパティを使って、パーティション キーを持たない既存のドキュメントにアクセスできます。これは、移行されないドキュメントの値です。 このプロパティは、すべての CRUD とクエリ操作で使用できます。 次の例では、NonePartitionKey から 1 つのドキュメントを読み取るサンプルを示します。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

ドキュメントを再パーティション分割する方法の完全なサンプルについては、[.Net サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)の GitHub リポジトリをご覧ください。 

## <a name="compatibility-with-sdks"></a>SDK との互換性

V2.x.x や V1.x.x などの古いバージョンの Azure Cosmos DB SDK では、システム定義のパーティション キー プロパティはサポートされていません。 そのため、古い SDK からコンテナー定義を読み取ると、パーティション キーの定義は含まれず、これらのコンテナーは以前とまったく同様に動作します。 古いバージョンの SDK でビルドされたアプリケーションは、何も変更されないので、引き続き非パーティション分割で動作します。 

移行されたコンテナーが最新/V3 バージョンの SDK によって使用され、ユーザーが新しいドキュメント内のシステム定義パーティション キーの設定を開始すると、古い SDK からそのようなドキュメントにアクセスする (読み取り、更新、削除、クエリ) ことはできなくなります。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB でのパーティション分割](partitioning-overview.md)
* [Azure Cosmos DB の要求ユニット](request-units.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md)
* [Azure Cosmos アカウントの使用](account-overview.md)