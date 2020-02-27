---
title: Azure VM のサイズ - GPU | Microsoft Docs
description: Azure の仮想マシンで使用できるさまざまな GPU 最適化済みのサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 9be3c14bb435aaa1cddf008a27389bb3b1fdbc3d
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492548"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 最適化済み仮想マシンのサイズ

GPU 最適化済み VM サイズは、1 つまたは複数の NVIDIA GPU を備えた、特殊な用途に特化した仮想マシンです。 これらのサイズは、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを意図して設計されています。 この記事では、GPU、vCPU、データ ディスク、NIC の数と種類についての情報を提供します。 このグループ内の各サイズのストレージのスループットおよびネットワーク帯域幅も含まれています。

- [NC シリーズ](nc-series.md)、[NCv2 シリーズ](ncv2-series.md)、[NCv3 シリーズ](ncv3-series.md)の各サイズは、コンピューティング集中型やネットワーク集中型のアプリケーションおよびアルゴリズム向けに最適化されています。 例としては、CUDA および OpenCL ベースのアプリケーションやシミュレーション、AI、ディープ ラーニングなどが挙げられます。 NCv3 シリーズは、NVIDIA の Tesla V100 GPU を搭載したハイ パフォーマンス コンピューティング ワークロードにフォーカスしています。 NC シリーズは Intel Xeon E5-2690 v3 2.60GHz v3 (Haswell) プロセッサを使用し、NCv2 シリーズおよび NCv3 シリーズ VM は Intel Xeon E5-2690 v4 (Broadwell) プロセッサを使用しています。

- [ND シリーズ](nd-series.md)および [NDv2 シリーズ](ndv2-series.md)のサイズは、ディープ ラーニング用のトレーニングと推論のシナリオにフォーカスしています。 これらは、NVIDIA Tesla P40 GPU および Intel Xeon E5-2690 v4 (Broadwell) プロセッサを使用しています。 NDv2 シリーズは、Intel Xeon Platinum 8168 (Skylake) プロセッサを使用しています。

- [NV シリーズ](nv-series.md)と [NVv3 シリーズ](nvv3-series.md)のサイズは、リモートの視覚化、ストリーミング、ゲーム、エンコーディング、および OpenGL や DirectX などのフレームワークを使用する VDI シナリオ用に最適化および設計されています。 これらの VM は、NVIDIA Tesla M60 GPU によってバックアップされされます。

- [NVv4 シリーズ](nvv4-series.md)の VM サイズは、VDI およびリモート視覚化用に最適化され、設計されています。 パーティション分割された GPU では、NVv4 は、より小さな GPU リソースを必要とするワークロードに適したサイズを提供します。 これらの VM は、AMD Radeon Instinct MI25 GPU によって支援されています。

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Azure N シリーズ VM の GPU 機能を利用するには、NVIDIA GPU ドライバーをインストールする必要があります。

[NVIDIA GPU ドライバー拡張機能](/extensions/hpccompute-gpu-windows.md)は、N シリーズ VM 上に適切な NVIDIA CUDA または GRID ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[NVIDIA GPU ドライバー拡張機能のドキュメント](/extensions/hpccompute-gpu-windows.md)を参照してください。 VM 拡張機能の一般情報については、「[Azure 仮想マシンの拡張機能と機能](/extensions/overview.md)」をご覧ください。

NVIDIA GPU ドライバーを手動でインストールすることを選択した場合、サポートされるオペレーティング システム、ドライバー、インストール、および検証の手順について、[Windows 用 N シリーズ GPU ドライバーのセットアップ](/windows/n-series-driver-setup.md)または [Linux 用 N シリーズ GPU ドライバーのセットアップ](/linux/n-series-driver-setup)に関する記事を参照してください。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

- N シリーズ VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。

- N シリーズ VM をデプロイするには、Resource Manager デプロイ モデルを使用する必要があります。

- N シリーズ VM は、それぞれのディスクに対してサポートされる Azure Storage の種類が異なります。 NC および NV の VM では、Standard Disk Storage (HDD) で提供される VM ディスクのみがサポートされます。 NCv2、NCv3、ND、NDv2、および NVv2 の VM では、Premium Disk Storage (SSD) で提供される VM ディスクのみがサポートされます。

- 多数の N シリーズ VM をデプロイする場合は、従量課金制サブスクリプションまたは他の購入オプションを検討してください。 [Azure 無料アカウント](https://azure.microsoft.com/free/)を使用している場合は、使用できる Azure コンピューティング コアの数に制限があります。

- Azure サブスクリプションの (リージョンごとの) コア クォータと NC、NCv2、NCv3、ND、NDv2、NV、または NVv2 コアの個別のクォータを増やす必要が生じる場合があります。 クォータを増やすためのリクエストは、[オンライン カスタマー サポートに申請](/../azure-supportability/how-to-create-azure-support-request.md) (無料) してください。 既定の制限は、サブスクリプション カテゴリによって異なる場合があります。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [コンピューティングの最適化](sizes-compute.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。