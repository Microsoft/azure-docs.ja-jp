---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/09/2018
ms.openlocfilehash: 89c2467843d7abc7c005804fd5263fe3beb668b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "74793420"
---
より正確な使用コストを見積もるには、ポーリング間隔にのみ基づいて計算するのではなく、それぞれの日に到着する可能性のあるメッセージまたはイベントの数を考慮します。 イベントまたはメッセージがトリガー条件を満たしている場合、多くのトリガーは、条件を満たす任意のすべての他の待機イベントまたはメッセージを直ちに読み取ろうとします。 この動作は、長いポーリング間隔を選択した場合でも、ワークフロー開始の対象となる待機イベントまたはメッセージの数に基づいてトリガーが起動することを意味します。 この動作に続くトリガーには、Azure Service Bus、Azure Event Hub などがあります。

したがって、たとえば、毎日エンドポイントをチェックするトリガーを設定したとします。 トリガーはエンドポイントを確認し、条件を満たす 15 のイベントを検出した場合、トリガーが起動し、対応するワークフローを 15 回実行します。 Logic Apps によって、これらの 15 のワークフローによって実行されるすべてのアクションを、ワークフローを実行するすべてのアクションが測定されます。 潜在的なコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)をお試しください。