---
title: テンプレート内の出力
description: Azure Resource Manager テンプレート (ARM テンプレート) で出力値を定義する方法について説明します。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: f8f13b6caf063cea79dc71775fb936f406a3ee6c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964016"
---
# <a name="outputs-in-arm-templates"></a>ARM テンプレート内の出力

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) で出力値を定義する方法について説明します。 デプロイされたリソースから値を返す必要がある場合は `outputs` を使用します。

各出力値の形式は、いずれかの[データ型](template-syntax.md#data-types)と一致している必要があります。

## <a name="define-output-values"></a>出力値の定義

次の例は、パブリック IP アドレスのリソース ID を返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>条件付き出力

`outputs` セクションでは、値を条件付きで返すことができます。 通常、リソースを[条件付きでデプロイ](conditional-resource-deployment.md)した場合は `outputs` で `condition` を使用します。 次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

条件付き出力の簡単な例については、[条件付き出力テンプレート](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)に関する説明をご覧ください。

## <a name="dynamic-number-of-outputs"></a>動的な出力の数

場合により、テンプレートの作成時に、返す必要がある値のインスタンスの数が不明なシナリオもあります。 `copy` 要素を使用することで、可変数の値を返すことができます。

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

詳細については、「[ARM テンプレートでの出力の反復処理](copy-outputs.md)」を参照してください。

## <a name="linked-templates"></a>リンク済みテンプレート

リンク済みテンプレートから出力値を取得するには、親テンプレートで [reference](template-functions-resource.md#reference) 関数を使用します。 親テンプレートの構文は次のとおりです。

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

リンクされたテンプレートから出力プロパティを取得する場合、プロパティ名にダッシュを含めることはできません。

次の例では、リンクされているテンプレートから値を取得することによってロード バランサーの IP アドレスを設定する方法を示します。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

[入れ子になったテンプレート](linked-templates.md#nested-template) の出力セクションでは `reference` 関数を使用できません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。

## <a name="get-output-values"></a>出力値の取得

デプロイが成功すると、出力値はデプロイの結果で自動的に返されます。

デプロイ履歴から出力値を取得するには、スクリプトを使用できます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>サンプル テンプレート

次の例は、出力を使用するためのシナリオを示しています。

|テンプレート  |説明  |
|---------|---------|
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 複合変数を作成し、それらの値を出力します。 リソースはデプロイしません。 |
|[パブリック IP アドレス](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | パブリック IP アドレスを作成し、リソース ID を出力します。 |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 前述のテンプレートにリンクします。 ロード バランサーの作成時に出力内のリソース ID を使用します。 |

## <a name="next-steps"></a>次の手順

* 出力に使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
