---
title: "VPN Gateway の概要: Azure 仮想ネットワークへのクロスプレミス VPN 接続を作成する | Microsoft Docs"
description: "この VPN Gateway の概要では、インターネット経由の VPN 接続を使用して Azure 仮想ネットワークに接続する方法について説明します。 基本的な接続構成の図が含まれています。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: ecfe6dab6e4deaa75d073badcb88d536396fe678
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---
# <a name="about-vpn-gateway"></a>VPN Gateway について

VPN ゲートウェイは、暗号化されたトラフィックをパブリック接続経由でオンプレミスの場所に送信する種類の仮想ネットワーク ゲートウェイです。 VPN ゲートウェイを使用すると、Microsoft ネットワークを経由して Azure 仮想ネットワーク間で暗号化されたトラフィックを送信することもできます。 Azure 仮想ネットワークとオンプレミスのサイトとの間で暗号化されたネットワーク トラフィックを送信するには、仮想ネットワーク用の VPN ゲートウェイを作成する必要があります。

各仮想ネットワークには VPN ゲートウェイを 1 つだけ作成できますが、同一の VPN ゲートウェイに対して複数の接続を作成することができます。 その一例は、マルチサイト接続構成です。 同一の VPN ゲートウェイへの複数の接続を作成する場合、そのゲートウェイで利用できる帯域幅は、(ポイント対サイト VPN を含め) すべての VPN トンネルによって共有されます。

### <a name="whatis"></a>仮想ネットワーク ゲートウェイとは

仮想ネットワーク ゲートウェイは、GatewaySubnet と呼ばれる特定のサブネットにデプロイされる 2 台以上の仮想マシンで構成されます。 GatewaySubnet に配置される VM は、仮想ネットワーク ゲートウェイの作成時に作成されます。 仮想ネットワーク ゲートウェイの VM は、ゲートウェイ固有のルーティング テーブルとゲートウェイ サービスを含むように構成されます。 仮想ネットワーク ゲートウェイの一部である VM を直接構成することはできません。また、GatewaySubnet に、その他のリソースをデプロイすべきではありません。

ゲートウェイの種類として 'Vpn' を使用して仮想ネットワーク ゲートウェイを作成すると、トラフィックを暗号化する特定の種類の仮想ネットワーク ゲートウェイ、つまり VPN ゲートウェイが作成されます。 VPN ゲートウェイの作成には最大 45 分かかることがあります。 VPN ゲートウェイに使用する VM を GatewaySubnet にデプロイし、指定された設定で構成しているためです。 この VM の性能は、選択したゲートウェイの SKU によって決まります。

## <a name="gwsku"></a>ゲートウェイの SKU

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

## <a name="configuring"></a>VPN ゲートウェイの構成

VPN ゲートウェイ接続は、特定の設定で構成された複数のリソースに依存します。 ほとんどのリソースは個別に構成できますが、場合によってはある一定の順序で構成を行う必要があります。

### <a name="settings"></a>設定

リソースごとに選択した設定は、適切な接続を作成するうえで非常に重要です。 VPN Gateway の個々のリソースと設定については、「 [VPN Gateway の設定について](vpn-gateway-about-vpn-gateway-settings.md)」を参照してください。 この記事には、ゲートウェイの種類、VPN の種類、接続の種類、ゲートウェイ サブネット、ローカル ネットワーク ゲートウェイ、検討が必要なその他のさまざまなリソース設定を把握するのに役立つ情報が記載されています。

### <a name="tools"></a>デプロイ ツール

Azure Portal などの構成ツールをどれか 1 つ使用して、リソースの作成と構成を開始できます。 その後、追加のリソースを構成したり、適用できる場合に既存のリソースを変更したりするために、PowerShell などの別のツールに切り替えることができます。 現時点では、すべてのリソースとリソースの設定を Azure Portal で構成することはできません。 各接続トポロジの記事の手順では、特定の構成ツールが必要な場合が指定されています。 

### <a name="models"></a>デプロイメント モデル

VPN ゲートウェイを構成する手順は、仮想ネットワークの作成に使用したデプロイメント モデルによって異なります。 たとえば、クラシック デプロイメント モデルを使用して VNet を作成した場合は、クラシック デプロイメント モデルに対応したガイドラインと手順を使用して VPN ゲートウェイ設定を作成し、構成します。 デプロイメント モデルの詳細については、 [Resource Manager デプロイメント モデルとクラシック デプロイメント モデルについて](../azure-resource-manager/resource-manager-deployment-model.md)のページを参照してください。

## <a name="diagrams"></a>接続トポロジの図

VPN ゲートウェイ接続ではさまざまな構成が利用できることを理解しておくことが重要です。 また、どの構成が自分のニーズに最適かを判断する必要があります。 以下のセクションでは、次の VPN ゲートウェイ接続に関する情報とトポロジの図を確認できます。また、これらのセクションには次の一覧が記載された表があります。

* 利用可能なデプロイメント モデル
* 利用可能な構成ツール
* 記事に直接移動するリンク (利用可能な場合)

図と説明を参考にして、要件を満たす接続トポロジを選択できます。 図は主要なベースライン トポロジを示していますが、図をガイドラインとして使用して、より複雑な構成を構築することもできます。

## <a name="s2smulti"></a>サイト間とマルチサイト (IPsec/IKE VPN トンネル)

### <a name="S2S"></a>サイト間

サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。 S2S 接続では、オンプレミスの VPN デバイスが必要です。そのデバイスは、パブリック IP アドレスを割り当てられていて、NAT の内側に配置されていない必要があります。 S2S 接続は、クロスプレミスおよびハイブリッド構成に使用できます。   

![Azure VPN Gateway サイト間接続の例](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>マルチサイト

この種類の接続は、サイト間接続の一種です。 仮想ネットワーク ゲートウェイから複数の VPN 接続を作成し、通常は複数のオンプレミスのサイトに接続します。 複数の接続を使用する場合は、(クラシック VNet を使用する際に動的ゲートウェイと呼ばれる) RouteBased という VPN の種類を使用する必要があります。 各仮想ネットワークに配置できる VPN ゲートウェイは 1 つのみであるため、ゲートウェイを経由するすべての接続は、使用可能な帯域幅を共有します。 これは一般に "マルチサイト" 接続と呼ばれます。

![Azure VPN Gateway マルチサイト接続の例](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>サイト間接続とマルチサイト接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>ポイント対サイト (VPN over SSTP)

ポイント対サイト (P2S) VPN ゲートウェイでは、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 ポイント対サイト VPN 接続は、自宅や会議室でのテレワークなど、リモートの場所から VNet に接続する場合に便利です。 P2S VPN は、VNet への接続が必要なクライアントがごく少ない場合に、サイト対サイト VPN の代わりに使用するソリューションとしても便利です。 

S2S 接続とは異なり、P2S 接続には、オンプレミスの公開 IP アドレスまたは VPN デバイスは必要ありません。 P2S 接続と S2S 接続は、両者の構成要件がすべて両立する場合に、同じ VPN ゲートウェイを使って組み合わせて使用できます。

P2S は、Secure Socket トンネリング プロトコル (SSTP) を使用します。これは、SSL ベースの VPN プロトコルです。 P2S VPN 接続は、クライアント コンピューターから接続を開始することによって確立されます。

![Azure VPN Gateway ポイント対サイト接続の例](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-point-to-site"></a>ポイント対サイト接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>VNet 間接続 (IPsec/IKE VPN トンネル)

仮想ネットワークどうし (VNet 間) の接続は、VNet をオンプレミス サイトの場所に接続することと似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 マルチサイト接続構成と VNet 間通信を組み合わせることもできます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

以下のような VNet を接続できます。

* 同じリージョンまたは異なるリージョンにある
* 同じサブスクリプションまたは異なるサブスクリプションにある 
* 同じデプロイメント モデルまたは異なるデプロイメント モデルである

![Azure VPN Gateway VNet 間接続の例](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>デプロイメント モデル間の接続

Azure には現在、クラシックと Resource Manager という 2 つのデプロイメント モデルがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。 新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。 VNet 間に接続を作成し、一方の VNet 内のリソースがもう一方の VNet 内のリソースと直接通信できるようにすることが可能です。

### <a name="vnet-peering"></a>VNET ピアリング

仮想ネットワークが特定の要件を満たしていれば、接続の作成に VNET ピアリングを使用することができます。 VNET ピアリングは、仮想ネットワーク ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (専用のプライベート接続)

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。

ExpressRoute 接続では、公共のインターネットを利用できません。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

ExpressRoute 接続では VPN ゲートウェイは使用されませんが、仮想ネットワーク ゲートウェイが必要な構成の一部として使用されます。 ExpressRoute 接続では、仮想ネットワーク ゲートウェイは "Vpn" ではなく "ExpressRoute" というゲートウェイの種類で構成されます。 ExpressRoute の詳細については、「 [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

## <a name="coexisting"></a>サイト間と ExpressRoute の共存接続

ExpressRoute は、パブリックなインターネットを経由せずに WAN から Azure などの Microsoft サービスに直接アクセスする、専用の接続です。 サイト間 VPN トラフィックは、暗号化が施されたうえでパブリック インターネット経由で送受信されます。 同じ仮想ネットワークに対してサイト間 VPN と ExpressRoute 接続が構成可能な場合、いくつかの利点があります。

ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。 この構成では、同一の仮想ネットワークに 2 つの仮想ネットワーク ゲートウェイが必要なことに注意してください (1 つのゲートウェイの種類は "Vpn"、もう 1 つのゲートウェイの種類は "ExpressRoute")。

![ExpressRoute と VPN Gateway の共存接続の例](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>S2S 接続と ExpressRoute 接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>価格

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

VPN ゲートウェイの SKU の詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。

## <a name="faq"></a>FAQ

VPN Gateway に関してよく寄せられる質問については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- VPN ゲートウェイの構成を計画します。 [VPN Gateway の計画と設計](vpn-gateway-plan-design.md)に関するページを参照してください。
- 詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。
- [サブスクリプションとサービスの制限](../azure-subscription-service-limits.md#networking-limits)に関するページを参照してください。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
