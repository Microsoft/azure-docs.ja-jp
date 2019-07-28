---
title: Azure Network Watcher の次ホップの概要 | Microsoft Docs
description: この記事では、Network Watcher の次ホップ機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: kumudd
manager: twooley
editor: ''
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: eb613455ccd6b6630d0d75b5a8458f812bb36846
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730215"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>次ホップを使用して仮想マシンのルーティングの問題を診断する

仮想マシン (VM) からのトラフィックは、ネットワーク インターフェイス (NIC) に関連付けられた有効なルートをもとに、送信先に送信されます。 次ホップは、特定の VM と NIC から次ホップの種類とパケットの IP アドレスを取得します。 次ホップを知ると、トラフィックが目的の宛先に転送されているかどうか、またはトラフィックがどこにも送信されていないかどうかを判断できます。 不適切なルートを構成して、トラフィックがオンプレミスの場所または仮想アプライアンスに向けられると、接続に関する問題が起こります。 また、次ホップは関連するルート テーブルを返します。 ルートがユーザー定義のルートとして定義されている場合、そのルートが返されます。 それ以外の場合、次ホップは**システム ルート**を返します。

![次ホップの概要](./media/network-watcher-next-hop-overview/figure1.png)

次ホップ機能から返されることを次ホップは次のとおりです。

* インターネット
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* なし

各次ホップの種類の詳細については、[ルーティングの概要](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

次ホップを使用して VM ネットワークのルーティングの問題を診断する方法については、[Azure portal](diagnose-vm-network-routing-problem.md)、[PowerShell](diagnose-vm-network-routing-problem-powershell.md)、または [Azure CLI](diagnose-vm-network-routing-problem-cli.md) を使用した仮想マシン ネットワークのルーティングの問題の診断に関するページを参照してください。
