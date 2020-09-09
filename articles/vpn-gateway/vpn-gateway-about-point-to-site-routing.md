---
title: Azure VPN Gateway:P2S ルーティングについて
description: さまざまなオペレーティング システム、リモート アクセス プロトコル、および仮想ネットワーク構成の Azure ポイント対サイト VPN ルーティングについて説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: kumud
ms.openlocfilehash: 54b839170d95918889d1295eb20308688fbfb2a7
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88032375"
---
# <a name="about-point-to-site-vpn-routing"></a>ポイント対サイト VPN ルーティングについて

この記事は、Azure ポイント対サイト VPN ルーティングの動作を理解するのに役立ちます。 P2S VPN ルーティングの動作は、クライアント OS、VPN 接続に使用されるプロトコル、および仮想ネットワーク (VNet) 間の接続方法によって異なります。

現在、Azure では、リモート アクセスに対して IKEv2 と SSTP の 2 つのプロトコルをサポートしています。 IKEv2 は、Windows、Linux、MacOS、Android、iOS など、多くのクライアント オペレーティング システム上でサポートされます。 SSTP は、Windows 上でのみサポートされます。 Windows VPN クライアントがある状態でネットワークのトポロジに変更を加えた場合は、変更をそのクライアントに適用するために、Windows クライアント用の VPN クライアント パッケージをダウンロードしてもう一度インストールする必要があります。

> [!NOTE]
> この記事は、IKEv2 に対してのみ適用されます。
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>図について

この記事にはいくつかの異なる図があります。 セクションごとに、異なるトポロジや構成が示されています。 この記事の目的により、サイト間 (S2S) 接続と VNet 間接続はどちらも IPsec トンネルであるため、同じように機能します。 この記事のすべての VPN ゲートウェイはルートベースです。

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>分離された 1 つの VNet

この例のポイント対サイト VPN ゲートウェイ接続は、他の仮想ネットワークに接続もピアリングもされていない VNet (VNet1) に関するものです。 この例では、クライアントから VNet1 にアクセスできます。

![分離された VNet ルーティング](./media/vpn-gateway-about-point-to-site-routing/1.jpg "分離された VNet ルーティング")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1 にアクセスできます

* Windows 以外のクライアントから VNet1 にアクセスできます

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>ピアリングされた複数の VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は VNet2 とピアリングされています。 VNet2 は VNet3 とピアリングされています。 VNet1 は VNet4 とピアリングされています。 VNet1 と VNet3 の間に直接ピアリングはありません。 VNet1 では [ゲートウェイ転送を許可する] が有効で、VNet2 と VNet4 では [リモート ゲートウェイを使用する] が有効です。

Windows を使用しているクライアントは直接ピアリングされた VNet にアクセスできますが、VNet ピアリングまたはネットワーク トポロジが変更された場合は VPN クライアントをもう一度ダウンロードする必要があります。 Windows 以外のクライアントは、直接ピアリングされた VNet にアクセスできます。 アクセスは推移的ではなく、直接ピアリングされた VNet のみに限定されます。

![ピアリングされた複数の VNet](./media/vpn-gateway-about-point-to-site-routing/2.jpg "ピアリングされた複数の VNet")

### <a name="address-space"></a>アドレス空間:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* VNet4:10.4.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.4.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1、VNet2、VNet4 にアクセスできますが、トポロジの変更を反映するには VPN クライアントをもう一度ダウンロードする必要があります。

* Windows 以外のクライアントから VNet1、VNet2、VNet4 にアクセスできます

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>S2S VPN を使用して接続されている複数の VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 の間に直接ピアリングやサイト間 VPN 接続はありません。 どのサイト間接続でも、ルーティングのための BGP は実行されていません。

Windows、または別のサポート対象 OS を使用しているクライアントから、VNet1 にのみアクセスできます。 その他の VNet にアクセスするには、BGP を使用する必要があります。

![複数の VNet と S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "複数の VNet と S2S")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1 にのみアクセスできます

* Windows 以外のクライアントから VNet1 にのみアクセスできます

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>S2S VPN (BGP) を使用して接続されている複数の VNet

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 の間に直接ピアリングやサイト間 VPN 接続はありません。 すべてのサイト間接続で、ルーティングのための BGP が実行されています。

Windows、または別のサポートさ対象 OS を使用しているクライアントから、サイト間 VPN を使用して接続されているすべての VNet にアクセスできますが、接続されている VNet へのルートを Windows クライアントに手動で追加する必要があります。

![複数の Vnet と S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "複数の VNet と S2S BGP")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1、VNet2、VNet3 にアクセスできますが、VNet2 と VNet3 へのルートを手動で追加する必要があります。

* Windows 以外のクライアントから VNet1、VNet2、VNet3 にアクセスできます

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>1 つの VNet とブランチ オフィス

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は他のどの仮想ネットワークとも接続またはピアリングされていませんが、BGP を実行していないサイト間 VPN 接続を通じてオンプレミスのサイトに接続されています。

Windows クライアントと Windows 以外のクライアントは VNet1 にのみアクセスできます。

![1 つの VNet とブランチ オフィスを含むルーティング](./media/vpn-gateway-about-point-to-site-routing/5.jpg "1 つの VNet とブランチ オフィスを含むルーティング")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントは VNet1 にのみアクセスできます

* Windows 以外のクライアントから VNet1 にのみアクセスできます

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>1 つの VNet とブランチ オフィス (BGP)

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は他のどの仮想ネットワークとも接続またはピアリングされていませんが、BGP を実行しているサイト間 VPN 接続を通じてオンプレミスのサイト (Site1) に接続されています。

Windows クライアントは VNet とブランチ オフィス (Site1) にアクセスできますが、Site1 へのルートをクライアントに手動で追加する必要があります。 Windows 以外のクライアントは、VNet とオンプレミスのブランチ オフィスにアクセスできます。

![1 つの VNet とブランチ オフィス (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "1 つの VNet とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1 と Site1 にアクセスできますが、Site1 へのルートを手動で追加する必要があります。

* Windows 以外のクライアントから VNet1 と Site1 にアクセスできます。


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>S2S を使用して接続された複数の VNet と 1 つのブランチ オフィス

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 のネットワーク間に直接ピアリングやサイト間 VPN トンネルはありません。 VNet3 は、サイト間 VPN 接続を使用してブランチ オフィス (Site1) に接続されています。 どの VPN 接続でも BGP は実行されていません。

すべてのクライアントが VNet1 にのみアクセスできます。

![複数の VNet S2S とブランチ オフィス](./media/vpn-gateway-about-point-to-site-routing/7.jpg "複数の VNet S2S とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1 にのみアクセスできます

* Windows 以外のクライアントから VNet1 にのみアクセスできます

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>S2S を使用して接続された複数の VNet と 1 つのブランチ オフィス (BGP)

この例のポイント対サイト VPN ゲートウェイ接続は VNet1 に関するものです。 VNet1 は、サイト間 VPN 接続を使用して VNet2 に接続されています。 VNet2 は、サイト間 VPN 接続を使用して VNet3 に接続されています。 VNet1 と VNet3 のネットワーク間に直接ピアリングやサイト間 VPN トンネルはありません。 VNet3 は、サイト間 VPN 接続を使用してブランチ オフィス (Site1) に接続されています。 すべての VPN 接続で BGP が実行されています。

Windows を使用しているクライアントは、サイト間 VPN 接続を使用して接続されている VNet とサイトにアクセスできますが、VNet2、VNet3、Site1 へのルートをクライアントに手動で追加する必要があります。 Windows 以外のクライアントは、手動で介入しなくても、サイト間 VPN 接続を使用して接続されている VNet とサイトにアクセスできます。 アクセスは推移的であり、クライアントは、接続されているすべての VNet とサイト (オンプレミス) のリソースにアクセスできます。

![複数の VNet S2S とブランチ オフィス](./media/vpn-gateway-about-point-to-site-routing/8.jpg "複数の VNet S2S とブランチ オフィス")

### <a name="address-space"></a>アドレス空間

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* VNet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>追加されたルート

* Windows クライアントに追加されたルート: 10.1.0.0/16、192.168.0.0/24

* Windows 以外のクライアントに追加されたルート: 10.1.0.0/16、10.2.0.0/16、10.3.0.0/16、10.101.0.0/16、192.168.0.0/24

### <a name="access"></a>アクセス

* Windows クライアントから VNet1、VNet2、VNet3、Site1 にアクセスできますが、VNet2、VNet3、Site1 へのルートをクライアントに手動で追加する必要があります。

* Windows 以外のクライアントから VNet1、VNet2、VNet3、Site1 にアクセスできます。

## <a name="next-steps"></a>次のステップ

[Azure Portal を使用した P2S VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照して P2S VPN の作成を開始します。
