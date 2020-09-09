---
title: Ev4 および Esv4 シリーズ - Azure Virtual Machines
description: Ev4 および Esv4 シリーズ VM の仕様。
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: b8a6e1013c131778bacb11bd885b9280fb6c6077
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182175"
---
# <a name="ev4-and-esv4-series"></a>Ev4 および Esv4 シリーズ

Ev4 および Esv4 シリーズは、ハイパースレッド構成の Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) プロセッサで実行されます。これは、メモリを集中的に使用するさまざまなエンタープライズ アプリケーションに最適であり、最大 504 GiB の RAM を搭載します。 これは、持続する 3.4 GHz の全コア ターボ クロック速度を特徴としています。

> [!NOTE]
> よく寄せられる質問については、[ローカル一時ディスクを持たない Azure VM のサイズ](azure-vms-no-temp-disk.md)に関する記事をご覧ください。

## <a name="ev4-series"></a>Ev4 シリーズ

Ev4 シリーズのサイズは、Intel Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Ev4 シリーズのインスタンスは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。 Ev4 シリーズ VM は Intel&reg; ハイパースレッディング テクノロジを備えています。

リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。 Premium Storage ディスクを使用するには、Esv4 サイズを使用してください。 Esv4 サイズの料金および課金の計算方法は、Ev4 シリーズと同じです。

ACU: 195 - 210

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

ライブ マイグレーション: サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | リモート ストレージのみ | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | リモート ストレージのみ | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | リモート ストレージのみ | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | リモート ストレージのみ | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | リモート ストレージのみ | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | リモート ストレージのみ | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | リモート ストレージのみ | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | リモート ストレージのみ | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 シリーズ

Esv4 シリーズのサイズは、Intel&reg; Xeon&reg; Platinum 8272CL (Cascade Lake) 上で実行されます。 Esv4 シリーズのインスタンスは、メモリへの負荷が高いエンタープライズ アプリケーションに最適です。 Esv4 シリーズ VM は Intel&reg; ハイパースレッディング テクノロジを備えています。 リモート データ ディスク ストレージは、仮想マシンとは別に課金されます。

ACU: 195-210

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション:サポートされています

メモリ保持更新: サポートされています

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | リモート ストレージのみ | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | リモート ストレージのみ | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | リモート ストレージのみ | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | リモート ストレージのみ | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | リモート ストレージのみ | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | リモート ストレージのみ | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | リモート ストレージのみ | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| リモート ストレージのみ | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [コア数を制限したサイズも提供しています](./constrained-vcpu.md)。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール: [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報: [ディスクの種類](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
