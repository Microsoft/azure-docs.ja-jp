---
title: Azure Site Recovery を使用して Azure VM を別のリージョンに移動する
description: Azure Site Recovery を使用して、異なる Azure リージョン間で Azure VM を移動します。
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0c7efc94bcde18e7b6ff43726602fa87641f3e76
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "86130623"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Azure VM を別の Azure リージョンに移動する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure VM を別の Azure リージョンに移動する理由とその手順について概要を示します。 


## <a name="reasons-to-move-azure-vms"></a>Azure VM を移動する理由

ユーザーが VM を移動する理由としては、次のケースが考えられます。

- 既にデプロイ済みのリージョンがあるとき、既存のアプリケーションまたはサービスのエンド ユーザーにより近いリージョンが新たにサポートされた場合。 このシナリオでは、待機時間の短縮を図るために、既存の VM をそのまま新しいリージョンに移動したいと考えるでしょう。 サブスクリプションを統合したい場合や、ガバナンス/組織のルール上、移動が必要となる場合にも、同じ手法を用います。
- 単一インスタンス VM として、または可用性セットの一部として、VM がデプロイされた場合。 可用性 SLA を向上させたい場合は、VM を可用性ゾーンに移動します。

## <a name="steps-to-move-azure-vms"></a>Azure VM を移動する手順

VM の移動には、次の手順が伴います。

1. 前提条件を確認します。
2. ソース VM を準備します。
3. ターゲット リージョンを準備します。
4. ターゲット リージョンにデータをコピーします。 Azure Site Recovery のレプリケーション テクノロジを使用して、ソース VM からターゲット リージョンにデータをコピーします。
5. 構成をテストします。 レプリケーションが完了したら、非運用ネットワークへのテスト フェールオーバーを実行して構成をテストします。
6. 移動を実行します。
7. ソース リージョンのリソースを破棄します。

> [!NOTE]
> これらの手順の詳細については、以下のセクションで説明します。
> [!IMPORTANT]
> 現在 Azure Site Recovery では、異なるリージョン間の VM の移動はサポートされますが、同じリージョン内での移動はサポートされません。

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>多層デプロイの一般的なアーキテクチャ

このセクションでは、Azure における多層アプリケーションの最も一般的なデプロイ アーキテクチャについて説明します。 ここでは、パブリック IP を持つ 3 階層アプリケーションの例を取り上げています。 各階層 (Web、アプリケーション、データベース) は、それぞれ 2 つの VM を有しており、Azure ロード バランサーによって他の階層に接続されています。 データベース層の VM 間には SQL Server Always On レプリケーションが存在し、高可用性が確保されています。

* **単一インスタンスの VM を各種階層にデプロイ**: 階層内の各 VM は単一インスタンスの VM として構成され、ロード バランサーによって他の階層に接続されます。 この構成は最も簡単に採用できます。

     ![階層への単一インスタンスの VM のデプロイ](media/move-vm-overview/regular-deployment.png)

* **各階層の VM を可用性セットにデプロイ**: 階層内の各 VM は可用性セットに構成されます。 [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)を使うことで、Azure にデプロイする VM は、クラスター内で切り離された複数のハードウェア ノードに確実に分散されます。 これにより、Azure 内でハードウェアまたはソフトウェアの障害が発生した場合に影響を受けるのは VM のサブセットに限定され、ソリューション全体は引き続き利用可能であり、運用可能であることが保証されます。

     ![可用性セットへの VM のデプロイ](media/move-vm-overview/avset.png)

* **各階層の VM を Availability Zones にデプロイ**: 階層内の各 VM は [Availability Zones](../availability-zones/az-overview.md) に構成されます。 Azure リージョン内の可用性ゾーンは、障害ドメインと更新ドメインを組み合わせたものです。 たとえば、Azure リージョンの 3 つのゾーンに 3 つ以上の VM を作成する場合、VM は実際には 3 つの障害ドメインと 3 つの更新ドメインに分散されます。 Azure プラットフォームは更新ドメインへのこの分散を認識し、異なるゾーン内の VM が同時に更新されないようにします。

     ![可用性ゾーンのデプロイ](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>VM をそのままターゲット リージョンに移動する

前述の[アーキテクチャ](#typical-architectures-for-a-multi-tier-deployment)をベースに、ターゲット リージョンに VM をそのまま移動した後のデプロイの概観を次に示します。

* **単一インスタンスの VM を各種階層にデプロイ**

     ![階層への単一インスタンスの VM のデプロイ](media/move-vm-overview/single-zone.png)

* **各階層の VM を可用性セットにデプロイ**

     ![リージョン間の可用性セット](media/move-vm-overview/crossregionaset.png)

* **各階層の VM を Availability Zones にデプロイ**

     ![Availability Zones への VM のデプロイ](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>VM を移動して可用性を高める

* **単一インスタンスの VM を各種階層にデプロイ**

     ![階層への単一インスタンスの VM のデプロイ](media/move-vm-overview/single-zone.png)

* **各階層の VM を可用性セットにデプロイ**: Azure Site Recovery を使用して VM のレプリケーションを有効にすると、可用性セット内の VM を個別の Availability Zones に構成できます。 移動操作の完了後、可用性の SLA は 99.99% になります。

     ![可用性セットと Availability Zones への VM のデプロイ](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> 
> * [Azure VM を別のリージョンに移動する](azure-to-azure-tutorial-migrate.md)
> 
> * [Azure VM を Availability Zones に移動する](move-azure-vms-avset-azone.md)

