---
title: Windows Server および Linux 上で Service Fabric クラスターを作成する | Microsoft Docs
description: Service Fabric クラスターは Windows Server または Linux で実行されます。つまり、Windows Server または Linux を実行できる任意の場所に Service Fabric アプリケーションをデプロイしてホストできます。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/28/2018
ms.author: dekapur
ms.openlocfilehash: e4540076b29cf3cd51f03239a1868e18a41781d9
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52726527"
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Windows Server または Linux での Service Fabric クラスターの作成
Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM をクラスター ノードといいます。 クラスターは多数のノードにスケールできます。 新しいノードがクラスターに追加されると、Service Fabric は、増加したノード数全体で、サービスのパーティションのレプリカとインスタンスのバランスを再調整します。 アプリケーション全体のパフォーマンスが向上し、メモリへのアクセスの競合が減少します。 クラスター内のノードが効率的に使用されていない場合、クラスター内のノードの数を削減できます。 Service Fabric は、各ノードのハードウェアを効率的に利用できるように、減らされたノード数全体で、再度パーティションのレプリカとインスタンスのバランスを再調整します。

Service Fabric を使用すると、Windows Server または Linux を実行するすべての VM またはコンピューター上に Service Fabric クラスターを作成できます。 つまり、オンプレミスか、Microsoft Azure か、クラウド プロバイダーかに関係なく、相互接続された一連の Windows Server コンピューターまたは Linux コンピューターがある任意の環境に、Service Fabric アプリケーションをデプロイして実行できます。

## <a name="create-service-fabric-clusters-on-azure"></a>Azure 上での Service Fabric クラスターの作成
Azure でのクラスターの作成は、リソース モデル テンプレートまたは [Azure Portal](https://portal.azure.com) を使用して行います。 詳細については、[Resource Manager テンプレートを使用した Service Fabric クラスターの作成](service-fabric-cluster-creation-via-arm.md)に関する記事または [Azure Portal からの Service Fabric クラスターの作成](service-fabric-cluster-creation-via-portal.md)に関する記事を参照してください。

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Azure 上のクラスターでサポートされているオペレーティング システム
クラスターは、次のオペレーティング システムが実行されている仮想マシン上に作成できます。

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (プレビュー サポート)

> [!NOTE]
> Windows Server 1709 で Service Fabric をデプロイする場合、(1) これは Long Term Servicing Branch ではないため、今後、バージョンの移動が必要になる可能性があります。また、(2) コンテナーをデプロイする場合、Windows Server 2016 で構築されたコンテナーは Windows Server 1709 で動作しません。その逆も同様です (デプロイするにはリビルドが必要です)。
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>オンプレミスまたはクラウド プロバイダーでの Service Fabric スタンドアロン クラスターの作成
Service Fabric には、オンプレミスまたは任意のクラウド プロバイダーにスタンドアロン Service Fabric クラスターを作成するためのインストール パッケージが用意されています。

Windows Server におけるスタンドアロン Service Fabric クラスターのセットアップの詳細については、「[Windows Server で実行されるスタンドアロン クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)」を参照してください。

  > [!NOTE]
  > Linux のスタンドアロン クラスターは、現在サポートされていません。 Linux は、開発および Azure Linux 複数マシン クラスターに適した 1 つのボックスでサポートされています。
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>クラウドのデプロイとオンプレミスのデプロイの比較
オンプレミスに Service Fabric クラスターを作成するプロセスは、一連の VM がある任意のクラウド上にクラスターを作成するプロセスとほぼ同じです。 VM をプロビジョニングするための最初の手順は、使用しているクラウド プロバイダーまたはオンプレミス環境に左右されます。 一連の VM を相互にネットワーク接続できる状態にした後は、Service Fabric パッケージの設定、クラスター設定の編集、クラスターの作成と管理のスクリプトの実行の手順はオンプレミスの場合と同じです。 これにより、新しいホスティング環境の使用を決めたときにも、Service Fabric クラスターの運用と管理の経験を活かすことができます。

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>スタンドアロンの Service Fabric クラスターを作成する利点
* クラスターをホストするクラウド プロバイダーを自由に選択できます。
* Service Fabric アプリケーションは、一度記述すると、複数のホスティング環境で最小限の変更で実行できます。
* Service Fabric アプリケーションの構築に関する知識は、ホスティング環境が変わっても適用できます。
* Service Fabric クラスターの実行と管理に関する運用経験は、環境が変わっても活かせます。
* ホスティング環境による制約がないため、広範な顧客にアプローチできます。
* データ センターまたはクラウド プロバイダーにブラックアウトが発生した場合でもサービスを別のデプロイ環境に移行できるため、広範囲に及ぶサービス停止に対する信頼性と保護が強化されています。

## <a name="supported-operating-systems-for-standalone-clusters"></a>スタンドアロンのクラスターでサポートされているオペレーティング システム
クラスターは、次のオペレーティング システムが実行されている VM 上またはコンピューター上に作成できます (Linux はまだサポートされていません)。

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>オンプレミスに作成されたスタンドアロンの Service Fabric クラスターと比較した場合の Azure 上の Service Fabric クラスターのメリット
オンプレミスのオプションと比較して、Azure で Service Fabric クラスターを実行することにはメリットがあります。そのため、クラスターの実行場所に関して特別なニーズがない場合は、Azure で実行することをお勧めします。 Azure では、他の Azure の機能やサービスとの統合が提供されているため、クラスターの操作と管理が容易で信頼性が高くなります。

* **Azure portal:** Azure portal を使用すると、クラスターの作成と管理が容易になります。
* **Azure Resource Manager:** Azure Resource Manager を使用すると、クラスターでユニットとして使用されているリソースの管理が容易になり、コストの追跡と要求を簡素化できます。
* **Azure リソースとしての Service Fabric クラスター** : Service Fabric クラスターは Azure リソースなので、Azure の他の Azure リソースと同じようにモデル化できます。
* **Azure インフラストラクチャとの統合** : Service Fabric は、OS、ネットワーク、およびその他のアップグレードに関して基になる Azure インフラストラクチャと連携するため、アプリケーションの可用性と信頼性が向上します。  
* **診断**:Azure では、Azure 診断および Log Analytics との統合が提供されます。
* **自動スケール**:Azure 上のクラスターの場合、仮想マシン スケール セットであるため、組み込みの自動スケール機能が提供されます。 オンプレミスおよび他のクラウド環境では、自動スケール機能を独自に構築するか、Service Fabric で公開されているクラスター スケーリング用 API を手動で使用してスケールする必要があります。

## <a name="next-steps"></a>次の手順

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-cluster-creation-via-portal.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

