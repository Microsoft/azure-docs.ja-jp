---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010797"
---
次の表に、PolicyBased および RouteBased の VPN Gateway の要件を示します。 この表は、リソース マネージャーとクラシック デプロイ モデルの両方に適用されます。 クラシック モデルでは、PolicyBased VPN Gateway は静的ゲートウェイと同じです。また、RouteBased のゲートウェイは動的ゲートウェイと同じです。

|  | **PolicyBased の Basic VPN Gateway** | **RouteBased の Basic VPN Gateway** | **RouteBased の Standard VPN Gateway** | **RouteBased の HighPerformance VPN Gateway** |
| --- | --- | --- | --- | --- |
| **サイト間接続 (S2S)** |PolicyBased VPN の構成 |RouteBased VPN の構成 |RouteBased VPN の構成 |RouteBased VPN の構成 |
| **ポイント対サイト接続 (P2S**) |サポートされていません |サポートされています (S2S と共存可能) |サポートされています (S2S と共存可能) |サポートされています (S2S と共存可能) |
| **認証方法** |事前共有キー |S2S 接続用の事前共有キー、P2S 接続用の証明書 |S2S 接続用の事前共有キー、P2S 接続用の証明書 |S2S 接続用の事前共有キー、P2S 接続用の証明書 |
| **S2S 接続の最大数** |1 |10 |10 |30 |
| **P2S 接続の最大数** |サポートされていません |128 |128 |128 |
| **アクティブ ルーティングのサポート (BGP)** (*) |サポートされていません |サポートされていません |サポートされています |サポートされています |

  (*) BGP はクラシック デプロイ モデルではサポートされません。
