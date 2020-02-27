---
title: Azure コンピューティング ユニットの概要 | Microsoft Docs
description: Azure コンピューティング ユニットの概念の概要。 ACU は、Azure SKU 間の CPU パフォーマンスを比較する方法を提供します。
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 8e406aea10c15a97a7e66bbdc3c1d889c5bd2c52
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492648"
---
# <a name="azure-compute-unit-acu"></a>Azure コンピューティング ユニット (ACU)

Azure コンピューティング ユニット (ACU) の概念は、Azure SKU 間で計算 (CPU) パフォーマンスを比較する手段を提供します。 これは、パフォーマンス ニーズを満たす可能性が最も高い SKU を簡単に見つけるのに役立ちます。 現在、ACU は小さい (Standard_A1) VM を 100 として標準化されており、他のすべての SKU についてはその SKU が標準ベンチマークをそれよりどれくらい速く実行できるかが表されます。

> [!IMPORTANT]
> ACU はガイドラインに過ぎません。 ワークロードの結果は異なる場合があります。
<br>

| SKU ファミリ | ACU \ vCPU | vCPU: コア |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 - A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 - A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 - A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 - A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 - A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 - D14](sizes-previous-gen.md) |160 から 250 | 1:1 |
| [D1_v2 - D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 - DS14](sizes-previous-gen.md) |160 から 250 | 1:1 |
| [DS1_v2 - DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 - 190* | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 - 190* | 2:1\*\*\* |
| [F2s_v2 - F72s_v2](fsv2-series.md) |195 から 210* | 2:1\*\*\* |
| [F1 - F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s - F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 - G5](sizes-previous-gen.md) |180 ～ 240* | 1:1 |
| [GS1 - GS5](sizes-previous-gen.md) |180 ～ 240* | 1:1 |
| [H](h-series.md) |290 ～ 300* | 1:1 |
| [HB](hb-series.md) |199 - 216** | 1:1 |
| [HC](hc-series.md) |297 - 315* | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 ～ 240* | 1:1 |
| [L8s_v2 - L80s_v2](lsv2-series.md) |150 - 175** | 2:1 |
| [M](m-series.md) | 160 から 180 | 2:1\*\*\* |

\* ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを向上させます。  パフォーマンス向上の量は、VM のサイズ、ワークロードのほか、同じホストで実行されている他のワークロードによって変動する場合があります。
** ACU は、AMD® Boost テクノロジを使用して CPU 周波数を上げ、パフォーマンスを向上させます。  パフォーマンス向上の量は、VM のサイズ、ワークロードのほか、同じホストで実行されている他のワークロードによって変動する場合があります。
*** ハイパースレッド化されており、入れ子になった仮想化を実行できます。

さまざまなサイズの詳細に関するリンクは次のとおりです。

- [汎用目的](sizes-general.md)
- [メモリの最適化](sizes-memory.md)
- [コンピューティングの最適化](sizes-compute.md)
- [GPU の最適化](sizes-gpu.md)
- [ハイ パフォーマンス コンピューティング](sizes-hpc.md)
- [ストレージの最適化](sizes-storage.md)