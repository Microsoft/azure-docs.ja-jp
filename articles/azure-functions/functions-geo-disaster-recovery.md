---
title: Azure Functions geo ディザスター リカバリー | Microsoft Docs
description: Azure Functions でリージョンを使用してフェールオーバーし、ディザスター リカバリーを利用する方法。
services: functions
documentationcenter: na
author: wesmc7777
manager: jeconnoc
keywords: Azure Functions, パターン, ベスト プラクティス, 関数
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: jehollan
ms.openlocfilehash: 87f8b2fac3a123f2b92f77a70535f952b25557e0
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309257"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions geo ディザスター リカバリー

Azure リージョンやデータ センター全体にダウンタイムが発生した場合に、別のリージョンで計算の処理を続行することが重要です。  この記事では、ディザスター リカバリーを可能にする関数をデプロイするために使用できる、いくつかの戦略について説明します。

## <a name="basic-concepts"></a>基本的な概念

Azure Functions は特定のリージョンで実行されます。  可用性を高めるために、同じ関数を複数のリージョンにデプロイできます。  複数のリージョンでは、関数を "*アクティブ/アクティブ*" パターンまたは "*アクティブ/パッシブ*" パターンで実行できます。  

* アクティブ/アクティブ。 両方のリージョンがアクティブであり、(重複またはローテーションして) イベントを受信しています。 アクティブ/アクティブは、HTTPS 関数を Azure Front Door と組み合わせる場合にお勧めします。
* アクティブ/パッシブ。 1 つのリージョンがアクティブでイベントを受信し、セカンダリがアイドル状態です。  フェールオーバーが必要になると、セカンダリ リージョンがアクティブになり、処理を引き継ぎます。  これは、Service Bus や Event Hubs のような HTTP 以外の関数の場合にお勧めします。

マルチリージョン デプロイの詳細については、[複数のリージョンでアプリを実行する](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)方法に関する記事をご覧ください。

## <a name="activeactive-for-https-functions"></a>HTTPS 関数でのアクティブ/アクティブ

関数のアクティブ/アクティブ デプロイを実現するには、両方のリージョンの間でイベントを調整できるコンポーネントが必要です。  HTTPS 関数の場合、この調整は [Azure Front Door](../frontdoor/front-door-overview.md) を使用して行われます。  Azure Front Door は、複数のリージョン関数の間で HTTPS 要求をルーティングおよびラウンドロビンすることができます。  また、各エンドポイントの正常性も定期的にチェックします。  リージョン関数が正常性チェックに応答しなくなった場合、Azure Front Door はそれをローテーションから外し、正常な関数だけにトラフィックを転送します。  

![Azure Front Door と関数のアーキテクチャ](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>HTTPS 以外の関数でのアクティブ/アクティブ

HTTPS 以外の関数でも、アクティブ/アクティブ デプロイを実現できます。  ただし、2 つのリージョンが相互に作用または調整し合う方法を検討する必要があります。  同じ関数アプリを 2 つのリージョンにデプロイし、それぞれが同じ Service Bus キューでトリガーする場合、それらはそのキューのデキューで競合するコンシューマーとして動作します。  これは、各メッセージがいずれかのインスタンスによってのみ処理されることを意味しますが、1 つの Service Bus に単一障害点があることも意味します。  2 つの Service Bus キューを (プライマリ リージョンに 1 つ、セカンダリ リージョンに 1 つ) デプロイし、2 つの関数アプリがそれらのリージョン キューを指している場合、2 つのリージョン間でどのようにキュー メッセージが分配されるのかが課題になります。  多くの場合、これは、各発行元がメッセージを "*両方*" のリージョンに発行しようとし、各メッセージが両方のアクティブ関数アプリによって処理されることを意味します。  これによってアクティブ/アクティブ パターンが作成されますが、計算の重複やデータをいつどのように統合するかに関する別の課題が生じます。  このような理由で、HTTPS 以外のトリガーではアクティブ/パッシブ パターンの使用をお勧めします。

## <a name="activepassive-for-non-https-functions"></a>HTTPS 以外の関数でのアクティブ/パッシブ

アクティブ/パッシブは、1 つの関数だけが各メッセージを処理する方法を提供しますが、障害が発生した場合にセカンダリ リージョンにフェールオーバーするメカニズムを提供します。  Azure Functions は [Azure Service Bus geo リカバリー](../service-bus-messaging/service-bus-geo-dr.md)と [Azure Event Hubs geo リカバリー](../event-hubs/event-hubs-geo-dr.md)とともに動作します。

例として Azure Event Hubs トリガーを使用した場合、アクティブ/パッシブ パターンには以下が含まれます。

* プライマリ リージョンとセカンダリ リージョンの両方にデプロイされた Azure Event Hubs。
* プライマリとセカンダリのイベント ハブをペアにするために、geo ディザスターが有効になります。  これにより、接続情報を変更せずにイベント ハブに接続し、プライマリからセカンダリに切り替えるために使用できる "別名" も作成されます。
* プライマリ リージョンとセカンダリ リージョンの両方にデプロイされた関数アプリ。
* 関数アプリは、それぞれのイベント ハブの "*直接*" (別名ではない) 接続文字列でトリガーされます。 
* イベント ハブへの発行元は、別名の接続文字列に対して発行する必要があります。 

![アクティブ/パッシブ アーキテクチャの例](media/functions-geo-dr/active-passive.png)

フェールオーバーの前に、共有された別名に送信する発行元はプライマリ イベント ハブにルーティングします。  プライマリ関数アプリは、プライマリ イベント ハブだけをリッスンします。  セカンダリ関数アプリはパッシブになり、アイドル状態になります。  フェールオーバーが開始されるとすぐに、共有された別名に送信する発行元はセカンダリ イベント ハブにルーティングするようになります。  セカンダリ関数アプリがアクティブになり、自動的にトリガーを開始します。  セカンダリ リージョンへの効果的なフェールオーバーは、イベント ハブから完全に実行でき、それぞれのイベント ハブがアクティブになった場合にのみ関数がアクティブになります。

[Service Bus](../service-bus-messaging/service-bus-geo-dr.md) と[イベント ハブ](../event-hubs/event-hubs-geo-dr.md)によるフェールオーバーに関する情報と考慮事項の詳細を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Front Door の作成](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs のフェールオーバーの考慮事項](../event-hubs/event-hubs-geo-dr.md#considerations)
