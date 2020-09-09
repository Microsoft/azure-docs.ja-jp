---
title: テンプレート関数 - 日付
description: Azure Resource Manager テンプレートで、日付の操作に使用する関数について説明します。
ms.topic: conceptual
ms.date: 06/22/2020
ms.openlocfilehash: abdc88ce15279b90f8f9dc05a38a2ae236498f12
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058046"
---
# <a name="date-functions-for-arm-templates"></a>ARM テンプレート用の日付関数

Resource Manager では、Azure Resource Manager (ARM) テンプレートで日付を操作するために、次の関数が提供されています。

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

ベースの値に期間を加算します。 ISO 8601 形式である必要があります。

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| base | はい | string | 加算する期間の開始日時の値。 [ISO 8601 タイムスタンプの形式](https://en.wikipedia.org/wiki/ISO_8601)を使用します。 |
| duration | はい | string | ベースに加算する時間の値。 負の値を指定することができます。 [ISO 8601 期間の形式](https://en.wikipedia.org/wiki/ISO_8601#Durations)を使用します。 |
| format | いいえ | string | 日時の結果の出力形式。 指定しない場合、ベース値の形式が使用されます。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="return-value"></a>戻り値

ベースに期間の値を加算した結果の datetime 値。

### <a name="examples"></a>例

次のテンプレート例は、時間の値を加算するさまざまな方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]"
        }
    },
    "variables": {
        "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
        "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
        "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [],
    "outputs": {
        "add3Years": {
            "value": "[variables('add3Years')]",
            "type": "string"
        },
        "subtract9Days": {
            "value": "[variables('subtract9Days')]",
            "type": "string"
        },
        "add1Hour": {
            "value": "[variables('add1Hour')]",
            "type": "string"
        },
    }
}
```

以前のテンプレートがベースの日時 `2020-04-07 14:53:14Z`でデプロイされている場合、出力は次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| add3Years | String | 4/7/2023 2:53:14 PM |
| subtract9Days | String | 3/29/2020 2:53:14 PM |
| add1Hour | String | 4/7/2020 3:53:14 PM |

次のテンプレート例は、Automation スケジュールの開始日時を設定する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "omsAutomationAccountName": {
            "type": "string",
            "defaultValue": "demoAutomation",
            "metadata": {
                "description": "Use an existing Automation account."
            }
        },
        "scheduleName": {
            "type": "string",
            "defaultValue": "demoSchedule1",
            "metadata": {
                "description": "Name of the new schedule."
            }
        },
        "baseTime":{
            "type":"string",
            "defaultValue": "[utcNow('u')]",
            "metadata": {
                "description": "Schedule will start one hour from this time."
            }
        }
    },
    "variables": {
        "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
    },
    "resources": [
        {
            "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",
            "type": "microsoft.automation/automationAccounts/schedules",
            "apiVersion": "2015-10-31",
            "location": "eastus2",
            "tags": {
            },
            "properties": {
                "description": "Demo Scheduler",
                "startTime": "[variables('startTime')]",
                "isEnabled": "true",
                "interval": 1,
                "frequency": "hour"
            }
        }
    ],
    "outputs": {
    }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

指定された形式で現在 (UTC) の datetime 値を返します。 形式が指定されていない場合は、ISO 8601 (yyyyMMddTHHmmssZ) 形式を使用します。 **この関数は、パラメーターの既定値でのみ使用できます。**

### <a name="parameters"></a>パラメーター

| パラメーター | 必須 | Type | 説明 |
|:--- |:--- |:--- |:--- |
| format |いいえ |string |文字列に変換する URI エンコードされた値。 [標準書式指定文字列](/dotnet/standard/base-types/standard-date-and-time-format-strings)または[カスタム書式指定文字列](/dotnet/standard/base-types/custom-date-and-time-format-strings)を使用します。 |

### <a name="remarks"></a>解説

この関数は、パラメーターの既定値に対する式の中でのみ使用できます。 この関数をテンプレートのその他の場所で使用すると、エラーが返されます。 テンプレートの他の場所でこの関数を使用することは、呼び出しのたびに異なる値が返されるため、許可されていません。 同じパラメーターで同じテンプレートをデプロイしても、同じ結果が生成される保証はありません。

[エラー発生時に以前の正常なデプロイにロールバックするオプション](rollback-on-error.md)を使用し、以前のデプロイに utcNow を使用するパラメーターが含まれている場合、パラメーターは再評価されません。 代わりに、以前のデプロイのパラメーター値が、ロールバック デプロイで自動的に再利用されます。

既定値に対する utcNow 関数に依存するテンプレートを再デプロイするときは注意が必要です。 再デプロイを行うときに、パラメーターの値を指定しないと、関数が再評価されます。 新しいリソースを作成するのではなく、既存のリソースを更新する場合は、以前のデプロイのパラメーター値を渡します。

### <a name="return-value"></a>戻り値

現在の UTC の datetime 値。

### <a name="examples"></a>例

次の例のテンプレートでは、datetime 値のさまざまな形式を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

前の例からの出力はデプロイごとに変わりますが、次のようになります。

| 名前 | Type | 値 |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

次の例では、タグ値を設定するときに関数からの値を使用する方法を示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager テンプレートのセクションの説明については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
