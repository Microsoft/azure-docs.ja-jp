---
title: インクルード ファイル
titleSuffix: Azure
description: インクルード ファイル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 34a23ce76ed0e9285a686073e1cbeb95347f7b7d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "81678666"
---
ピアリングの一覧を取得するには、**Get-AzPeering** コマンドを実行します。

```powershell
Get-AzPeering ResourceGroupName "PeeringResourceGroup" -Name "SeattleExchangePeering"
```

次の例は、エンド ツー エンドのプロビジョニングが正常に完了した場合の応答を示します。

```powershell
    Name                     : SeattleExchangePeering
    Sku                      : Basic_Exchange_Free
    Kind                     : Exchange
    PeeringLocation          : Seattle
    ProvisioningState        : Succeeded
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.101
    MicrosoftIPv4Address     : 10.21.31.51
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:101
    MicrosoftIPv6Address     : fe01::3e:51
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```