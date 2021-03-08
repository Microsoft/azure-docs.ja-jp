---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/11/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 0056e18b6cb3aad2a4504bbe20b3b3421793489e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356271"
---
これらのサンプルでは、Azure Monitor を使用して、Azure の委任されたリソース管理用にオンボードされたサブスクリプションのアラートを作成する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) | 管理テナント内の過去 1 日間のアクティビティを照会し、[追加または削除された委任](../articles/lighthouse/how-to/monitor-delegation-changes.md) (または、成功しなかった試行) があればレポートします。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/alert-using-actiongroup) | このテンプレートは、Azure アラートを作成し、既存のアクション グループに接続します。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/multiple-loganalytics-alerts) | Kusto クエリに基づいてログ警告を複数作成します。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegation-alert-for-customer) | ユーザーが管理テナントにサブスクリプションを委任するときに、テナントにアラートをデプロイします。|
| [workbook-activitylogs-by-domain](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) | サブスクリプション全体の Azure のアクティビティ ログを、ドメイン名でフィルター処理するオプションを使用して表示します。 |
