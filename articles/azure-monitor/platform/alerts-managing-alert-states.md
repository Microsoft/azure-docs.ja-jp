---
title: アラートとスマート グループの状態を管理する
description: アラートとスマート グループ インスタンスの状態を管理します
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 2aa521b0552b60e5a875a5f46ab9887c6e5b6e3e
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54472060"
---
# <a name="manage-alert-and-smart-group-states"></a>アラートとスマート グループの状態を管理する
Azure Monitor のアラートは[アラート状態と監視条件](https://aka.ms/azure-alerts-overview)を持つようになり、スマート グループも同様に[スマート グループの状態](https://aka.ms/smart-groups)を持つようになりました。 状態の変更は、アラートまたはスマート グループにそれぞれ関連付けられた履歴にキャプチャされるようになりました。 この記事では、アラートおよびスマート グループの両方について、状態を変更するプロセスを説明します。

## <a name="change-the-state-of-an-alert"></a>アラートの状態を変更する
1. 次のさまざまな方法で、アラートの状態を変更できます。 
    * [すべてのアラート] ページで、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/)  をクリックします。   
    ![監視](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページで、[状態の変更]/(Change State/)  をクリックすることができます   
    ![監視](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * 特定のアラート インスタンスについての [アラートの詳細] ページの [Smart Group] (スマート グループ) ペインで、目的のアラートの横にあるチェック ボックスをクリックすることができます    
    ![監視](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * スマート グループの [詳細] ページのメンバー アラートの一覧で、状態を変更するアラートの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/) をクリックすることができます。   
    ![監視](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。   
![監視](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. これが完了すると、それぞれのアラートの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。    
![監視](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>スマート グループの状態を変更する
1. 次のさまざまな方法で、スマート グループの状態を変更できます。
    1. スマート グループのリスト ページで、状態を変更するスマート グループの横にあるチェック ボックスをクリックし、[状態の変更]/(Change State/) をクリックします  
    ![監視](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. スマート グループの [詳細] ページで、状態の変更をクリックすることができます        
    ![監視](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. [状態の変更] (Change State) をクリックすると、ポップアップが表示され、状態 (新規/確認済み/終了) を選択して必要に応じてコメントを入力できます。 
![監視](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  スマート グループの状態を変更しても、個々のメンバーのアラートの状態は変更されません。

1. これが完了すると、それぞれのスマート グループの履歴に状態の変更が記録されます。 この情報は、それぞれの詳細ページを開き、履歴のセクションを確認することによって表示できます。     
![監視](./media/alerts-managing-alert-states/state-sg-history.jpg)

