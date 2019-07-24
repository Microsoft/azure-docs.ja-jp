---
title: Azure Web アプリケーション ファイアウォールの監視とログ記録
description: Front Door の監視とログ記録での Web アプリケーション ファイアウォール (WAF) について説明します
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 5368ed8d1e60a646366065e2cf617fb2f3735b53
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59782889"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure Web アプリケーション ファイアウォールの監視とログ記録 

Azure Web アプリケーション ファイアウォール (WAF) の監視とログ記録は、Azure Monitor および Azure Monitor ログのログ記録およびそれらとの統合を通じて提供されます。

> [!IMPORTANT]
> Front Door 用の WAF の監視とログ記録の機能は、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="azure-monitor"></a>Azure Monitor

Front Door での WAF のログは、[Azure Monitor](../azure-monitor/overview.md) と統合されます。 Azure Monitor により、WAF のアラートやログなどの診断情報を追跡できます。 Front Door リソース内での WAF の監視は、ポータルの **[診断]** タブから、または Azure Monitor サービスから直接、構成できます。

Azure portal から、Front Door リソースの種類に移動します。 左側の **[監視]** > **[メトリック]** タブから **WebApplicationFirewallRequestCount** を追加し、WAF 規則に一致する要求の数を追跡できます。 アクションの種類と規則の名前に基づいて、カスタム フィルターを作成できます。

![WAFMetrics](./media//waf-front-door-monitor/waf-frontdoor-metrics.png)

## <a name="logs-and-diagnostics"></a>ログと診断

Front Door の WAF では、検出された脅威ごとに詳細なレポートが提供されます。 ログ記録は Azure Diagnostics ログに統合されており、json 形式でアラートが記録されます。 これらのログは、[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)と統合できます。

![WAFDiag](./media/waf-front-door-monitor/waf-frontdoor-diagnostics.png)

FrontdoorAccessLog では、顧客のバックエンドに転送されるすべての要求が記録されます。 FrontdoorWebApplicationFirewallLog では、WAF 規則と一致するすべての要求が記録されます。

次のクエリ例では、ブロックされた要求についての WAF ログを取得します。

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

次のクエリ例では、AccessLogs のエントリを取得します。

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"


```

## <a name="next-steps"></a>次の手順

- [Front Door](front-door-overview.md) の詳細を確認する。

