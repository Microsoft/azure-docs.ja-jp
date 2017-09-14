---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - Azure Portal | Microsoft Docs"
description: "この記事では、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 7db29c186cf6e6f3b40a680ab76f1d2763f806ba
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


IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを確認できます。 検証は、受信または送信トラフィックに対して実行できます。 このシナリオは、仮想マシンが現在外部リソースまたは別のリソースと通信可能な状態であるかどうかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかどうかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 ブロック対象のトラフィックが NSG により適切にブロックされているかどうかを確認することも、IP フロー検証を使用する別の理由として挙げられます。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関するページの手順に従って Network Watcher を作成済みであること、または既存の Network Watcher インスタンスを保持していることを前提としています。 また、有効な仮想マシンのあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して、仮想マシンがポート 443 で別のマシンと通信可能かどうかを確認します。 トラフィックが拒否されている場合は、そのトラフィックを拒否するセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)に関する記事をご覧ください。

### <a name="run-ip-flow-verify"></a>IP フロー検証の実行

Network Watcher に移動し、**[IP フローの確認]** をクリックします。 トラフィックを確認する仮想マシンとネットワーク インターフェイスを選択します。 その他のフィルター情報を入力し、**[チェック]** をクリックします。

**[チェック]** をクリックすると、指定した条件に基づいてフローが確認されます。 結果は、**[アクセス許可]** または **[アクセス拒否]** のいずれかになります。 アクセスが拒否された場合、トラフィックをブロックしているネットワーク セキュリティ グループ (NSG) とセキュリティ規則が表示されます。 トラフィックの拒否が想定された動作の場合、規則が正常に適用されています。

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てる必要があります。

次の図からわかるように、送信 HTTPS トラフィックが許可されています。

![IP フロー検証の概要][1]

次の図に示すように、トラフィックを受信に変更し、受信ポートを 123 に変更します。 今回はトラフィックが拒否され、トラフィックを拒否したネットワーク セキュリティ グループおよびセキュリティ規則と共に、"アクセス拒否" というメッセージが表示されます。

![IP フローの結果][2]

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、ネットワーク セキュリティ グループと定義済みのセキュリティ規則を突き止めます。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














