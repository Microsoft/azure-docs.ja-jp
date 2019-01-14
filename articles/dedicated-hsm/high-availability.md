---
title: 高可用性 - Azure の専用 HSM | Microsoft Docs
description: Azure の専用 HSM の高可用性の例と基本的な考慮事項
services: dedicated-hsm
author: barclayn
manager: mbaldwin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 991482d3d6be4d09b37014e5bb03708987e0f74e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078176"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicated HSM の高可用性

Azure Dedicated HSM は、Microsoft の高可用データセンターによって支えられています。 ただし、どのような高可用データセンターでも、局地的な障害や、極端な状況ではリージョン レベルの障害に対して脆弱です。 Microsoft はリージョン内の異なるデータセンターに HSM デバイスをデプロイすることで、複数のデバイスをプロビジョニングする際にデバイスが単一のラックを共有しないようにしています。 1 つのリージョン内のデータセンター全体でこのような HSM をペアリングすることで、さらに高いレベルの高可用性を達成できます。 また、ディザスター リカバリーの状況でリージョンのフェールオーバーに対応するために、複数のリージョンにまたがってデバイスをペアリングすることもできます。 このような多層の高可用性構成では、どのようなデバイスの障害も自動的に対処され、アプリケーションの機能を維持することができます。 また、すべてのデータセンターには予備のデバイスとコンポーネントがオンサイトに用意されているため、デバイスが故障しても適切なタイミングで交換できます。

## <a name="high-availability-example"></a>高可用性の例

ソフトウェア レベルで高可用性を実現するための HSM デバイスの構成方法については、「Gemalto Luna Network HSM Administration Guide」(Gemalto Luna Network HSM 管理ガイド) を参照してください。 このドキュメントは、[Gemalto カスタマー サポート ポータル](https://supportportal.gemalto.com/csm/)で入手できます。

次の図は、高可用アーキテクチャを示しています。 リージョン内の複数のデバイスと、別のリージョン内のペアリングされた複数のデバイスを使用しています。 このアーキテクチャは、4 つ以上の HSM デバイスと仮想ネットワーク コンポーネントを使用しています。

![高可用性の図](media/high-availability/high-availability.png)

## <a name="next-steps"></a>次の手順

デバイスのプロビジョニングやアプリケーションの設計、アプリケーションのデプロイ前に、高可用性やセキュリティなど、サービスのすべての主要概念を十分に理解しておくことをお勧めします。
さらに詳しい概念レベルのトピックを次に示します。

* [デプロイ アーキテクチャ](deployment-architecture.md)
* [物理的なセキュリティ](physical-security.md)
* [ネットワーク](networking.md)
* [サポート可能性](supportability.md)
* [監視](monitoring.md)

高可用性に対応する HSM デバイスの構成の詳細については、Gemalto カスタマー サポート ポータルの「Administrator Guides」(管理者ガイド) のセクション 6 を参照してください。
