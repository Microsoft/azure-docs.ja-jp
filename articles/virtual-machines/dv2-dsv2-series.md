---
title: Dv2 および Dsv2 シリーズ - Azure Virtual Machines
description: Dv2 と Dsv2 シリーズ VM の仕様。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cdfb8e333e3834adf242029e9e00eac9cf9abebe
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492412"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 および DSv2 シリーズ

オリジナルの D シリーズの後継である Dv2 および Dsv2 シリーズは、より強力な CPU と最適な CPU 対メモリ構成を備えているため、ほとんどの運用環境のワークロードに適しています。 Dv2 シリーズは D シリーズよりも、およそ 35% 高速です。 Dv2 シリーズは、Intel Turbo Boost Technology 2.0 を備えた、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのプロセッサ上で実行されています。 Dv2 シリーズのメモリ構成とディスク構成は D シリーズと同じです。

## <a name="dv2-series"></a>Dv2 シリーズ

Dv2 シリーズのサイズは、Intel Turbo Boost Technology 2.0 を備えた、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのプロセッサ上で実行されます。

ACU: 210-250

Premium Storage: サポートされていません

Premium Storage キャッシュ:サポートされていません

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 一時ストレージの最大スループット: IOPS/読み取り MBps/書き込み MBps | 最大データ ディスク数 | スループット: IOPS | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4 x 500  | 2/750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8 x 500  | 2/1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16 x 500 | 4/3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32 x 500 | 8/6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64 x 500 | 8/12000 |

## <a name="dsv2-series"></a>DSv2 シリーズ

DSv2 シリーズのサイズは、Intel Turbo Boost Technology 2.0 を備えた、Intel® Xeon® 8171M 2.1 GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell)、Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) のいずれかのプロセッサ上で実行され、Premium Storage が使用されます。

ACU: 210-250

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

| Size | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | 最大データ ディスク数 | キャッシュが有効な場合の一時ストレージの最大スループット: IOPS/MBps (キャッシュ サイズは GiB 単位) | キャッシュが無効な場合の最大ディスク スループット: IOPS/MBps | 最大 NIC 数/想定ネットワーク帯域幅 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2/750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2/1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4/3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8/6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8/12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。