---
title: 変数の複数のインスタンスを定義する
description: 変数を作成するときに、Azure Resource Manager テンプレートで copy 操作を使用して、複数回、反復処理を行います。
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 9e252a0b9721ffec99535c5d30e609e12e9e67eb
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210656"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの変数の反復

この記事では、Azure Resource Manager テンプレートで 1 つの変数に対して複数の値を作成する方法について説明します。 テンプレートの変数セクションに **copy** 要素を追加することにより、デプロイ時に変数の項目数を動的に設定できます。 テンプレートの構文を繰り返す必要もありません。

また、[リソース](copy-resources.md)と[リソース内のプロパティ](copy-properties.md)で copy を使用することもできます。

## <a name="variable-iteration"></a>変数の反復処理

この copy 要素には、次の一般的な形式があります。

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

**name** プロパティは、ループを識別する任意の値です。 **count** プロパティは、変数に必要な反復回数を指定します。

**input** プロパティは、繰り返すプロパティを指定します。 **input** プロパティの値から構築される要素の配列を作成します。 それは、1 つのプロパティ (文字列など) にすることも、複数のプロパティを持つオブジェクトにすることもできます。

次の例では、文字列値の配列を作成する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

前のテンプレートは、次の値を持つ配列を返します。

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

次の例では、name、diskSizeGB、および diskIndex という 3 つのプロパティを持つオブジェクトの配列を作成する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

前の例は、次の値を持つ配列を返します。

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> 変数の反復処理では、オフセット引数がサポートされます。 オフセットは、copyIndex('diskNames', 1) など、反復処理の名前の後に指定する必要があります。 オフセット値を指定しない場合、最初のインスタンスでは、既定値は 0 になります。
>

また、変数内で copy 要素を使用することもできます。 次の例は、配列を値の 1 つとして持つオブジェクトを作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

前の例は、次の値を持つオブジェクトを返します。

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

次の例では、変数で copy を使用できるさまざまな方法を示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure PowerShell 2.6 以降、Azure CLI 2.0.74 以降、または REST API バージョン **2019-05-10** 以降を使用してテンプレートをデプロイする場合は、count を 0 に設定できます。 以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

## <a name="example-templates"></a>サンプル テンプレート

次の例では、1 つの変数に対して複数の値を作成するための一般的なシナリオを示します。

|Template  |説明  |
|---------|---------|
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |変数を反復処理する各種の方法を示します。 |
|[Multiple security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |ネットワーク セキュリティ グループに複数のセキュリティ規則をデプロイします。 セキュリティ規則はパラメーターから構築されます。 パラメーターについては、[複数の NSG パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)に関するページを参照してください。 |

## <a name="next-steps"></a>次のステップ

* チュートリアルについては、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
* copy 要素のその他の用途については、「[Azure Resource Manager テンプレートでのリソースの反復](copy-resources.md)」および「[Azure Resource Manager テンプレートでのプロパティの反復](copy-properties.md)」を参照してください。
* テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](template-syntax.md)」を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](deploy-powershell.md)」を参照してください。

