---
title: テンプレート内のパラメーター
description: Azure Resource Manager テンプレート (ARM テンプレート) でパラメーターを定義する方法について説明します。
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 929d871bc0087d4fda585773b349dee4e0945c7c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934715"
---
# <a name="parameters-in-arm-templates"></a>ARM テンプレートのパラメーター

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でパラメーターを定義および使用する方法について説明します。 パラメーターに異なる値を指定することにより、さまざまな環境でテンプレートを再利用できます。

Resource Manager は、デプロイ操作を開始する前にパラメーター値を解決します。 テンプレートでパラメーターが使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

各パラメーターは、いずれかの[データ型](template-syntax.md#data-types)に設定する必要があります。

## <a name="define-parameter"></a>パラメーターを定義する

単純なパラメーター定義の例を次に示します。 `storageSKU` という名前のパラメーターを定義します。 パラメーターは、文字列値であり、使用目的に対して有効な値のみを受け入れます。 デプロイ時に値が指定されなかった場合、パラメーターは既定値を使用します。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }
}
```

## <a name="use-parameter"></a>パラメーターを使用する

テンプレートでは、[parameters](template-functions-deployment.md#parameters) 関数を使用してパラメーターの値を参照します。 次の例では、パラメーター値を使用してストレージ アカウントの SKU を設定します。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

## <a name="template-functions"></a>テンプレート関数

パラメーターの既定値を指定する場合は、ほとんどのテンプレート関数を使用できます。 別のパラメーター値を使用して既定値を作成できます。 次のテンプレートは、既定値での関数の使用方法を示します。 サイトの名前が指定されていない場合は、一意の文字列値が作成されて、**site** に付加されます。 ホスト プランの名前が指定されていない場合は、サイトの値が使用され、**-plan** が付加されます。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

パラメーター セクションでは、[reference](template-functions-resource.md#reference) 関数も、いずれの [list](template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。パラメーターが解決されるとき、デプロイの前に実行することはできません。

## <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

次の例は、オブジェクトであるパラメーターを示しています。 既定値は、オブジェクトの予想されるプロパティを示しています。

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

ドット演算子を使用して、オブジェクトのプロパティを参照します。

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2015-06-15",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、パラメーターを使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |

## <a name="next-steps"></a>次のステップ

* パラメーターに使用できるプロパティの詳細については、「[ARM テンプレートの構造と構文について](template-syntax.md)」をご覧ください。
* パラメーター値をファイルとして渡す方法については、「[Resource Manager パラメーター ファイルの作成](parameter-files.md)」を参照してください。
* パラメーターの作成に関する推奨事項については、[ベスト プラクティス - パラメーター](template-best-practices.md#parameters)に関する記事をご覧ください。
