---
title: Load Balancer 用の Azure CLI サンプル
titleSuffix: Azure Load Balancer
description: Azure CLI のサンプル
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499349"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Load Balancer 用の Azure CLI サンプル

次の表には、Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

* [高可用性を確保するための、VM へのトラフィックの負荷分散](./scripts/load-balancer-linux-cli-sample-nlb.md)

  可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。

* [可用性ゾーン間での VM の負荷分散](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  リージョン内の異なる可用性ゾーンに 3 つの VM と、ゾーン冗長フロント エンド IP アドレスを持つ Standard Load Balancer を作成します。 このロード バランサー構成は、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護することに役立ちます。

* [特定の可用性ゾーン内での VM の負荷分散](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  3 つの VM と、指定したリージョンの 1 つのゾーン内でデータ パスとリソースを合わせるのに役立つゾーンのフロント エンド IP を持つ Standard Load Balancer を作成します。

* [VM 上の複数の Web サイトの負荷分散](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Azure 可用性セットに接続され、Azure Load Balancer 経由でアクセス可能な、複数の IP 構成を持つ仮想マシンを 2 つ作成します。
