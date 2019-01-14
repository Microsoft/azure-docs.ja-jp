---
title: Azure Log Analytics クエリの workspace() 式 | Microsoft Docs
description: workspace 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定のワークスペースからデータを取得するために、Log Analytics クエリで使用します。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: 24a737a728b0a249fda76cbff481bea284ac24aa
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53182946"
---
# <a name="workspace-expression-in-log-analytics-query"></a>Log Analytics クエリの workspace() 式

`workspace` 式は、同じリソース グループ、別のリソース グループ、または別のサブスクリプション内の特定のワークスペースからデータを取得するために、Log Analytics クエリで使用します。 これは、Application Insights クエリにログ データを含めたり、ログ クエリで複数のワークスペースのデータをクエリしたりする場合に便利です。


## <a name="syntax"></a>構文

`workspace(`*識別子*`)`

## <a name="arguments"></a>引数

- *識別子*:次の表のいずれかの形式を使用してワークスペースを識別します。

| ID | 説明 | 例
|:---|:---|:---|
| リソース名 | 人間が判読できるワークスペースの名前 (別名 "コンポーネント名") | workspace("contosoretail") |
| 修飾名 | "subscriptionName/resourceGroup/componentName" の形式のワークスペースのフルネーム | workspace('Contoso/ContosoResource/ContosoWorkspace') |
| ID | ワークスペースの GUID | workspace("b438b3f6-912a-46d5-9db1-b42069242ab4") |
| Azure リソース ID | Azure リソースの識別子 | workspace("/subscriptions/e4227-645-44e-9c67-3b84b5982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail") |


## <a name="notes"></a>メモ

* ワークスペースへの読み取りアクセスが必要です。
* 関連する式は、Application Insights のアプリケーション間でクエリすることができる `app` です。

## <a name="examples"></a>例

```Kusto
workspace("contosoretail").Update | count
```
```Kusto
workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count
```
```Kusto
workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>次の手順

- Application Insights アプリを参照するには、[アプリ式](workspace-expression.md)に関するページを参照してください。
- [Log Analytics データ](../../azure-monitor/log-query/log-query-overview.md)の格納方法に関するページをご覧ください。