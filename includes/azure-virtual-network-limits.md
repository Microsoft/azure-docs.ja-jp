---
title: インクルード ファイル
description: インクルード ファイル
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: b963cc459094e823499e962736550dcea2345ba2
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854113"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>ネットワークの制限 - Azure Resource Manager
次の制限は、サブスクリプションごとにリージョン単位で **Azure Resource Manager** デプロイ モデルを通して管理されるネットワーク リソースにのみ適用されます。 [サブスクリプションの上限に対する現在のリソース使用状況の確認](../articles/networking/check-usage-against-limits.md)に関するページを参照してください。

> [!NOTE]
> 最近、すべての既定の制限がそれぞれの上限まで引き上げられました。 上限列が存在しない場合、記載されているリソースに調整可能な制限がないことを意味します。 過去にこれらの制限をサポートにより引き上げていて、次の表のように更新された制限が表示されない場合は、[オンライン カスタマー サポートに申請 (無料)](../articles/azure-resource-manager/templates/error-resource-quota.md) できます

| リソース | 制限 | 
| --- | --- |
| 仮想ネットワーク |1,000 |
| 仮想ネットワークあたりのサブネット数 |3,000 |
| 仮想ネットワークあたりの VNet ピアリング |500 |
| [仮想ネットワークあたりの仮想ネットワーク ゲートウェイ (VPN ゲートウェイ)](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [仮想ネットワークあたりの仮想ネットワーク ゲートウェイ (ExpressRoute ゲートウェイ)](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| 仮想ネットワークあたりの DNS サーバー数 |20 |
| 仮想ネットワークごとのプライベート IP アドレス |65,536 |
| ネットワーク インターフェイスごとのプライベート IP アドレス |256 |
| 仮想マシンごとのプライベート IP アドレス |256 |
| ネットワーク インターフェイスごとのパブリック IP アドレス |256 |
| 仮想マシンごとのパブリック IP アドレス |256 |
| [仮想マシンまたはロール インスタンスの NIC ごとの同時 TCP または UDP フロー数](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| ネットワーク インターフェイス カード |65,536 |
| ネットワーク セキュリティ グループ |5,000 |
| NSG あたりの NSG ルール数 |1,000 |
| セキュリティ グループで送信元または送信先に指定された IP アドレスおよび IP アドレス範囲 |4,000 |
| アプリケーション セキュリティ グループ |3,000 |
| IP 構成ごと、NIC ごとのアプリケーション セキュリティ グループ |20 |
| アプリケーション セキュリティ グループごとのIP 構成 |4,000 |
| ネットワーク セキュリティ グループのすべてのセキュリティ規則内で指定できるアプリケーション セキュリティ グループ |100 |
| ユーザー定義ルート テーブル |200 |
| ルート テーブルあたりのユーザー定義ルート |400 |
| Azure VPN Gateway ごとのポイント対サイト ルート証明書 |20 |
| 仮想ネットワーク TAP |100 |
| 仮想ネットワーク TAP ごとのネットワーク インターフェイス TAP 構成数 |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>パブリック IP アドレスの制限
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| パブリック IP アドレス<sup>1</sup> | Basic で 10。 | サポートにお問い合せください。 |
| 静的パブリック IP アドレス<sup>1</sup> | Basic で 10。 | サポートにお問い合せください。 |
| 標準パブリック IP アドレス<sup>1</sup> | 10 | サポートにお問い合せください。 |
| パブリック IP プレフィックス | サブスクリプション内の標準パブリック IP の数によって制限されます | サポートにお問い合せください。 |
| パブリック IP プレフィックス長 | /28 | サポートにお問い合せください。 |

<sup>1</sup> パブリック IP アドレスに対する既定の制限は、無料試用版や従量課金制、CSP などプラン カテゴリの種類によって異なります。 たとえば、Enterprise Agreement サブスクリプションの既定値は 1000 です。

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Load Balancer の制限
次の制限は、サブスクリプションごとにリージョン単位で Azure Resource Manager デプロイメント モデルを通して管理されるネットワーク リソースにのみ適用されます。 [サブスクリプションの上限に対する現在のリソース使用状況の確認](../articles/networking/check-usage-against-limits.md)に関するページを参照してください。

**Standard Load Balancer**

| リソース                                | 制限         |
|-----------------------------------------|-------------------------------|
| ロード バランサー                          | 1,000                         |
| リソースあたりのルール数                      | 1,500                         |
| NIC あたりのルール数 (1 つの NIC のすべての IP にわたる) | 該当なし                           |
| フロントエンド IP 構成              | 600                           |
| バックエンド プールのサイズ                       | 1,000 IP 構成、単一仮想ネットワーク |
| ロード バランサーあたりのバックエンド リソース数 <sup>1<sup> | 150                   |
| 高可用性ポート                 | 内部フロント エンドごとに 1 個       |
| Load Balancer あたりのアウトバウンド規則数        | 600                           |
| [TCP アイドル タイムアウト](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 分 / 30 分          |

<sup>1</sup>制限は、最大 150 のリソース (スタンドアロンの仮想マシン リソース、可用性セット リソース、仮想マシン スケール セット配置グループの任意の組み合わせ) です。

**Basic Load Balancer**

| リソース                                | 制限        |
|-----------------------------------------|------------------------------|
| ロード バランサー                          | 1,000                        |
| リソースあたりのルール数                      | 250                          |
| NIC あたりのルール数 (1 つの NIC のすべての IP にわたる) | 該当なし                          |
| フロントエンド IP 構成              | 200                          |
| バックエンド プールのサイズ                       | 300 IP 構成、単一の可用性セット |
| Load Balancer あたりの可用性セット数     | 150                          |

<a name="virtual-networking-limits-classic"></a>次の制限は、サブスクリプションごとに**クラシック** デプロイ モデルを通じて管理されるネットワーク リソースのみに適用されます。 [サブスクリプションの上限に対する現在のリソース使用状況の確認](../articles/networking/check-usage-against-limits.md)に関するページを参照してください。

| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| 仮想ネットワーク |100 |100 |
| ローカル ネットワーク サイト |20 |50 |
| 仮想ネットワークあたりの DNS サーバー数 |20 |20 |
| 仮想ネットワークごとのプライベート IP アドレス |4,096 |4,096 |
| 仮想マシンまたはロール インスタンスの NIC ごとの同時 TCP または UDP フロー数 |2 つ以上の NIC で 500,000 (最大 1,000,000)。 |2 つ以上の NIC で 500,000 (最大 1,000,000)。 |
| ネットワーク セキュリティ グループ (NSG) |200 |200 |
| NSG あたりの NSG ルール数 |200 |1,000 |
| ユーザー定義ルート テーブル |200 |200 |
| ルート テーブルあたりのユーザー定義ルート |400 |400 |
| パブリック IP アドレス (動的) |500 |500 |
| 予約済みパブリック IP アドレス |500 |500 |
| デプロイあたりのパブリック VIP |5 |サポートにお問い合せください |
| 展開あたりのプライベート VIP (内部負荷分散) |1 |1 |
| エンドポイント アクセス制御リスト (ACL) |50 |50 |