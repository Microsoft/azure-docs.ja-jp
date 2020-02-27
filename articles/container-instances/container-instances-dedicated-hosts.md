---
title: 専用ホストにデプロイする
description: 専用ホストを使用して、Azure Container Instances ワークロードに対して真のホストレベルの分離を実現します
ms.topic: article
ms.date: 01/17/2020
author: dkkapur
ms.author: dekapur
ms.openlocfilehash: adad0ddfc78530b3a3a7c139d9a95ec4790c8053
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934145"
---
# <a name="deploy-on-dedicated-hosts"></a>専用ホストへのデプロイ

"Dedicated" は、コンテナーを安全に実行するための分離された専用コンピューティング環境を提供する Azure Container Instances (ACI) SKU です。 Dedicated SKU を使用すると、各コンテナー グループに Azure データセンター内の専用物理サーバーが割り当てられるため、組織のセキュリティおよびコンプライアンス要件を満たすために役立つ完全なワークロードの分離が保証されます。 

Dedicated SKU は、物理サーバーの観点からワークロードの分離を必要とするコンテナー ワークロードに適しています。

## <a name="prerequisites"></a>前提条件

* Dedicated SKU を使用するサブスクリプションに対する既定の制限は 0 です。 この SKU を運用コンテナーのデプロイに使用する場合は、専用の上限を上げるための [Azure サポート リクエスト][azure-support]を作成してください。

## <a name="use-the-dedicated-sku"></a>Dedicated SKU を使用する

> [!IMPORTANT]
> Dedicated SKU の使用は、現在ロールアウトされている最新の API バージョン (2019-12-01) でのみ使用できます。デプロイ テンプレートでこの API バージョンを指定してください。
>

API バージョン 2019-12-01 以降、デプロイ テンプレートのコンテナー グループのプロパティのセクションに、ACI のデプロイに必要な `sku` プロパティが存在します。 現在、このプロパティは、ACI 用の Azure Resource Manager デプロイ テンプレートの一部として使用できます。 テンプレートを使用した ACI リソースのデプロイについて詳しくは、「[チュートリアル: Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group)」をご覧ください。 

`sku` プロパティの値は、次のいずれかが可能です。
* `Standard` - ACI の標準的なデプロイのための選択肢。ハイパーバイザー レベルのセキュリティが引き続き保証されます。 
* `Dedicated` - コンテナー グループ専用の物理ホストによるワークロード レベルの分離に使用されます。

## <a name="modify-your-json-deployment-template"></a>JSON デプロイ テンプレートを変更する

デプロイ テンプレートで、次のプロパティを変更または追加します。
* `resources` の下で、`apiVersion` を `2012-12-01` に設定します。
* コンテナー グループのプロパティの下に、`Dedicated` プロパティと値 `sku` を追加します。

Dedicated SKU を使用するコンテナー グループ デプロイ テンプレートのリソース セクションのスニペットの例を次に示します。

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "sku": "Dedicated",
            "containers": {
                [...]
            }
        }
    }
]
```

次に示すのは、単一のコンテナー インスタンスを実行中のサンプル コンテナー グループをデプロイする完全なテンプレートです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Dedicated",
                "containers": [
                    {
                        "name": "container1",
                        "properties": {
                            "image": "nginx",
                            "command": [
                                "/bin/sh",
                                "-c",
                                "while true; do echo `date`; sleep 1000000; done"
                            ],
                            "ports": [
                                {
                                    "protocol": "TCP",
                                    "port": 80
                                }
                            ],
                            "environmentVariables": [],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "Always",
                "ipAddress": {
                    "ports": [
                        {
                            "protocol": "TCP",
                            "port": 80
                        }
                    ],
                    "type": "Public"
                },
                "osType": "Linux"
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-your-container-group"></a>コンテナー グループをデプロイする

デスクトップ上でデプロイ テンプレート ファイルを作成および編集した場合は、そのファイルをドラッグすることによって Cloud Shell ディレクトリにアップロードできます。 

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] コマンドで、テンプレートをデプロイします。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

数秒以内に、Azure から最初の応答を受信します。 専用ホストで正常なデプロイが実行されます。

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
