---
title: Azure Sentinel に Microsoft Web アプリケーション ファイアウォール データを接続する | Microsoft Docs
description: Azure Sentinel に Microsoft Web アプリケーション ファイアウォール データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: e7dc1e6c1bb1ca81ada59cb3dae8fecbc6452b7f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029774"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Microsoft Web アプリケーション ファイアウォールからデータを接続する



Azure Application Gateway の Microsoft Web アプリケーション ファイアウォール (WAF) からログをストリームできます。 この WAF によって、SQL インジェクションやクロスサイト スクリプティングなどの一般的な Web 脆弱性からアプリケーションが保護されます。また、ルールをカスタマイズして偽陽性を減らすことができます。 次の手順に従って、Microsoft の Web アプリケーション ファイアウォールのログを Azure Sentinel にストリーミングします。


## <a name="prerequisites"></a>前提条件

- 既存の Application Gateway リソース

## <a name="connect-to-microsoft-web-application-firewall"></a>Microsoft Web アプリケーション ファイアウォールに接続する

既に Microsoft Web アプリケーション ファイアウォールがある場合は、既存のゲートウェイ リソースがあることを確認します。
Microsoft Web アプリケーション ファイアウォールがデプロイされ、データを取得している場合は、アラート データを Azure Sentinel に簡単にストリーミングできます。
    
1. Azure Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** を選択します。
1. [Data connectors]\(データ コネクタ\) ページで **[WAF]** タイルを選択します。
1. [[Application Gateway resource]\(Application Gateway リソース\)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways)  に移動し、使用する WAF を選択します。
    1. **[診断設定]** を選択します。
    1. 表の下の **[+ Add diagnostic setting]\(+ 診断設定の追加\)** を選択します。
    1. **[診断設定]** ページの **[名前]** に入力し、 **[Log Analytics への送信]** を選択します。
    1. **[Log Analytics ワークスペース]** の下で Azure Sentinel ワークスペースを選択します。
    1. 分析するログの種類を選択します。 お勧めするのは、ApplicationGatewayAccessLog および ApplicationGatewayFirewallLog です。
1. Microsoft Web アプリケーション ファイアウォール アラートのために Log Analytics 内で関連するスキーマを使用するには、**AzureDiagnostics** を検索します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Microsoft Web アプリケーション ファイアウォールを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
