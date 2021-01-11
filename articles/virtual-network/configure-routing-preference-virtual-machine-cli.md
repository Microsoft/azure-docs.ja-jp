---
title: VM 用にルーティング優先設定を構成する - Azure CLI
description: Azure コマンド ライン インターフェイス (CLI) を使用して、ルーティング優先設定を選択してパブリック IP アドレスを持つ VM を作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49b0df3e750d4d23cb6a64f3f7266613fd2f2981
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501833"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-cli"></a>Azure CLI を使用して VM 用にルーティング優先設定を構成する

この記事では、仮想マシン用にルーティング優先設定を構成する方法について説明します。 ルーティング優先設定オプションとして **[インターネット]** を選択すると、VM からインターネットへのトラフィックは ISP ネットワーク経由でルーティングされます。 既定のルーティングは、Microsoft グローバル ネットワーク経由です。

この記事では、Azure CLI を使用して、パブリック インターネット経由でトラフィックをルーティングするように設定されたパブリック IP を持つ仮想マシンを作成する方法について説明します。

> [!IMPORTANT]
> 現在、ルーティング優先設定はパブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="register-the-feature-for-your-subscription"></a>サブスクリプションに機能を登録する
現在、ルーティング優先設定の機能はプレビューの段階です。 次のようにして、サブスクリプションに機能を登録します。
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```
## <a name="create-a-resource-group"></a>リソース グループを作成する
1. Cloud Shell を使用する場合は、手順 2 に進みます。 コマンド セッションを開き、`az login` で Azure にサインインします。
2. [az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 次の例では、Azure の米国東部リージョンにリソース グループを作成します。

    ```azurecli
    az group create --name myResourceGroup --location eastus
    ```

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成
インターネットから仮想マシンにアクセスするには、パブリック IP アドレスを作成する必要があります。 パブリック IP アドレスは、[az network public-ip create](/cli/azure/network/public-ip) で作成します。 次の例では、"*米国東部*" リージョンに、ルーティング優先設定の種類が "*インターネット*" のパブリック IP アドレスを作成します。

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

VM をデプロイする前に、サポートするネットワーク リソース (ネットワーク セキュリティ グループ、仮想ネットワーク、仮想 NIC) を作成する必要があります。

### <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

VNet での受信方向および送信方向の通信を管理する規則に対するネットワーク セキュリティ グループを、[az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg?view=azure-cli-latest#az-network-nsg-create) で作成します

```azurecli
az network nsg create \
--name myNSG  \
--resource-group MyResourceGroup \
--location eastus
```

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

[az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest#az-network-vnet-create) を使用して仮想ネットワークを作成します。 次の例では、サブネット *mySubNet* を含む *myVNET* という名前の仮想ネットワークを作成します。

```azurecli
# Create a virtual network
az network vnet create \
--name myVNET \
--resource-group MyResourceGroup \
--location eastus

# Create a subnet
az network vnet subnet create \
--name mySubNet \
--resource-group MyResourceGroup \
--vnet-name myVNET \
--address-prefixes "10.0.0.0/24" \
--network-security-group myNSG
```

### <a name="create-a-nic"></a>NIC を作成する

VM 用の仮想 NIC を作成するには、[az network nic create](https://docs.microsoft.com/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) を使用します。 次の例では、VM に接続される仮想 NIC を作成します。

```azurecli-interactive
# Create a NIC
az network nic create \
--name mynic  \
--resource-group MyResourceGroup \
--network-security-group myNSG  \
--vnet-name myVNET \
--subnet mySubNet  \
--private-ip-address-version IPv4 \
--public-ip-address MyRoutingPrefIP
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して VM を作成します。 次の例では、Windows Server 2019 VM と、必要な仮想ネットワーク コンポーネント (まだ存在しない場合) を作成します。

```azurecli
az vm create \
--name myVM \
--resource-group MyResourceGroup \
--nics mynic \
--size Standard_A2 \
--image MicrosoftWindowsServer:WindowsServer:2019-Datacenter:latest \
--admin-username myUserName
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、[az group delete](/cli/azure/group#az-group-delete) を使用して、リソース グループとそのグループに含まれているすべてのリソースを削除できます。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次のステップ

- [パブリック IP アドレスでのルーティング優先設定](routing-preference-overview.md)について詳しく学習する。
- Azure での[パブリック IP アドレス](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)について詳しく学習する。
- [パブリック IP アドレスの設定](virtual-network-public-ip-address.md#create-a-public-ip-address)について詳しく学習する。
