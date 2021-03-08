---
title: NP シリーズ - Azure Virtual Machines
description: NP シリーズ VM の仕様。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 8d350e248d09f29496f4461b902eba96d8375732
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668284"
---
# <a name="np-series-preview"></a>NP シリーズ (プレビュー)

NP シリーズの仮想マシンでは、機械学習の推論、ビデオのトランスコード、データベースの検索と分析など、ワークロードを加速させるために [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA を利用しています。 また、NP シリーズの VM は Intel Xeon 8171M (Skylake) の CPU を搭載し、全コア ターボ クロック速度は 3.2 GHz です。


[Premium Storage](premium-storage-performance.md): サポートされています<br>
[Premium Storage キャッシュ](premium-storage-performance.md): サポートされています<br>
[ライブ マイグレーション](maintenance-and-updates.md): サポートされていません<br>
[メモリ保持更新](maintenance-and-updates.md): サポートされていません<br>
VM 世代サポート: 第 1 世代<br>
[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-cli.md):サポートされています<br>
[エフェメラル OS ディスク](ephemeral-os-disks.md):サポートされていません <br>
<br>

| サイズ | vCPU | メモリ:GiB | 一時ストレージ (SSD) GiB | FPGA | FPGA メモリ:GiB | 最大データ ディスク数 | 最大 NIC 数/想定ネットワーク帯域幅 (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1/7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2/15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4/30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー
サポートされているオペレーティングシステムの全一覧については、[Xilinx Runtime (XRT) のリリース ノート](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)を参照してください。

プレビュー プログラム中に、Microsoft Azure エンジニアリング チームが、ドライバーのインストールに関する具体的な手順を共有します。

## <a name="other-sizes"></a>その他のサイズ

- [汎用](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [ストレージの最適化](sizes-storage.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [旧世代](sizes-previous-gen.md)

## <a name="next-steps"></a>次のステップ

[Azure コンピューティング ユニット (ACU)](acu.md) を確認することで、Azure SKU 全体の処理性能を比較できます。
