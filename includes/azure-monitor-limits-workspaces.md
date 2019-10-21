---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 017c02a10137579e6e3497775e9e4a3ac0a5d72d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350865"
---
**データの収集量と保持期間** 

| レベル | 1 日あたりの制限 | データの保持 | Comment (コメント) |
|:---|:---|:---|:---|
| 現在の GB あたりの価格レベル<br>(2018 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来の Free レベル<br>(2016 年 4 月に導入) | 500 MB | 7 日 | ワークスペースが 1 日あたり 500 MB の制限に達した場合、データ インジェストが停止し、翌日の始めに再開されます。 1 日は UTC に基づきます。 Azure Security Center によって収集されるデータは、この 1 日あたり 500 MB の制限には含まれず、この制限を超えても引き続き収集されます。  |
| 従来の GB あたりの Standalone レベル<br>(2016 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来のノードあたり (OMS)<br>(2016 年 4 月に導入) | 制限なし | 30 日 - 730 日 | 31 日を超えるデータ保持期間は追加料金で使用できます。 Azure Monitor の価格を確認してください。 |
| 従来の Standard レベル | 制限なし | 30 日  | 保持期間を調整することはできません。 |
| 従来の Premium レベル | 制限なし | 365 日  | 保持期間を調整することはできません。 |

**サブスクリプションあたりのワークスペースの数。**

| Pricing tier    | ワークスペースの制限 | 説明
|:---|:---|:---|
| Free レベル  | 10 | この制限を増やすことはできません。 |
| その他のすべてのレベル | 制限なし | リソース グループ内のリソースの数とサブスクリプションあたりのリソース グループの数によって制限されます。 |

**Azure Portal**

| Category | 制限 | 説明 |
|:---|:---|:---|
| ログ クエリによって返されるレコードの最大数 | 10,000 | クエリでクエリ スコープ、時間範囲、およびフィルターを使用して、結果を減らします。 |


**データ コレクター API**

| Category | 制限 | 説明 |
|:---|:---|:---|
| 1 回の投稿の最大サイズ | 30 MB | 大量の場合は複数の投稿に分割します。 |
| フィールド値の最大サイズ  | 32 KB | 32 KB を超えるフィールドは切り詰められます。 |

**Search API**

| Category | 制限 | 説明 |
|:---|:---|:---|
| 1 つのクエリで返されるレコードの最大数 | 500,000 | |
| 返されるデータの最大サイズ | 64,000,000 バイト (~61 MiB)| |
| クエリの最大実行時間 | 10 分 | 詳細については、[タイムアウト](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts)に関するページをご覧ください。  |
| 最大要求レート | AAD ユーザーまたはクライアントの IP アドレスごとに、30 秒あたり 200 件の要求 | 詳細については、[レート制限](https://dev.loganalytics.io/documentation/Using-the-API/Limits)に関するページをご覧ください。 |

**一般的なワークスペースの制限**

| Category | 制限 | 説明 |
|:---|:---|:---|
| テーブルの最大列数         | 500 | |
| 列名の最大文字数 | 500 | |
| 容量の上限に達しているリージョン | 米国中西部 | このリージョンは一時的な容量の上限に達しているため、現在新しいワークスペースを作成できません。 この制限は、2019 年 10 月末までに対処される予定です。 |
| データのエクスポート | 現在、利用できません | データの集計とエクスポートには Azure Function または Logic App を使用してください。 | 

**データ インジェストのボリューム レート**


Azure Monitor とは、毎月増加するテラバイト単位のデータを送信する何千もの顧客にサービスを提供する高スケールのデータ サービスです。 [診断設定](../articles/azure-monitor/platform/diagnostic-settings.md)を利用して Azure リソースから送信されるデータの既定のインジェスト ボリューム レート上限はワークスペースあたり約 **6 GB/分**です。 これは概算値であり、実際のサイズはログの長さとその圧縮率に左右され、データの種類によって異なることがあります。 この上限は、エージェントまたは[データ コレクター API](../articles/azure-monitor/platform/data-collector-api.md) から送信されるデータには適用されません。

1 つのワークスペースに高い比率でデータを送信すると、一部のデータが削除され、しきい値を超え続けている間、6 時間ごとにワークスペースの*操作*テーブルにイベントが送信されます。 インジェスト ボリュームがインジェスト率の制限を超えている場合、または間もなくそれに達すると予測される場合は、サポート リクエストを開いて、ワークスペースの増加を要求できます。
 
ワークスペース内でのそのようなイベントの通知を受け取るには、ゼロより大きい結果数のアラート ロジック ベースを使った次のクエリを使用して、[ログ アラート ルール](../articles/azure-monitor/platform/alerts-log.md)を作成します。

``` Kusto
Operation
|where OperationCategory == "Ingestion"
|where Detail startswith "The rate of data crossed the threshold"
``` 


>[!NOTE]
>Log Analytics を使用してきた期間の長さによっては、レガシ価格レベルにアクセスできることがあります。 詳細については、[Log Analytics のレガシ価格レベル](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers)に関するページを参照してください。 