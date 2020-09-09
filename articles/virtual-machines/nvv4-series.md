---
title: NVv4 シリーズ
description: NVv4 シリーズ VM の仕様。
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 9b841b6422a4314b43a594cb0b22040f884228eb
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543896"
---
# <a name="nvv4-series"></a>NVv4 シリーズ 

NVv4 シリーズ仮想マシンは [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU および AMD EPYC 7V12 (Rome) CPU を搭載しています。 NVv4 シリーズの場合、Azure では、部分的な GPU を備えた仮想マシンを導入しています。 16 GiB フレーム バッファーを備えた完全な GPU に対して 2 GiB フレーム バッファーを備えた 8 分の 1 の GPU を下限として、GPU で強化されたグラフィックス アプリケーションと仮想デスクトップに対して適正なサイズの仮想マシンを選択します。 NVv4 仮想マシンでは現在、Windows ゲスト オペレーティング システムのみがサポートされています。

<br>

ACU: 230-260

Premium Storage: サポートされています

Premium Storage キャッシュ:サポートされています

ライブ マイグレーション: サポートされていません

メモリ保持更新: サポートされていません

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | GPU | GPU メモリ: GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2/1,000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4/2,000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8/4,000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8/8,000 |

<sup>1</sup> NVv4 シリーズ VM では、AMD 同時実行マルチスレッド技術を考慮しています

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

Windows を実行している Azure NVv4 シリーズ VM の GPU 機能を利用するには、AMD GPU ドライバーがインストールされている必要があります。

AMD GPU ドライバーを手動でインストールする場合、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順については、[Windows 用 N シリーズ AMD GPU ドライバーのセットアップ](./windows/n-series-amd-driver-setup.md)に関する記事を参照してください。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
