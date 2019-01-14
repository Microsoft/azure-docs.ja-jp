---
title: PowerShell を使用して Azure Data Factory で共有のセルフホステッド統合ランタイムを作成する | Microsoft Docs
description: Azure Data Factory 内で共有のセルフホステッド統合ランタイムを作成する方法について説明します。これにより、複数のデータ ファクトリから統合ランタイムにアクセスできます。
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: abnarain
ms.openlocfilehash: 2f132bc14e62636a3ddd269323497733de1007b7
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021154"
---
# <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory-with-powershell"></a>PowerShell を使用して Azure Data Factory で共有のセルフホステッド統合ランタイムを作成する

このステップ バイ ステップ ガイドでは、Azure PowerShell を使用して Azure Data Factory 内で共有のセルフホステッド統合ランタイムを作成する方法について説明します。 これにより、共有のセルフホステッド統合ランタイムを他のデータ ファクトリで使用できます。 このチュートリアルでは、次の手順を実行します。 

1. データ ファクトリを作成します。 
1. セルフホステッド統合ランタイムを作成します。
1. セルフホステッド統合ランタイムを他のデータ ファクトリと共有します。
1. リンクされた統合ランタイムを作成します。
1. 共有を取り消します。

## <a name="prerequisites"></a>前提条件 

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。 

- **Azure PowerShell**。 「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.11.0)」に記載されている手順に従います。 PowerShell を使用してスクリプトを実行し、他のデータ ファクトリと共有できるセルフホステッド統合ランタイムを作成できます。 

> [!NOTE]  
> 現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory)」ページで目的のリージョンを選択してください。

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. Windows PowerShell Integrated Scripting Environment (ISE) を起動します。

1. 変数を作成します。 次のスクリプトをコピーして貼り付けます。 **SubscriptionName** や **ResourceGroupName** などの変数を実際の値に置き換えます。 

    ```powershell
    # If input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
    $SubscriptionName = "[Azure subscription name]" 
    $ResourceGroupName = "[Azure resource group name]" 
    $DataFactoryLocation = "EastUS" 

    # Shared Self-hosted integration runtime information. This is a Data Factory compute resource for running any activities 
    # Data factory name. Must be globally unique 
    $SharedDataFactoryName = "[Shared Data factory name]" 
    $SharedIntegrationRuntimeName = "[Shared Integration Runtime Name]" 
    $SharedIntegrationRuntimeDescription = "[Description for Shared Integration Runtime]"

    # Linked integration runtime information. This is a Data Factory compute resource for running any activities
    # Data factory name. Must be globally unique
    $LinkedDataFactoryName = "[Linked Data factory name]"
    $LinkedIntegrationRuntimeName = "[Linked Integration Runtime Name]"
    $LinkedIntegrationRuntimeDescription = "[Description for Linked Integration Runtime]"
    ```

1. サインインしてサブスクリプションを選択します。 サインインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。

    ```powershell
    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

1. リソース グループとデータ ファクトリを作成します。

    > [!NOTE]  
    > この手順は省略可能です。 既にデータ ファクトリがある場合は、この手順をスキップしてください。 

    [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-6.11.0) コマンドを使用して [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 次の例では、`myResourceGroup` という名前のリソース グループを WestEurope の場所に作成します。 

    ```powershell
    New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
    ```

    Data Factory を作成するには、次のコマンドを実行します。 

    ```powershell
    Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                             -Location $DataFactoryLocation `
                             -Name $SharedDataFactoryName
    ```

## <a name="create-a-self-hosted-integration-runtime"></a>自己ホスト型統合ランタイムを作成する

> [!NOTE]  
> この手順は省略可能です。 他のデータ ファクトリと共有するセルフホステッド統合ランタイムが既にある場合は、この手順をスキップしてください。

次のコマンドを実行して、セルフホステッド統合ランタイムを作成します。

```powershell
$SharedIR = Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Type SelfHosted `
    -Description $SharedIntegrationRuntimeDescription
```

### <a name="get-the-integration-runtime-authentication-key-and-register-a-node"></a>統合ランタイムの認証キーを取得してノードを登録する

次のコマンドを実行して、セルフホステッド統合ランタイムの認証キーを取得します。

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeKey `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName
```

応答には、このセルフホステッド統合ランタイムの認証キーが含まれています。 このキーは、統合ランタイム ノードを登録するときに使用します。

### <a name="install-and-register-the-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムをインストールして登録する

1. セルフホステッド統合ランタイムのインストーラーを「[Azure Data Factory Integration Runtime](https://aka.ms/dmg)」からダウンロードします。

2. インストーラーを実行して、セルフホステッド統合をローカル コンピューターにインストールします。

3. 前の手順で取得した認証キーを使用して、新しいセルフホステッド統合を登録します。

## <a name="share-the-self-hosted-integration-runtime-with-another-data-factory"></a>セルフホステッド統合ランタイムを他のデータ ファクトリと共有する

### <a name="create-another-data-factory"></a>別のデータ ファクトリを作成する

> [!NOTE]  
> この手順は省略可能です。 共有先のデータ ファクトリが既にある場合は、この手順をスキップしてください。

```powershell
$factory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $LinkedDataFactoryName
```
### <a name="grant-permission"></a>アクセス許可を付与する

作成して登録したセルフホステッド統合ランタイムにアクセスする必要があるデータ ファクトリに対してアクセス許可を付与します。

> [!IMPORTANT]  
> この手順はスキップしないでください。

```powershell
New-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId ` #MSI of the Data Factory with which it needs to be shared
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' ` #This is the Contributor role
    -Scope $SharedIR.Id
```

## <a name="create-a-linked-self-hosted-integration-runtime"></a>リンクされたセルフホステッド統合ランタイムを作成する

次のコマンドを実行して、リンクされたセルフホステッド統合ランタイムを作成します。

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $LinkedDataFactoryName `
    -Name $LinkedIntegrationRuntimeName `
    -Type SelfHosted `
    -SharedIntegrationRuntimeResourceId $SharedIR.Id `
    -Description $LinkedIntegrationRuntimeDescription
```

これで、このリンクされた統合ランタイムを任意のリンクされたサービスで使用できます。 リンクされた統合ランタイムでは、共有された統合ランタイムを使用してアクティビティを実行します。

## <a name="revoke-integration-runtime-sharing-from-a-data-factory"></a>データ ファクトリからの統合ランタイムの共有を取り消す

共有された統合ランタイムからデータ ファクトリのアクセス権を取り消すには、次のコマンドを実行します。

```powershell
Remove-AzureRMRoleAssignment `
    -ObjectId $factory.Identity.PrincipalId `
    -RoleDefinitionId 'b24988ac-6180-42a0-ab88-20f7382dd24c' `
    -Scope $SharedIR.Id
```

既存のリンクされた統合ランタイムを削除するには、共有された統合ランタイムに対して次のコマンドを実行します。

```powershell
Remove-AzureRmDataFactoryV2IntegrationRuntime `
    -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $SharedDataFactoryName `
    -Name $SharedIntegrationRuntimeName `
    -Links `
    -LinkedDataFactoryName $LinkedDataFactoryName
```

## <a name="next-steps"></a>次の手順

- [Azure Data Factory の統合ランタイムの概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)を確認します。

- [Azure portal 上でセルフホステッド統合ランタイムを作成する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)方法を確認します。
