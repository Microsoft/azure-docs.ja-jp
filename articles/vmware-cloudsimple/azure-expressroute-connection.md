---
title: Azure VMware Solutions (AVS) - ExpressRoute を使用して Azure ネットワークに AVS プライベート クラウドを接続する
description: ExpressRoute を使用して AVS プライベート クラウド環境を Azure 仮想ネットワークに接続する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015237"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>ExpressRoute を使用して AVS プライベート クラウド環境を Azure 仮想ネットワークに接続する

Azure ExpressRoute を使用して、お使いの AVS プライベート クラウドを Azure 仮想ネットワークに接続できます。 この高帯域幅と低待機時間の接続によって、AVS プライベート クラウド環境から Azure サブスクリプションで実行されているサービスにアクセスできます。

仮想ネットワーク接続により、次のことを実行できます。

* AVS プライベート クラウド上の仮想マシンのバックアップ ターゲットとして Azure を使用します。
* AVS プライベート クラウドの vSAN データストアを暗号化する Azure サブスクリプション内の KMS サーバーを展開します。
* ハイブリッド アプリケーションを使用します。アプリケーションの Web 層は、アプリケーションおよびデータベースの層が AVS プライベート クラウドで実行する間にパブリック クラウドで実行します。

![仮想ネットワークへの Azure ExpressRoute 接続](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>仮想ネットワーク接続の設定

AVS プライベート クラウドへの仮想ネットワーク接続を設定するには、承認キー、ピア回線 URI、および Azure サブスクリプションへのアクセスが必要です。 この情報は、AVS ポータルの仮想ネットワーク接続に関するページで確認できます。 手順については、[Azure 仮想ネットワークから AVS 接続へのピアリング情報を取得する](virtual-network-connection.md)方法に関する記事を参照してください。 情報の取得に問題がある場合は、 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">サポート リクエスト</a>を送信してください。

> [!TIP]
> Azure 仮想ネットワーク、ゲートウェイ サブネット、仮想ネットワーク ゲートウェイが既にある場合は、手順 4 に進むことができます。

1. Azure サブスクリプションで仮想ネットワークを作成し、選択したアドレス空間が AVS プライベート クラウドのアドレス空間と異なることを確認します。 Azure 仮想ネットワークが既にある場合は、既存のものを使用できます。 詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。
2. Azure 仮想ネットワークにゲートウェイ サブネットを作成します。 Azure 仮想ネットワークにゲートウェイ サブネットが既にある場合は、既存のものを使用できます。 詳細については、「[ゲートウェイ サブネットを作成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet)」を参照してください。
3. 仮想ネットワークに仮想ネットワーク ゲートウェイを作成します。 既存の仮想ネットワーク ゲートウェイがある場合は、既存のものを使用できます。 詳細については、「[仮想ネットワーク ゲートウェイを作成する](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)」を参照してください。
4. [仮想ネットワークを回線に接続する (異なるサブスクリプション)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription) に関する記事の説明に従い、承認キーを利用して、仮想ネットワークと AVS プライベート クラウドの間の接続を作成します。

> [!WARNING]
> 既存の仮想ネットワーク ゲートウェイを使用していて、AVS ExpressRoute 回線と同じ場所への ExpressRoute 接続がある場合、その接続は確立されません。 新しい仮想ネットワークを作成し、前述の手順に従ってください。

## <a name="test-the-virtual-network-connection"></a>仮想ネットワーク接続のテスト

接続が作成されたら、 **[設定]** の下の **[プロパティ]** を選択して、接続の状態を確認できます。 状態とプロビジョニングの状態が **[成功]** と表示されます。

![接続の状態](media/azure-expressroute-connection.png)

仮想ネットワーク接続をテストするには、次のようにします。

1. Azure サブスクリプションで仮想マシンを作成します。
2. お使いの AVS プライベート クラウド vCenter の IP アドレスを検索します (ウェルカム メールを参照)。
3. Azure 仮想ネットワーク内に作成した仮想マシンからクラウド vCenter に ping を実行します。
4. お使いの AVS プライベート クラウド vCenter で実行されている仮想マシンから Azure 仮想マシンに ping を実行します。

接続の確立に問題がある場合は、 <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">サポート リクエスト</a>を送信してください。
