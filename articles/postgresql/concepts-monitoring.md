---
title: 監視とチューニング - Azure Database for PostgreSQL - 単一サーバー
description: この記事では、Azure Database for PostgreSQL (単一サーバー) の監視およびチューニング機能について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: f6a042dfee000dd6341368b46db32fe36060cc72
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2020
ms.locfileid: "87171589"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) を監視およびチューニングする
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for PostgreSQL には、サーバーの動作の分析情報を提供する各種の監視オプションが用意されています。

## <a name="metrics"></a>メトリック
Azure Database for PostgreSQL には、PostgreSQL サーバーをサポートするリソースの動作に関する洞察を提供する各種のメトリックが用意されています。 各メトリックは 1 分間隔で出力され、最大 [93 日分の履歴](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#retention-of-metrics)が保持されます。 メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
Azure Database for PostgreSQL では、次のメトリックを使用できます。

|メトリック|メトリックの表示名|ユニット|説明|
|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合 (Basic レベルのサーバーには適用されません。)|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|バイト|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|storage_limit|ストレージの制限|バイト|このサーバーの最大のストレージ|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|サーバーの最大サーバー ログ ストレージのうち、使用されているサーバー ログ ストレージの割合。|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|バイト|使用されているサーバー ログ ストレージの量。|
|serverlog_storage_limit|サーバー ログ ストレージの上限|バイト|このサーバーの最大サーバー ログ ストレージ。|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続|Count|確立後、失敗した接続の数。|
|network_bytes_egress|Network Out|バイト|アクティブな接続全体のネットワーク送信。|
|network_bytes_ingress|Network In|バイト|アクティブな接続全体のネットワーク受信。|
|backup_storage_used|使用済みバックアップ ストレージ|バイト|使用されているバックアップ ストレージの量。 このメトリックは、サーバーに設定されているバックアップ保持期間に基づいて保持されているすべてのデータベースの完全バックアップ、差分バックアップ、ログ バックアップによって使用されるストレージの合計を表します。 バックアップの頻度はサービスによって管理され、[概念に関する記事](concepts-backup.md)で説明されています。 geo 冗長ストレージの場合、バックアップ ストレージの使用量は、ローカル冗長ストレージの 2 倍になります。|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas (レプリカ間の最大ラグ)|バイト|マスターと最も遅れているレプリカの間のバイト単位でのラグ。 このメトリックは、マスター サーバーのみで使用できます。|
|pg_replica_log_delay_in_seconds|Replica Lag (レプリカ ラグ)|Seconds|最後に再生されたトランザクションからの時間。 このメトリックは、レプリカ サーバーのみで使用できます。|

## <a name="server-logs"></a>サーバー ログ
サーバーでのログ記録を有効にできます。 これらのリソース ログは、[Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)、Event Hubs、およびストレージ アカウントに送信できます。 ログ記録の詳細については、[サーバー ログ](concepts-server-logs.md)に関するページをご覧ください。

## <a name="query-store"></a>クエリ ストア
[クエリ ストア](concepts-query-store.md)は、クエリ ランタイム統計や待機イベントなど、一定期間のクエリ パフォーマンスを追跡記録します。 この機能により、**azure_sys** という名前のシステム データベースの query_store スキーマにクエリ ランタイム パフォーマンス情報が保持されます。 さまざまな構成ノブを介してデータのコレクションとストレージを制御できます。

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) はクエリ ストアと連動し、データを視覚化します。視覚化したデータには Azure portal からアクセスできます。 これらのグラフにより、パフォーマンスに影響を与える主要なクエリを特定できます。 Query Performance Insight には、Azure Database for PostgreSQL サーバーのポータル ページの **[サポート + トラブルシューティング]** セクションからアクセスできます。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項
[パフォーマンスの推奨事項](concepts-performance-recommendations.md)機能によって、ワークロード パフォーマンス改善の機会が特定されます。 パフォーマンスの推奨事項によって、ワークロードのパフォーマンスを改善する可能性がある新しいインデックスを作成するための推奨事項が提供されます。 推奨インデックスを作成するために、この機能は、クエリ ストアから報告されたスキーマ、ワークロードなどのさまざまなデータベースの特性を考慮します。 顧客は、パフォーマンスに関する推奨事項を実装した後、パフォーマンスをテストし、変更の影響を評価する必要があります。 

## <a name="planned-maintenance-notification"></a>計画メンテナンスの通知

**計画メンテナンスの通知**によって、Azure Database for PostgreSQL - Single Server に対して今後予定されているメンテナンスに関するアラートを受信できます。 これらの通知は [Service Health の](../service-health/overview.md)計画メンテナンスに統合されており、サブスクリプションに対してスケジュールされたすべてのメンテナンスを 1 か所に表示できます。 また、異なるリソースに対しては異なる連絡先が必要になる場合があるため、さまざまなリソース グループに対して適切なユーザーへの通知をスケーリングすることも可能です。 今後のメンテナンスに関する通知は、イベントの 72 時間前に受信します。

> [!Note]
> Microsoft では、**計画メンテナンスの通知**の 72 時間での通知をすべてのイベントに対して提供するために、あらゆる試みを行います。 ただし、重大時やセキュリティ更新プログラムに関する場合には、イベントが迫ってから通知が送信されたり、あるいは通知が省略されたりすることがあります。

### <a name="to-receive-planned-maintenance-notification"></a>計画メンテナンスの通知を受信するには

1. [ポータル](https://portal.azure.com)で、 **[サービス正常性]** を選択します。
2. **[アラート]** セクションで、 **[正常性アラート]** を選択します。
3. **[+ サービス正常性アラートの追加]** を選択し、フィールドに入力します。
4. 必須フィールドに入力します。 
5. **[イベントの種類]** を選択し、 **[計画メンテナンス]** または **[すべて選択]** を選択します
6. **[アクション グループ]** で、アラートの受信方法 (電子メールの取得、ロジック アプリのトリガーなど) を定義します。  
7. [ルールの作成時に有効にする] を確実に [はい] に設定します。
8. **[アラート ルールの作成]** を選択してアラートを完成させます

**サービス正常性アラート**の作成方法の詳細な手順については、「[サービス通知のアクティビティ ログ アラートを作成する](../service-health/alerts-activity-log-service-notifications.md)」を参照してください。

> [!IMPORTANT]
> 計画メンテナンスの通知は、現在、米国中西部**を除く**すべてのリージョンでプレビューとして利用できます

## <a name="next-steps"></a>次のステップ
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-on-metric.md)に関するページをご覧ください。
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
- [サーバーの監視のベスト プラクティス](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/)に関するブログをお読みください。
