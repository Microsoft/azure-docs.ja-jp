---
title: Azure をパブリック クラウドに接続する | Microsoft Docs
description: Azure を他のパブリック クラウドに接続するさまざまな方法について説明します
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 07/24/2019
ms.author: duau
ms.openlocfilehash: f413b6100fb2a7d1c8bc06d787468cc3e79c0169
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397476"
---
# <a name="connecting-azure-with-public-clouds"></a>Azure をパブリック クラウドに接続する

多くの企業が、マルチクラウド戦略により、ビジネスおよび技術上の目標を達成しようとしています。 これには、コスト、柔軟性、機能の可用性、冗長性、データ主権などが含まれます。この戦略は、両方のクラウドの長所を活用するのに役立ちます。 

この方法では、企業はネットワークとアプリケーションのアーキテクチャに関する課題にも直面します。 たとえば、待ち時間やデータのスループットの問題です。 これらの課題に対処するために、顧客は複数のクラウドに対する直接接続を検討しています。 一部のサービス プロバイダーは、顧客向けに複数のクラウド プロバイダーに接続するためのソリューションを提供しています。 それ以外にも、顧客が自分でルーターをデプロイして複数のパブリック クラウドに接続することもできます。
## <a name="connectivity-via-expressroute"></a>ExpressRoute 経由での接続性
ExpressRoute を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 顧客は ExpressRoute によって、Microsoft クラウド サービスへの接続を確立できます。

ExpressRoute 経由で接続するには 3 つの方法があります。

1. レイヤー 3 プロバイダー
2. レイヤー 2 プロバイダー
3. 直接接続

### <a name="layer3-provider"></a>レイヤー 3 プロバイダー

レイヤー 3 プロバイダーは、一般に IP VPN プロバイダーまたは MPLS VPN プロバイダーと呼ばれます。 顧客は、データ センター、ブランチ、クラウド間のマルチポイント接続にこれらのプロバイダーを利用します。 顧客は、BGP または既定の静的ルートで L3 プロバイダーに接続します。 サービス プロバイダーは、顧客サイト、データセンター、パブリック クラウド間のルートをアドバタイズします。 
 
レイヤー 3 プロバイダー経由で接続する場合、Microsoft は BGP で顧客の VNET ルートをサービス プロバイダーにアドバタイズします。 プロバイダーは、2 つの異なる実装をする場合があります。

![レイヤー 3 プロバイダーを示す図。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l3.png)

すべてのクラウド プロバイダーからのトラフィックが顧客ルーターに到達する場合、プロバイダーは個別の VRF 内の各クラウド プロバイダーにランディングする場合があります。 顧客がサービス プロバイダーに依頼して BGP を実行している場合、これらのルートは既定で他のクラウド プロバイダーに再びアドバタイズされます。 

サービス プロバイダーがすべてのクラウド プロバイダーを同じ VRF にランディングしている場合、ルートはサービス プロバイダーから他のクラウド プロバイダーに直接アドバタイズされます。 これは、eBGP ルートが既定で他の eBGP 近隣にアドバタイズされる場合の標準の BGP 操作を想定しています。

各パブリック クラウドには異なるプレフィックス制限があるため、ルートの配布時には、サービス プロバイダーはこれらの制限を考慮する必要があります。

### <a name="layer2-provider-and-direct-connection"></a>レイヤー 2 プロバイダーと直接接続

どちらのモデルでも物理的な接続は異なりますが、レイヤー 3 では MSEE と顧客のルーターの間に BGP が直接確立されます。 ExpressRoute Direct の顧客の場合は、MSEE に直接接続します。 一方でレイヤー 2 の場合、サービス プロバイダーは VLAN を顧客のオンプレミスからクラウドに拡張します。 顧客は、レイヤー 2 ネットワーク上で BGP を実行して、DC をクラウドに接続します。
![レイヤー 2 プロバイダーと直接接続を示す図。](media/expressroute-connect-azure-to-public-cloud/azure-to-public-clouds-l2.png)
どちらの場合でも、顧客は、各パブリック クラウドへのポイント ツー ポイント接続を利用できます。 顧客は、各パブリック クラウドに対して個別の BGP 接続を確立します。 1 つのクラウド プロバイダーによって受信されたルートは、既定で他のクラウド プロバイダーにアドバタイズされます。 各クラウド プロバイダーには異なるプレフィックス制限があるため、ルートをアドバタイズする際には、顧客はこれらの制限を考慮する必要があります。 顧客は、他のパブリック クラウドからルートをアドバタイズしながら、Microsoft に依頼して通常の BGP ノブを使用できます。

## <a name="direct-connection-with-expressroute"></a>ExpressRoute を使用した直接接続

顧客は、クラウド プロバイダーの直接接続サービスに ExpressRoute を直接接続することを選択できます。 2 つのクラウド プロバイダーが連続して接続され、それらのルーター間で BGP が直接確立されます。 この種類の接続は、現在、Oracle で使用できます。

## <a name="site-to-site-vpn"></a>サイト間 VPN

顧客は、インターネットを利用して、Azure のインスタンスを他のパブリック クラウドと接続できます。 ほとんどすべてのクラウド プロバイダーが、サイト間 VPN 機能を提供しています。 ただし、特定のバリアントがないことが原因で互換性の問題が発生することがあります。 たとえば、一部のクラウド プロバイダーは IKEv1 しかサポートしていないため、そのクラウドでは VPN 終了エンドポイントが必要です。 IKEv2 をサポートしているクラウド プロバイダーでは、両方のクラウド プロバイダーの VPN ゲートウェイの間に直接トンネルを確立できます。

サイト間 VPN は、高スループットで待ち時間の短いソリューションとは見なされていません。 ただし、物理接続のバックアップとして使用することはできます。

## <a name="next-steps"></a>次のステップ
ExpressRoute と仮想ネットワーク接続についてさらにご質問がある場合は、「[ExpressRoute の FAQ][ER-FAQ]」を参照してください。

Azure と Oracle の間の接続については、[Azure と Oracle Cloud の間の直接接続の設定][ER-OCI]に関する記事を参照してください。

<!--Link References-->
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[ER-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking



