---
title: リソース プロバイダーとリソースの種類
description: リソース マネージャーをサポートするリソース プロバイダー、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 581b653c6d4769f7777b0ca56f136d25443c1ae4
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500012"
---
# <a name="azure-resource-providers-and-types"></a>Azure リソース プロバイダーと種類

リソースをデプロイするときに、リソース プロバイダーと種類に関する情報を取得しなければならないケースは少なくありません。 たとえば、キーとシークレットを格納するには、Microsoft.KeyVault リソース プロバイダーを使用します。 このリソース プロバイダーには、キー コンテナーを作成する vaults というリソースの種類が用意されています。

リソースの種類を表す名前は、 **{resource-provider}/{resource-type}** のような形式になります。 キー コンテナーのリソースの種類は **Microsoft.KeyVault/vaults** です。

この記事では、次のことについて説明します。

* Azure のすべてのリソース プロバイダーを表示する
* リソース プロバイダーの登録ステータスを確認する
* リソース プロバイダーを登録する
* リソース プロバイダーのリソースの種類を表示する
* リソースの種類の有効な場所を表示する
* リソースの種類の有効な API のバージョンを表示する

これらの手順は、Azure Portal、Azure PowerShell、または Azure CLI を介して実行できます。

リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](azure-services-resource-providers.md)」 (Azure サービスのリソースプロバイダー) を参照してください。

## <a name="azure-portal"></a>Azure portal

すべてのリソース プロバイダー、およびサブスクリプションの登録状態を表示するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) にサインインする
2. Azure portal のメニューで **[すべてのサービス]** を選択します。

    ![サブスクリプションの選択](./media/resource-providers-and-types/select-all-services.png)

3. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、 **[サブスクリプション]** を選択します。
4. サブスクリプションの一覧から、表示するサブスクリプションを選択します。
5. **[リソース プロバイダー]** を選択し、利用可能なリソース プロバイダーの一覧を表示します。

    ![リソース プロバイダーの表示](./media/resource-providers-and-types/show-resource-providers.png)

6. リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 登録の範囲は常にサブスクリプションです。 既定では、多数のリソース プロバイダーが自動的に登録されます。 ただし、一部のリソース プロバイダーについては、手動で登録する必要がある場合もあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。 リソース プロバイダーを登録するには、 **[登録]** を選択します。 前のスクリーン ショットでは、**Microsoft.Blueprint** の **[登録]** リンクが強調表示されています。

    サブスクリプション内に特定のリソース プロバイダーからのリソースの種類がまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、以下を実行します。

1. [Azure portal](https://portal.azure.com) にサインインする
2. Azure portal のメニューで **[すべてのサービス]** を選択します。
3. **[すべてのサービス]** ボックスで **"リソース エクスプ ローラー (resource explorer)"** と入力し、 **[リソース エクスプ ローラー]** を選択します。

    ![[すべてのサービス] を選択する](./media/resource-providers-and-types/select-resource-explorer.png)

4. 右矢印を選択して **[プロバイダー]** を展開します。

    ![プロバイダーの選択](./media/resource-providers-and-types/select-providers.png)

5. 表示するリソース プロバイダーとリソースの種類を展開します。

    ![リソースの種類の選択](./media/resource-providers-and-types/select-resource-type.png)

6. リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 リソース エクスプローラーでは、リソースの種類の有効な場所が表示されます。

    ![場所の表示](./media/resource-providers-and-types/show-locations.png)

7. API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 リソース エクスプローラーでは、リソースの種類の有効 API のバージョンが表示されます。

    ![API のバージョンの表示](./media/resource-providers-and-types/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

これは次のような結果を返します。

```output
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 登録の範囲は常にサブスクリプションです。 既定では、多数のリソース プロバイダーが自動的に登録されます。 ただし、一部のリソース プロバイダーについては、手動で登録する必要がある場合もあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

これは次のような結果を返します。

```output
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

サブスクリプション内に特定のリソース プロバイダーからのリソースの種類がまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

これは次のような結果を返します。

```output
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

次のような結果が返されます。

```output
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

次のような結果が返されます。

```output
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

次のような結果が返されます。

```output
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

これは次のような結果を返します。

```output
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーと連携するように構成されます。 登録の範囲は常にサブスクリプションです。 既定では、多数のリソース プロバイダーが自動的に登録されます。 ただし、一部のリソース プロバイダーについては、手動で登録する必要がある場合もあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するためのアクセス許可が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。

```azurecli
az provider register --namespace Microsoft.Batch
```

登録が進行中であることを示すメッセージが返されます。

サブスクリプション内に特定のリソース プロバイダーからのリソースの種類がまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch
```

これは次のような結果を返します。

```output
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

次のような結果が返されます。

```output
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

次のような結果が返されます。

```output
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

次のような結果が返されます。

```output
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>次のステップ

* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](../templates/template-syntax.md)に関するページを参照してください。 
* リソース プロバイダーのテンプレートのスキーマを表示するには、「[テンプレート リファレンス](/azure/templates/)」を参照してください。
* リソース プロバイダーを Azure サービスにマップされるリストについては、「[Resource providers for Azure services](azure-services-resource-providers.md)」 (Azure サービスのリソースプロバイダー) を参照してください。
* リソース プロバイダーの操作を表示するには、「[Azure REST API](/rest/api/)」を参照してください。
