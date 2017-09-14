---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - Azure CLI | Microsoft Docs"
description: "この記事では、Azure CLI を使用して、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0b52257a6c38a4392573672b7190d2269c2f145a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017

---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher の IP フロー検証コンポーネントを使用して VM から送受信されるトラフィックが許可されているか拒否されているかを確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)


IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを検証できます。 このシナリオは仮想マシンが現在、外部リソースまたはバックエンドと通信可能な状態にあるかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 ブロック対象のトラフィックが NSG により適切にブロックされているかどうかを確認することも、IP フロー検証を使用する別の理由として挙げられます。

この記事では、Azure CLI 2.0 を使います。Azure CLI 2.0 は、Resource Manager デプロイメント モデルを対象とする Microsoft の次世代 CLI であり、Windows、Mac、Linux で利用できます。

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンドライン インターフェイス (Azure CLI) をインストール](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)する必要があります。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順に従って Network Watcher を作成済みであること、または既存の Network Watcher インスタンスを保持していることを前提としています。 また、有効な仮想マシンのあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して仮想マシンが既知の Bing IP アドレスと通信可能かどうかを検証します。 トラフィックが拒否されている場合は、そのトラフィックを拒否するセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)に関する記事を参照してください。

## <a name="get-a-vm"></a>VM の取得

IP フロー検証では、仮想マシン上の IP アドレスとリモートの通信先間のトラフィックをテストします。 このコマンドレットには、仮想マシンの ID が必要です。 使用する仮想マシンの ID が既にわかっている場合、この手順は省略できます。

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>NIC の取得

仮想マシンの NIC の IP アドレスが必要になります。この例では、仮想マシン上の NIC を取得します。 仮想マシン上のテスト対象 IP アドレスが既にわかっている場合、この手順は省略できます。

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>IP フロー検証の実行

コマンドレットの実行に必要な情報が揃ったため、`az network watcher test-ip-flow` コマンドレットを実行してトラフィックをテストします。 この例では、1 番目の NIC の最初の IP アドレスを使用します。

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てて実行する必要があります。

## <a name="review-results"></a>結果の確認

`az network watcher test-ip-flow` を実行すると結果が返されます。以下の例は、前の手順で返された結果です。

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、ネットワーク セキュリティ グループと定義済みのセキュリティ規則を突き止めます。

NSG 設定の監査方法については、[Network Watcher を使用したネットワーク セキュリティ グループ (NSG) の監査](network-watcher-nsg-auditing-powershell.md)に関する記事をご覧ください。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

