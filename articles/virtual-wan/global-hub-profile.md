---
title: Azure Virtual WAN のグローバルまたはハブベースの VPN プロファイルをダウンロードする | Microsoft Docs
description: Virtual WAN の自動化されたスケーラブルなブランチ間接続、利用可能なリージョン、パートナーについて説明します。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/31/2020
ms.author: alzam
ms.openlocfilehash: 3b7e765dbd024d46939e8989993f0c882b2a8f4b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965348"
---
# <a name="download-a-global-or-hub-based-profile-for-user-vpn-clients"></a>ユーザー VPN クライアント用にグローバルまたはハブベースのプロファイルをダウンロードする

Azure Virtual WAN では、リモート ユーザー用に 2 つの種類の接続を提供しています。グローバルとハブベースです。 以降のセクションを利用してプロファイルについて確認し、ダウンロードしてください。

## <a name="global-profile"></a>グローバル プロファイル

プロファイルでは、すべてのアクティブなユーザー VPN ハブを含むロード バランサーが提示されます。 ユーザーは、ユーザーの地理上の場所に最も近いハブに誘導されます。 この種類の接続は、ユーザーがさまざまな場所に頻繁に移動する場合に便利です。 **グローバル** プロファイルをダウンロードするには:

1. 仮想 WAN に移動します。
2. **[ユーザー VPN 構成]** をクリックします。
3. プロファイルをダウンロードする対象の構成を強調表示します。
4. **[Download virtual WAN user VPN profile]\(仮想 WAN ユーザー VPN プロファイルのダウンロード\)** をクリックします。

   ![グローバル プロファイル](./media/global-hub-profile/global1.png)

## <a name="hub-based-profile"></a>パブベース プロファイル

プロファイルでは、単一のハブが提示されます。 ユーザーは、このプロファイルを使用して、特定のハブにのみ接続できます。 **ハブベース** プロファイルをダウンロードするには:

1. 仮想 WAN に移動します。
2. [概要] ページの **[ハブ]** をクリックします。

    ![ハブ プロファイル 1](./media/global-hub-profile/hub1.png)
3. **[User VPN (Point to site)]\(ユーザー VPN (ポイントからサイトへ)\)** をクリックします。
4. **[Download virtual Hub User VPN profile]\(仮想ハブ ユーザー VPN プロファイルのダウンロード\)** をクリックします。

   ![ハブ プロファイル 2](./media/global-hub-profile/hub2.png)
5. **[EAPTLS]\(EAPTLS\)** のチェックをオンにします。
6. **[Generate and download profile]\(プロファイルを生成してダウンロードする\)** をクリックします。

   ![ハブ プロファイル 3](./media/global-hub-profile/download.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
