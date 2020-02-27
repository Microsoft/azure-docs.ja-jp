---
title: カスタム リソースのプロキシのリファレンス
description: Azure カスタム リソース プロバイダーのカスタム リソースのプロキシのリファレンス。 この記事では、プロキシ カスタム リソースを実装するエンドポイントの要件とベスト プラクティスについて説明します。
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2020
ms.locfileid: "75649199"
---
# <a name="custom-resource-proxy-reference"></a>カスタム リソースのプロキシのリファレンス

この記事では、プロキシ カスタム リソースを実装するエンドポイントの要件とベスト プラクティスについて説明します。 Azure カスタム リソースプロバイダーについてよくご存じでない場合は、[カスタム リソースプロバイダーの概要](overview.md)に関するページを参照してください。

## <a name="how-to-define-a-proxy-resource-endpoint"></a>プロキシ リソース エンドポイントの定義方法

プロキシ リソースは、**routingType** を "Proxy" と指定することで作成できます。

サンプルのカスタム リソースプロバイダー:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>プロキシ リソース エンドポイントの構築

"プロキシ" リソース **エンドポイント**を実装する**エンドポイント**では、Azure で新しい API 向けに要求と応答を処理する必要があります。 この場合、**resourceType** により、シングル リソース上で CRUD を実行するための `PUT`、`GET`、`DELETE` 用、そしてすべての既存リソースを取得するための `GET` 用に、新しい Azure リソース API が生成されます。

> [!NOTE]
> `id`、`name`、`type` フィールドは必須ではありません。ただし、カスタム リソースを既存の Azure エコシステムと統合する場合は必要となります。

サンプルの応答:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

パラメーター リファレンス:

プロパティ | サンプル | 説明
---|---|---
name | '{myCustomResourceName}' | カスタム リソースの名前。
型 | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | リソースの種類の名前空間。
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | リソース ID。

### <a name="create-a-custom-resource"></a>カスタム リソースの作成

Azure API 着信要求:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>カスタム リソースの削除

Azure API 着信要求:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同様に、**エンドポイント**からの応答は次に顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>カスタム リソースの取得

Azure API 着信要求:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>すべてのカスタム リソースの列挙

Azure API 着信要求:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

この要求は、その後、次の形式で**エンドポイント**に転送されます。

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

同様に、**エンドポイント**からの応答は、その後顧客に返されます。 エンドポイントからの応答は、次のように返される必要があります。

- 有効な JSON オブジェクト ドキュメント。 すべての配列と文字列は、最上位のオブジェクトの下で入れ子にする必要があります。
- `Content-Type` ヘッダーは "application/json; charset=utf-8" に設定される必要があります。
- リソースの一覧は最上位レベルの `value` プロパティの下に配置する必要があります。

**エンドポイント**の応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Azure カスタム リソースプロバイダーの応答:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

- [Azure カスタム リソースプロバイダーの概要](overview.md)
- [クイック スタート:Azure カスタム リソースプロバイダーの作成とカスタム リソースのデプロイ](./create-custom-provider.md)
- [チュートリアル:Azure でカスタム アクションとカスタム リソースを作成する](./tutorial-get-started-with-custom-providers.md)
- [方法: カスタム アクションを Azure REST API に追加する](./custom-providers-action-endpoint-how-to.md)
- [リファレンス: カスタム リソースのキャッシュのリファレンス](proxy-cache-resource-endpoint-reference.md)
