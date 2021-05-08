---
title: Imperva WAF Gateway アプライアンスを Azure Sentinel に接続する | Microsoft Docs
description: Imperva WAF Gateway コネクタを使用して、Imperva WAF ログを Azure Sentinel にプルする方法について説明します。 Imperva WAF データをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698435"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Imperva WAF Gateway アプライアンスを Azure Sentinel に接続する

> [!IMPORTANT]
> Imperva WAF Gateway コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Imperva WAF Gateway アプライアンスを Azure Sentinel に接続する方法について説明します。 Imperva WAF Gateway データ コネクタを使用すると、Imperva WAF Gateway ログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 Imperva WAF Gateway と Azure Sentinel の統合には、CEF 形式の Syslog、Linux ベースのログ フォワーダー、および Log Analytics エージェントを使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Imperva WAF Gateway ログを Azure Sentinel に送信する

ログを Azure Sentinel に送るには、Linux ベースのログ転送サーバー (rsyslog または syslog-ng を実行) に Syslog メッセージを CEF 形式で送信するように Imperva WAF Gateway アプライアンスを構成します。 このサーバーには Log Analytics エージェントをインストールします。このエージェントで、ログをお使いの Azure Sentinel ワークスペースに転送します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Imperva WAF Gateway (Preview)]\(Imperva WAF Gateway (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. **[手順]** タブの **[構成]** の手順に従います。

    1. **[1. Linux Syslog agent configuration]\(Linux Syslog エージェントの構成\)** - ログ フォワーダーをまだ実行していない場合、または別のものが必要な場合は、この手順を実行します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 1: ログ フォワーダーをデプロイする](connect-cef-agent.md)」を参照してください。

    1. **[2. Common Event Format (CEF) のログを Syslog エージェントに転送する]** - このコネクタには、**アクション インターフェイス** と **アクション セット** が **Imperva SecureSphere MX** 管理コンソールで作成されている必要があります。 Imperva の手順に従って、[Azure Sentinel への Imperva WAF Gateway アラート ログを有効にします](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert)。 この構成には、次の要素を含める必要があります。
        - ログの送信先 – お使いのログ転送サーバーのホスト名、IP アドレス、またはこれらの両方
        - プロトコルとポート – TCP 514
        - ログの形式 – CEF
        - ログの種類 – すべて使用可能

    1. **3. Validate connection\(接続を検証する\)** - コネクタ ページのコマンドをコピーし、ログ フォワーダーでそれを実行することで、データの取り込みを検証します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 3: 接続を検証する](connect-cef-verify.md)」を参照してください。

        ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

Log Analytics で Imperva WAF Gateway のデータのクエリを実行するには、以下をクエリ ウィンドウにコピーし、選択した他のフィルターを適用します。

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

その他の便利なクエリの例については、コネクタ ページの **[次の手順]** タブを参照してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Imperva WAF Gateway を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。