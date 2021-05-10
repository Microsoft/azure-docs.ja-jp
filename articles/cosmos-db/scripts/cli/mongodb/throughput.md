---
title: MongoDB リソースに対する Azure Cosmos DB API のスループット (RU/秒) 操作の Azure CLI スクリプト
description: MongoDB リソースに対する Azure Cosmos DB API のスループット (RU/秒) 操作の Azure CLI スクリプト
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: fc477fdd842025722a2bcfcd0172ab9d57fb0d4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790716"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API のデータベースまたはグラフに対する Azure CLI でのスループット (RU/秒) 操作
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.12.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、共有スループットが設定された MongoDB データベースと専用スループットが設定されたコレクションが作成され、その後、両方のスループットが更新されます。 その後、スクリプトは標準スループットから自動スケーリング スループットに移行し、移行後に自動スケーリング スループットの値を読み取ります。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB アカウントを作成します。 |
| [az cosmosdb mongodb database create](/cli/azure/cosmosdb/mongodb/database#az_cosmosdb_mongodb_database_create) | Azure Cosmos MongoDB API データベースを作成します。 |
| [az cosmosdb mongodb collection create](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create) | Azure Cosmos MongoDB API コレクションを作成します。 |
| [az cosmosdb mongodb database throughput update](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_update) | Azure Cosmos MongoDB API データベースの RU を更新します。 |
| [az cosmosdb mongodb collection throughput update](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput_update) | Azure Cosmos MongoDB API コレクションの RU を更新します。 |
| [az cosmosdb mongodb database throughput migrate](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_migrate) | データベースのスループットを移行します。 |
| [az cosmosdb mongodb collection throughput migrate](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput-migrate) | コレクションのスループットを移行します。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB CLI の詳細については、[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)を参照してください。

Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。
