---
title: サンプル - ストレージ アカウントのクール アクセス層の拒否
description: このサンプル ポリシー定義は、BLOB ストレージ アカウントにクール アクセス層の使用を禁止します。
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/23/2019
ms.author: dacoulte
ms.openlocfilehash: 21ee0e62e300b5807e3a38a0834c3365ab123dad
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977233"
---
# <a name="sample---deny-cool-access-tiering-for-storage-accounts"></a>サンプル - ストレージ アカウントのクール アクセス層の拒否

このポリシーは、BLOB ストレージ アカウントにクール アクセス層の使用を禁止します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

[!code-json[main](../../../../policy-templates/samples/Storage/storage-account-access-tier/azurepolicy.json "Deny cool access tiering for storage accounts")]

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

[![ポリシーのサンプルを Azure にデプロイする](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FStorage%2Fstorage-account-access-tier%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name "storage-account-access-tier" -DisplayName "Deny cool access tiering for storage accounts" -description "Ensures there's no usage of cool access tiering for storage." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzPolicyAssignment -Name <assignmentname> -Scope <scope>  -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'storage-account-access-tier' --display-name 'Deny cool access tiering for storage accounts' --description 'Ensures there's no usage of cool access tiering for storage.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Storage/storage-account-access-tier/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "storage-account-access-tier"
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

- 他のサンプルについては、「[Azure Policy のサンプル](index.md)」をご確認ください。