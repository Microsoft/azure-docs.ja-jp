---
title: 'Azure ExpressRoute: ピアリングの構成'
description: この記事では、ExpressRoute のプライベートおよび Microsoft ピアリングを作成してプロビジョニングする手順を説明します。 この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: 8ac38c25821a9f636ac98903599ee7d3acfa5e17
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395725"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute 回線のピアリングの作成と変更を行う

この記事は、Azure portal を使用して、Azure Resource Manager (ARM) ExpressRoute 回線のルーティング構成を作成して管理するときに役立ちます。 また、ExpressRoute 回線の状態確認、ピアリングの更新、または削除およびプロビジョニング解除を行うこともできます。 別の方法を使用して回線を操作する場合は、次の一覧から記事を選択してください。

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [パブリック ピアリング](about-public-peering.md)
> * [ビデオ - プライベート ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [ビデオ - Microsoft ピアリング](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (クラシック)](expressroute-howto-routing-classic.md)
> 

ExpressRoute 回線にプライベート ピアリングおよび Microsoft ピアリングを構成できます (Azure パブリック ピアリングは新しい回線では非推奨です)。 ピアリングは、任意の順序で構成できます。 ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。 ルーティング ドメインとピアリングの詳細については、[ExpressRoute のルーティング ドメイン](expressroute-circuit-peerings.md)に関する記事をご覧ください。 パブリック ピアリングについては、[ExpressRoute のパブリック ピアリング](about-public-peering.md)に関する記事をご覧ください。

## <a name="configuration-prerequisites"></a>構成の前提条件

* 構成を開始する前に必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページおよび[ワークフロー](expressroute-workflows.md) ページを確認してください。
* アクティブな ExpressRoute 回線が必要です。 手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-portal-resource-manager.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。 ピアリングを構成するためには、ExpressRoute 回線がプロビジョニングされて有効な状態になっている必要があります。 
* 共有キー/MD5 ハッシュを使用する場合は、必ずトンネルの両側でこれを使用し、英数字の文字数を最大 25 文字に制限してください。 特殊文字はサポートされていません。 

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。 サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IP VPN) を使う場合、接続プロバイダーがユーザーに代わってルーティングを構成して管理します。 

> [!IMPORTANT]
> 現在のところ、サービス管理ポータルでは、サービス プロバイダーが構成したピアリングをアドバタイズしていません。 できるだけ早くこの機能を提供できるように取り組んでいます。 BGP ピアリングを構成する前に、ご利用のサービス プロバイダーにお問い合わせください。
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft ピアリング

このセクションでは、ExpressRoute 回線用の Microsoft ピアリング構成を作成、取得、更新、および削除します。

> [!IMPORTANT]
> 2017 年 8 月 1 日より前に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが定義されていない場合でも、すべてのサービス プレフィックスが Microsoft ピアリングでアドバタイズされます。 2017 年 8 月 1 日以降に構成された ExpressRoute 回線の Microsoft ピアリングでは、ルート フィルターが回線に接続されるまで、プレフィックスはアドバタイズされません。 詳しくは、「[Configure a route filter for Microsoft peering](how-to-routefilter-powershell.md)」(Microsoft ピアリング用にルート フィルターを構成する) をご覧ください。
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft ピアリングを作成するには

1. ExpressRoute 回線を構成します。 **[プロバイダーの状態]** をチェックして、続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。

   接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Microsoft ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、お使いの接続プロバイダーがお客様に代わってルーティングを管理しない場合は、回線を作成した後、次の手順を続行してください。

   **回線 - プロバイダーの状態:未プロビジョニング**

   [![ExpressRoute デモ回線の [概要] ページを示すスクリーンショット。赤い枠によって、[未プロビジョニング] に設定されたプロバイダーの状態が強調表示されている。](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **回線 - プロバイダーの状態:プロビジョニング済み**

   [![ExpressRoute デモ回線の [概要] ページを示すスクリーンショット。赤い枠によって、[プロビジョニング済み] に設定されたプロバイダーの状態が強調表示されている。](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. 回路の Microsoft ピアリングを構成する 続行する前に、次の情報を確認してください。

   * プライマリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * セカンダリ リンク用の /30 サブネット。 これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。
   * アドバタイズするプレフィックス:BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。 パブリック IP アドレス プレフィックスのみが受け入れられます。 一連のプレフィックスを送信する場合は、コンマ区切りのリストを送信できます。 これらのプレフィックスは、RIR/IRR に登録する必要があります。
   * **省略可能 -** 顧客 ASN:ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスを登録する AS 番号を指定できます。
   * ルーティング レジストリ名: AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。
3. 次の例のように、構成するピアリングを選ぶことができます。 Microsoft ピアリング行を選択します。

   [![Microsoft ピアリング行の選択](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Microsoft ピアリング行の選択")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Microsoft ピアリングを構成します。 すべてのパラメーターを指定したら、構成を**保存**します。 次の図は構成例です。

   ![Microsoft ピアリングを構成する](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Microsoft は、指定された 'アドバタイズされたパブリック プレフィックス' と 'ピア ASN' (または '顧客 ASN') がインターネット ルーティング レジストリでユーザーに割り当てられているかどうかを確認します。 別のエンティティからパブリック プレフィックスを取得している場合、およびルーティング レジストリに割り当てが記録されていない場合、自動検証は完了せず、手動検証が必要になります。 自動検証が失敗した場合は、[検証が必要です] というメッセージが表示されます。 
>
> '検証が必要です' というメッセージが表示された場合は、ルーティング レジストリにプレフィックスの所有者として一覧表示されているエンティティによって組織にパブリック プレフィックスが割り当てられていることを示すドキュメントを収集し、下に示すようにサポート チケットを開くことにより、これらのドキュメントを手動検証のために送信してください。 
>

   回線が "検証が必要です" の状態になった場合、サポート チケットを開き、プレフィックスの所有権を示す証拠をサポート チームに示してください。 次の例のように、ポータルから直接サポート チケットを開くことができます。

   ![検証が必要です - サポート チケット](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. 構成が正常に受け付けられると、次のような画面が表示されます。

   ![ピアリングの状態: 構成済み](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "ピアリングの状態: 構成済み")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft ピアリングの詳細を表示するには

Microsoft ピアリングの行を選択して、そのピアリングのプロパティを表示できます。

[![Microsoft ピアリングのプロパティの表示](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "プロパティの表示")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft ピアリング構成を更新するには

変更するピアリングの行を選択し、次にピアリング プロパティを変更して変更内容を保存できます。

![ピアリングの行を選択する](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft ピアリングを削除するには

次の図のように削除アイコンをクリックして、ピアリングの構成を削除できます。

![ピアリングを削除する](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除します。

### <a name="to-create-azure-private-peering"></a>Azure プライベート ピアリングを作成するには

1. ExpressRoute 回線を構成します。 続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。 

   接続プロバイダーが管理対象レイヤー 3 サービスを提供する場合は、Azure プライベート ピアリングを有効にするように接続プロバイダーに依頼できます。 その場合は、次のセクションにリストされている手順に従う必要はありません。 ただし、お使いの接続プロバイダーがお客様に代わってルーティングを管理しない場合は、回線を作成した後、次の手順を続行してください。

   **回線 - プロバイダーの状態:未プロビジョニング**

   [![ExpressRoute デモ回線の [概要] ページを示すスクリーンショット。赤い枠によって、[未プロビジョニング] に設定されたプロバイダーの状態が強調表示されている。](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **回線 - プロバイダーの状態:プロビジョニング済み**

   [![ExpressRoute デモ回線の [概要] ページを示すスクリーンショット。赤い枠によって、[プロビジョニング済み] に設定されたプロバイダーの状態が強調表示されている。](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. 回線用に Azure プライベート ピアリングを構成します。 次の手順に進む前に、以下のものがそろっていることを確認します。

   * プライマリ リンク用の /30 サブネット。 サブネットを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * セカンダリ リンク用の /30 サブネット。 サブネットを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。 このサブネットから、ユーザーは 1 番目に使用可能な IP アドレスを自分のルーターに割り当て、Microsoft は 2 番目に使用可能な IP アドレスをそのルーターに割り当てます。
   * このピアリングを確立するための有効な VLAN ID。 回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。 プライマリとセカンダリの両方のリンクに対し、同じ VLAN ID を使用する必要があります。
   * ピアリングの AS 番号。 2 バイトと 4 バイトの AS 番号の両方を使用することができます。 このピアリングではプライベート AS 番号を使用できます (ただし、65515 ～ 65520 までの番号を除きます)。
   * プライベート ピアリングを設定するときに、オンプレミスの Edge ルーターから BGP 経由で Azure にルートをアドバタイズする必要があります。
   * **省略可能 -** MD5 を使用する場合には、パスワードを準備します。
3. 次の例で示すように、Azure プライベート ピアリング行を選択します。

   [![プライベート ピアリング行の選択](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "プライベート ピアリング行の選択")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. プライベート ピアリングを構成します。 すべてのパラメーターを指定したら、構成を**保存**します。

   ![プライベート ピアリングを構成する](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. 構成が正常に受け付けられると、次のような画面が表示されます。

   ![保存されたプライベート ピアリング](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure プライベート ピアリングの詳細を表示するには

ピアリングを選択して、Azure プライベート ピアリングのプロパティを表示することができます。

[![プライベート ピアリングのプロパティの表示](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "プライベート ピアリングのプロパティの表示")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure プライベート ピアリングの構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。 更新後、変更を保存します。

![プライベート ピアリングを更新する](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure プライベート ピアリングを削除するには

ピアリングの構成を削除するには、次の図ように削除アイコンを選びます。

> [!WARNING]
> この例を実行する前に、すべての仮想ネットワークと ExpressRoute Global Reach 接続が削除されていることを確認する必要があります。 
> 
> 

![プライベート ピアリングを削除する](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>次のステップ

次の手順では、 [ExpressRoute 回線に VNet をリンク](expressroute-howto-linkvnet-portal-resource-manager.md)します。
* ExpressRoute ワークフローの詳細については、「 [ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。
* 回路ピアリングの詳細については、「 [ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
* 仮想ネットワークの詳細については、「 [仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。
