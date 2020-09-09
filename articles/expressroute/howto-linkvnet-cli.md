---
title: 'Azure ExpressRoute: VNet を回線にリンクする:CLI'
description: この記事では、Resource Manager デプロイ モデルと CLI を使用して ExpressRoute 回線に仮想ネットワーク (VNet) をリンクする方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/27/2020
ms.author: duau
ms.openlocfilehash: ac36e303cbeaf5167b5bbec3cea503c37b276058
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393209"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-cli"></a>CLI を使用して仮想ネットワークを ExpressRoute 回線に接続する

この記事では、CLI を使用して仮想ネットワーク (VNet) を Azure ExpressRoute 回線にリンクする方法について説明します。 Azure CLI を使用してリンクするには、仮想ネットワークが Resource Manager デプロイ モデルを使って作成されている必要があります。 仮想ネットワークは、同じサブスクリプションにあっても、別のサブスクリプションの一部であってもかいまいません。 別の方法を使用して VNet を ExpressRoute 回線に接続する場合は、次の一覧から記事を選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [ビデオ - Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-linkvnet-classic.md)
> 

## <a name="configuration-prerequisites"></a>構成の前提条件

* 最新バージョンのコマンド ライン インターフェイス (CLI) が必要となります。 詳細については、「 [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認する必要があります。

* アクティブな ExpressRoute 回線が必要です。 
  * 手順に従って、 [ExpressRoute 回線を作成](howto-circuit-cli.md) し、接続プロバイダー経由で回線を有効にしてください。 
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](howto-routing-cli.md) に関する記事を参照してください。 
  * Azure プライベート ピアリングが構成されていることを確認します。 また、エンド ツー エンド接続を有効にできるように、ネットワークと Microsoft の間の BGP ピアリングを起動しておく必要があります。
  * 仮想ネットワークと仮想ネットワーク ゲートウェイを作成し、完全にプロビジョニングします。 指示に従って [ExpressRoute 用の仮想ネットワーク ゲートウェイを構成](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli)します。 `--gateway-type ExpressRoute` を使用してください。

* 最大 10 個の仮想ネットワークを標準 ExpressRoute 回線に接続できます。 標準 ExpressRoute 回線を使用する場合は、すべての仮想ネットワークが同じ地理的リージョンに存在する必要があります。 

* 単一の VNet を最大 4 つの ExpressRoute 回線にリンクできます。 以下のプロセスを使用して、接続先の各 ExpressRoute 回線の新しい接続オブジェクトを作成します。 ExpressRoute 回線は、同じサブスクリプション、異なるサブスクリプション、または両方の組み合わせにすることができます。

* ExpressRoute Premium アドオンを有効にした場合は、ExpressRoute 回線の地理的リージョンの外部にある仮想ネットワークをリンクしたり、さらに多くの仮想ネットワークを ExpressRoute 回線にリンクしたりすることができます。 Premium アドオンの詳細については、[FAQ](expressroute-faqs.md) を参照してください。

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>同じサブスクリプション内の仮想ネットワークを回線に接続する

次のコマンドを使用して、ExpressRoute 回線に仮想ネットワーク ゲートウェイを接続できます。 コマンドを実行する前に、仮想ネットワーク ゲートウェイが作成されており、リンクの準備ができていることを確認してください。

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>別のサブスクリプション内の仮想ネットワークを回線に接続する

複数のサブスクリプションで ExpressRoute 回線を共有できます。 下図に、複数のサブスクリプションで ExpressRoute 回線を共有するしくみについて概略を示します。

大規模クラウド内のそれぞれの小規模クラウドは、組織内のさまざまな部門に属するサブスクリプションを表すために使用されています。 組織内の各部門はサービスのデプロイ用に固有のサブスクリプションを使用できますが、1 つの ExpressRoute 回線を共有することで、オンプレミス ネットワークに接続し直すことができます。 1 つの部門 (この例では IT) で ExpressRoute 回線を所有できます。 組織内の他のサブスクリプションも ExpressRoute 回線を使用できます。

> [!NOTE]
> 専用回線の接続と帯域幅の料金は、ExpressRoute 回線の所有者が負担することになります。 すべての仮想ネットワークが同じ帯域幅を共有します。
> 
> 

![サブスクリプション間接続](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration---circuit-owners-and-circuit-users"></a>管理 - 回線所有者と回線ユーザー

"回線所有者" は、ExpressRoute 回線リソースの権限のあるパワー ユーザーです。 回線所有者は、"回線ユーザー" が利用できる承認を作成できます。 回線ユーザーは、ExpressRoute 回線と同じサブスクリプション内にない仮想ネットワーク ゲートウェイの所有者です。 回線ユーザーは、承認を利用できます (仮想ネットワークごとに 1 つの承認)。

回線所有者は、承認をいつでも変更し、取り消す権限を持っています。 承認を取り消すと、アクセスが取り消されたサブスクリプションからすべてのリンク接続が削除されます。

### <a name="circuit-owner-operations"></a>回線所有者の操作

**承認を作成するには**

回線所有者が承認を作成すると、承認キーが作成されます。回線ユーザーは、その承認キーを使用して、各自の仮想ネットワーク ゲートウェイを ExpressRoute 回線に接続することができます。 承認は、1 つの接続に対してのみ有効です。

次の例は、承認を作成する方法を示します。

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization
```

このコマンドレットの応答に、承認キーと状態が含まれます。

```output
"authorizationKey": "0a7f3020-541f-4b4b-844a-5fb43472e3d7",
"authorizationUseStatus": "Available",
"etag": "W/\"010353d4-8955-4984-807a-585c21a22ae0\"",
"id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/authorizations/MyAuthorization1",
"name": "MyAuthorization1",
"provisioningState": "Succeeded",
"resourceGroup": "ExpressRouteResourceGroup"
```

**承認を確認するには**

回線所有者は、次の例のコマンドレットを実行し、特定の回線で発行されるすべての承認を確認できます。

```azurecli
az network express-route auth list --circuit-name MyCircuit -g ExpressRouteResourceGroup
```

**承認を追加するには**

回線所有者は、次の例のコマンドレットを使用して承認を追加できます。

```azurecli
az network express-route auth create --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

**承認を削除するには**

回線所有者は、次の例のコマンドレットを実行して、ユーザーに対する承認を取り消したり削除したりすることができます。

```azurecli
az network express-route auth delete --circuit-name MyCircuit -g ExpressRouteResourceGroup -n MyAuthorization1
```

### <a name="circuit-user-operations"></a>回線ユーザーの操作

回線ユーザーは、ピア ID と回線所有者が作成した承認キーを必要とします。 承認キーは GUID です。

```powershell
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**接続の承認を利用するには**

回線ユーザーは次の例のコマンドレットを実行し、リンク承認を利用できます。

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit --authorization-key "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**接続の承認を解除するには**

ExpressRoute 回線を仮想ネットワークにリンクしている接続を削除することで、承認を解除できます。

## <a name="modify-a-virtual-network-connection"></a>仮想ネットワーク接続を変更する
仮想ネットワーク接続の特定のプロパティを更新することができます。 

**接続の重みを更新するには**

仮想ネットワークは、複数の ExpressRoute 回線に接続できます。 複数の ExpressRoute 回線から同じプレフィックスを受け取る場合があります。 このプレフィックスを宛先とするトラフィックをどの接続が送信するかを選択するには、接続の *RoutingWeight* を変更します。 *RoutingWeight* が最も高い接続でトラフィックが送信されます。

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --routing-weight 100
```

*RoutingWeight* の範囲は 0 ～ 32000 です。 既定値は 0 です。

## <a name="configure-expressroute-fastpath"></a>ExpressRoute FastPath を構成する 
仮想ネットワーク ゲートウェイが Ultra Performance または ErGw3AZ である場合は、[ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) を有効にすることができます。 FastPath を使用すると、オンプレミス ネットワークと仮想ネットワーク間における 1 秒あたりのパケット数や 1 秒あたりの接続数など、データ パスのパフォーマンスが向上します。 

**新しい接続で FastPath を構成する**

```azurecli
az network vpn-connection create --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true --vnet-gateway1 VNet1GW --express-route-circuit2 MyCircuit
```

**既存の接続を更新して FastPath を有効にする**

```azurecli
az network vpn-connection update --name ERConnection --resource-group ExpressRouteResourceGroup --express-route-gateway-bypass true
```

## <a name="next-steps"></a>次のステップ

ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
