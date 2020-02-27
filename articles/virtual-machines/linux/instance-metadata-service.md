---
title: Azure Instance Metadata Service
description: Linux VM のコンピューティング、ネットワーク、および今後のメンテナンス イベントに関する情報を取得するための RESTful インターフェイスです。
services: virtual-machines-linux
documentationcenter: ''
author: KumariSupriya
manager: paulmey
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: 0e04f7e190ef22fb5c2b288e478cac5ffaf89141
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962511"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Azure Instance Metadata Service (IMDS) は、現在実行中の仮想マシン インスタンスに関する情報を提供し、仮想マシンの管理と構成に使用できます。
提供される情報には、SKU、ネットワークの構成、今後のメンテナンス イベントなどがあります。 使用できるデータの完全な一覧については、「[メタデータ API](#metadata-apis)」を参照してください。

Azure Instance Metadata Service は、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成されたすべての IaaS VM にアクセスできる REST エンドポイントです。
このエンドポイントは、VM からのみアクセスすることができる、よく知られているルーティング不可能な IP アドレス (`169.254.169.254`) で使用できます。

> [!IMPORTANT]
> このサービスは、すべての Azure リージョンで**一般公開**されています。  仮想マシン インスタンスに関する新しい情報を公開する更新が定期的に行われています。 このページには、使用できる最新の[メタデータ API](#metadata-apis) が反映されています。

## <a name="service-availability"></a>サービスの提供状況

このサービスは、一般公開されている Azure リージョンで使用できます。 すべての API バージョンが、すべての Azure リージョンで利用可能なわけではありません。

リージョン                                        | 提供状況                                 | サポートされているバージョン
-----------------------------------------------|-----------------------------------------------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | 一般公開 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15、2019-11-01
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | 一般公開 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15、2019-11-01
[Azure China 21Vianet](https://www.azure.cn/)                                            | 一般公開 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15、2019-11-01
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | 一般公開 | 2017-04-02、2017-08-01、2017-12-01、2018-02-01、2018-04-02、2018-10-01、2019-02-01、2019-03-11、2019-04-30、2019-06-01、2019-06-04、2019-08-01、2019-08-15、2019-11-01

この表は、サービスが更新された場合や、バージョンが新しくサポートされた場合に更新されます。

Instance Metadata Service を試すには、上記のリージョンで [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) または [Azure ポータル](https://portal.azure.com)から VM を作成し、この後の例に従います。
IMDS のクエリ方法のその他の例については、[Azure Instance Metadata のサンプル](https://github.com/microsoft/azureimds)を参照してください。

## <a name="usage"></a>使用法

### <a name="versioning"></a>バージョン管理

Instance Metadata Service はバージョン管理されています。HTTP 要求で API バージョンを指定することは必須です。

この[可用性テーブル](#service-availability)に一覧表示されている最新バージョンを確認できます。

新しいバージョンが追加されても、特定のデータ形式への依存関係がスクリプトにある場合、互換性を確保するために古いバージョンにもアクセスできます。

バージョンが指定されていない場合は、最新のサポートされているバージョンの一覧と共にエラーが返されます。

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**応答**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>ヘッダーの使用

Instance Metadata Service のクエリを実行するときは、要求が意図せずリダイレクトされないように、`Metadata: true` ヘッダーを指定する必要があります。

### <a name="retrieving-metadata"></a>メタデータの取得

インスタンス メタデータは、[Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) を使用して作成/管理されている実行中の VM で使用できます。 仮想マシン インスタンスのすべてのデータ カテゴリにアクセスするには、次の要求を使用します。

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> すべてのインスタンス メタデータ クエリでは、大文字小文字が区別されます。

### <a name="data-output"></a>データ出力

既定では、Instance Metadata Service は JSON 形式でデータを返します (`Content-Type: application/json`)。 ただし、要求すれば、別の API から別の形式でデータが返されます。
次の表は、API がサポートできるその他のデータ形式を示しています。

API | 既定のデータ形式 | その他の形式
--------|---------------------|--------------
/instance | json | text
/scheduledevents | json | なし
/attested | json | なし

既定以外の応答形式にアクセスするには、要求のクエリ文字列パラメーターとして要求の形式を指定します。 次に例を示します。

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> リーフ ノードでは `format=json` は機能しません。 これらのクエリでは、既定の形式が json の場合は、`format=text` を明示的に指定する必要があります。

### <a name="security"></a>Security

Instance Metadata Service エンドポイントには、実行中の仮想マシン インスタンスからのみ、ルーティング不可能な IP アドレスでアクセスできます。 さらに、`X-Forwarded-For` ヘッダーがあるすべての要求は、サービスによって拒否されます。
また、実際の要求が意図しないリダイレクトの一部として行われたのではなく、直接意図されたものであったことを明確に示すために、要求に `Metadata: true`ヘッダーを含める必要があります。

### <a name="error"></a>エラー

見つからないデータ要素または無効な形式の要求がある場合、Instance Metadata Service は標準 HTTP エラーを返します。 次に例を示します。

HTTP 状態コード | 理由
----------------|-------
200 OK |
400 Bad Request | リーフ ノードのクエリ時に `Metadata: true` ヘッダーがないか、その形式になっていません。
404 見つかりません | 要求された要素は存在しません
405 Method Not Allowed | `GET` 要求のみがサポートされています
429 Too Many Requests | 現在 API は、1 秒あたり最大 5 つのクエリをサポートしています
500 Service Error     | しばらくしてからやり直してください

### <a name="examples"></a>例

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次のすべての例の応答は、読みやすくするために整えられています。

#### <a name="retrieving-network-information"></a>ネットワーク情報の取得

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="retrieving-public-ip-address"></a>パブリック IP アドレスの取得

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>インスタンスのすべてのメタデータの取得

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Prod;Role:WorkerRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Windows 仮想マシンでのメタデータの取得

**要求**

インスタンス メタデータは、Windows で Powershell ユーティリティ `curl` を使用して取得できます。 

```bash
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

または、`Invoke-RestMethod` コマンドレットを使用して取得できます。

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get 
```

**応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>メタデータ API

次の API は、メタデータ エンドポイントを介して使用できます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
attested | 「[構成証明済みデータ](#attested-data)」をご覧ください | 2018-10-01
identity | Azure リソースのマネージド ID。 「[アクセス トークンの取得](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)」を参照してください | 2018-02-01
instance | 「[インスタンス API](#instance-api)」を参照してください | 2017-04-02
scheduledevents | [スケジュールされたイベント](scheduled-events.md)に関する記事を参照してください。 | 2017-08-01

#### <a name="instance-api"></a>インスタンス API

次のコンピューティング カテゴリは、インスタンス API を通じて使用できます。

> [!NOTE]
> 次のカテゴリは、メタデータ エンドポイントを通じて、インスタンス/コンピューティングを介してアクセスされます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
azEnvironment | VM が実行されている Azure 環境 | 2018-10-01
customData | この機能は現在無効になっており、使用可能になると、このドキュメントは更新されます | 2019-02-01
location | VM を実行中の Azure リージョン | 2017-04-02
name | VM の名前 | 2017-04-02
offer | VM イメージのオファーの情報。Azure イメージ ギャラリーからデプロイされるイメージについてのみ存在します。 | 2017-04-02
osType | Linux または Windows | 2017-04-02
placementGroupId | お使いの仮想マシン スケール セットの[配置グループ](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) | 2017-08-01
プラン | VM が Azure Marketplace イメージである場合、[プラン](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)にはその名前、製品、および発行元が含まれています | 2018-04-02
platformUpdateDomain |  VM を実行中の[更新ドメイン](manage-availability.md) | 2017-04-02
platformFaultDomain | VM を実行中の[障害ドメイン](manage-availability.md) | 2017-04-02
provider | VM のプロバイダー | 2018-10-01
publicKeys | VM とパスに割り当てられた[公開キーのコレクション](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) | 2018-04-02
publisher | VM イメージの発行元 | 2017-04-02
resourceGroupName | お使いの仮想マシンの[リソース グループ](../../azure-resource-manager/management/overview.md) | 2017-08-01
resourceId | リソースの[完全修飾](https://docs.microsoft.com/rest/api/resources/resources/getbyid) ID | 2019-03-11
sku | VM イメージの特定の SKU | 2017-04-02
storageProfile | [ストレージ プロファイル](#storage-profile)を参照してください | 2019-06-01
subscriptionId | 仮想マシンの Azure サブスクリプション | 2017-08-01
tags | お使いの仮想マシンの[タグ](../../azure-resource-manager/management/tag-resources.md)  | 2017-08-01
tagsList | プログラムによる解析を簡単にするために JSON 配列として書式設定されたタグ  | 2019-06-04
version | VM イメージのバージョン | 2017-04-02
vmId | VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) | 2017-04-02
vmScaleSetName | お使いの仮想マシン スケール セットの[仮想マシン スケール セット名](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) | 2017-12-01
vmSize | [VM サイズ](sizes.md) | 2017-04-02
ゾーン | 仮想マシンの[可用性ゾーン](../../availability-zones/az-overview.md) | 2017-12-01

次のネットワーク カテゴリは、インスタンス API を介して使用できます。

> [!NOTE]
> 次のカテゴリは、メタデータ エンドポイント経由で、インスタンス/ネットワーク/インターフェイスを介してアクセスされます。

Data | 説明 | 導入されたバージョン
-----|-------------|-----------------------
ipv4/privateIpAddress | VM のローカル IPv4 アドレス | 2017-04-02
ipv4/publicIpAddress | VM のパブリック IPv4 アドレス | 2017-04-02
subnet/address | VM のサブネット アドレス | 2017-04-02
subnet/prefix | サブネットのプレフィックス (24 など) | 2017-04-02
ipv6/ipAddress | VM のローカル IPv6 アドレス | 2017-04-02
macAddress | VM の mac アドレス | 2017-04-02

## <a name="attested-data"></a>構成証明済みデータ

Instance Metadata Service によって提供されるシナリオの一部は、提供されるデータが Azure から送信されたものであることを保証するものです。 マーケットプレースのイメージが Azure で実行されるイメージであることを確認できるよう、この情報の一部に署名します。

### <a name="example-attested-data"></a>構成証明済みデータの例

> [!NOTE]
> すべての API の応答は、JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

 **要求**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

api-version は必須フィールドです。 サポートされる API バージョンについては、「[サービスの提供状況](#service-availability)」セクションを参照してください。
nonce は、省略可能な 10 桁の文字列です。 指定しない場合は、その代わりに IMDS によって現在の UTC タイムスタンプが返されます。 IMDS のキャッシュ メカニズムにより、以前にキャッシュされた nonce 値が返される場合もあります。

 **応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

signature BLOB は、ドキュメントの [pkcs7](https://aka.ms/pkcs7) で署名されたバージョンです。 署名に使用される証明書と共に、ドキュメントの作成と有効期限のための vmId、sku、nonce、subscriptionId、timeStamp のような VM の詳細とイメージに関するプラン情報が含まれています。 プラン情報は Azure Marketplace のイメージにのみ設定されます。 証明書は、応答から抽出でき、応答が有効で Azure から送られたものであることを検証するために使用できます。

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Windows 仮想マシンでの構成証明済みメタデータの取得

 **要求**

インスタンス メタデータは、Windows で Powershell ユーティリティ `curl` を使用して取得できます。

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 または、`Invoke-RestMethod` コマンドレットを使用して取得できます。

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

api-version は必須フィールドです。 サポートされる API バージョンについては、「サービスの提供状況」セクションを参照してください。
nonce は、省略可能な 10 桁の文字列です。 指定しない場合は、その代わりに IMDS によって現在の UTC タイムスタンプが返されます。 IMDS のキャッシュ メカニズムにより、以前にキャッシュされた nonce 値が返される場合もあります。

 **応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

signature BLOB は、ドキュメントの [pkcs7](https://aka.ms/pkcs7) で署名されたバージョンです。 署名に使用される証明書と共に、ドキュメントの作成と有効期限のための vmId、sku、nonce、subscriptionId、timeStamp のような VM の詳細とイメージに関するプラン情報が含まれています。 プラン情報は Azure Marketplace のイメージにのみ設定されます。 証明書は、応答から抽出でき、応答が有効で Azure から送られたものであることを検証するために使用できます。


## <a name="example-scenarios-for-usage"></a>使用に関するシナリオ例  

### <a name="tracking-vm-running-on-azure"></a>Azure で動作している VM の追跡

サービス プロバイダーは、ソフトウェアを実行している VM の数を追跡する必要があったり、VM の一意性を追跡する必要のあるエージェントを使用していたりする場合があります。 VM の一意の ID を取得できるようにするには、Instance Metadata Service から `vmId` フィールドを使用します。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**応答**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>障害/更新ドメインに基づくコンテナー、データ パーティションの配置

特定のシナリオでは、異なるレプリカの配置が非常に重要です。 たとえば、[Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) を介した[ HDFS レプリカの配置](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps)またはコンテナーの配置では、VM が実行されている `platformFaultDomain` と `platformUpdateDomain` を把握しておかなければならない場合があります。
これらの決定を行うために、インスタンスの[可用性ゾーン](../../availability-zones/az-overview.md)を使用することもできます。
このデータは、Instance Metadata Service を使用して直接照会できます。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**応答**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>サポート ケースの際の VM に関する詳細情報の取得

サービス プロバイダーとして、VM の詳細情報を得る必要があるサポート コールを受けることがあります。 顧客にコンピューティング メタデータの共有を依頼すると、サポート担当者が Azure 上の VM の種類を確認するための基本情報を得られる場合があります。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
        "dataDisks": [
          {
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
              "uri": ""
            },
            "lun": "0",
            "managedDisk": {
              "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
              "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
              "uri": ""
            },
            "writeAcceleratorEnabled": "false"
          }
        ],
        "imageReference": {
          "id": "",
          "offer": "UbuntuServer",
          "publisher": "Canonical",
          "sku": "16.04.0-LTS",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": "30",
          "diffDiskSettings": {
            "option": "Local"
          },
          "encryptionSettings": {
            "enabled": "false"
          },
          "image": {
            "uri": ""
          },
          "managedDisk": {
            "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
            "storageAccountType": "Standard_LRS"
          },
          "name": "exampleosdiskname",
          "osType": "Linux",
          "vhd": {
            "uri": ""
          },
          "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>VM が実行されている Azure 環境の取得

Azure には、[Azure Government](https://azure.microsoft.com/overview/clouds/government/) など多数のソブリン クラウドがあります。 ランタイムの決定を行うために、Azure 環境が必要な場合があります。 次の例では、この動作を実現する方法を示します。

**要求**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**応答**
```bash
AzurePublicCloud
```
クラウドと Azure 環境の値を以下に示します。

 クラウド   | Azure 環境
---------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>VM のタグの取得

論理的に分類するために Azure VM にタグが適用されている場合があります。 次の要求を使用して、VM に割り当てられたタグを取得できます。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**応答**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` フィールドは、セミコロンで区切られたタグを含む文字列です。 これは、タグ自体でセミコロンが使用されている場合に問題になることがあります。 プログラムでタグを抽出するようにパーサーが記述されている場合、`tagsList` フィールドに依存してください。これは区切り記号のない JSON 配列であり、結果的に解析が楽になります。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=json"
```

**応答**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

### <a name="validating-that-the-vm-is-running-in-azure"></a>VM が Azure で実行されていることの検証

Marketplace ベンダーは、自分たちのソフトウェアが Azure でのみ実行されるようにライセンスされていることを確認することを望みます。 誰かがオンプレミスの外に VHD をコピーした場合、それを検出する機能が必要です。 Instance Metadata Service を呼び出すことによって、Marketplace ベンダーは、応答が Azure からのみであることを保証する署名付きデータを取得できます。

> [!NOTE]
> jq をインストールする必要があります。

**要求**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **応答**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Data | 説明
-----|------------
nonce | 要求でのユーザー提供の省略可能な文字列。 要求で nonce が提供されなかった場合は、現在の UTC タイムスタンプが返されます
プラン | VM の Azure Marketplace イメージの[プラン](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan)には、名前、製品、および発行元が含まれています
timestamp/createdOn | 最初の署名付きドキュメントが作成された UTC タイムスタンプ
timestamp/expiresOn | 署名付きドキュメントの有効期限が切れる UTC タイムスタンプ
vmId |  VM の[一意の識別子](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/)
subscriptionId | `2019-04-30` で導入された、仮想マシンの Azure サブスクリプション
sku | VM イメージの特定の SKU、`2019-11-01` で導入

#### <a name="verifying-the-signature"></a>署名の検証

上記の署名を取得した後は、署名が Microsoft からのものであることを確認できます。 また、中間証明書と証明書チェーンも確認できます。 最後に、サブスクリプション ID が正しいことを確認できます。

> [!NOTE]
> パブリック クラウド用の証明書とソブリン クラウド用の証明書は異なります。

 クラウド | Certificate
---------|-----------------
[一般公開されている全世界のすべての Azure リージョン](https://azure.microsoft.com/regions/)     | metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | metadata.azure.us
[Azure China 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | metadata.azure.cn
[Azure Germany](https://azure.microsoft.com/overview/clouds/germany/)                    | metadata.microsoftazure.de

```bash

# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

検証時、ネットワークの制約が原因で中間証明書をダウンロードできない場合は、中間証明書をピン留めできます。 ただし、Azure では、標準 PKI 手順に従って、証明書がロールオーバーされます。 ピン留めされた証明書は、ロールオーバーの実行時に更新する必要があります。 中間証明書を更新する変更を計画すると、そのたびに、Azure ブログが更新され、Azure の顧客に通知されます。 中間証明書は、[こちら](https://www.microsoft.com/pki/mscorp/cps/default.htm)で確認できます。 中間証明書は、リージョンごとに異なる場合があります。

### <a name="failover-clustering-in-windows-server"></a>Windows Server でのフェールオーバー クラスタリング

一部のシナリオでは、フェールオーバー クラスタリングで Instance Metadata Service のクエリを実行する際に、ルーティング テーブルにルートを追加する必要があります。

1. 管理者特権でコマンド プロンプトを開きます。

2. 次のコマンドを実行し、IPv4 ルーティング テーブルのネットワーク宛先のインターフェイスのアドレス (`0.0.0.0`) をメモします。

```bat
route print
```

> [!NOTE] 
> フェールオーバー クラスターが有効になっている Windows Server VM からの次の出力例には、わかりやすくするために IPv4 ルーティング テーブルのみが含まれています。

```bat
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. 次のコマンドを実行し、IPv4 ルーティング テーブルのネットワーク宛先のインターフェイスのアドレス (`0.0.0.0`) を使用します。この例では `10.0.1.10` です。

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>ストレージ プロファイル

Instance Metadata Service では、VM に関連付けられているストレージ ディスクの詳細を提供できます。 このデータは、instance/compute/storageProfile エンドポイントにあります。

VM のストレージ プロファイルは、イメージ参照、OS ディスク、データ ディスクの 3 つのカテゴリに分類されます。

イメージ参照オブジェクトには、OS イメージに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
id      | Resource ID
offer   | プラットフォームまたは marketplace イメージのオファー
publisher | イメージ発行者
sku     | イメージ SKU
version | プラットフォームまたは marketplace イメージのバージョン

OS ディスク オブジェクトには、VM によって使用される OS ディスクに関する次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
image   | ソース ユーザー イメージの仮想ハード ディスク
lun     | ディスクの論理ユニット番号
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで writeAccelerator が有効になっているかどうか

データ ディスク アレイには、VM に接続されているデータ ディスクの一覧が含まれています。 各データ ディスク オブジェクトには、次の情報が含まれています。

Data    | 説明
--------|-----------------
caching | キャッシュの要件
createOption | VM が作成された方法に関する情報
diffDiskSettings | 一時ディスクの設定
diskSizeGB | ディスクのサイズ (GB)
encryptionSettings | ディスクの暗号化の設定
image   | ソース ユーザー イメージの仮想ハード ディスク
managedDisk | マネージド ディスクのパラメーター
name    | ディスク名
osType  | ディスクに含まれている OS の種類
vhd     | 仮想ハード ディスク
writeAcceleratorEnabled | ディスクで writeAccelerator が有効になっているかどうか

VM のストレージ情報のクエリを実行する方法の例を次に示します。

**要求**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**応答**

> [!NOTE]
> 応答は JSON 文字列です。 次の例の応答は、読みやすくするために整えられています。

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>VM 内でさまざまな言語を使用してメタデータ サービスを呼び出す例

Language | 例
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Go  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>よく寄せられる質問

1. `400 Bad Request, Required metadata header not specified` エラーが発生します。 これはどういう意味でしょうか。
   * Instance Metadata Service では、要求に `Metadata: true` ヘッダーを渡す必要があります。 このヘッダーを REST 呼び出しに渡すと、Instance Metadata Service へのアクセスが許可されます。
2. 使用している VM に関するコンピューティング情報を取得できないのはなぜですか。
   * 現時点では、Instance Metadata Service は、Azure Resource Manager で作成されたインスタンスのみをサポートします。 今後、クラウド サービス VM に対するサポートが追加される可能性があります。
3. 少し前に、Azure Resource Manager を介して仮想マシンを作成しました。 コンピューティング メタデータ情報が表示されないのはなぜですか。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
4. 新しいバージョンに入力されたすべてのデータが表示されません。
   * 2016 年 9 月以降に作成されたすべての VM については、[タグ](../../azure-resource-manager/management/tag-resources.md)を追加して、コンピューティング メタデータの表示を開始してください。 それよりも古い VM (2016 年 9 月よりも前に作成された VM) については、VM に対して拡張機能またはデータ ディスクを追加/削除してメタデータを更新してください。
5. `500 Internal Server Error` エラーが発生します。なぜですか。
   * 指数バックオフ システムに基づいて、要求を再試行してください。 問題が解決しない場合は、Azure サポートにお問い合わせください。
6. その他の質問/コメントはどこで共有すればいいですか。
   * コメントは https://feedback.azure.com で送信してください。
7. このサービスは仮想マシン スケール セット インスタンスで機能しますか。
   * はい。Metadata Service は、スケール セット インスタンスで利用できます。
8. このサービスのサポートを受けるにはどうすればよいですか。
   * サービスのサポートを受けるには、時間がかかる再試行の後もメタデータの応答を取得できない VM を管理する Azure portal でサポート問題を作成します。
9. サービスの呼び出しの要求がタイムアウトになりました。
   * メタデータの呼び出しは、VM のプライマリ ネットワーク カードに割り当てられたプライマリ IP アドレスから行う必要があります。また、ルートを変更した場合は、ネットワーク カードからのアドレス 169.254.0.0/16 用のルートが必要です。
10. 仮想マシン スケール セットでタグを更新しましたが、VM とは異なり、インスタンスにタグが表示されません。
    * 現時点では、スケール セットのタグは、再起動/再イメージ化/インスタンスに対するディスクの変更の際に VM に対してのみ表示されます。

    ![Instance Metadata のサポート](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>次のステップ

- 詳細については、「[スケジュール化されたイベント](scheduled-events.md)」を参照してください。
