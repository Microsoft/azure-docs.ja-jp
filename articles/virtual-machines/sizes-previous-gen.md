---
title: Azure Linux VM のサイズ - 旧世代 | Microsoft Docs
description: Azure の Linux 仮想マシンに使用できる旧世代のサイズを一覧表示します。 このシリーズのストレージのスループットとネットワーク帯域幅に加え、vCPU、データ ディスク、NIC の数に関する情報を一覧表示します。
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: cd45d38759bcf41307ba42d68d504266719579d2
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492512"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>旧世代の仮想マシンのサイズ

このセクションでは、仮想マシンのサイズの前の世代の情報を提供します。 これらのサイズも、使用できますが、より新しい世代が使用可能です。

## <a name="f-series"></a>F シリーズ

F シリーズは 2.4 GHz Intel Xeon® E5-2673 v3 (Haswell) プロセッサを基盤としています。このプロセッサは Intel Turbo Boost Technology 2.0 によって最高 3.1 GHz のクロック速度を達成できます。 これは、Dv2 シリーズ VM と同じ CPU パフォーマンスです。  

F シリーズ VM は、より高速の CPU を必要としつつも、vCPU あたりのメモリや一時ストレージについてはそれほど多くを要求しないワークロードに最適です。  F シリーズのもたらす価値は、分析、ゲーム サーバー、Web サーバー、およびバッチ処理などのワークロードに恩恵を与えます。

ACU: 210 から 250

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>Fs シリーズ <sup>1</sup>

Fs シリーズには、Premium Storage に加え、F シリーズのすべての利点が備わっています。

ACU: 210 から 250

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

MBps = 10^6 バイト/秒、GiB = 1024^3 バイト。

<sup>1</sup> Fs シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[Windows](windows/premium-storage-performance.md) 用または [Linux](linux/premium-storage-performance.md) 用のハイ パフォーマンスのための設計に関する記事を参照してください。  

## <a name="nvv2-series"></a>NVv2 シリーズ

**新しいサイズ (推奨)** :[NVv3 シリーズ](nvv3-series.md)

NVv2 シリーズの仮想マシンは、Intel Broadwell CPU を使用した [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU および NVIDIA GRID テクノロジを搭載しています。 これらの仮想マシンは、お客様がデータを視覚化したり、表示する結果をシミュレートしたり、CAD で作業したり、コンテンツをレンダリングおよびストリーミングしたりしたいと考える、GPU で高速化されたグラフィックス アプリケーションや仮想デスクトップを対象にしています。 さらに、これらの仮想マシンは、エンコーディングやレンダリングなどの単精度のワークロードを実行できます。 NVv2 仮想マシンは Premium Storage をサポートし、以前の NV シリーズと比較して 2 倍のシステム メモリ (RAM) を備えています。  

NVv2 インスタンス内の各 GPU には GRID ライセンスが付属しています。 このライセンスでは柔軟性が確保され、NV インスタンスを仮想ワークステーションとして 1 人のユーザーに対して使用したり、仮想アプリケーションのシナリオで 25 人のユーザーが同時に VM に接続したりできます。

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数 | 仮想ワークステーション | 仮想アプリケーション |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="older-generations-of-virtual-machine-sizes"></a>旧世代の仮想マシンのサイズ

このセクションでは、より古い世代の仮想マシンのサイズの情報が提供されます。 これらのサイズは引き続きサポートされますが、追加容量は得られません。 一般提供されるより新しいまたは代替のサイズがあります。 ニーズに最も合う VM サイズを選択するには、「[Azure の Linux 仮想マシンのサイズ](linux/sizes.md)」を参照してください。  

Windows VM のサイズ変更の詳細については、[Linux VM のサイズ変更](linux/change-vm-size.md)に関するページを参照してください。  

<br>

### <a name="basic-a"></a>Basic A  

**新しいサイズ (推奨)** :[Av2 シリーズ](av2-series.md)

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

Basic レベルのサイズは主に、負荷分散や自動スケール、メモリ消費量の多い仮想マシンのいずれも必要としない用途 (開発ワークロードなど) 向けです。

| サイズ – サイズ\名前 | vCPU | メモリ|NIC (最大)| 一時ディスクの最大サイズ | 最大 データ ディスク数 (各ディスク 1,023 GB)| 最大 IOPS (各ディスク 300) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 x 300  |
| A1\Basic_A1 | 1 | 1.75 GB | 2 | 40 GB  | 2  | 2 x 300  |
| A2\Basic_A2 | 2 | 3.5 GB  | 2 | 60 GB  | 4  | 4 x 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 x 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 x 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 ～ A4 (CLI と PowerShell の使用)

クラシック デプロイ モデルでは、一部の VM サイズが CLI と PowerShell で若干異なります。

* Standard_A0: ExtraSmall
* Standard_A1: Small
* Standard_A2: Medium
* Standard_A3: Large
* Standard_A4: ExtraLarge

### <a name="a-series"></a>A シリーズ  

**新しいサイズ (推奨)** :[Av2 シリーズ](av2-series.md)

ACU: 50-100

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (HDD):GiB | 最大データ ディスク数 | データ ディスクの最大スループット:IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> | 1 | 0.768 | 20 | 1 | 1x500 | 2/100 |
| Standard_A1 | 1 | 1.75 | 70  | 2  | 2x500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 x 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 x 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 x 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 x 500 | 4/2000 |

<sup>1</sup> A0 サイズは、物理ハードウェアでオーバーサブスクライブされます。 この特定のサイズの場合のみ、他の顧客デプロイメントは、実行中のワークロードのパフォーマンスに影響することがあります。 下に、予想される基準として相対パフォーマンスを示していますが、約 15% の変動の可能性があります。

<br>

### <a name="a-series---compute-intensive-instances"></a>A シリーズ - コンピューティング集中型インスタンス  

**新しいサイズ (推奨)** :[Av2 シリーズ](av2-series.md)

ACU: 225

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

A8 ～ A11 と H シリーズのサイズは、 *コンピューティング集中型インスタンス*とも呼ばれます。 これらのサイズを実行するハードウェアは、ハイ パフォーマンス コンピューティング (HPC) クラスター アプリケーション、モデリング、シミュレーションなど、コンピューティング集中型およびネットワーク集中型アプリケーション用に設計および最適化されています。 A8 ～ A11 シリーズは Intel Xeon E5-2670 @ 2.6 GHZ を使用し、H シリーズは Intel Xeon E5-2667 v3 @ 3.2 GHz を使用します。  

| Size | vCPU | メモリ:GiB | 一時ストレージ (HDD):GiB | 最大データ ディスク数 | データ ディスクの最大スループット:IOPS | 最大 NIC 数|
|---|---|---|---|---|---|---|
| Standard_A8&nbsp;<sup>1</sup> | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A9&nbsp;<sup>1</sup> | 16 | 112 | 382 | 64 | 64 x 500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 x 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64 x 500 | 4 |

<sup>1</sup> MPI アプリケーションの場合、専用の RDMA バックエンド ネットワークが FDR InfiniBand ネットワークによって有効になり、超低待機時間と高帯域幅を実現します。  

<br>

### <a name="d-series"></a>D シリーズ  

**新しいサイズ (推奨)** :[Dv3 シリーズ](dv3-dsv3-series.md)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D シリーズ - メモリ最適化済み  

**新しいサイズ (推奨)** :[Dv3 シリーズ](dv3-dsv3-series.md)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series"></a>DS シリーズ  

**新しいサイズ (推奨)** :[Dsv3 シリーズ](dv3-dsv3-series.md)

ACU: 160 から 250 <sup>1</sup>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS シリーズ - メモリ最適化済み  

**新しいサイズ (推奨)** :[Dsv3 シリーズ](dv3-dsv3-series.md)

ACU: 160 から 250 <sup>1、2</sup>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> DS シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。  詳細については、[Windows](windows/premium-storage-performance.md) 用または [Linux](linux/premium-storage-performance.md) 用のハイ パフォーマンスのための設計に関する記事を参照してください。
<sup>2</sup> VM ファミリは、次の CPU のいずれかで実行できます。2.2 GHz Intel Xeon® E5-2660 v2、2.4 GHz Intel Xeon® E5-2673 v3 (Haswell)、または 2.3 GHz Intel XEON® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls シリーズ

Ls シリーズでは、[Intel® Xeon® プロセッサ E5 v3 ファミリ](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html)を使用し、最大 32 個の vCPU を提供します。 Ls シリーズは、G/GS シリーズと同じ CPU パフォーマンスであり、vCPU あたり 8 GiB のメモリを搭載しています。

Ls シリーズでは、持続性のあるデータ ディスクで実現できる IOPS を向上させるためのローカル キャッシュの作成はサポートされません。 Ls シリーズの VM は、ローカル ディスクの高スループットと IOPS によって、1 つの VM で障害が発生した場合に複数の VM にデータをレプリケートして永続性を実現する Apache Cassandra や MongoDB などの NoSQL ストアにとって最適なものになっています。

ACU: 180 から 240

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ (GiB) | 一時ストレージ (GiB) | 最大データ ディスク数 | 一時ストレージの最大スループット (IOPS/MBps) | キャッシュ不使用時の最大ディスク スループット (IOPS/MBps) | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s&nbsp;<sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Ls シリーズの VM で実現可能な最大ディスク スループットは、接続されたディスクの数、サイズ、ストライピングによって制限される場合があります。 詳細については、[Windows](windows/premium-storage-performance.md) 用または [Linux](linux/premium-storage-performance.md) 用のハイ パフォーマンスのための設計に関する記事を参照してください。

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

### <a name="gs-series"></a>GS シリーズ

ACU: 180 から 240 <sup>1</sup>

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4&nbsp;<sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5&nbsp;<sup>2、&nbsp;3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> GS シリーズの VM で実現可能な最大ディスク スループット (IOPS または MBps) は、接続ディスクの数、サイズ、ストライピングによって制限される場合があります。 詳細については、[Windows](windows/premium-storage-performance.md) 用または [Linux](linux/premium-storage-performance.md) 用のハイ パフォーマンスのための設計に関する記事を参照してください。

<sup>2</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

<sup>3</sup> コア数を制限したサイズも提供しています。

<br>

### <a name="g-series"></a>G シリーズ

ACU: 180 から 240

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数/スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5&nbsp;<sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。
<br>

## <a name="other-sizes"></a>その他のサイズ

* [汎用](sizes-general.md)
* [コンピューティングの最適化](sizes-compute.md)
* [メモリの最適化](sizes-memory.md)
* [ストレージの最適化](sizes-storage.md)
* [GPU](sizes-gpu.md)
* [ハイ パフォーマンス コンピューティング](sizes-hpc.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。