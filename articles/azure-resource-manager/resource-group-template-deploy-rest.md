---
title: REST API とテンプレートを使用してリソースをデプロイする | Microsoft Docs
description: Azure Resource Manager と Resource Manager REST API を使用してリソースを Azure にデプロイします。 リソースは Resource Manager テンプレートで定義されます。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: tomfitz
ms.openlocfilehash: 5b3170d640257774339697ee7915169c2f5e451f
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973351"
---
# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ

この記事では、Resource Manager REST API と Resource Manager テンプレートを使用して Azure にリソースをデプロイする方法について説明します。  

テンプレートは要求本文またはファイルへのリンクに含めることができます。 ファイルを使用する場合は、ローカル ファイルまたは URI を通じてアクセスできる外部ファイルを使用できます。 テンプレートがストレージ アカウントにある場合は、テンプレートへのアクセスを制限し、デプロイ時に Shared Access Signature (SAS) トークンを設定できます。

## <a name="deployment-scope"></a>デプロイのスコープ

管理グループ、Azure サブスクリプション、またはリソース グループをデプロイの対象として指定できます。 多くの場合、リソース グループをデプロイの対象にします。 管理グループまたはサブスクリプションのデプロイを使用するのは、指定したスコープ全体にポリシーとロールの割り当てを適用するときです。 また、リソース グループを作成し、それにリソースをデプロイする場合も、サブスクリプション デプロイを使用します。 使用するコマンドは、デプロイのスコープに応じて異なります。

**リソース グループ**にデプロイするには、[デプロイ - 作成](/rest/api/resources/deployments/createorupdate)に関するページのコマンドを使用します。 要求は以下に送信されます。

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

**サブスクリプション**にデプロイするには、[デプロイ - サブスクリプション スコープで作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページのコマンドを使用します。 要求は以下に送信されます。

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

**管理グループ**にデプロイするには、[デプロイ - 管理グループ スコープで作成](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)に関するページを参照してください。 要求は以下に送信されます。

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

この記事の例では、リソース グループ デプロイを使用します。 サブスクリプション デプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする

1. [一般的なパラメーターおよびヘッダー](/rest/api/azure/) (認証トークンを含む) を設定します。

1. 既存のリソース グループがない場合は、リソース グループを作成します。 ソリューションに必要なサブスクリプション ID、新しいリソース グループの名前、場所を指定します。 詳細については、「[リソース グループの作成](/rest/api/resources/resourcegroups/createorupdate)」を参照してください。

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   次のような要求本文を使用します。

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. [テンプレート デプロイを検証する](/rest/api/resources/deployments/validate)操作を実行して、デプロイを実行する前に検証します。 デプロイをテストする場合、(次の手順で示すように) デプロイの実行時に必要なパラメーターを正確に指定します。

1. テンプレートをデプロイするには、要求 URI にサブスクリプション ID、リソース グループの名前、デプロイの名前を指定します。 

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   要求の本文に、テンプレートとパラメーター ファイルへのリンクを指定します。 パラメーター ファイルの詳細については、「[Resource Manager パラメーター ファイルを作成する](resource-manager-parameter-files.md)」を参照してください。

   **[モード]** が **[増分]** に設定されていることに注意してください。 完全デプロイメントを実行するには、 **[モード]** を **[完全]** に設定します。 テンプレートにないリソースを誤って削除する可能性があるため、完全モードを使用する際は注意してください。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    応答の内容、要求の内容、またはその両方を記録するには、要求に **debugSetting** を含めます。

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    ストレージ アカウントを設定して、Shared Access Signature (SAS) トークンを使用することができます。 詳細については、「[Delegating Access with a Shared Access Signature (Shared Access Signature を使用したアクセスの委任)](https://docs.microsoft.com/rest/api/storageservices/delegating-access-with-a-shared-access-signature)」を参照してください。

    パラメーターに機密性の高い値(パスワードなど) を提供する必要がある場合は、その値を Key Vault に追加します。 前の例で示したように、デプロイ中に Key Vault を取得します。 詳細については、「 [デプロイメント時にセキュリティで保護された値を渡す](resource-manager-keyvault-parameter.md)」を参照してください。 

1. テンプレートとパラメーターのファイルにリンクするのではなく、それらを要求本文に含めることができます。 次の例は、テンプレートとパラメーターをインラインにした要求の本文を示しています。

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2018-02-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. テンプレートのデプロイの状態を取得するには、[デプロイ - 取得](/rest/api/resources/deployments/get)に関するページを参照してください。

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>次の手順

- エラーが発生したときに正常なデプロイにロールバックするには、「[エラー発生時に正常なデプロイにロールバックする](rollback-on-error.md)」を参照してください。
- リソース グループに存在するが、テンプレートで定義されていないリソースの処理方法を指定するには、「[Azure Resource Manager のデプロイ モード](deployment-modes.md)」を参照してください。
- 非同期 REST 操作の処理の詳細については、「[Track asynchronous Azure operations (非同期の Azure 操作の追跡)](resource-manager-async-operations.md)」を参照してください。
- テンプレートの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。

