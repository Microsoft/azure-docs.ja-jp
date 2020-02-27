---
title: Azure サービス用の仮想ネットワーク
titlesuffix: Azure Virtual Network
description: 仮想ネットワークにリソースをデプロイすることの利点について説明します。 仮想ネットワークに置いたリソースは互いに通信できるほか、オンプレミスのリソースと通信できます。トラフィックがインターネットを通過することがありません。
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048840"
---
# <a name="virtual-network-integration-for-azure-services"></a>Azure サービスの仮想ネットワーク統合

Azure 仮想ネットワークに Azure サービスを統合すると、仮想ネットワーク内の仮想マシンまたはコンピューティング リソースからサービスにプライベート アクセスできるようになります。
次のオプションを使用して、仮想ネットワークで Azure サービスを統合することができます。
- サービスの専用インスタンスを仮想ネットワークにデプロイする。 サービスは、仮想ネットワーク内で、また、オンプレミス ネットワークからプライベート アクセスできます。
- [プライベート リンク](../private-link/private-link-overview.md)を使用して、仮想ネットワークとオンプレミス ネットワークからサービスの特定のインスタンスにプライベートでアクセスします。

[サービス エンドポイント](virtual-network-service-endpoints-overview.md)を介して仮想ネットワークをサービスに拡張することによって、パブリック エンドポイントを使用してサービスにアクセスすることもできます。 サービス エンドポイントを使用することで、仮想ネットワークに対してサービス リソースをセキュリティで保護することができます。
 
## <a name="deploy-azure-services-into-virtual-networks"></a>仮想ネットワークに Azure サービスをデプロイする

[仮想ネットワーク](virtual-networks-overview.md)に専用の Azure サービスをデプロイすると、プライベート IP アドレスを利用してサービス リソースに非公開で通信できます。

![仮想ネットワークにデプロイされたサービス](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

仮想ネットワーク内にサービスをデプロイすると、次のことができるようになります。

- 仮想ネットワーク内のリソースは、プライベート IP アドレスを利用し、非公開で互いと通信できます。 たとえば、仮想ネットワーク内で、HDInsight と仮想マシンで実行されている SQL Server の間でデータを直接転送できます。
- オンプレミスのリソースは、[サイト間 VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) または [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) とプライベート IP アドレスを利用し、仮想ネットワーク内のリソースにアクセスできます。
- 仮想ネットワーク内のリソースがプライベート IP アドレスで互いに通信できるように仮想ネットワークを[ピアリング](virtual-network-peering-overview.md)できます。
- 仮想ネットワーク内のサービス インスタンスは通常、Azure サービスによって完全に管理されます。 たとえば、リソースの正常性が監視され、また負荷に応じてスケーリングされます。
- サービス インスタンスは、仮想ネットワークのサブネットにデプロイされます。 サブネットのインバウンド ネットワーク アクセスとアウトバウンド ネットワーク アクセスは、サービスで提供されるガイダンスに基づき、[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)経由で開放する必要があります。
- また、いくつかのサービスには、デプロイ先のサブネットに関して制限が課され、ポリシーの適用、ルート、同じサブネット内の VM とサービス リソースの組み合わせに制限があります。 具体的な制限は、時間の経過と共に変わる可能性があるため、サービスごとにそれらをチェックしてください。 そのようなサービスの例として、Azure NetApp Files、Dedicated HSM、Azure Container Instances、App Service があります。 
- 場合によっては、サブネットが特定のサービスをホストできる明示的な識別子として、[委任されたサブネット](virtual-network-manage-subnet.md#add-a-subnet)がサービスで必要になることがあります。 委任されたサブネットにサービス固有のリソースを作成するための明示的なアクセス許可を、サービスは委任により取得します。
- [委任されたサブネットを含む仮想ネットワーク](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)での REST API 応答の例を参照してください。 委任サブネット モデルを使用するサービスを網羅した一覧は、[Available Delegations](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) API で取得できます。

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>仮想ネットワークにデプロイできるサービス

|カテゴリ|サービス| 専用¹ サブネット
|-|-|-|
| Compute | 仮想マシン:[Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[クラウド サービス](https://msdn.microsoft.com/library/azure/jj156091):仮想ネットワーク (クラシック) のみ<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| いいえ <br/> いいえ <br/> いいえ <br/> いいえ²
| ネットワーク | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[ネットワーク仮想アプライアンス](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | はい <br/> はい <br/> はい <br/> いいえ
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database マネージド インスタンス](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| はい <br/> はい <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |いいえ² <br/> いいえ² <br/> 
| ID | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |いいえ <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service エンジン](https://github.com/Azure/acs-engine)と Azure Virtual Network CNI [プラグイン](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|いいえ²<br/> はい <br/><br/> いいえ
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service 環境](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|はい <br/> はい <br/> はい
| ホストされている | [Azure の専用 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|はい <br/> はい <br/>
| | |

¹ "専用" とは、そのサブネットにデプロイできるのがサービス固有のリソースのみであり、ユーザーの VM/VMSS と一緒にデプロイすることはできないことを意味します。 <br/> ² これらのサービスは専用サブネット内に作成することをお勧めしますが、サービスによって定められた必須の要件ではありません。
