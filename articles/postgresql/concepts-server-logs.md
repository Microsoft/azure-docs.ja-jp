---
title: Azure Database for PostgreSQL - Single Server でのサーバー ログ
description: この記事では、Azure Database for PostgreSQL - Single Server がクエリ ログとエラー ログを生成する方法、およびログのリテンション期間を構成する方法について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 17083029f2377037b99abfa3ce8371661eccb957
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029992"
---
# <a name="server-logs-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server でのサーバー ログ
Azure Database for PostgreSQL ではクエリ ログとエラー ログが生成されます。 クエリとエラー ログを使用して、構成エラーと十分に最適化されていないパフォーマンスの特定、トラブルシューティング、修復を行えます。 トランザクション ログへのアクセスは含まれていません。 

## <a name="configure-logging"></a>ログの構成 
サーバー パラメーターのログを使用して、サーバーでログを構成できます。 新しい各サーバーで、**log_checkpoints** と **log_connections** は既定でオンになります。 ログのニーズに合わせて調整できる追加のパラメーターがあります。 

![Azure Database for PostgreSQL - ログ パラメーター](./media/concepts-server-logs/log-parameters.png)

これらのパラメーターの詳細については、PostgreSQL の「[Error Reporting and Logging](https://www.postgresql.org/docs/current/static/runtime-config-logging.html)」(エラー レポートとログ記録) のドキュメントを参照してください。 Azure Database for PostgreSQL パラメーターを構成する方法については、[portal のドキュメント](howto-configure-server-parameters-using-portal.md)または　[CLI のドキュメント](howto-configure-server-parameters-using-cli.md)を参照してください。

## <a name="access-server-logs-through-portal-or-cli"></a>portal または CLI によるサーバー ログへのアクセス
ログを有効にしている場合、[Azure portal](howto-configure-server-logs-in-portal.md)、[Azure CLI](howto-configure-server-logs-using-cli.md)、および Azure REST API を使用して、Azure Database for PostgreSQL ログ ストレージからそれらにアクセスできます。 ログ ファイルのローテーションは、1 時間ごとか 100 MB ごとのどちらか早い方のタイミングで行われます。 このログ ストレージのリテンション期間を設定するには、サーバーに関連付けられている **log\_retention\_period** パラメーターを使用します。 既定値は 3 日間です。最大値は 7 日間です。 サーバーには、ログ ファイルを保持するために、十分なストレージが割り当てられている必要があります (このリテンション期間パラメーターは、Azure 診断ログに影響しません)。


## <a name="diagnostic-logs"></a>診断ログ
Azure Database for PostgreSQL は、Azure Monitor の診断ログと統合されます。 PostgreSQL サーバーでログを有効にしたら、[Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)、Event Hubs、または Azure Storage への出力を選択できます。 

> [!IMPORTANT]
> サーバー ログに対するこの診断機能は、General Purpose 価格レベルとメモリ最適化[価格レベル](concepts-pricing-tiers.md)でのみ使用できます。

Azure portal を使用して診断ログの有効にするには:

   1. ポータルで、Postgres サーバーのナビゲーション メニューの *[診断設定]* に移動します。
   2. *[診断設定の追加]* を選択します。
   3. この設定に名前を付けます。 
   4. 任意の下流の場所 (ストレージ アカウント、イベントハブ、ログ診断) を選択します。 
   5. 必要なデータ型を選択します。
   6. 設定を保存します。

次の表で、各ログの内容を説明します。 選択した出力エンドポイントに応じて、含まれるフィールドとそれらが表示される順序が異なることがあります。 

|**フィールド** | **説明** |
|---|---|
| TenantId | テナント ID |
| SourceSystem | `Azure` |
| TimeGenerated [UTC] | ログが記録されたときのタイムスタンプ (UTC) |
| 種類 | ログの種類。 常に `AzureDiagnostics` |
| SubscriptionId | サーバーが属するサブスクリプションの GUID |
| ResourceGroup | サーバーが属するリソース グループの名前 |
| ResourceProvider | リソース プロバイダーの名前。 常に `MICROSOFT.DBFORPOSTGRESQL` |
| ResourceType | `Servers` |
| ResourceId | リソース URI |
| リソース | サーバーの名前 |
| Category | `PostgreSQLLogs` |
| OperationName | `LogEvent` |
| errorLevel | ログ レベル、例:LOG、ERROR、NOTICE |
| Message | プライマリ ログ メッセージ | 
| Domain | サーバーのバージョン (postgres 10 など) |
| Detail | セカンダリ ログ メッセージ (該当する場合) |
| ColumnName | 列の名前 (該当する場合) |
| SchemaName | スキーマの名前 (該当する場合) |
| DatatypeName | データ型の名前 (該当する場合) |
| LogicalServerName | サーバーの名前 | 
| _ResourceId | リソース URI |
| Prefix (プレフィックス) | ログ行のプレフィックス |



## <a name="next-steps"></a>次の手順
- [Azure portal](howto-configure-server-logs-in-portal.md) または [Azure CLI](howto-configure-server-logs-using-cli.md) からのログへのアクセスを確認する。
- [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)を確認する。
