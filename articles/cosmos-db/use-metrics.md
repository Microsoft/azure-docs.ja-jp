---
title: Azure Cosmos DB のメトリックを使用した監視とデバッグ
description: Azure Cosmos DB のメトリックを使用して、一般的な問題をデバッグし、データベースを監視します。
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 24f321e3c3c0fe8e85633edb505879874e8c772f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019234"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB のメトリックを使用した監視とデバッグ

Azure Cosmos DB には、スループット、ストレージ、整合性、可用性、および待機時間のメトリックが用意されています。 Azure portal では、これらのメトリックの集計ビューが提供されます。 Azure Monitor API から Azure Cosmos DB メトリックを表示することもできます。 メトリックのディメンション値 (コンテナー名など) は、大文字と小文字が区別されません。 そのため、これらのディメンション値に対して文字列比較を行う場合は、大文字と小文字を区別しない比較を使用する必要があります。 Azure Monitor からメトリックを表示する方法の詳細については、[Azure Monitor からのメトリックの取得](cosmos-db-azure-monitor-metrics.md)に関する記事を参照してください。

この記事では、一般的なユース ケースと、Azure Cosmos DB メトリックを使用してこれらの問題を分析およびデバッグする手順について説明します。 メトリックは 5 分間隔で収集され、7 日間保持されます。

## <a name="view-metrics-from-azure-portal"></a>Azure portal からメトリックを表示する

1. [Azure portal](https://portal.azure.com/) にサインインします

1. **[メトリック]** ウィンドウを開きます。 既定では、[メトリック] ウィンドウには、Azure Cosmos アカウント内のすべてのデータベースのストレージ、インデックス、要求単位のメトリックが表示されます。 これらのメトリックをデータベース、コンテナー、またはリージョン別にフィルター処理することができます。 特定の時間の粒度でメトリックをフィルター処理することもできます。 スループット、ストレージ、可用性、待機時間、および一貫性の各メトリックの詳細が、別々のタブで提供されます。 

   :::image type="content" source="./media/use-metrics/performance-metrics.png" alt-text="Azure portal での Cosmos DB のパフォーマンス メトリック":::

**[メトリック]** ウィンドウから、次のメトリックを入手できます。 

* **スループット メトリック** - このメトリックでは、消費された要求の数、またはコンテナーに対してプロビジョニングされたスループットまたはストレージの容量を超過しているために失敗した (応答コード 429) 要求の数が示されます。

* **ストレージ メトリック** - このメトリックでは、データのサイズとインデックスの使用状況が示されます。

* **可用性メトリック** - このメトリックでは、1 時間あたりの要求の合計に対する成功した要求の割合が示されます。 成功率は、Azure Cosmos DB の SLA によって定義されます。

* **待機時間メトリック** - このメトリックでは、アカウントが動作しているリージョンで Azure Cosmos DB によって観察された読み取りと書き込みの待機時間が示されます。 Geo レプリケートされたアカウントのリージョン間での待機時間を視覚化することができます。 このメトリックでは、エンド ツー エンドの要求の待機時間は表されません。

* **整合性メトリック** - このメトリックでは、選択した整合性モデルの最終的な整合性が示されます。 マルチリージョン アカウントでは、このメトリックには、選択したリージョン間でのレプリケーションの待機時間も示されます。

* **システム メトリック** - このメトリックでは、マスター パーティションによって処理されているメタデータ要求の数が示されます。 スロットルされた要求を識別するためにも役立ちます。

次のセクションで、Azure Cosmos DB のメトリックを使用する一般的なシナリオについて説明します。 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>成功した要求数とエラーになった要求数の把握

まず [Azure Portal](https://portal.azure.com) を開き、 **[メトリック]** ブレードに移動します。 このブレードで、[1 分あたりに容量を超過した要求の数] グラフを見つけます。 このグラフには、状態コードで区分された毎分の合計要求が表示されます。 HTTP 状態コードの詳細については、「[HTTP Status Codes for Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」(Azure Cosmos DB の HTTP 状態コード) を参照してください。

最も一般的なエラー状態コードは 429 (レート制限/調整) です。 このエラーは、Azure Cosmos DB への要求がプロビジョニングされたスループットを超えることを意味します。 この問題の最も一般的な解決策は、そのコレクションの [RU をスケール アップ](./set-throughput.md)することです。

:::image type="content" source="media/use-metrics/metrics-12.png" alt-text="毎分の要求数":::

## <a name="determine-the-throughput-distribution-across-partitions"></a>パーティション全体のスループットの分散を決める

適切なカーディナリティのパーティション キーを持つことは、スケーラブルなアプリケーションのために重要です。 パーティションごとに分けられたパーティション コンテナーのスループットの分散を決めるには、[Azure Portal](https://portal.azure.com) の **[メトリック]** ブレードに移動します。 **[スループット]** タブの **[各物理パーティションによる 1 秒あたりの最大消費 RU]** グラフにストレージの内訳が表示されます。 次の図は、パーティションが左端に偏っていることでわかるように、データの分散が不適切な例です。

:::image type="content" source="media/use-metrics/metrics-17.png" alt-text="1 つのパーティションの使用率が高い":::

スループット分散が不均一の場合、*ホット* パーティションが発生します。また、その結果、要求が調整され、再パーティションが必要になる可能性があります。 Azure Cosmos DB でのパーティション分割の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](./partition-data.md)」を参照してください。

## <a name="determine-the-storage-distribution-across-partitions"></a>パーティション全体のストレージの分散を決める

適切なカーディナリティのパーティションを持つことは、スケーラブルなアプリケーションのために重要です。 パーティションごとに分けられたパーティション コンテナーのストレージの分散を決めるには、[Azure portal](https://portal.azure.com) の [メトリック] ブレードに移動します。 [ストレージ] タブの [上位パーティション キーで使用されるデータとインデックスのストレージ] グラフに、ストレージの内訳が表示されます。 次の図は、パーティションが左端に偏っていることでわかるように、データ ストレージの分散が不適切なことを示しています。

:::image type="content" source="media/use-metrics/metrics-07.png" alt-text="不適切なデータ分散の例":::

グラフのパーティションをクリックすると、パーティション キーの分散が偏っている根本原因を確認できます。

:::image type="content" source="media/use-metrics/metrics-05.png" alt-text="パーティション キーによる分散の偏り":::

分散の偏りの原因となっているパーティション キーを特定した後は、必要に応じて、より分散されたパーティション キーでコンテナーを再パーティションします。 Azure Cosmos DB でのパーティション分割の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](./partition-data.md)」を参照してください。

## <a name="compare-data-size-against-index-size"></a>データ サイズとインデックス サイズを比較する

Azure Cosmos DB の合計使用ストレージは、データ サイズとインデックス サイズ両方の組み合わせです。 通常、インデックス サイズは、データ サイズよりもはるかに小さいサイズです。 [Azure Portal](https://portal.azure.com) の [メトリック] ブレードの [ストレージ] タブには、データとインデックスに基づくストレージ使用量の内訳が表示されます。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

インデックス領域を節約するには、[インデックス ポリシー](index-policy.md)を調整します。

## <a name="debug-why-queries-are-running-slow"></a>クエリの実行速度が遅い原因をデバッグする

SQL API SDK の Azure Cosmos DB は、クエリ実行の統計情報を提供します。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* は、クエリの各コンポーネントが実行にかかった時間について詳細情報を提供します。 クエリの時間が長くなる最も一般的な根本原因はスキャンです。つまり、クエリがインデックスを利用できなかったことを示します。 この問題は、フィルター条件を修正することで解決できます。

## <a name="next-steps"></a>次のステップ

Azure portal で提供されているメトリックを使用して、問題の監視とデバッグを行う方法について説明しました。 データベースのパフォーマンスを改善する方法については、次の記事を参照してください。

* Azure Monitor からメトリックを表示する方法の詳細については、[Azure Monitor からのメトリックの取得](cosmos-db-azure-monitor-metrics.md)に関する記事を参照してください。 
* [Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)
* [Azure Cosmos DB のパフォーマンスに関するヒント](performance-tips.md)
