---
title: Azure SQL Data Warehouse の管理と監視 - クエリのアクティビティ、リソースの使用状況 | Microsoft Docs
description: Azure SQL Data Warehouse の管理と監視に利用できる機能について説明します。 データ ウェアハウスのクエリ アクティビティとリソース使用状況を把握するには、Azure portal と動的管理ビュー (DMV) を使用します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/09/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 786ae1f18d52c6763b60f5019ecfe365f1cd540a
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71334102"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのリソース使用状況とクエリ アクティビティを監視する
Azure portal 内にある Azure SQL Data Warehouse のリッチな監視エクスペリエンスでは、データ ウェアハウスのワークロードに関する分析情報が表示されます。 データ ウェアハウスを監視するときの推奨されるツールである Azure portal では、構成可能なリテンション期間、アラート、推奨事項、およびメトリックとログのカスタマイズ可能なグラフとダッシュボードが提供されます。 ポータルでは、Operations Management Suite (OMS) や Azure Monitor (ログ) などの他の Azure 監視サービスと統合して、お使いのデータ ウェアハウスだけでなく、統合された監視エクスペリエンスに対する Azure 分析プラットフォーム全体も含む、総合的な監視エクスペリエンスを提供することもできます。 このドキュメントでは、SQL Data Warehouse で分析プラットフォームの最適化と管理に使用できる監視機能について説明します。 

## <a name="resource-utilization"></a>リソース使用率 
Azure portal では、SQL Data Warehouse に対して以下のメトリックを使用できます。 これらのメトリックは、[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection#metrics) を通じて表示できます。


| メトリックの名前             | 説明                                                  | 集計の種類 |
| ----------------------- | ------------------------------------------------------------ | ---------------- |
| CPU の割合          | データ ウェアハウスの全ノードでの CPU 使用率      | 最大値          |
| データ IO の割合      | データ ウェアハウスの全ノードでの IO 使用率       | 最大値          |
| メモリの割合       | データ ウェアハウスの全ノードでのメモリ使用率 (SQL Server) | 最大値          |
| 成功した接続  | データへの成功した接続の数                 | 合計            |
| 失敗した接続      | データ ウェアハウスへの失敗した接続の数           | 合計            |
| ファイアウォールによってブロックされる     | ブロックされたデータ ウェアハウスへのログインの数     | 合計            |
| DWU 上限               | データ ウェアハウスのサービス レベル目標                | 最大値          |
| DWU の割合          | CPU の割合とデータ IO の割合の最大値        | 最大値          |
| 使用済み DWU                | DWU の制限 * DWU の割合                                   | 最大値          |
| キャッシュ ヒットの割合    | (キャッシュ ヒット数/キャッシュ ミス数) * 100: キャッシュ ヒット数はローカル SSD キャッシュでのすべての列ストア セグメント ヒット数の合計、キャッシュ ミス数はローカル SSD キャッシュでの列ストア セグメント ミス数を全ノードについて合計した値 | 最大値          |
| 使用されたキャッシュの割合   | (使用されたキャッシュ/キャッシュ容量) * 100: 使用されたキャッシュは全ノードのローカル SSD キャッシュでの全バイト数の合計、キャッシュ容量は全ノードのローカル SSD キャッシュのストレージ容量の合計 | 最大値          |
| ローカル tempdb の割合 | すべてのコンピューティング ノードでの、ローカル tempdb の使用率 - 値は 5 分ごとに出力されます | 最大値          |

> メトリックを表示してアラートを設定するときに考慮が必要な事項は次のとおりです。
>
> - 接続の失敗と成功がレポートされるのは、特定のデータ ウェアハウスについてです。論理サーバーについてはレポートされません
> - データ ウェアハウスがアイドル状態であっても、メモリの割合には使用率が反映されます。アクティブなワークロードのメモリ消費は反映されません。 追加のキャッシュ容量のスケーリングによってワークロードのパフォーマンスが要件を満たすように向上するかどうかに関する総合的な意思決定を行うために、このメトリックとその他 (tempdb、gen2 キャッシュ) を使用して追跡します。


## <a name="query-activity"></a>クエリ アクティビティ
T-SQL を使用して SQL Data Warehouse を監視するときのプログラム エクスペリエンスでは、動的管理ビュー (DMV) のセットがサービスによって提供されます。 これらのビューは、アクティブのトラブルシューティングを行うときと、ワークロードでパフォーマンスのボトルネックを特定するときに役に立ちます。

SQL Data Warehouse で提供される DMV の一覧を表示するのには、[こちらのドキュメント](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)をご覧ください。 

## <a name="metrics-and-diagnostics-logging"></a>メトリックと診断のロギング
メトリックとログはどちらも、Azure Monitor (具体的には [Azure Monitor ログ](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) コンポーネント) にエクスポートでき、[ログ クエリ](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)を使用してプログラムでアクセスできます。 SQL Data Warehouse のログ待ち時間は約 10-15 分です。 待ち時間に影響を与える要因については、次のドキュメントをご覧ください。


## <a name="next-steps"></a>次の手順
次のハウツー ガイドでは、データ ウェアハウスを監視および管理するときの一般的なシナリオとユース ケースについて説明されています。

- [DMV を利用してデータ ウェアハウスのワークロードを監視する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)
