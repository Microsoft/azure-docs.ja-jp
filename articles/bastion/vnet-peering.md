---
title: VNet ピアリングと Azure Bastion のアーキテクチャ
description: この記事では、VNet ピアリングと Azure Bastion を一緒に使用して VM に接続する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: 472261666c86b666efd09c7217d12e5a795a50d9
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094891"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet ピアリングと Azure Bastion のアーキテクチャ (プレビュー)

Azure Bastion と VNet ピアリングは一緒に使用できます。 VNet ピアリングが構成されている場合、ピアリングされた各 VNet に Azure Bastion をデプロイする必要はありません。 つまり、Azure Bastion ホストが 1 つの仮想ネットワーク (VNet) 内に構成されている場合は、追加の Bastion ホストをデプロイしなくても、ピアリングされた VNet にデプロイされている VM に接続するために使用できます。 VNet ピアリングの詳細については、[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)に関するページを参照してください。

Azure Bastion は、次の種類のピアリングで動作します。

* **仮想ネットワーク ピアリング:** 同じ Azure リージョン内の仮想ネットワークを接続します。
* **グローバル仮想ネットワーク ピアリング:** Azure リージョン間で仮想ネットワークを接続します。

## <a name="architecture"></a>Architecture

VNet ピアリングが構成されている場合、Azure Bastion はハブ アンド スポークまたはフルメッシュのトポロジにデプロイできます。 Azure Bastion デプロイは、サブスクリプションやアカウント、仮想マシン単位ではなく、仮想ネットワーク単位です。

仮想ネットワーク内に Azure Bastion サービスをプロビジョニングすると、ピアリングされた VNet はもちろん、同じ VNet 内のすべての VM で RDP/SSH エクスペリエンスを利用できます。 これは、Bastion デプロイを単一 VNet に統合しても、ピアリングされた VNet にデプロイされた VM にはアクセスすることができ、全体的なデプロイが一元化されることを意味します。

:::image type="content" source="./media/vnet-peering/design.png" alt-text="設計とアーキテクチャの図":::

この図は、ハブ アンド スポーク モデルでの Azure Bastion デプロイのアーキテクチャを示しています。 この図では、以下の構成を確認できます。

* Bastion ホストは、一元化されたハブ仮想ネットワーク内にデプロイされています。
* 一元化されたネットワーク セキュリティ グループ (NSG) がデプロイされています。
* Azure VM にパブリック IP は必要ありません。

**手順:**

1. 任意の HTML5 ブラウザーを使用して Azure portal に接続します。
1. 接続する仮想マシンを選択します。
1. ピアリングされた VNet 全体で Azure Bastion がシームレスに検出されます。
1. 1 回クリックすると、ブラウザーで RDP または SSH セッションが開きます。 RDP と SSH の同時セッションの制限については、[RDP と SSH のセッション](bastion-faq.md#limits)に関するページを参照してください。

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="のインスタンスに接続するときには、":::

   Azure Bastion を介した VM への接続に関する詳細については、以下を参照してください。

   * [VM に接続する - RDP](bastion-connect-vm-rdp.md)。
   * [VM に接続する - SSH](bastion-connect-vm-ssh.md)。

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。