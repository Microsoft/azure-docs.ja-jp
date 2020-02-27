---
title: サブスクリプションにリソースをデプロイする
description: Azure Resource Manager テンプレートでリソース グループを作成する方法について説明します。 Azure サブスクリプション スコープでリソースをデプロイする方法も示します。
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 50db0b4d46ff4e367411829aa75fa017a168372f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207657"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>サブスクリプション レベルでリソース グループとリソースを作成する

通常、Azure リソースは Azure サブスクリプションのリソース グループにデプロイします。 ただし、サブスクリプション レベルでリソースを作成することもできます。 サブスクリプション レベルのデプロイを使用して、リソース グループの作成や[ロールベースのアクセス制御](../../role-based-access-control/overview.md)の割り当てなど、そのレベルで意味のあるアクションを実行します。

サブスクリプション レベルでテンプレートをデプロイするには、Azure CLI、PowerShell、または REST API を使用します。 Azure portal は、サブスクリプション レベルでのデプロイをサポートしていません。

## <a name="supported-resources"></a>サポートされているリソース

サブスクリプション レベルでは、次のリソースの種類をデプロイできます。

* [deployments](/azure/templates/microsoft.resources/deployments)
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>スキーマ

サブスクリプション レベルのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマと異なります。

テンプレートの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

パラメーター ファイルの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#
```

## <a name="deployment-commands"></a>デプロイ コマンド

サブスクリプション レベルのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドと異なります。

Azure CLI の場合は、[az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


PowerShell デプロイ コマンドについては、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API の場合は、[デプロイ - サブスクリプション スコープでの作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページを参照してください。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

サブスクリプション レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="use-template-functions"></a>テンプレート関数を使用する

サブスクリプション レベルのデプロイでは、テンプレート関数を使用する場合に、次のようないくつかの重要な考慮事項があります。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) 関数がサポートされています。
* [resourceId()](template-functions-resource.md#resourceid) 関数は、サポートされています。 これを使用して、サブスクリプション レベルのデプロイで使用されているリソースのリソース ID を取得します。 リソース グループ パラメーターに値を指定しないでください。

  たとえば、ポリシー定義のリソース ID を取得するには、次を使用します。
  
  ```json
  resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```
  
  返されるリソース ID の形式は次のとおりです。

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

  または、[subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) 関数を使用して、サブスクリプション レベルのリソースのリソース ID を取得します。

## <a name="create-resource-groups"></a>リソース グループを作成する

Azure Resource Manager テンプレートでリソース グループを作成するには、[Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) リソースを定義してリソース グループの名前と場所を指定します。 同じテンプレートでリソース グループを作成し、そのリソース グループにリソースをデプロイすることができます。

次のテンプレートでは、空のリソース グループを作成します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

複数のリソース グループを作成するには、リソース グループで [copy 要素](copy-resources.md)を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

リソースの反復については、[Azure Resource Manager テンプレートでリソースの複数のインスタンスをデプロイする](./copy-resources.md)ことに関するページと、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./template-tutorial-create-multiple-instances.md)」を参照してください。

## <a name="resource-group-and-resources"></a>リソース グループとリソース

リソース グループを作成してそれにリソースをデプロイするには、入れ子になったテンプレートを使います。 入れ子になったテンプレートでは、リソース グループにデプロイするリソースを定義します。 リソースをデプロイする前にリソース グループを確実に存在させるには、リソース グループに依存するように入れ子になったテンプレートを設定します。

次の例では、リソース グループを作成し、ストレージ アカウントをリソース グループにデプロイします。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2018-05-01",
      "location": "[parameters('rgLocation')]",
      "name": "[parameters('rgName')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2017-10-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              }
              "kind": "StorageV2",
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="create-policies"></a>ポリシーの作成

### <a name="assign-policy"></a>ポリシーの割り当て

次の例は、既存のポリシー定義をサブスクリプションに割り当てます。 ポリシーがパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシーがパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Azure CLI を使ってこのテンプレートをデプロイするには、次のコマンドを使います。

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>ポリシーを定義して割り当てる

ポリシーは同じテンプレートで[定義](../../governance/policy/concepts/definition-structure.md)して割り当てることができます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

サブスクリプションでポリシー定義を作成し、サブスクリプションに割り当てるには、次の CLI コマンドを使用します。

```azurecli
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="template-samples"></a>テンプレートのサンプル

* リソース グループを作成してロックし、アクセス許可を付与します。 [こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)を参照してください。
* リソース グループ、ポリシー、およびポリシーの割り当てを作成します。  [こちら](https://github.com/Azure/azure-docs-json-samples/blob/master/subscription-level-deployment/azuredeploy.json)を参照してください。

## <a name="next-steps"></a>次のステップ

* ロールの割り当てについては、「[RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する](../../role-based-access-control/role-assignments-template.md)」をご覧ください。
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* サンプル テンプレートは [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments) に掲載されています。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](template-syntax.md)に関するページを参照してください。
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](template-functions.md)に関するページを参照してください。
