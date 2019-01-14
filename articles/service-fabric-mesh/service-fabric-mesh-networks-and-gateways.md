---
title: Azure Service Fabric のネットワークの概要 | Microsoft Docs
description: Service Fabric Mesh でのネットワーク、ゲートウェイ、およびインテリジェントなトラフィック ルーティングについて説明します。
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d9f5c71d05477094d733fd6b798831b3ff02ce1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2018
ms.locfileid: "52893450"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric Mesh アプリケーションでのネットワークの概要
この記事では、さまざまな種類のロード バランサー、お使いのアプリケーションのネットワークがゲートウェイによって他のネットワークに接続される方法、およびお使いのアプリケーション内のサービス間でトラフィックがルーティングされる方法について説明します。

## <a name="layer-4-vs-layer-7-load-balancers"></a>レイヤー 4 のロード バランサーとレイヤー 7 のロード バランサーの比較
負荷分散は、ネットワークに関する [OSI モデル](https://en.wikipedia.org/wiki/OSI_model)の異なるレイヤー (多くの場合は、レイヤー 4 (L4) とレイヤー 7 (L7)) で実行できます。  通常は、2 種類のロード バランサーがあります。

- L4 のロード バランサーはネットワーク トランスポート層で動作し、パケットの内容に関係なく、パケットの配信を処理します。 パケット ヘッダーのみがロード バランサーによって調べられるので、分散の条件は IP アドレスとポートに限られます。 たとえば、あるクライアントはロード バランサーに TCP 接続します。 ロード バランサーでは、(SYN に直接応答することで) 接続が終了され、バックエンドが選択されて、バックエンドに新しい TCP 接続が行われます (新しい SYN を送信)。 通常、L4 のロード バランサーは、L4 の TCP/UDP 接続またはセッションのレベルでのみ動作します。 したがって、負荷分散では、バイトがリダイレクトされ、同じセッションからのバイトは同じバックエンドに送られます。 L4 のロード バランサーでは、移動されるバイトのアプリケーションの詳細は認識されません。 バイトは、どのようなアプリケーション プロトコルでもかまいません。

- L7 のロード バランサーはアプリケーション レイヤーで動作し、各パケットの内容が処理されます。 HTTP、HTTPS、WebSocket などのプロトコルを認識するので、パケットの内容が検査されます。 これにより、ロード バランサーでは高度なルーティングを実行できます。 たとえば、あるクライアントはロード バランサーに単一の HTTP/2 TCP 接続を行います。 ロード バランサーでは、2 つのバックエンド接続が行われます。 クライアントからロード バランサーに 2 つの HTTP/2 ストリームが送信されると、ストリーム 1 はバックエンド 1 に送信され、ストリーム 2 はバックエンド 2 に送信されます。 したがって、要求の負荷が大幅に異なる多重化クライアントであっても、効率的にバックエンド間に分散されます。 

## <a name="networks-and-gateways"></a>ネットワークとゲートウェイ
[Service Fabric リソース モデル](service-fabric-mesh-service-fabric-resources.md)では、ネットワーク リソースは、依存関係として参照している可能性があるアプリケーション リソースまたはサービス リソースとは関係なく、個別にデプロイできるリソースです。 インターネットに接続されたアプリケーション用のネットワークを作成するために使用されます。 異なるアプリケーションの複数のサービスを、同じネットワークに追加できます。 このプライベート ネットワークは、Azure 仮想ネットワーク (VNet) ではなく、Service Fabric によって作成および管理されます。 アプリケーションを動的にネットワーク リソースに追加および削除して、VNet 接続を有効および無効にできます。 

ゲートウェイは、2 つのネットワークのブリッジに使用されます。 ゲートウェイ リソースでは、任意のプロトコルに対する L4 ルーティングと、高度な HTTP (S) アプリケーション ルーティングのための L7 ルーティングを提供する、[Envoy プロキシ](https://www.envoyproxy.io/)が展開されます。 ゲートウェイでは、外部ネットワークからお使いのネットワークにトラフィックがルーティングされ、トラフィックのルーティング先のサービスが決定されます。  外部ネットワークは、オープン ネットワーク (基本的に、パブリック インターネット) でも Azure 仮想ネットワークでもよく、他の Azure アプリケーションおよびリソースと接続できます。 

![ネットワークとゲートウェイ][Image1]

ネットワーク リソースが `ingressConfig` で作成されるときに、パブリック IP がネットワーク リソースに割り当てられます。 パブリック IP は、ネットワーク リソースの有効期間に関連付けられます。

Mesh アプリケーションを作成するときは、既存のネットワーク リソースを参照する必要があります。 新しいパブリック ポートを追加したり、既存のポートをイングレスの構成から削除したりできます。 アプリケーションのリソースで参照されているネットワーク リソースの削除は失敗します。 アプリケーションを削除すると、ネットワーク リソースが削除されます。

## <a name="next-steps"></a>次の手順 
Service Fabric Mesh の詳細については、以下の概要ページを参照してください。
- [Service Fabric Mesh の概要](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png