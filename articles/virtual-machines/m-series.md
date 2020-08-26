---
title: M シリーズ - Azure Virtual Machines
description: M シリーズ VM の仕様。
author: ayshakeen
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: jushiman
ms.openlocfilehash: 79d0386e12b38eb6590b68ff959911ddc0a0ab5f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505548"
---
# <a name="m-series"></a>M シリーズ

M シリーズは、多数の vCPU (最大 128 個の vCPU) と大量のメモリ (最大 3.8 TiB) を提供します。 このシリーズも非常に大規模なデータベースや他のアプリケーションに最適であり、多数の vCPU と大量のメモリによるメリットを活用できます。 M シリーズのサイズは、Intel&reg; Xeon&reg; CPU E7-8890 v3 @ 2.50 GHz と Intel&reg; Xeon&reg; Platinum 8280M (Cascade Lake) の両方に対応しています。

M シリーズの VM は、Intel&reg; ハイパー スレッディング テクノロジを利用しています。

ACU: 160-180

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション: サポートされていません

メモリ保持更新: サポートされていません

書き込みアクセラレータ:[サポートされています](./how-to-enable-write-accelerator.md)

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合および一時ストレージの最大スループットIOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数|必要なネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M8ms                    | 8   | 218.75 | 256   | 8  | 10000/100 (793)     | 5000/125   | 4|2000  |
| Standard_M16ms                   | 16  | 437.5  | 512   | 16 | 20000/200 (1587)    | 10000/250  | 8|4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M32ms                   | 32  | 875    | 1024  | 32 | 40000/400 (3174)    | 20000/500  | 8|8000  |
| Standard_M64s <sup>1</sup>       | 64  | 1024   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ls <sup>1</sup>      | 64  | 512    | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M64ms <sup>1</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 (6348)    | 40000/1000 | 8|16000 |
| Standard_M128s <sup>1</sup>    | 128 | 2048   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M128ms <sup>1、2</sup>   | 128 | 3892   | 4096  | 64 | 160000/1600 (12696) | 80000/2000 | 8|30000 |
| Standard_M64 <sup>1</sup>        | 64  | 1024   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M64m <sup>1</sup>       | 64  | 1792   | 7168  | 64 | 80000/800 (1228)    | 40000/1000 | 8|16000 |
| Standard_M128 <sup>1</sup>     | 128 | 2048   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |
| Standard_M128m <sup>1</sup>    | 128 | 3892   | 14336 | 64 | 250000/1600 (2456)  | 80000/2000 | 8|32000 |

<sup>1</sup> 64 個を超える vCPU では、Windows Server 2016、Ubuntu 16.04 LTS、SLES 12 SP2、および LIS 4.2.1 が付属する Red Hat Enterprise Linux、CentOS 7.3、または Oracle Linux 7.3 の、サポートされているゲスト OS のいずれかが必要です。

<sup>2</sup> インスタンスは、単一の顧客専用のハードウェアに分離されます。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>その他のサイズと情報

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

料金計算ツール:[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)

ディスクの種類の詳細情報:[ディスクの種類](./linux/disks-types.md#ultra-disk)


## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。