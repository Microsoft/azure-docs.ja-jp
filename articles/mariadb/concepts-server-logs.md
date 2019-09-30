---
title: Azure Database for MariaDB 用のサーバー ログ
description: Azure Database for MariaDB で利用できるログと、さまざまなログ記録レベルを有効にするため利用可能なパラメーターについて説明します。
author: rachel-msft
ms.author: raagyema
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: 10dbd4d7fa838ee7f8a3f70b3caadb570877d685
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259963"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での低速クエリ ログ
Azure Database for MariaDB では、ユーザーは低速クエリ ログを使用できます。 トランザクション ログへのアクセスはサポートされていません。 低速クエリ ログは、トラブルシューティングの目的でパフォーマンスのボトルネックを特定するために使用できます。

低速クエリ ログについて詳しくは、[低速クエリ ログ](https://mariadb.com/kb/en/library/slow-query-log-overview/)に関する MariaDB のドキュメントをご覧ください。

## <a name="access-slow-query-logs"></a>低速クエリ ログにアクセスする
Azure portal と Azure CLI を使用して、Azure Database for MariaDB の低速クエリ ログを一覧表示およびダウンロードできます。

Azure portal で Azure Database for MariaDB サーバーを選択します。 **[監視]** の見出しの下の、 **[サーバー ログ]** ページを選択します。

Azure CLI の詳細については、「[Configure and access server logs using Azure CLI (Azure CLI を使用したサーバー ログの構成とアクセス)](howto-configure-server-logs-cli.md)」を参照してください。

## <a name="log-retention"></a>ログのリテンション期間
ログは、作成日から最大 7 日間使用できます。 使用可能なログの合計サイズが 7 GB を超える場合は、空き領域を利用できるようになるまで、古いファイルから削除されます。

ログのローテーションは、24 時間ごとか 7 GB ごとのどちらか早い方のタイミングで行われます。

## <a name="configure-slow-query-logging"></a>低速クエリ ログを構成する
既定では、低速クエリ ログは無効です。 有効にするには、slow_query_log をオンに設定します。

調整できるその他のパラメーターは次のとおりです。

- **long_query_time**: long_query_time (秒単位) より長いクエリがある場合は、そのクエリが記録されます。 既定値は 10 秒です。
- **log_slow_admin_statements**: オンの場合は、slow_query_log に書き込まれるステートメントに、ALTER_TABLE や ANALYZE_TABLE などの管理ステートメントが含まれます。
- **log_queries_not_using_indexes**: インデックスを使用していないクエリを slow_query_log に記録するかどうかを決定します。
- **log_throttle_queries_not_using_indexes**:このパラメーターは、低速クエリ ログに書き込むことができる、インデックスを使用していないクエリの数を制限します。 このパラメーターは、Log_queries_not_using_indexes がオンに設定されている場合に有効です。

低速クエリ ログのパラメーターの完全な説明については、MariaDB の[低速クエリ ログのドキュメント](https://mariadb.com/kb/en/library/slow-query-log-overview/)を参照してください。

## <a name="diagnostic-logs"></a>診断ログ
Azure Database for MariaDB は、Azure Monitor 診断ログと統合されます。 MariaDB サーバーで低速クエリ ログを有効にした場合、それらが Azure Monitor のログ、イベント ハブ、または Azure Storage に出力されるようにすることもできます。 診断ログを有効にする方法の詳細については、[診断ログのドキュメント](../azure-monitor/platform/resource-logs-overview.md)の操作方法のセクションを参照してください。

> [!IMPORTANT]
> サーバー ログに対するこの診断機能は、General Purpose 価格レベルとメモリ最適化[価格レベル](concepts-pricing-tiers.md)でのみ使用できます。

次の表は、各ログの内容を説明しています。 出力方法に応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。

| **プロパティ** | **説明** |
|---|---|
| `TenantId` | テナント ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| `Type` | ログの種類。 常に `AzureDiagnostics` |
| `SubscriptionId` | サーバーが属するサブスクリプションの GUID |
| `ResourceGroup` | サーバーが属するリソース グループの名前 |
| `ResourceProvider` | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | リソース URI |
| `Resource` | サーバーの名前 |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | サーバーの名前 |
| `start_time_t` [UTC] | クエリの開始時刻 |
| `query_time_s` | クエリの実行にかかった合計時間 |
| `lock_time_s` | クエリがロックされていた合計時間 |
| `user_host_s` | ユーザー名 |
| `rows_sent_s` | 送信された行の数 |
| `rows_examined_s` | 検査された行の数 |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | 挿入 ID |
| `sql_text_s` | クエリ全体 |
| `server_id_s` | サーバー ID |
| `thread_id_s` | スレッド ID |
| `\_ResourceId` | リソース URI |

## <a name="next-steps"></a>次の手順
- [Azure portal からサーバー ログを構成しアクセスする方法](howto-configure-server-logs-portal.md)
