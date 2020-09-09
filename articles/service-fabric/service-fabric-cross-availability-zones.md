---
title: Availability Zones をまたがるクラスターのデプロイ
description: Availability Zones をまたがる Azure Service Fabric クラスターを作成する方法について説明します。
author: peterpogorski
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: pepogors
ms.openlocfilehash: d763511032ebff9116702b1f649751a4b7b52afd
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518998"
---
# <a name="deploy-an-azure-service-fabric-cluster-across-availability-zones"></a>Availability Zones をまたがる Azure Service Fabric クラスターのデプロイ
Azure の Availability Zones は高可用性を備えたサービスで、アプリケーションとデータをデータセンターの障害から保護します。 可用性ゾーンは、Azure リージョン内に独立した電源、冷却手段、ネットワークを備えた一意の物理的な場所です。

Service Fabric では、特定のゾーンに固定されるノード タイプをデプロイすることによって、Availability Zones をまたがるクラスターをサポートします。 これにより、アプリケーションの高可用性が確保されます。 Azure Availability Zones は一部のリージョンでのみ使用できます。 詳細については、「[Azure の Availability Zones の概要](../availability-zones/az-overview.md)」をご覧ください。

サンプル テンプレートを使用できます。[Service Fabric クロス可用性ゾーン テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)

## <a name="recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones"></a>Availability Zones をまたがる Azure Service Fabric クラスターのプライマリ ノードのタイプに推奨されるトポロジ
Availability Zones 間で分散された Service Fabric クラスターでは、クラスター状態の高可用性が確保されます。 Service Fabric クラスターをゾーン間にまたがらせるには、リージョンでサポートされている各可用性ゾーンにプライマリ ノード タイプを作成する必要があります。 これにより、各プライマリ ノード タイプ間にシード ノードが均等に分散されます。

プライマリ ノード タイプの推奨されるトポロジでは、下記のリソースが必要です。

* Platinum に設定されたクラスター信頼性レベル。
* プライマリとしてマークされた 3 つのノード タイプ。
    * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
    * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。
* Standard SKU を使用した単一のパブリック IP リソース。
* Standard SKU を使用した単一のロード バランサー リソース。
* 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG。

>[!NOTE]
> Service Fabric ではゾーンをまたがる単一の仮想マシン スケール セットがサポートされないため、仮想マシン スケール セットの単一の配置グループ プロパティが true に設定されている必要があります。

 ![Azure Service Fabric 可用性ゾーンのアーキテクチャ][sf-architecture]

## <a name="networking-requirements"></a>ネットワーク要件
### <a name="public-ip-and-load-balancer-resource"></a>パブリック IP とロード バランサーのリソース
仮想マシン スケール セット リソースに対するゾーン プロパティを有効にするには、その仮想マシン スケール セットによって参照されているロード バランサーと IP リソースの両方で *Standard* SKU が使用されている必要があります。 SKU プロパティを使用せずにロード バランサーまたは IP リソースを作成すると、Availability Zones をサポートしない Basic SKU が作成されます。 Standard SKU ロード バランサーは既定で外部からのすべてのトラフィックをブロックします。外部トラフィックを許可するには、NSG をサブネットにデプロイする必要があります。

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat('LB','-', parameters('clusterName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
        "name": "Standard"
    }
}
```

```json
{
    "apiVersion": "2018-11-01",
    "type": "Microsoft.Network/loadBalancers",
    "name": "[concat('LB','-', parameters('clusterName')]", 
    "location": "[parameters('computeLocation')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkSecurityGroups/', concat('nsg', parameters('subnet0Name')))]"
    ],
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "[parameters('addressPrefix')]"
            ]
        },
        "subnets": [
        {
            "name": "[parameters('subnet0Name')]",
            "properties": {
                "addressPrefix": "[parameters('subnet0Prefix')]",
                "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', concat('nsg', parameters('subnet0Name')))]"
              }
            }
          }
        ]
    },
    "sku": {
        "name": "Standard"
    }
}
```

>[!NOTE]
> パブリック IP およびロード バランサー リソースへの SKU のインプレース変更を行うことはできません。 Basic SKU のある既存のリソースから移行する場合は、この記事の移行のセクションを参照してください。

### <a name="virtual-machine-scale-set-nat-rules"></a>仮想マシン スケール セットの NAT 規則
ロード バランサーのインバウンド NAT 規則は、仮想マシン スケール セットの NAT プールと一致する必要があります。 各仮想マシン スケール セットに一意のインバウンド NAT プールが必要です。

```json
{
"inboundNatPools": [
    {
        "name": "LoadBalancerBEAddressNatPool0",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "50999",
            "frontendPortRangeStart": "50000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool1",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "51999",
            "frontendPortRangeStart": "51000",
            "protocol": "tcp"
        }
    },
    {
        "name": "LoadBalancerBEAddressNatPool2",
        "properties": {
            "backendPort": "3389",
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPortRangeEnd": "52999",
            "frontendPortRangeStart": "52000",
            "protocol": "tcp"
        }
    }
    ]
}
```

### <a name="standard-sku-load-balancer-outbound-rules"></a>Standard SKU Load Balancer のアウトバウンド規則
Standard Load Balancer および Standard パブリック IP では、Basic SKU を使用する場合と比較して、アウトバウンド接続に新しい機能とさまざまな動作が導入されています。 Standard SKU を操作するときにアウトバウンド接続が必要な場合は、Standard パブリック IP アドレスまたは Standard パブリック Load Balancer で明示的に定義する必要があります。 詳細については、[アウトバウンド接続](../load-balancer/load-balancer-outbound-connections.md)と [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) に関するページを参照してください。

>[!NOTE]
> 標準テンプレートでは、既定ですべてのアウトバウンド トラフィックを許可する NSG が参照されます。 インバウンド トラフィックは、Service Fabric 管理操作に必要なポートに制限されます。 NSG 規則は、要件に合わせて変更できます。

>[!NOTE]
> Standard SKU SLB を使用しているすべての Service Fabric クラスターでは、ポート 443 での送信トラフィックを許可する規則を確実に各ノード タイプに設定する必要があります。 これはクラスターの設定を完了するために必要であり、このルールのないデプロイは失敗します。


### <a name="enabling-zones-on-a-virtual-machine-scale-set"></a>仮想マシン スケール セットでのゾーンの有効化
ゾーンを有効にするには、仮想マシン スケール セットで、仮想マシン スケール セット リソースに次の 3 つの値を含める必要があります。

* 最初の値は、**zones** プロパティで、仮想マシン スケール セットをデプロイする可用性ゾーンを指定します。
* 2 つ目の値は、"singlePlacementGroup" プロパティで、true に設定する必要があります。
* 3 つ目の値は、Service Fabric 仮想マシン スケール セット拡張の "faultDomainOverride" プロパティです。 このプロパティの値には、この仮想マシン スケール セットを配置するリージョンとゾーンを含める必要があります。 例: "faultDomainOverride": "eastus/az1" Azure Service Fabric クラスターにはクロス リージョンのサポートがないため、すべての仮想マシン スケール セット リソースを同じリージョンに配置する必要があります。

```json
{
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('vmNodeType1Name')]",
    "location": "[parameters('computeLocation')]",
    "zones": ["1"],
    "properties": {
        "singlePlacementGroup": "true",
    },
    "virtualMachineProfile": {
    "extensionProfile": {
    "extensions": [
    {
    "name": "[concat(parameters('vmNodeType1Name'),'_ServiceFabricNode')]",
    "properties": {
        "type": "ServiceFabricNode",
        "autoUpgradeMinorVersion": false,
        "publisher": "Microsoft.Azure.ServiceFabric",
        "settings": {
            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
            "nodeTypeRef": "[parameters('vmNodeType1Name')]",
            "dataPath": "D:\\\\SvcFab",
            "durabilityLevel": "Silver",
            "certificate": {
                "thumbprint": "[parameters('certificateThumbprint')]",
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
            "systemLogUploadSettings": {
                "Enabled": true
            },
            "faultDomainOverride": "eastus/az1"
        },
        "typeHandlerVersion": "1.0"
    }
}
```

### <a name="enabling-multiple-primary-node-types-in-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースでの複数のプライマリ ノード タイプの有効化
クラスター リソースにプライマリとして 1 つまたは複数のノード タイプを設定するには、"isPrimary" プロパティを "true" に設定します。 Availability Zones をまたがって Service Fabric クラスターをデプロイする場合、個別のゾーンに 3 つのノードのタイプが必要です。

```json
{
    "reliabilityLevel": "Platinum",
    "nodeTypes": [
    {
        "name": "[parameters('vmNodeType0Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt0applicationEndPort')]",
            "startPort": "[parameters('nt0applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt0fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt0ephemeralEndPort')]",
            "startPort": "[parameters('nt0ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt0fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt0InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType1Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt1applicationEndPort')]",
            "startPort": "[parameters('nt1applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt1fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt1ephemeralEndPort')]",
            "startPort": "[parameters('nt1ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt1fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt1InstanceCount')]"
    },
    {
        "name": "[parameters('vmNodeType2Name')]",
        "applicationPorts": {
            "endPort": "[parameters('nt2applicationEndPort')]",
            "startPort": "[parameters('nt2applicationStartPort')]"
        },
        "clientConnectionEndpointPort": "[parameters('nt2fabricTcpGatewayPort')]",
        "durabilityLevel": "Silver",
        "ephemeralPorts": {
            "endPort": "[parameters('nt2ephemeralEndPort')]",
            "startPort": "[parameters('nt2ephemeralStartPort')]"
        },
        "httpGatewayEndpointPort": "[parameters('nt2fabricHttpGatewayPort')]",
        "isPrimary": true,
        "vmInstanceCount": "[parameters('nt2InstanceCount')]"
    }
    ],
}
```

## <a name="migrate-to-using-availability-zones-from-a-cluster-using-a-basic-sku-load-balancer-and-a-basic-sku-ip"></a>Basic SKU Load Balancer と Basic SKU IP を使用したクラスターから Availability Zones の使用に移行する
Basic SKU で Load Balancer と IP を使用していたクラスターを移行するには、まず Standard SKU を使用したまったく新しい Load Balancer と IP リソースを作成する必要があります。 これらのリソースをインプレースで更新することはできません。

使用する新しいクロス可用性ゾーン ノードのタイプで、新しい LB と IP が参照されている必要があります。 上の例では、3 つの新しい仮想マシン スケール セットのリソースがゾーン 1、2、および 3 に追加されています。 これらの仮想マシン スケール セットは、新しく作成された LB と IP を参照し、Service Fabric クラスター リソース内でプライマリ ノードのタイプとしてマークされます。

まず、既存の Resource Manager テンプレートに新しいリソースを追加する必要があります。 これらのリソースには次が含まれます。
* Standard SKU を使用したパブリック IP リソース。
* Standard SKU を使用した Load Balancer リソース。
* 仮想マシン スケール セットをデプロイするサブネットによって参照されている NSG。
* プライマリとしてマークされた 3 つのノード タイプ。
    * 各ノード タイプは異なるゾーンに配置されているその独自の仮想マシン スケール セットにマップされている必要があります。
    * 各仮想マシン スケール セットには少なくとも 5 つのノード (Silver 耐久性) が必要です。

これらのリソースの例については、[サンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/10-VM-Ubuntu-2-NodeType-Secure)を参照してください。

```powershell
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $Template `
    -TemplateParameterFile $Parameters
```

リソースのデプロイが完了したら、元のクラスターから、プライマリ ノード タイプのノードを無効にしていくことができます。 ノードを無効にすると、システム サービスが、上記の手順でデプロイされた新しいプライマリ ノード タイプに移行されます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint $ClusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My 

Write-Host "Connected to cluster"

$nodeNames = @("_nt0_0", "_nt0_1", "_nt0_2", "_nt0_3", "_nt0_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames) {
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

ノードをすべて無効にすると、ゾーンに分散されたプライマリ ノード タイプで、システム サービスが実行されます。 その後、無効にしたノードをクラスターから削除できます。 ノードを削除したら、元の IP、Load Balancer、および仮想マシン スケール セット リソースを削除できます。

```powershell
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}

$scaleSetName="nt0"
Remove-AzureRmVmss -ResourceGroupName $groupname -VMScaleSetName $scaleSetName -Force

$lbname="LB-cluster-nt0"
$oldPublicIpName="LBIP-cluster-0"
$newPublicIpName="LBIP-cluster-1"

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

さらに、デプロイした Resource Manager テンプレートから、これらのリソースへの参照を削除する必要があります。

最後の手順には、DNS 名とパブリック IP の更新が含まれます。

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

Remove-AzureRmLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force
Remove-AzureRmPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force

$PublicIP = Get-AzureRmPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP

```

[sf-architecture]: ./media/service-fabric-cross-availability-zones/sf-cross-az-topology.png
