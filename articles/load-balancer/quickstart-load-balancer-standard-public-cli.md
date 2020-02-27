---
title: 'クイックスタート: パブリック ロード バランサーを作成する - Azure CLI'
titleSuffix: Azure Load Balancer
description: このクイックスタートでは、Azure CLI を使用してパブリック ロード バランサーを作成する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
tags: azure-resource-manager
Customer intent: I want to create a Load balancer so that I can load balance internet traffic to VMs.
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fdbd002ac946f3ac3a1a67980905d4ed6f5510c5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470345"
---
# <a name="quickstart-create-a-standard-load-balancer-to-load-balance-vms-using-azure-cli"></a>クイック スタート:Azure CLI を使用して VM の負荷を分散する Standard Load Balancer を作成する

このクイックスタートでは、パブリック ロード バランサーを作成する方法を示します。 ロード バランサーをテストするには、Ubuntu サーバーを実行する 2 つの仮想マシン (VM) をデプロイし、2 つの VM 間で Web アプリの負荷を分散します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.28 以降のバージョンを実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](https://docs.microsoft.com/cli/azure/group) を使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、*myResourceGroupSLB* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
  az group create \
    --name myResourceGroupSLB \
    --location eastus
```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

インターネット上の Web アプリにアクセスするには、ロード バランサーのパブリック IP アドレスが必要です。 *myResourceGroupSLB* に *myPublicIP* という Standard ゾーン冗長パブリック IP アドレスを作成するには、[az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) を使用します。

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard
```

ゾーンのパブリック IP アドレスをゾーン 1 に作成するには、次のようにします。

```azurecli-interactive
  az network public-ip create --resource-group myResourceGroupSLB --name myPublicIP --sku standard --zone 1
```

Basic パブリック IP を作成するには、```-SKU Basic``` を使用します。 Basic パブリック IP は、**Standard** Load Balancer と共に利用することはできません。 運用環境のワークロードには **Standard** の使用をお勧めします。

> [!IMPORTANT]
> 以降このクイックスタートでは、前の SKU 選択プロセスで **Standard** SKU が選択されたことを前提に話を進めます。

## <a name="create-azure-load-balancer"></a>Azure Load Balancer の作成

このセクションでは、ロード バランサーの以下のコンポーネントを作成および構成する方法について説明します。
  - ロード バランサーの着信ネットワーク トラフィックを受け取るフロントエンド IP プール。
  - フロントエンド プールから負荷分散されたネットワーク トラフィックが送信されるバックエンド IP プール。
  - バックエンド VM インスタンスの正常性を判断する正常性プローブ。
  - VM に対するトラフィックの分散方法を定義するロード バランサー規則。

### <a name="create-the-load-balancer"></a>ロード バランサーを作成する

[az network lb create](https://docs.microsoft.com/cli/azure/network/lb?view=azure-cli-latest) を使用して、**myLoadBalancer** という名前のパブリック Azure Load Balancer を作成します。これには、**myFrontEnd** という名前のフロントエンド プールと、前のステップで作成したパブリック IP アドレス **myPublicIP** に関連付けられている **myBackEndPool** という名前のバックエンド プールを含めます。 Basic パブリック IP を作成するには、```--sku basic``` を使用します。 Microsoft では、運用環境のワークロードに Standard SKU をお勧めします。

```azurecli-interactive
  az network lb create \
    --resource-group myResourceGroupSLB \
    --name myLoadBalancer \
    --sku standard \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool       
  ```

> [!IMPORTANT]
> 以降このクイックスタートでは、前の SKU 選択プロセスで **Standard** SKU が選択されたことを前提に話を進めます。

### <a name="create-the-health-probe"></a>正常性プローブを作成する

正常性プローブは、すべての仮想マシン インスタンスを確認し、ネットワーク トラフィックを送信できるかどうかを確認します。 プローブのチェックで失敗した仮想マシン インスタンスは、オンラインに戻り、プローブ チェックにより正常と判定されるまで、ロード バランサーから削除されます。 [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe?view=azure-cli-latest) を使用して正常性プローブを作成し、仮想マシンの正常性を監視します。 

```azurecli-interactive
  az network lb probe create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80   
```

### <a name="create-the-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則は、着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest) を使用してロード バランサー規則 *myLoadBalancerRuleWeb* を作成します。この規則では、フロントエンド プール *myFrontEnd* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。 

```azurecli-interactive
  az network lb rule create \
    --resource-group myResourceGroupSLB \
    --lb-name myLoadBalancer \
    --name myHTTPRule \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEnd \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe  
```

## <a name="configure-virtual-network"></a>仮想ネットワークを構成する

いくつかの VM をデプロイしてロード バランサーをテストする前に、サポート用の仮想ネットワーク リソースを作成します。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) を使用して、*myVnet* という名前の仮想ネットワークを作成します。*myResourceGroup* に、*mySubnet* という名前のサブネットを含めます。

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupSLB \
    --location eastus \
    --name myVnet \
    --subnet-name mySubnet
```
###  <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

Standard Load Balancer の場合、バックエンドが扱う VM には、ネットワーク セキュリティ グループに属している NIC が必要です。 ネットワーク セキュリティ グループを作成して、仮想ネットワークへの受信接続を定義します。

```azurecli-interactive
  az network nsg create \
    --resource-group myResourceGroupSLB \
    --name myNetworkSecurityGroup
```

### <a name="create-a-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

ネットワーク セキュリティ グループ規則を作成して、ポート 80 を通じた受信接続を許可します。

```azurecli-interactive
  az network nsg rule create \
    --resource-group myResourceGroupSLB \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleHTTP \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 80 \
    --access allow \
    --priority 200
```
### <a name="create-nics"></a>NIC の作成

[az network nic create](/cli/azure/network/nic#az-network-nic-create) を使用して 3 つのネットワーク インターフェイスを作成し、それらをパブリック IP アドレスとネットワーク セキュリティ グループに関連付けます。 

```azurecli-interactive

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM1 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM2 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool
  
  az network nic create \
    --resource-group myResourceGroupSLB \
    --name myNicVM3 \
    --vnet-name myVnet \
    --subnet mySubnet \
    --network-security-group myNetworkSecurityGroup \
    --lb-name myLoadBalancer \
    --lb-address-pools myBackEndPool

```

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、ロード バランサーのバックエンド サーバーとして使用する 3 つの仮想マシンを作成します。 ロード バランサーが正常に作成されたことを確認するには、仮想マシンに NGINX もインストールします。

Basic パブリック IP で Basic Load Balancer を作成する場合、仮想マシンの追加先となる可用性セットを、[az vm availabilityset create](/cli/azure/network/nic) を使用して作成する必要があります。 Standard Load Balancer の場合、この追加手順は不要です。 Microsoft では、Standard の使用をお勧めします。

### <a name="create-three-virtual-machines"></a>仮想マシンを 3 つ作成する

cloud-init 構成ファイルを使って、NGINX をインストールし、Linux 仮想マシンで "Hello World" Node.js アプリを実行することができます。 現在のシェルで、cloud-init.txt という名前のファイルを作成し、次の構成をコピーして、そのシェルに貼り付けます。 cloud-init ファイル全体 (特に最初の行) を、確実かつ正確にコピーしてください。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
``` 
 
[az vm create](/cli/azure/vm#az-vm-create) で、仮想マシンを作成します。

 ```azurecli-interactive

  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM1 \
    --availability-set myAvailabilitySet \
    --nics myNicVM1 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait
   
  az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM2 \
    --availability-set myAvailabilitySet \
    --nics myNicVM2 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

   az vm create \
    --resource-group myResourceGroupSLB \
    --name myVM3 \
    --availability-set myAvailabilitySet \
    --nics myNicVM3 \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --custom-data cloud-init.txt \
    --no-wait

```
VM がデプロイされるまでに、数分かかる場合があります。

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

ロード バランサーのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

```azurecli-interactive
  az network public-ip show \
    --resource-group myResourceGroupSLB \
    --name myPublicIP \
    --query [ipAddress] \
    --output tsv
``` 
   ![ロード バランサーをテストする](./media/load-balancer-standard-public-cli/running-nodejs-app.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、[az group delete](/cli/azure/group#az-group-delete) コマンドを使用して、リソース グループ、ロード バランサー、およびすべての関連リソースを削除できます。

```azurecli-interactive 
  az group delete --name myResourceGroupSLB
```
## <a name="next-steps"></a>次のステップ
このクイック スタートでは、Standard Load Balancer を作成し、それに VM をアタッチして、ロード バランサー トラフィック規則と正常性プローブを構成してから、ロード バランサーをテストしました。 Azure Load Balancer についてさらに学習するには、[Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)に進んでください。

[Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)について理解を深めます。