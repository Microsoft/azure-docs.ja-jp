---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015930"
---
## <a name="scenario"></a>シナリオ

VM の静的 IP アドレスの構成方法をわかりやすく説明するために、このドキュメントでは次のシナリオを使用しています。

![仮想ネットワーク シナリオ: フロントエンドとバックエンドのサブネット、フロントエンド サブネット用の静的 IP アドレス](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

このシナリオでは、*FrontEnd* サブネット内に *DNS01* という名前の VM を作成してから、それを静的 IP アドレス *192.168.1.101* を使用するように設定します。
