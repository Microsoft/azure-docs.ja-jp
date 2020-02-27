---
title: Azure Monitor で複数の Application Insights リソースを統合する | Microsoft Docs
description: この記事では、Azure Monitor のログで関数を使用して、複数の Application Insights リソースのクエリを実行し、そのデータを視覚化する方法について詳しく説明します。
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: ce58aae3b1db1f0f338d353025d4f277aeb6944f
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137498"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Azure Monitor で複数の Application Insights リソースを統合する 
この記事では、非推奨になった Application Insights Connector を引き継ぐものとして、お使いのすべての Application Insights のログ データのクエリと表示を 1 か所で行う方法について説明します。Azure サブスクリプションは違っていてもかまいません。 1 回のクエリに含めることができる Application Insights リソースの数は 100 個に制限されています。

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>複数の Application Insights リソースに対してクエリを実行するための推奨される方法 
複数の Application Insights リソースを 1 つのクエリの中に指定するのは面倒であり、管理が難しくなる可能性があります。 代わりに、関数を活用して、クエリのロジックとアプリケーションのスコープを分離できます。  

次の例で、複数の Application Insights リソースを監視し、失敗した要求の数をアプリケーション名別に視覚化する方法を示します。

union 演算子とアプリケーションの一覧を使用して関数を作成した後、クエリを *applicationsScoping* というエイリアスの関数としてワークスペースに保存します。 

アプリケーションの一覧は、ポータルでワークスペースのクエリ エクスプローラーに移動して、編集する関数を選択して保存するか、または `SavedSearch` PowerShell コマンドレットを使用することで、いつでも変更できます。 

>[!NOTE]
>ログ アラートでは、アラートの作成時にアラート ルール リソース (ワークスペースやアプリケーションなど) のアクセス検証が実行されるため、この方法は使用できません。 アラートの作成後に新しいリソースを関数に追加することはサポートされません。 ログ アラートにおけるリソースの範囲指定に関数を使用する場合は、ポータルまたは Resource Manager テンプレートでアラート ルールを編集して、リソースの範囲指定を更新する必要があります。 または、リソースの一覧をログ アラート クエリに含めることもできます。

`withsource= SourceApp` コマンドを使用すると、ログを送信したアプリケーションを示す列が結果に追加されます。 この例では、SourceApp プロパティからアプリケーション名を抽出する parse 演算子は省略できます。 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

これで、リソース間クエリで applicationsScoping 関数を使用する準備が整いました。  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

このクエリでは、Application Insights のスキーマが使用されます。ただし、applicationsScoping 関数で Application Insights データ構造が返されるため、クエリはワークスペース内で実行されます。 関数のエイリアスによって、定義したすべてのアプリケーションからの要求の和集合が返されます。 このクエリによって、失敗した要求がフィルター処理され、アプリケーション別に傾向が視覚化されます。

![クロスクエリの結果の例](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>ログ アラートでの[リソース間のクエリ](../log-query/cross-workspace-query.md)は、新しい [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) でサポートされています。 [従来の Log Alerts API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api) から切り替えていない場合、Azure Monitor では既定で、[従来の Log Analytics Alert API](../platform/api-alerts.md) を使用して Azure portal から新しいログ アラート ルールが作成されます。 切り替えた後は、Azure portal での新しいアラート ルールに対して新しい API が既定になり、リソース間のクエリのログ アラート ルールを作成できます。 切り替えを行わなくても、[scheduledQueryRules API 用の ARM テンプレート](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)を使用することで、[リソース間のクエリ](../log-query/cross-workspace-query.md)のログ アラート ルールを作成できます。ただし、このアラート ルールは、[scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) では管理できますが、Azure portal では管理できません。

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights と Log Analytics ワークスペースのスキーマの相違点
次の表に、Log Analytics と Application Insights のスキーマの違いを示します。  

| Log Analytics ワークスペースのプロパティ| Application Insights のリソースのプロパティ|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Browser | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Country | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| OperationID | operation_id |
| OperationName | operation_Name | 
| OS | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | success | 
| ResponseCode | resultCode | 
| Role | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>次のステップ

[ログ検索](../../azure-monitor/log-query/log-query-overview.md)を使用して Application Insights アプリの詳細情報を表示します。
