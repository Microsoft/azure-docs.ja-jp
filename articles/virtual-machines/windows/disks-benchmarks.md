---
title: Azure Disk Storage 上のアプリケーションのベンチマーク
description: Azure 上でアプリケーションのベンチマークを実行するプロセスについて説明します。
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: d8bf90f4ecea992b3212200905cd9204e4079b1b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683965"
---
# <a name="benchmarking-a-disk"></a>ディスクのベンチマーク

ベンチマークは、アプリケーションのさまざまなワークロードをシミュレートし、ワークロードごとにアプリケーションのパフォーマンスを測定するプロセスです。 [高パフォーマンス用の設計に関する記事](../premium-storage-performance.md)に記載されている手順を使用して、アプリケーションのパフォーマンス要件を収集しました。 アプリケーションをホストする VM でベンチマーク ツールを実行することで、アプリケーションが Premium Storage を使用して実現できるパフォーマンス レベルを確認できます。 この記事では、Azure Premium Storage ディスクと共にプロビジョニングされた Standard DS14 VM のベンチマークの例を示します。

一般的なベンチマーク ツールである Iometer (Windows) と FIO (Linux) を使用しました。 これらのツールは、ワークロードのような実稼働をシミュレートする複数のスレッドを起動し、システム パフォーマンスを測定します。 これらのツールを使用して、通常はアプリケーションに合わせて変更できないブロック サイズやキューの深さなどのパラメーターを構成することもできます。 これにより、さまざま種類のアプリケーション ワークロードに対応するために、Premium ディスクと共にプロビジョニングされた高スケール VM でパフォーマンスを最大限に引き上げる柔軟性が得られます。 各ベンチマーク ツールの詳細については、「[Iometer](http://www.iometer.org/)」と「[fio](http://freecode.com/projects/fio)」をご覧ください。

以下の例に従うために、Standard DS14 VM を作成し、11 個の Premium Storage ディスクを VM に接続します。 11 個のディスクのうち、10 個のディスクのホスト キャッシュを "None" に構成し、これらのディスクを NoCacheWrites というボリュームにストライピングします。 残りのディスクでは、ホストキャッシュを "ReadOnly" に構成し、このディスクを使用して CacheReads というボリュームを作成します。 この設定を使用して、Standard DS14 VM から最大の読み取り/書き込みパフォーマンスが得られます。 Premium SSD を使用して DS14 VM を作成する詳細な手順については、[高パフォーマンス用の設計](../premium-storage-performance.md)に関する記事を参照してください。

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>次のステップ

[ハイ パフォーマンスのための設計](../premium-storage-performance.md)に関する記事に進んでください。

この記事では、プロトタイプについて、既存のアプリケーションと同様のチェックリストを作成します。 ベンチマーク ツールを使用して、ワークロードをシミュレートし、プロトタイプ アプリケーションでパフォーマンスを測定します。 こうすることで、どのディスク オファリングがアプリケーションのパフォーマンス要件に合っているか、上回っているかを判断できます。 その後、実稼働アプリケーションにも同じガイドラインを実装できます。