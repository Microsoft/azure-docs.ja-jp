---
title: 仮想ネットワーク規則を使用する - Azure portal - Azure Database for PostgreSQL - Single Server
description: Azure Portal を使用して Azure Database for PostgreSQL - Single Server で VNet サービス エンドポイントとルールを作成および管理する
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 5682213780f6b5c1bbbc02dfa947c48a4d2707de
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86112461"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Azure Portal を使用して Azure Database for PostgreSQL - Single Server で VNet サービス エンドポイントと VNet ルールを作成および管理する
Virtual Network (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for PostgreSQL サーバーに拡張します。 制限事項も含め、Azure Database for PostgreSQL VNet サービス エンドポイントの概要については、[Azure Database for PostgreSQL サーバー VNet サービス エンドポイント](concepts-data-access-and-security-vnet.md)に関する記事をご覧ください。 VNet サービス エンドポイントは、Azure Database for PostgreSQL でサポートされるすべてのリージョンで利用できます。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。
> VNet ピアリングでは、トラフィックがサービス エンドポイントを含む共通 VNet Gateway を通過して、ピアにフローするようになっている場合は、ACL/VNet ルールを作成して、Gateway VNet 内の Azure Virtual Machines が Azure Database for PostgreSQL サーバーにアクセスできるようにしてください。


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Azure portal で VNet ルールを作成してサービス エンドポイントを有効にする

1. PostgreSQL サーバー ページの [設定] 見出しで、 **[接続のセキュリティ]** をクリックして Azure Database for PostgreSQL の [接続のセキュリティ] ウィンドウを開きます。 

2. [Azure サービスへのアクセスを許可] が **[オフ]** に設定されていることを確認します。

> [!Important]
> 制御を [オン] に設定したままにすると、Azure PostgreSQL Database サーバーはすべてのサブネットからの通信を受け入れます。 制御を [オン] に設定したままにすると、セキュリティの観点からアクセス過多になる可能性があります。 Microsoft Azure Virtual Network サービス エンドポイント機能は、Azure Database for PostgreSQL の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

3. 次に、 **[+ 既存の仮想ネットワークを追加]** をクリックします。 既存の VNet がない場合は、 **[+ 新しい仮想ネットワークの作成]** をクリックして作成できます。 「[クイック スタート:Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください

   ![Azure Portal - [接続のセキュリティ] のクリック](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. VNet ルール名を入力し、サブスクリプション、仮想ネットワーク、サブネット名を選択して、 **[有効にする]** をクリックします。 これにより、**Microsoft.SQL** サービス タグを使用してサブネット上で VNet サービス エンドポイントが自動的に有効になります。

   ![Azure portal - VNet の構成](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    アカウントには、仮想ネットワークとサービス エンドポイントを作成するためのアクセス許可が必要です。

    サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。
    
    Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。
    
    [組み込みロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)と、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)に割り当てる方法の詳細をご覧ください。
    
    Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 VNet と Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 両方のサブスクリプションに、**Microsoft.Sql** リソース プロバイダーが登録されていることを確認してください。 詳細については、[resource-manager-registration][resource-manager-portal] に関するページをご覧ください

   > [!IMPORTANT]
   > サービス エンドポイントを構成する前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **仮想ネットワーク サービス エンドポイント:** [仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MySQL サーバーを含むすべての Azure Database サービスに対してサービス エンドポイント トラフィックが構成されることに注意することが重要です。 
   > 

5. 有効になったら、 **[OK]** をクリックすると、VNet ルールと共に VNet サービス エンドポイントが有効になっていることが表示されます。

   ![VNet サービス エンドポイントが有効で、VNet ルールが作成されている](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>次のステップ
- 同様に、スクリプトを作成して、[Azure CLI を使用して VNet サービス エンドポイントを有効にし、Azure Database for PostgreSQL の VNET ルールを作成](howto-manage-vnet-using-cli.md)できます。
- Azure Database for PostgreSQL サーバーに接続する方法のヘルプについては、「[Azure Database for PostgreSQL の接続ライブラリ](./concepts-connection-libraries.md)」をご覧ください。

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md