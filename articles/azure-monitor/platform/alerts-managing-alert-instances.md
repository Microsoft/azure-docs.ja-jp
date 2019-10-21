---
title: Azure Monitor でのアラートのインスタンスの管理
description: Azure 全体でのアラートのインスタンスの管理
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: robb
ms.subservice: alerts
ms.openlocfilehash: 91a770b522011f3955ae0956e289886eb204cf47
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71702905"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>統合アラートを使用したアラートのインスタンスの管理
Azure Monitor の[統合アラート エクスペリエンス](https://aka.ms/azure-alerts-overview)では、1 つのウィンドウで、Azure 全体のさまざまな種類のアラートをすべて表示できます。 1 つのウィンドウで、複数のサブスクリプションが対象になります。 この記事では、アラート インスタンスを表示する方法と、トラブルシューティングのために特定のアラート インスタンスを見つける方法を示します。

> [!NOTE]
   >  過去 30 日以内に生成されたアラートのみにアクセスできます。

## <a name="go-to-the-alerts-page"></a>アラート ページに移動する

以下のいずれかの方法で、アラート ページに移動できます。

   + [Azure portal](https://portal.azure.com/) で、 **[モニター]**  >  **[アラート]** の順に選択します。  
     ![Monitor アラートのスクリーンショット](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
   + 特定のリソースのコンテキストを使用します。 リソースを開き、 **[監視]** セクションに移動して、 **[アラート]** を選択します。 ランディング ページでは、その特定のリソースに関するアラートが表示されるように、事前にフィルター処理が行われています。
   
     ![リソース、監視、アラートのスクリーンショット](media/alerts-managing-alert-instances/alert-resource.JPG)
    
   + 特定のリソース グループのコンテキストを使用します。 リソース グループを開き、 **[監視]** セクションに移動して、 **[アラート]** を選択します。 ランディング ページでは、その特定のリソース グループに関するアラートが表示されるように、事前にフィルター処理が行われています。    
   
     ![リソース グループ、監視、アラートのスクリーンショット](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>アラート インスタンスを検索する

**[アラートの概要]** ページでは、Azure 全体のすべてのアラート インスタンスの概要がわかります。 概要ビューでは、**複数のサブスクリプション** (最大 5 個) を選択するか、**リソース グループ**、特定の**リソース**、または**時間の範囲**でフィルター処理することで、表示を変更できます。 **[アラート合計数]** またはいずれかの重大度バンドを選択して、アラートのリスト ビューに移動します。     
   ![[アラートの概要] ページのスクリーンショット](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
**[すべてのアラート]** ページに、Azure 全体のすべてのアラート インスタンスが一覧表示されます。 アラートの通知から portal を表示した場合に、その特定のアラート インスタンスを絞り込むためにフィルターを使用することができます。

> [!NOTE]
>  いずれかの重大度バンドを選択してページを表示した場合、一覧はその重大度に合わせて事前にフィルター処理されています。

前のページで使用可能なフィルターとは別に、モニター サービス (たとえば、メトリックのプラットフォーム)、モニターの状態 (起動済みまたは解決済み)、重大度、アラートの状態 (新規、確認済み、/解決済み)、またはスマート グループ ID に基づいてフィルター処理することができます。

   ![[すべてのアラート] ページのスクリーンショット](media/alerts-managing-alert-instances/all-alerts.jpg)

   > [!NOTE]
   >  いずれかの重大度バンドを選択してページを表示した場合、一覧はその重大度に合わせて事前にフィルター処理されています。
 
いずれかのアラート インスタンスを選択すると、 **[アラートの詳細]** ページが開き、その特定のアラート インスタンスに関する情報の詳細を確認できます。   
   ![[アラートの詳細] ページのスクリーンショット](media/alerts-managing-alert-instances/alert-details.jpg)  

