---
title: Azure Cosmos DB でデータベース アカウントを管理する方法について
description: Azure Cosmos DB でデータベース アカウントを管理する方法について
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: d3ee6db382f848a029f3e0355abdfe5eecc80656
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54035833"
---
# <a name="manage-database-accounts-in-azure-cosmos-db"></a>Azure Cosmos DB でデータベース アカウントを管理する

この記事では、Azure Cosmos DB アカウントを管理する方法について説明します。 具体的には、マルチホームの設定、リージョンの追加/削除、複数の書き込みリージョンの構成、およびフェールオーバーの優先度の設定を行います。 

## <a name="create-a-database-account"></a>データベース アカウントの作成

### <a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a id="create-database-account-via-cli"></a>Azure CLI

```bash
# Create an account
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group Name>
```

## <a name="configure-clients-for-multi-homing"></a>マルチホームに関するクライアントの構成

### <a id="configure-clients-multi-homing-dotnet"></a>.NET SDK

```csharp
// Create a new connection policy.
ConnectionPolicy policy = new ConnectionPolicy
    {
        // Note: These aren't required settings for multi-homing,
        // just suggested defaults.
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true,
    };
// Add regions to preferred locations.
// The name of the location will match what you see in the portal, etc.
policy.PreferredLocations.Add("East US");
policy.PreferredLocations.Add("North Europe");

// Pass the connection policy with the preferred locations on it to the client.
DocumentClient client = new DocumentClient(new Uri(this.accountEndpoint), this.accountKey, policy);
```

### <a id="configure-clients-multi-homing-java-async"></a>Java Async SDK

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setPreferredLocations(Collections.singleton("West US"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy).build();
```

### <a id="configure-clients-multi-homing-java-sync"></a>Java Sync SDK

```java
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
Collection<String> preferredLocations = new ArrayList<String>();
preferredLocations.add("Australia East");
connectionPolicy.setPreferredLocations(preferredLocations);
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy);
```

### <a id="configure-clients-multi-homing-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Set up the connection policy with your preferred regions.
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.PreferredLocations = ["West US", "Australia East"];

// Pass that connection policy to the client.
const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy
});
```

### <a id="configure-clients-multi-homing-python"></a>Python SDK

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.PreferredLocations = ['West US', 'Japan West']
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy)

```

## <a name="addremove-regions-from-your-database-account"></a>データベース アカウントのリージョンの追加/削除

### <a id="add-remove-regions-via-portal"></a>Azure Portal

1. Azure Cosmos DB アカウントに移動して、**[データをグローバルにレプリケートする]** メニューを開きます。

2. リージョンを追加するには、目的のリージョンに対応する六角形 (**+** ラベルが付いたもの) をマップ上で選択します。 リージョンを追加するには、**+ [リージョンの追加]** オプションを選択し、ドロップダウン メニューからリージョンを選択します。

3. リージョンを削除するには、チェック マークの付いた青い六角形をマップ上で選択して、1 つ以上のリージョンを消去します。 または、右側で、リージョンの横にある "ごみ箱" (🗑) アイコンを選択します。

4. 変更を保存するには、**[OK]** を選択します。

   ![リージョンの追加や削除を行うメニュー](./media/how-to-manage-database-account/add-region.png)

単一リージョン書き込みモードでは、書き込みリージョンを削除できません。 その現在の書き込みリージョンを削除する前に、別のリージョンへのフェールオーバーを行う必要があります。

複数リージョン書き込みモードでは、少なくとも 1 つのリージョンがあれば、どのリージョンも追加または削除できます。

### <a id="add-remove-regions-via-cli"></a>Azure CLI

```bash
# Given an account created with 1 region like so
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0'

# Add a new region by adding another region to the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=0 westus=1'

# Remove a region by removing a region from the list
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'westus=0'
```

## <a name="configure-multiple-write-regions"></a>複数の書き込みリージョンの構成

### <a id="configure-multiple-write-regions-portal"></a>Azure Portal

データベース アカウントを作成する際に、必ず **[Multi-region Writes]\(複数リージョンの書き込み\)** 設定を有効にしてください。

![Azure Cosmos アカウントの作成のスクリーンショット](./media/how-to-manage-database-account/account-create.png)

### <a id="configure-multiple-write-regions-cli"></a>Azure CLI

```bash
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-multiple-write-locations true
```

### <a id="configure-multiple-write-regions-arm"></a>Resource Manager テンプレート

次の JSON コードは、Azure Resource Manager テンプレートの例です。 これを使用すると、有界整合性制約の一貫性ポリシーを使用して Azure Cosmos DB アカウントをデプロイすることができます。 最大整合性制約間隔は 5 秒に設定されます。 許容される古い要求の最大数は 100 に設定されます。 Resource Manager テンプレートの形式と構文について学習するには、[Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) に関する記事を参照してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "BoundedStaleness",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```


## <a id="manual-failover"></a>Azure Cosmos DB アカウントでの手動フェールオーバーの有効化

### <a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Azure Cosmos DB アカウントに移動して、**[データをグローバルにレプリケートする]** メニューを開きます。

2. メニューの上部で、**[手動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[手動フェールオーバー]** メニューで、新しい書き込みリージョンを選択します。 チェック ボックスをオンにして、このオプションによって書き込みリージョンが変更されることを理解していることを示します。

4. フェールオーバーをトリガーするには、**[OK]** を選択します。

   ![手動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/manual-failover.png)

### <a id="enable-manual-failover-via-cli"></a>Azure CLI

```bash
# Given your account currently has regions with priority like so: 'eastus=0 westus=1'
# Change the priority order to trigger a failover of the write region
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --locations 'eastus=1 westus=0'
```

## <a id="automatic-failover"></a>Azure Cosmos DB アカウントでの自動フェールオーバーの有効化

### <a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Azure Cosmos DB アカウントで、**[データをグローバルにレプリケートする]** メニューを開きます。 

2. ウィンドウの上部で、**[自動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[自動フェールオーバー]** ウィンドウで、**[自動フェールオーバーの有効化]** を **[ON]** に設定してください。 

4. **[保存]** を選択します。

   ![自動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/automatic-failover.png)

このメニューでフェールオーバーの優先度を設定することもできます。

### <a id="enable-automatic-failover-via-cli"></a>Azure CLI

```bash
# Enable automatic failover on account creation
az cosmosdb create --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Enable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover true

# Disable automatic failover on an existing account
az cosmosdb update --name <Azure Cosmos account name> --resource-group <Resource Group name> --enable-automatic-failover false
```

## <a name="set-failover-priorities-for-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントでのフェールオーバーの優先度の設定

### <a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Azure Cosmos DB アカウントで、**[データをグローバルにレプリケートする]** メニューを開きます。 

2. ウィンドウの上部で、**[自動フェールオーバー]** を選択します。

   ![[データをグローバルにレプリケートする] メニュー](./media/how-to-manage-database-account/replicate-data-globally.png)

3. **[自動フェールオーバー]** ウィンドウで、**[自動フェールオーバーの有効化]** を **[ON]** に設定してください。 

4. フェールオーバーの優先度を変更するには、カーソルを置くと行の左側に表示される 3 つのドットを使い、読み込みリージョンをドラッグします。 

5. **[保存]** を選択します。

   ![自動フェールオーバーのポータル メニュー](./media/how-to-manage-database-account/automatic-failover.png)

このメニューでは書き込みリージョンを変更できません。 書き込みリージョンを手動で変更するには、手動フェールオーバーを行う必要があります。

### <a id="set-failover-priorities-via-cli"></a>Azure CLI

```bash
az cosmosdb failover-priority-change --name <Azure Cosmos account name> --resource-group <Resource Group name> --failover-policies 'eastus=0 westus=2 southcentralus=1'
```

## <a name="next-steps"></a>次の手順

Azure Cosmos DB の一貫性レベルとデータ競合を管理する方法について学習しましょう。 次の記事を参照してください。

* [一貫性の管理](how-to-manage-consistency.md)
* [リージョン間の競合の管理](how-to-manage-conflicts.md)

