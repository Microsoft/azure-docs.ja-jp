---
title: Azure Resource Manager と PowerShell を使用して Azure Resource Manager グループを管理する | Microsoft Docs
description: Azure PowerShell を使用して Azure Resource Manager グループを管理します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 59c83cb1becf8869d0e8710121816d1bb75e621e
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001568"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure PowerShell を使用して Azure Resource Manager リソース グループを管理する

[Azure Resource Manager](resource-group-overview.md) で Azure PowerShell を使用して Azure リソースグループを管理する方法について説明します。 Azure リソースの管理については、[Manage Azure resources by using Azure PowerShell (Azure PowerShell を使用して Azure リソースを管理する)](./manage-resources-powershell.md) を参照してください。

リソース グループの管理に関するその他のアーティクル:

- [Azure Portal を使用して Azure リソース グループを管理する](./manage-resources-portal.md)
- [Azure CLI を使用して Azure リソース グループを管理する](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>リソース グループとは何か

リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 リソース グループには、ソリューションのすべてのリソースか、グループとして管理したいリソースのみを含めることができます。 組織のニーズに合わせてリソースをリソース グループに割り当てる方法を指定してください。 通常は、同じライフサイクルを共有するリソースを同じリソース グループに追加して、グループとして簡単にデプロイ、更新、および削除できるようにします。

リソース グループには、リソースについてのメタデータが格納されます。 そのため、リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。 コンプライアンス上の理由から、データは特定のリージョンに格納されるようにする必要があります。

リソース グループには、リソースについてのメタデータが格納されます。 リソース グループの場所を指定するとき、このメタデータが格納される場所を指定することになります。

## <a name="create-resource-groups"></a>リソース グループを作成する

次の PowerShell スクリプトでは、リソース グループを作成し、そのリソース グループを表示します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>リソース グループの一覧を表示する

次の PowerShell スクリプトでは、サブスクリプションの下にリソース グループを一覧表示します。

```azurepowershell-interactive
Get-AzResourceGroup
```

1 つの リソース グループを取得するには:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>リソース グループの削除

次の PowerShell スクリプトはリソース グループを削除します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Azure Resource Manager によってリソースの削除の順序が決定される方法の詳細については、「[Azure Resource Manager によるリソース グループの削除](./resource-group-delete.md)」をご覧ください。

## <a name="deploy-resources-to-an-existing-resource-group"></a>リソースを既存のリソース グループにデプロイする

「[Deploy resources to an existing resource group (リソースを既存のリソース グループにデプロイする)](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)」をご覧ください。

リソース グループのデプロイを検証するには、[Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0) をご覧ください。

## <a name="deploy-a-resource-group-and-resources"></a>リソース グループとリソースをデプロイする

Resource Manager テンプレートを使用して、リソース グループを作成し、そのグループにリソースをデプロイできます。 詳しくは、「[リソース グループを作成してリソースをデプロイする](./deploy-to-subscription.md#resource-group-and-resources)」をご覧ください。

## <a name="redeploy-when-deployment-fails"></a>デプロイに失敗したときに再デプロイする

この機能は、"*エラー時のロールバック*" とも呼ばれます。 詳細については、「[デプロイに失敗したときに再デプロイする](./rollback-on-error.md)」を参照してください。

## <a name="move-to-another-resource-group-or-subscription"></a>別のリソース グループまたはサブスクリプションに移動する

グループ内のリソースを別のリソース グループに移動できます。 詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](./resource-group-move-resources.md)」を参照してください。

## <a name="lock-resource-groups"></a>リソース グループをロックする

ロックを適用することで、組織の他のユーザーが重要なリソース (Azure サブスクリプション、リソース グループ、リソースなど) を誤って削除または変更するのを防ぐことができます。 

次のスクリプトでは、リソース グループをロックして、そのリソース グループを削除できないようにします。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

次のスクリプトでは、リソース グループのすべてのロックを取得します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

詳細については、[「Azure Resource Manager によるリソースのロック」](resource-group-lock-resources.md)を参照してください。

## <a name="tag-resource-groups"></a>リソース グループにタグを適用する

リソース グループやリソースにタグを適用して、アセットを論理的に整理できます。 詳細については、[タグを使用した Azure リソースの整理](./resource-group-using-tags.md#powershell) に関する記事をご覧ください。

## <a name="export-resource-groups-to-templates"></a>リソース グループをテンプレートにエクスポートする

リソース グループを設定したら、リソース グループの Resource Manager テンプレートを表示できます。 テンプレートのエクスポートには、2 つの利点があります。

- テンプレートに完全なインフラストラクチャが含まれているため、ソリューションの将来のデプロイを自動化します。
- ソリューションを表す JavaScript Object Notation (JSON) を調べることで、テンプレートの構文を確認できます。

リソース グループ内のすべてのリソースをエクスポートするには、[Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) コマンドレットを使用して、リソース グループ名を指定します。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

これでテンプレートはローカル ファイルとして保存されます。

リソース グループ内のすべてのリソースをエクスポートする代わりに、どのリソースをエクスポートするかを選択できます。

1 つのリソースをエクスポートするには、そのリソースの ID を渡します。

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

複数のリソースをエクスポートするには、リソース ID を配列で渡します。

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

テンプレートをエクスポートするときには、テンプレート内でパラメーターを使用するかどうかを指定できます。 既定では、リソース名用のパラメーターが含められますが、既定値はありません。 そのパラメーターの値は、デプロイ時に渡す必要があります。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

リソース内では、パラメーターは名前用に使用されます。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

テンプレートをエクスポートするときに `-IncludeParameterDefaultValue` パラメーターを使用する場合、テンプレート パラメーターには、現在の値に設定される既定値が含められます。 その既定値を使用することも、異なる値を渡して既定値を上書きすることもできます。

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

テンプレートをエクスポートするときに `-SkipResourceNameParameterization` パラメーターを使用する場合、リソース名用のパラメーターはテンプレートに含められません。 代わりに、リソース名はリソースに対して直接、現在の値に設定されます。 この名前はデプロイ時にカスタマイズすることはできません。

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

詳細については、「[Azure portal のテンプレートへの単一および複数リソースのエクスポート](./export-template-portal.md)」を参照してください。

## <a name="manage-access-to-resource-groups"></a>リソース グループへのアクセスを管理する

[ロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) は、Azure に存在するリソースに対するアクセス権を管理するための手法です。 詳細については、[RBAC と Azure PowerShell を使用したアクセスの管理](../role-based-access-control/role-assignments-powershell.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Resource Manager については、「[Azure Resource Manager の概要](./resource-group-overview.md)」を参照してください。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](/azure/azure-resource-manager/)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。