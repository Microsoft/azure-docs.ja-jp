---
title: テンプレート関数 - 論理
description: Azure Resource Manager テンプレートで論理値を判定するために使用する関数について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8fe1c00240fc24c3c1454b118f9e0d9a9d54fe4e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677391"
---
# <a name="logical-functions-for-arm-templates"></a>ARM テンプレート用の論理関数

Resource Manager には、Azure Resource Manager (ARM) テンプレートで比較を行うための関数がいくつか用意されています。

* [and](#and)
* [bool](#bool)
* [if](#if)
* [not](#not)
* [or](#or)

## <a name="and"></a>and

`and(arg1, arg2, ...)`

すべてのパラメーター値が true かどうかを確認します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| 残りの引数 |いいえ |boolean |true かどうかを確認する追加の引数。 |

### <a name="return-value"></a>戻り値

すべての値が true の場合は **True** を、それ以外の場合は **False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="bool"></a>[bool]

`bool(arg1)`

パラメーターをブール値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |文字列または整数 |ブール値に変換する値。 |

### <a name="return-value"></a>戻り値
変換後の値のブール値。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json)では、ブール値を文字列または整数と共に使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "trueString": {
            "value": "[bool('true')]",
            "type" : "bool"
        },
        "falseString": {
            "value": "[bool('false')]",
            "type" : "bool"
        },
        "trueInt": {
            "value": "[bool(1)]",
            "type" : "bool"
        },
        "falseInt": {
            "value": "[bool(0)]",
            "type" : "bool"
        }
    }
}
```

既定値を使用した場合の前の例の出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| trueString | Bool | True |
| falseString | Bool | False |
| trueInt | Bool | True |
| falseInt | Bool | False |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

条件が true か false かに基づいて値を返します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| condition |はい |boolean |true か false かどうかを確認する値。 |
| trueValue |はい | string、int、object、または array |条件が true の場合に返される値。 |
| falseValue |はい | string、int、object、または array |条件が false の場合に返される値。 |

### <a name="return-value"></a>戻り値

最初のパラメーターが **True** の場合、2 番目のパラメーターを返します。そうでない場合、3 番目のパラメーターを返します。

### <a name="remarks"></a>解説

条件が **True** の場合、true の値のみが評価されます。 条件が **False** の場合、false の値のみが評価されます。 **if** 関数の場合、条件付きで有効な式のみを含めることができます。 たとえば、1 つの条件の下で存在し、他の条件下では存在しないリソースを参照できます。 条件付きでの式の評価の例については、次のセクションを参照してください。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json)では、`if` 関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "yesOutput": {
            "type": "string",
            "value": "[if(equals('a', 'a'), 'yes', 'no')]"
        },
        "noOutput": {
            "type": "string",
            "value": "[if(equals('a', 'b'), 'yes', 'no')]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| yesOutput | String | はい |
| noOutput | String | no |
| objectOutput | Object | { "test": "value1" } |

次の[テンプレート例](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json)では、条件付きで有効な式限定で、この関数を使用する方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "logAnalytics": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "condition": "[not(empty(parameters('logAnalytics')))]",
            "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2017-03-30",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
                },
                "protectedSettings": {
                    "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
                }
            }
        }
    ],
    "outputs": {
        "mgmtStatus": {
            "type": "string",
            "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
        }
    }
}
```

## <a name="not"></a>not

`not(arg1)`

ブール値をその反対の値に変換します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |変換する値。 |

### <a name="return-value"></a>戻り値

パラメーターが **False** の場合、**True** を返します。 パラメーターが **True** の場合、**False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json)では、**not** と [equals](template-functions-comparison.md#equals) を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    ],
    "outputs": {
        "checkNotEquals": {
            "type": "bool",
            "value": "[not(equals(1, 2))]"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="or"></a>or

`or(arg1, arg2, ...)`

いずれかのパラメーター値が true かどうかを確認します。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| arg1 |はい |boolean |true かどうかを確認する最初の値。 |
| arg2 |はい |boolean |true かどうかを確認する 2 番目の値。 |
| 残りの引数 |いいえ |boolean |true かどうかを確認する追加の引数。 |

### <a name="return-value"></a>戻り値

いずれかの値が true の場合は **True** を返し、そうでない場合は **False** を返します。

### <a name="examples"></a>例

次の[テンプレート例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json)では、論理関数を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [ ],
    "outputs": {
        "andExampleOutput": {
            "value": "[and(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "orExampleOutput": {
            "value": "[or(bool('true'), bool('false'))]",
            "type": "bool"
        },
        "notExampleOutput": {
            "value": "[not(bool('true'))]",
            "type": "bool"
        }
    }
}
```

前の例からの出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| andExampleOutput | Bool | False |
| orExampleOutput | Bool | True |
| notExampleOutput | Bool | False |

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。

