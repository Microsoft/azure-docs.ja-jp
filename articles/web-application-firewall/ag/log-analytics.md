---
title: Azure Log Analytics を使用して Application Gateway Web アプリケーション ファイアウォールのログを調べる
description: この記事では、Azure Log Analytics を使用して Application Gateway Web アプリケーション ファイアウォールのログを調べる方法を見ていただきます
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: 22273cf23e48de62d99a8c149ae3e2b1c05db3af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87005431"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Log Analytics を使用して Application Gateway Web アプリケーション ファイアウォール (WAF) のログを調べる

Application Gateway の WAF が動作するようになったら、ログを有効にして、各要求で起きていることを調べることができます。 ファイアウォールのログでは、WAF で評価、照合、ブロックされていることについての分析情報が提供されます。 Azure Monitor Log Analytics では、ファイアウォール ログ内のデータを調べて、さらに多くの分析情報を得ることができます。 Log Analytics ワークスペースの作成について詳しくは、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/learn/quick-create-workspace.md)」をご覧ください。 ログ クエリについて詳しくは、「[Azure Monitor のログ クエリの概要](../../azure-monitor/log-query/log-query-overview.md)」をご覧ください。

## <a name="import-waf-logs"></a>WAF のログをインポートする

ファイアウォールのログを Log Analytics にインポートするには、[Application Gateway のバックエンドの正常性、リソース ログ、およびメトリック](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)に関する記事をご覧ください。 Log Analytics ワークスペース内にファイアウォールのログを取得したら、データの表示、クエリの記述、視覚エフェクトの作成、ポータルのダッシュボードへの追加を行うことができます。

## <a name="explore-data-with-examples"></a>例でデータを調べる

ファイアウォール ログの生のデータを表示するには、次のクエリを実行できます。

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

次のクエリのような表示になります。

![Log Analytics のクエリ](../media/log-analytics/log-query.png)

データにドリルダウンして、ここからグラフをプロットしたり、視覚エフェクトを作成したりできます。 出発点として、次のクエリをご覧ください。

### <a name="matchedblocked-requests-by-ip"></a>IP ごとの一致/ブロック要求数

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>URI ごとの一致/ブロック要求数

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>上位の一致ルール

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>上位 5 つの一致ルール グループ

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>ダッシュボードに追加する

クエリを作成した後は、それをダッシュボードに追加できます。  Log Analytics ワークスペースの右上にある **[ダッシュボードにピン留め]** を選択します。 前の 4 つのクエリをサンプル ダッシュボードにピン留めすると、データをひとめで確認できるようになります。

![ダッシュボード](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>次のステップ

[Application Gateway のバックエンドの正常性、リソース ログ、およびメトリック](../../application-gateway/application-gateway-diagnostics.md)