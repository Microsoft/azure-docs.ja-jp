---
title: PowerShell サンプル - Azure SQL Database マネージド インスタンスを作成する | Microsoft Docs
description: Azure SQL Database マネージド インスタンスを作成する Azure PowerShell サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 7df94a465cac03a65bd1df15bf5c1cabda315551
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53336473"
---
# <a name="use-powershell-with-azure-resource-manager-template-to-create-an-azure-sql-database-managed-instance"></a>PowerShell と Azure Resource Manager テンプレートを使用して Azure SQL Database マネージド インスタンスを作成する

Azure PowerShell ライブラリと Azure Resource Manager テンプレートを使用して Azure SQL Database マネージド インスタンスを作成できます。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

Azure PowerShell コマンドで、定義済みの Azure Resource Manager テンプレートを使用してデプロイを開始できます。 テンプレート内に次のプロパティを指定できます。
- インスタンス名
- SQL 管理者のユーザー名とパスワード。 
- インスタンスのサイズ (コアの数と最大ストレージ サイズ)。
- インスタンスが配置される VNet とサブネット。
- インスタンスのサーバー レベルの照合順序 (プレビュー)。

インスタンス名、SQL 管理者ユーザー名、VNet/サブネット、および照合順序は、後で変更することはできません。 その他のインスタンスのプロパティは変更できます。

## <a name="prerequisites"></a>前提条件

このサンプルでは、お使いのマネージド インスタンスに対する[有効なネットワーク環境の作成](../sql-database-managed-instance-create-vnet-subnet.md)または[既存の VNet の変更](../sql-database-managed-instance-configure-vnet-subnet.md)が済んでいるものとします。 サンプルではコマンドレット [New-AzureRmResourceGroupDeployment](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) と [Get-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetwork) を使用するので、次の PowerShell モジュールをインストールしてあることを確認してください。

```
Install-Module AzureRM.Network
Install-Module AzureRM.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

インスタンスを作成するために使用されるテンプレートを表すファイル内に、次の内容を格納する必要があります。
```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },          
            "sku": {
                "name": "GP_Gen4",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen4", 
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```
サブネットが適切に構成された Azure VNet が既に存在することを前提としています。 これに当てはまらない場合は、別の [Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment)を使用して、独立して実行できるネットワーク環境またはそのテンプレートに含まれるネットワーク環境を準備します。

このファイルの内容を .Json ファイルとして保存し、ファイル パスを次の PowerShell スクリプトに配置し、スクリプト内のオブジェクトの名前を変更します。 

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Managed Instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy Instance using Azure Resource Manager template:
New-AzureRmResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```
スクリプトが正常に実行されると、すべての Azure サービスおよび構成済み IP アドレスから SQL Database へアクセスできるようになります。 

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../sql-database-powershell-samples.md)のページにあります。



