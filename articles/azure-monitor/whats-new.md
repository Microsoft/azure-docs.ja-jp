---
title: Azure Monitor のドキュメントの最新情報
description: 毎月更新される Azure Monitor のドキュメントに対する重要な更新。
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 02/10/2021
ms.openlocfilehash: b21e5ea8e25844e3e025915b0b9f15162c642f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713441"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Azure Monitor のドキュメントの最新情報

この記事では、Azure Monitor に関して新たに執筆された記事または大幅に更新された記事を一覧で紹介しています。 毎月第 1 週目に更新され、前月から更新された記事が掲載されます。

## <a name="january-2021"></a>2021 年 1 月 

### <a name="general"></a>全般 
- [Azure Monitor の FAQ](faq.md) - Application Insights のデバイス情報に関するエントリを追加しました。
### <a name="agents"></a>エージェント  
- [Azure Monitor Logs で分析する Windows イベント トレーシング (ETW) イベントの収集](./agents/data-sources-event-tracing-windows.md) - 新しい記事。
- [Azure Monitor のデータ収集ルール (プレビュー)](./agents/data-collection-rule-overview.md) - PowerShell サンプルと CLI サンプルへのリンクを追加しました。

### <a name="alerts"></a>警告  
- [セキュア エクスポートを使用して Azure を ITSM ツールに接続するように Azure を構成する](./alerts/itsm-connector-secure-webhook-connections-azure-configuration.md) - 新しい記事。
- [ITSMC ダッシュボード内の [コネクタの状態] のエラー](./alerts/itsmc-dashboard-errors.md) - 新しい記事。
- [ITSMC ダッシュボードを使用してエラーを調査する](./alerts/itsmc-dashboard.md) - 新しい記事。
- [Azure メトリック警告のトラブルシューティング](./alerts/alerts-troubleshoot-metric.md) - 動的しきい値のセクションを追加しました。
- [IT Service Management Connector での問題のトラブルシューティング](./alerts/itsmc-troubleshoot-overview.md) - 新しい記事。

### <a name="application-insights"></a>Application Insights
- [Azure Application Insights におけるテレメトリの関連付け](app/correlation.md) - OpenCensus Python で、あるモジュールから別のモジュールを呼び出す際のトレースの関連付けに関する情報を追加しました。
- [Web ページ向けの Application Insights](app/javascript.md) - 新しい記事。
- [Application Insights JavaScript SDK 用のクリック分析自動収集プラグイン](app/javascript-click-analytics-plugin.md) - 新しい記事。
- [コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション](app/codeless-overview.md) - Python 列を追加しました。
- [Application Insights JavaScript SDK の React プラグイン](app/javascript-react-plugin.md) - 新しい記事。
- [テレメトリ プロセッサ (プレビュー) - Azure Monitor Application Insights for Java](app/java-standalone-telemetry-processors.md) - 書き直し記事。
- [Azure Application Insights による利用状況分析](app/usage-overview.md) - 新しい記事。
- [Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける](app/change-analysis.md) - エラー メッセージを追加しました。


### <a name="insights"></a>洞察    
- [Azure Monitor for Azure Data Explorer (プレビュー)](insights/data-explorer.md) - 新しい記事。

### <a name="logs"></a>ログ    
- [Azure Monitor のカスタマー マネージド キー](./logs/customer-managed-keys.md) - ユーザー割り当てマネージド ID を紹介します。
- [Azure Monitor ログ専用クラスター](./logs/logs-dedicated-clusters.md) - 応答コードを更新しました。
- [クロス サービス クエリ - Azure Monitor と Azure Data Explorer (プレビュー)](/azure/azure-monitor/platform/azure-data-explorer-monitor-cross-service-query) - 新しい記事。

### <a name="metrics"></a>メトリック
- [Azure Monitor メトリックによるメトリックの集計と表示の説明](./essentials/metrics-aggregation-explained.md) - 新しい記事。

### <a name="platform-logs"></a>プラットフォーム ログ
- [Azure Monitor リソース ログでサポートされているサービスとカテゴリ](./essentials/resource-logs-categories.md) - 新しい記事。

### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックのデータ ソース](./visualize/workbooks-data-sources.md) - マージと変更分析を追加しました。


## <a name="december-2020"></a>2020 年 12 月

### <a name="general"></a>全般
- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - エラー メッセージを追加しました。
- [Azure Monitor と統合するパートナー](partners.md) - イベント ハブ統合に関するセクションを追加しました。

### <a name="agents"></a>エージェント
- [Azure Monitor を使用した Azure Data Explorer のクロスリソース クエリ](logs/azure-monitor-data-explorer-proxy.md) - 新しい記事。
- [Azure 監視エージェントの概要](agents/agents-overview.md) - Oracle 8 のサポートを追加しました。

### <a name="alerts"></a>警告
- [Azure メトリック アラートのトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - 動的しきい値のトラブルシューティングを追加しました。
- [Log Analytics の IT Service Management Connector](alerts/itsmc-definition.md) - 新しい記事。
- [IT Service Management Connector 概要](alerts/itsmc-overview.md) - トラブルシューティング情報を再構成しました。
- [Cherwell を IT Service Management Connector に接続する](alerts/itsmc-connections-cherwell.md) - 新しい記事。
- [Provance を IT Service Management Connector に接続する](alerts/itsmc-connections-provance.md) - 新しい記事。
- [SCSM を IT Service Management Connector に接続する](alerts/itsmc-connections-scsm.md) - 新しい記事。
- [ServiceNow を IT Service Management Connector に接続する](alerts/itsmc-connections-servicenow.md) - 新しい記事。
- [ServiceNow の同期に関する問題を手動で修正する方法](alerts/itsmc-resync-servicenow.md) - トラブルシューティング情報を再構成しました。




### <a name="application-insights"></a>Application Insights
- [JavaScript Web アプリのための Azure Application Insights](app/javascript.md) - 接続文字列の設定を追加しました。
- [Azure Application Insights 標準メトリック](app/standard-metrics.md) - 新しい記事。
- [Azure Monitor Application Insights Java](app/java-in-process-agent.md) - アプリケーションからのカスタム テレメトリの送信に関する情報を追加しました。
- [Application Insights からのテレメトリの連続エクスポート](app/export-telemetry.md) - 診断設定ベースのエクスポートを追加しました。
- [Azure Functions で .NET および .NET Core アプリのスナップショット デバッガーを有効にする](app/snapshot-debugger-function-app.md) - 新しい記事。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Azure Government 用の IP アドレスを追加しました。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - 診断サービスのサイト拡張機能の状態ページに関する情報を追加しました。
- [Azure Application Insights 可用性テストのトラブルシューティング](app/troubleshoot-availability.md) - ping テストのトラブルシューティングに関する情報を更新しました。
- [Azure Monitor Application Insights for Java のトラブルシューティング](app/java-standalone-troubleshoot.md) - 新しい記事。

### <a name="containers"></a>コンテナー
- [Container insights のレポート](insights/container-insights-reports.md) - 新しい記事。

### <a name="logs"></a>ログ
- [Azure Monitor ログ専用クラスター](logs/logs-dedicated-clusters.md) - 自動化されたコマンド、リンクを解除して削除する方法、トラブルシューティングを追加しました。
- [Azure Monitor と Azure Data Explorer 間のクロス サービス クエリ (プレビュー)](logs/azure-data-explorer-monitor-cross-service-query.md) - 新しい記事。
- [Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)](logs/logs-data-export.md) - ARM テンプレートを追加しました。

### <a name="metrics"></a>メトリック
- [Azure メトリックス エクスプローラーの高度な機能](essentials/metrics-charts.md) - リソース スコープ ピッカーに関する情報を追加しました。
- [メトリックス エクスプローラーでの複数のリソースの表示](essentials/metrics-dynamic-scope.md) - 新しい記事。

### <a name="networks"></a>ネットワーク
- [Azure Monitor の Azure Networking Analytics ソリューション](insights/azure-networking-analytics.md) - Network Insights ブックに関する情報を追加しました。

### <a name="virtual-machines"></a>Virtual Machines
- [ハイブリッド環境向けに Azure Monitor を有効にする](vm/vminsights-enable-hybrid.md) - 依存関係エージェントの新しいバージョン。


### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックのマップの視覚化](visualize/workbooks-map-visualizations.md) - 新しい記事。
- [Azure Monitor ブックで独自のストレージを使用する](visualize/workbooks-bring-your-own-storage.md) - 新しい記事。


## <a name="november-2020"></a>2020 年 11 月

### <a name="general"></a>全般
- [Azure Monitor サービスの制限](service-limits.md) - Azure Arc をサポートするために更新しました。

### <a name="agents"></a>エージェント
- [Azure 監視エージェントの概要](agents/agents-overview.md) - Azure Arc をサポートするために更新しました。
- [Azure Monitor エージェントをインストールする](agents/azure-monitor-agent-install.md) - 新しい記事。
- [Azure Monitor エージェントの概要](agents/azure-monitor-agent-overview.md) - Azure Arc をサポートするために更新しました。
- [エージェント用の Resource Manager テンプレートのサンプル](agents/resource-manager-agent.md) - Azure Arc をサポートするために更新しました。

### <a name="alerts"></a>警告
- [Azure portal でのアクション グループの作成および管理](alerts/action-groups.md) - Webhook の送信元 IP アドレスを追加しました。

### <a name="application-insights"></a>Application Insights
- [Azure Monitor Application Insights を監視する Java のコード不要のアプリケーション](app/java-in-process-agent.md) - 構成例を追加しました。
- [Application Insights JavaScript SDK の React プラグイン](app/javascript-react-plugin.md) - React フックの使用に関するセクションを追加しました。
- [Application Insights Java 2.x SDK からのアップグレード](app/java-standalone-upgrade-from-2x.md) - 新しい記事。
- [Microsoft.ApplicationInsights.SnapshotCollector のリリース ノート](app/snapshot-collector-release-notes.md) - 新しい記事。

### <a name="autoscale"></a>自動スケール
- [Azure での自動スケーリングの使用](autoscale/autoscale-get-started.md) - 別のリージョンへの自動スケーリングの移動に関するセクションを追加しました。

### <a name="data-collection"></a>データ コレクション
- [Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](agents/data-collection-rule-azure-monitor-agent.md) - Azure Arc をサポートするために更新しました。
- [Azure Monitor のデータ収集ルール (プレビュー)](agents/data-collection-rule-overview.md) - Azure Arc をサポートするために更新しました。
- [データ収集ルールのための Resource Manager テンプレート サンプル](agents/resource-manager-data-collection-rules.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション
- [セキュア エクスポートを使用して Azure を ITSM ツールに接続する](alerts/it-service-management-connector-secure-webhook-connections.md) - ServiceNow への接続に関するセクションを追加しました。

### <a name="logs"></a>ログ
- [Log Analytics と Excel を統合する](logs/log-excel.md) - 新しい記事。
- [Log Analytics データのセキュリティ](logs/data-security.md) - 追加のセキュリティ機能に関するセクションを追加しました。
- [Log Analytics の Power BI との統合](logs/log-powerbi.md) - 新しい記事。
- [Azure Monitor ログ レコード内の標準列](logs/log-standard-columns.md) - _SubscriptionId 列を追加しました。

ログ クエリに関するコンテンツの再構成による新規および更新された記事

- [Log Analytics のチュートリアル](logs/log-analytics-tutorial.md)
- [Azure Monitor でのログ クエリ](logs/log-query-overview.md)
- [Azure Monitor の Log Analytics の概要](logs/log-analytics-overview.md)
- [Azure Data Explorer と Azure Monitor でのクエリのサンプル](/azure/data-explorer/kusto/query/samples?pivots=azuremonitor)
- [チュートリアル:Azure Data Explorer と Azure Monitor で Kusto クエリを使用する](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor)



### <a name="virtual-machines"></a>仮想マシン

- [VM insights の有効化の概要](vm/vminsights-enable-overview.md) - サポートされているリージョンを追加しました。

VM insights のゲストの正常性 (プレビュー) に関する新しい記事

- [VM insights のゲストの正常性 (プレビュー)](vm/vminsights-health-overview.md)
- [VM insights のゲストの正常性アラート (プレビュー)](vm/vminsights-health-alerts.md)
- [VM insights のゲストの正常性 (プレビュー) での監視を構成する](vm/vminsights-health-configure.md)
- [データ収集ルールを使用して VM insights のゲストの正常性 (プレビュー) での監視を構成する](vm/vminsights-health-configure-dcr.md)
- [VM insights のゲストの正常性 (プレビュー) の有効化](vm/vminsights-health-enable.md)
- [VM insights のゲストの正常性 (プレビュー) のトラブルシューティング](vm/vminsights-health-troubleshoot.md)





## <a name="october-2020"></a>2020 年 10 月

### <a name="general"></a>全般
- [Azure Monitor API の提供終了](logs/operationalinsights-api-retirement.md) - 新しい記事。

### <a name="agents"></a>エージェント
- [Azure Monitor によって監視される内容](monitor-reference.md) - エージェントに関するセクションを追加しました。

### <a name="alerts"></a>警告
- [Azure portal でのアクション グループの作成および管理](alerts/action-groups.md) - サービス タグに関するセクションを追加しました。
- [メトリック アラート用の Resource Manager テンプレート サンプル](alerts/resource-manager-alerts-metric.md) - コンテンツの一致パラメーターとテストの場所を追加しました。
- [Azure メトリック アラートのトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - ルールの構成に関するベスト プラクティスを追加しました。

### <a name="application-insights"></a>Application Insights
- [Application Insights JavaScript SDK の Angular プラグイン](app/javascript-angular-plugin.md) - 新しい記事。
- [ASP.NET Core アプリケーション用の Azure Application Insights](app/asp-net-core.md) - ILogger ログに関する FAQ を追加しました。
- [Azure Application Insights を使用して ASP.NET に監視を構成する](app/asp-net.md) - 記事を改訂しました。
- [Azure Application Insights のログベースのメトリックと事前に集計されたメトリック](app/pre-aggregated-metrics-log-metrics.md) - 事前に集計されたメトリックを含むテーブルを追加しました。
- [任意の Web サイトの可用性と応答性を監視する](app/monitor-web-app-availability.md) - 位置情報の作成タグに関するセクションを追加しました。
- [任意の場所の Java アプリケーションを監視する - Azure Monitor Application Insights](app/java-standalone-config.md) - 構成例を追加しました。
- [任意の場所の Java アプリケーションを監視する - Azure Monitor Application Insights](app/java-standalone-telemetry-processors.md) - 新しい記事。
- [Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける](app/change-analysis.md) - 仮想マシンとアクティビティ ログに関するセクションを追加しました。
  
### <a name="autoscale"></a>自動スケール
- [Azure での自動スケーリングの使用](autoscale/autoscale-get-started.md) - 別のリージョンへの自動スケーリングの移動に関するセクションを追加しました。

### <a name="containers"></a>コンテナー
- [Container insights で PV の監視を構成する](containers/container-insights-persistent-volumes.md) - 新しい記事。
- [Container insights エージェントを管理する方法](containers/container-insights-manage-agent.md) - Azure Arc 対応 Kubernetes クラスターのサポートを追加しました。
- [Container insights のメトリック アラート](containers/container-insights-metric-alerts.md) - Azure Arc 対応 Kubernetes クラスターのサポートを追加しました。

### <a name="insights-and-solutions"></a>分析情報とソリューション
- [IT Service Management Connector - Azure Monitor のセキュア エクスポート](alerts/it-service-management-connector-secure-webhook-connections.md) - ServiceNow に関するセクションを追加しました。

### <a name="logs"></a>ログ
- [ロジック アプリを使用して Log Analytics ワークスペースから Azure ストレージにデータをアーカイブする](logs/logs-export-logic-app.md) - 新しい記事。
- [Azure Monitor の Log Analytics ワークスペースのデータ エクスポート (プレビュー)](logs/logs-data-export.md) - イベント ハブを対象とした REST 要求のサンプル本文を追加しました。
- [Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md) - Azure Monitor ログと Azure Security Center の課金の関係に関する情報を追加しました。 ノードごとの価格レベルを使用する場合のノード数のクエリを追加しました。 
- [Azure Monitor で Log Analytics ワークスペースの正常性を監視する](logs/monitor-workspace.md) - 新しい記事。
- [Azure Data Explorer を使用して Azure Monitor のデータのクエリを実行する (プレビュー)](logs/azure-data-explorer-monitor-proxy.md) - 新しい記事。
- [Azure Data Explorer を使用して Azure Monitor からエクスポートされたデータのクエリを実行する (プレビュー)](logs/azure-data-explorer-query-storage.md) - 新しい記事。

### <a name="networks"></a>ネットワーク
- [Azure Monitor for Networks プレビュー](insights/network-insights-overview.md) - トラブルシューティングのセクションを追加しました。 接続に関するセクションを追加しました。

### <a name="platform-logs"></a>プラットフォーム ログ
- [Azure アクティビティ ログのイベント スキーマ](essentials/activity-log-schema.md) - 重大度レベルの説明を追加しました。

### <a name="virtual-machines"></a>仮想マシン
- [Azure Monitor for VMs での変更分析](vm/vminsights-change-analysis.md) - 新しい記事。
- [Azure Monitor for VMs の有効化の概要](vm/vminsights-enable-overview.md) - サポートされているリージョンを追加しました。
- [メトリックのために Container insights を更新する方法](containers/container-insights-update-metrics.md) - Azure Arc 対応 Kubernetes クラスターのサポートを追加しました。



## <a name="september-2020"></a>2020 年 9 月

### <a name="general"></a>全般
- [Azure Monitor についてよくあるご質問](faq.md) - OpenTelemetry に関するセクションを追加しました。

### <a name="agents"></a>エージェント
- [Azure Monitor エージェントの概要](agents/azure-monitor-agent-overview.md) - 新しいエージェントに切り替える際の決定要因を追加しました。
- [Azure 監視エージェントの概要](agents/agents-overview.md) - Windows 10 のサポートを追加しました。

### <a name="alerts"></a>警告
- [Azure Resource Manager テンプレートでログ アラートを作成する](alerts/alerts-log-create-templates.md) - 新しい記事。
- [Azure のメトリック アラートのトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - メトリック アラート ルールの ARM テンプレートのエクスポートに関するセクションを追加しました。

### <a name="application-insights"></a>Application Insights
- [Azure Monitor Application Insights の新しいワークスペースベースのリソースを作成する](app/create-workspace-resource.md) - プレビューの名称を削除しました。
- [Azure Application Insights でのデータ保持と保存](app/data-retention-privacy.md) - Mac と Linux のデータ損失保護に対する新しいサポートについて詳細を追加しました。
- [Application Insights のイベント カウンター](app/eventcounters.md) - 既定で収集されるカウンターに関する注意を追加しました。
- [Application Insights のログベースのメトリックと事前に集計されたメトリック](app/pre-aggregated-metrics-log-metrics.md) - プレビューの名称を削除しました。
- [Azure Monitor Application Insights のクラシック リソースをワークスペースベースのリソースに移行する](app/convert-classic-resource.md) - 新しい記事。
- [Java アプリケーションを任意の環境で監視する - Azure Monitor Application Insights](app/java-in-process-agent.md) - 新しいプレビュー バージョンのエージェントに合わせて更新しました。
- [Azure Application Insights を使用した ASP.NET の Web アプリ分析を設定する](app/asp-net.md) - 記事を改訂しました。
- [Azure Application Insights におけるテレメトリ チャネル](app/telemetry-channels.md) - Mac と Linux のデータ損失保護に対する新しいサポートに関する詳細を追加しました。
- [Azure Application Insights スナップショット デバッガーのトラブルシューティング](app/snapshot-debugger-troubleshoot.md) - スナップショット デバッガーのトラブルシューティングに SSL セクションを追加しました。
- [Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける](app/change-analysis.md) - 仮想マシンとアクティビティ ログを追加しました。


### <a name="containers"></a>コンテナー
- [Container insights を使用して Azure Arc 対応 Kubernetes クラスターを構成する](containers/container-insights-enable-arc-enabled-clusters.md) - サービス プリンシパルを使用して監視を有効にする場合のガイダンスを追加しました。
- [Container insights によるデプロイと HPA メトリック](containers/container-insights-deployment-hpa-metrics.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション
- [Azure Monitor for Azure Cache for Redis](insights/redis-cache-insights-overview.md) - プレビューの名称を削除しました。
- [Azure Monitor for Networks (プレビュー)](insights/network-insights-overview.md) - 接続とトラフィックのセクションを追加しました。
- [IT Service Management Connector - Azure Monitor のセキュア エクスポート](alerts/it-service-management-connector-secure-webhook-connections.md) - 新しい記事。
- [Azure Monitor の IT Service Management Connector](alerts/itsmc-connections.md) - Cherwell と Provance の ITSM 統合に関する注意を追加しました。
- [Azure Monitor for Key Vault でキー コンテナーを監視する](insights/key-vault-insights-overview.md) - プレビューの名称を削除しました。

### <a name="logs"></a>ログ
- [Azure Monitor ログ クエリでの監査クエリ](logs/query-audit.md) - 新しい記事。
- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - カスタマー ロックボックスを追加しました。
- [Azure Monitor ログ専用クラスター](logs/logs-dedicated-clusters.md) - 新しい記事。
- [Azure Monitor ログのデプロイの設計](logs/design-logs-deployment.md) - スケールとインジェスト ボリューム レートの制限に関するセクションを更新しました。
- [Azure Monitor Log Analytics のログ クエリのスコープ](logs/scope.md) - ワークスペースベースのアプリケーションが含まれるよう更新しました。
- [Azure Monitor のログ](logs/data-platform-logs.md) - ワークスペースベースのアプリケーションが含まれるよう更新しました。
- [Azure Monitor ログ レコード内の標準列](logs/log-standard-columns.md) - ワークスペースベースのアプリケーションが含まれるよう更新しました。
- [Azure Monitor サービスの制限](service-limits.md) - ユーザー クエリの調整に関する制限を更新しました。
- [Azure Monitor Log Analytics でのカスタマー マネージド ストレージ アカウントの使用](logs/private-storage.md) - 記事を改訂しました。
- [Azure Log Analytics におけるデータの表示と分析](./logs/data-platform-logs.md) - ワークスペースベースのアプリケーションが含まれるよう更新しました。


### <a name="platform-logs"></a>プラットフォーム ログ
- [Azure アクティビティ ログのイベント スキーマ - Azure Monitor](essentials/activity-log-schema.md) - 重大度レベルを追加しました。
- [診断設定用の Resource Manager テンプレートのサンプル](essentials/resource-manager-diagnostic-settings.md) - Azure ストレージ アカウントのサンプルを追加しました。

### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックのグラフの視覚化](visualize/workbooks-chart-visualizations.md) - 新しい記事。
- [Azure Monitor ブックの複合棒グラフ レンダラー](visualize/workbooks-composite-bar.md) - 新しい記事。
- [Azure Monitor ブックのグラフの視覚化](visualize/workbooks-graph-visualizations.md) - 新しい記事。
- [Azure Monitor ブックのグリッドの視覚化](visualize/workbooks-grid-visualizations.md) - 新しい記事。
- [Azure Monitor ブックの蜂の巣の視覚化](visualize/workbooks-honey-comb.md) - 新しい記事。
- [Azure Monitor ブックのテキストの視覚化](visualize/workbooks-text-visualizations.md) - 新しい記事。
- [Azure Monitor ブックのタイルの視覚化](visualize/workbooks-tile-visualizations.md) - 新しい記事。
- [Azure Monitor ブックのツリーの視覚化](visualize/workbooks-tree-visualizations.md) - 新しい記事。




## <a name="august-2020"></a>2020 年 8 月

### <a name="general"></a>全般

- [Azure Monitor によって監視される内容](monitor-reference.md) - Azure Monitor エージェントを含むように更新されました。


### <a name="agents"></a>エージェント
- [Azure Monitor エージェントの概要](agents/azure-monitor-agent-overview.md) - 新しい記事。
- [ハイブリッド環境向けに Azure Monitor を有効にする](vm/vminsights-enable-hybrid.md) - 依存関係エージェントのバージョンが更新されました。
- [Azure 監視エージェントの概要](agents/agents-overview.md) - Azure Monitor エージェントを追加し、サポートされている OS の表をまとめました。


#### <a name="new-and-updated-articles-from-restructure-of-agent-content"></a>エージェントに関するコンテンツの再構成による新規および更新された記事
- [VM insights の有効化の概要](vm/vminsights-enable-overview.md)
- [Linux コンピューターに Log Analytics エージェントをインストールする](agents/agent-linux.md)
- [Windows コンピューターに Log Analytics エージェントをインストールする](agents/agent-windows.md)
- [Log Analytics エージェントの概要](agents/log-analytics-agent.md)

### <a name="application-insights"></a>Application Insights
- [JavaScript Web アプリのための Azure Application Insights](app/javascript.md) - 追加したセクションでは、クライアントとサーバーの相関関係と、CORS の相関関係の構成を明確にしました。
- [Azure Monitor Application Insights の新しいワークスペースベースのリソースを作成する](app/create-workspace-resource.md) - ワークスペースベースのアプリケーションによって提供される機能を追加しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - ライブ メトリックス ストリームの IP アドレスを更新しました。
- [Java アプリケーションを任意の環境で監視する - Azure Monitor Application Insights](app/java-in-process-agent.md) - サポートされるカスタム テレメトリの表を追加しました。
- [Application Insights JavaScript SDK の Native React プラグイン](app/javascript-react-native-plugin.md) - 新しい記事。
- [Application Insights JavaScript SDK の React プラグイン](app/javascript-react-plugin.md) - 新しい記事。
- [Application Insights の監視機能を備えた Azure Functions アプリを作成するための Resource Manager テンプレートのサンプル](app/resource-manager-function-app.md) - 新しい記事。
- [Application Insights 監視機能を備えた Azure App Service Web アプリを作成するための Resource Manager テンプレート サンプル](app/resource-manager-web-app.md) - 新しい記事。
- [Azure Application Insights による利用状況分析](app/usage-overview.md) - ビデオを追加しました。

### <a name="autoscale"></a>自動スケール
- [Azure での自動スケーリングの使用](autoscale/autoscale-get-started.md) - App Service の正常なインスタンスへのルーティングに関するセクションを追加しました。

### <a name="data-collection"></a>データ コレクション
- [Azure Monitor エージェント用のデータ収集の構成 (プレビュー)](agents/data-collection-rule-azure-monitor-agent.md) - 新しい記事。
- [Azure Monitor のデータ収集ルール (プレビュー)](agents/data-collection-rule-overview.md) - 新しい記事。


### <a name="containers"></a>Containers
- [Container insights によるデプロイと HPA メトリック](containers/container-insights-deployment-hpa-metrics.md) - 新しい記事。

### <a name="insights"></a>洞察
- [Azure Monitor の監視ソリューション](insights/solutions.md) - 新しい UI について更新しました。
- [Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor.md) - サポートされるワークスペース リージョンを追加しました。


### <a name="logs"></a>ログ
- [Azure Monitor に関する FAQ](faq.md) - ワークスペースからデータを削除するためのエントリが追加されました。 502 および 503 応答に関するエントリを追加しました。
  - [Azure Monitor ログのデプロイの設計](logs/design-logs-deployment.md) - インジェスト ボリューム レートの制限に関するセクションを更新しました。
- [Azure Monitor ログの使用状況とコストの管理](logs/manage-cost-storage.md) - 使用状況クエリをより効率的なクエリ形式に更新しました。
- [Azure Monitor でログ クエリを最適化する](logs/query-optimization.md) - パフォーマンス指標に固有の値を追加しました。
- [診断設定用の Resource Manager テンプレートのサンプル](essentials/resource-manager-diagnostic-settings.md) - ログ クエリの監査ログのサンプルを追加しました。


### <a name="platform-logs"></a>プラットフォーム ログ
- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](essentials/diagnostic-settings.md) - 診断設定のリージョン要件を追加しました。

### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックの概要](visualize/workbooks-overview.md) - ビデオを追加しました。
- [Azure ブック テンプレートを別のリージョンに移動する](visualize/workbook-templates-move-region.md) - 新しい記事。
- [Azure ブックを別のリージョンに移動する](visualize/workbooks-move-region.md) - 新しい記事。



## <a name="july-2020"></a>2020 年 7 月

### <a name="general"></a>全般
- [Azure Monitor をデプロイする](deploy-scale.md) - VM insights のオンボード コンテンツの再構築。
- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](logs/private-link-security.md) - 制限に関してセクションを追加しました。

### <a name="alerts"></a>警告
- [Azure Monitor アラートのアクション ルール](alerts/alerts-action-rules.md) - CLI プロセスを追加しました。
- [Azure portal でのアクション グループの作成および管理](alerts/action-groups.md) - UI の変更を反映して更新しました。
- [Azure Monitor Log Analytics のサンプル クエリ](logs/example-queries.md) - 新しい記事。
- [Azure Monitor のログ アラートのトラブルシューティング](alerts/alerts-troubleshoot-log.md) - アラート ルール クォータに関するセクションを追加しました。
- [Azure メトリック警告のトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - まだ生成されていないカスタム メトリックに対するアラート ルールに関するセクションを追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](alerts/alerts-metric-overview.md) - 集計の粒度を選択するための推奨事項を追加しました。

### <a name="application-insights"></a>Application Insights
- [Azure Web アプリ拡張機能のリリース ノート - Application Insights](app/web-app-extension-release-notes.md) - 新しい記事。
- [Application Insights リソース用の Resource Manager テンプレートのサンプル](app/resource-manager-app-resource.md) - 新しい記事。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - ASP.NET Core アプリのプロファイラーを Azure App Service で実行するバグに関する注意を追加しました。 

### <a name="containers"></a>Containers
- [Container insights からのログ アラート](containers/container-insights-log-alerts.md) - 新しい記事。
- [Container insights からのメトリック アラート](containers/container-insights-metric-alerts.md) - 新しい記事。

### <a name="logs"></a>ログ
- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - エラー メッセージとクエリの CMK 構成のセクションを追加しました。
- [Azure Monitor HTTP データ コレクター API](logs/data-collector-api.md) - Python 3 のサンプルを追加しました。
- [Azure Monitor でログ クエリを最適化する](logs/query-optimization.md) - サブクエリを使用するときの複数のデータ スキャンの回避に関するセクションを追加しました。
- [チュートリアル:Log Analytics クエリの使用方法](./logs/log-analytics-tutorial.md) - ビデオを追加しました。

### <a name="platform-logs"></a>プラットフォーム ログ
- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](essentials/diagnostic-settings.md) - ビデオを追加しました。
- [Azure Monitor 用の Resource Manager テンプレートのサンプル](/resource-manager-samples.md) - ログの送信先の種類を使用する ARM サンプルを追加しました。 

### <a name="solutions"></a>ソリューション
- [Azure Monitor での監視ソリューション](insights/solutions.md) - CLI プロセスを追加しました。
- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 提供終了日を変更しました。

### <a name="virtual-machines"></a>仮想マシン

VM insights に関するコンテンツの再構成による新規および更新された記事

- [VM insights とは](vm/vminsights-overview.md)
- [VM insights の Log Analytics ワークスペースを構成する](vm/vminsights-configure-workspace.md)
- [Linux コンピューターを Azure Monitor に接続する](agents/agent-linux.md)
- [ハイブリッド環境で Azure Monitor を有効にする](vm/vminsights-enable-hybrid.md)
- [Azure portal で 1 つの仮想マシンまたは仮想マシン スケール セットで Azure Monitor を有効にする](vm/vminsights-enable-portal.md)
- [Azure Policy を使用して VM insights を有効にする](./vm/vminsights-enable-policy.md)
- [VM insights の有効化の概要](vm/vminsights-enable-overview.md)
- [PowerShell を使用して VM insights を有効にする](vm/vminsights-enable-powershell.md)
- [Resource Manager テンプレートを使用して VM insights を有効にする](vm/vminsights-enable-resource-manager.md)
- [PowerShell またはテンプレートを使用して VM insights を有効にする](./vm/vminsights-enable-powershell.md)


### <a name="visualizations"></a>視覚化
- [Log Analytics ダッシュボードの視覚化のアップグレード](logs/dashboard-upgrade.md) - 更新間隔を更新しました。
- [Azure Monitor からのデータを視覚化する](visualizations.md) - ビデオを追加しました。


## <a name="june-2020"></a>2020 年 6 月

### <a name="general"></a>全般
- [Azure Monitor をデプロイする](deploy-scale.md) - 新しい記事。
- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - billingtype プロパティを更新しました。 PowerShell コマンドを追加しました。

### <a name="agents"></a>エージェント
- [Log Analytics エージェントの概要](agents/log-analytics-agent.md) - Python 2 要件を追加しました。

### <a name="alerts"></a>警告
- [ターゲット リソースが別の Azure リージョンに移動されるときにアラート ルールまたはアクション ルールを更新する方法](alerts/alerts-resource-move.md) - 新しい記事。
- [Azure メトリック警告のトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - 新しい記事。
- [Azure Monitor におけるログ アラートのトラブルシューティング](alerts/alerts-troubleshoot-metric.md) - 新しい記事。
  
### <a name="application-insights"></a>Application Insights
- [JavaScript Web アプリのための Azure Application Insights](app/javascript.md) - JavaScript SDK セクションを更新。 読み込みエラーを報告するようにスニペットを更新しました。
- [BYOS (Bring Your Own Storage) を Profiler および Visual Studio スナップショット デバッガー用に構成する](app/profiler-bring-your-own-storage.md) - 新しい記事。
- [OpenCensus Python を使用した Azure Application Insights における受信要求の追跡](app/opencensus-python-request.md) - OpenCensus のログと構成を更新しました。
- [Azure Application Insights を使用してライブ ASP.NET Web アプリを監視する](app/monitor-performance-live-website-now.md) - Status Monitor v1 が非推奨になる日付を更新しました。
- [Azure Application Insights を使用して Node.js サービスを監視する](app/nodejs.md) - 以前のバージョンからの移行と SDK 構成含む複数の更新
- [Azure Monitor を使用して Python アプリを監視する (プレビュー)](app/opencensus-python.md) - Azure Monitor エクスポーターの構成に関するセクションを追加しました。
- [コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション](app/codeless-overview.md) - 新しい記事。
- [JavaScript Web アプリの SDK 読み込みエラーのトラブルシューティング](app/javascript-sdk-load-failure.md) - 新しい記事。

### <a name="containers"></a>Containers
- [お使いのハイブリッド Kubernetes クラスターの監視を停止する方法](containers/container-insights-optout-hybrid.md) - Arc 対応 Kubernetes に関するセクションを追加しました。
- [Azure Arc 対応 Kubernetes クラスターに Container insights を構成する](containers/container-insights-enable-arc-enabled-clusters.md) - 新しい記事。
- [Container insights を使用して Azure Red Hat OpenShift v4.x を構成する](containers/container-insights-azure-redhat4-setup.md) - 前提条件を更新しました。
- [Container insights のライブ データ (プレビュー) の設定](containers/container-insights-livedata-setup.md) - Azure US Government で利用できない機能に関する注を削除しました。

### <a name="insights"></a>洞察
- [FAQ - Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor-faq.md) - ExpressRoute モニターの FAQ を追加しました。

### <a name="logs"></a>ログ
- [Azure Log Analytics ワークスペースの削除と復旧](logs/delete-workspace.md) - PowerShell コマンドを追加しました。 トラブルシューティングを更新しました。
- [Azure Monitor で Log Analytics ワークスペースを管理する](logs/manage-access.md) - Azure RBAC セクションに許可されないテーブルの例を追加しました。
- [Azure Monitor ログの使用量とコストを管理する](logs/manage-cost-storage.md) - データ サイズの計算に関する詳細を追加しました。 データ ボリューム アラートの構成について更新しました。 Azure Sentinel によって収集されるセキュリティ データの詳細。 データ上限の明確化。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](logs/logicapp-flow-connector.md) - コネクタの制限を追加しました。

### <a name="metrics"></a>メトリック
- [Azure Monitor でサポートされているメトリック (リソースの種類別)](essentials/metrics-supported.md) - SQL Server のメトリックを更新しました。


### <a name="platform-logs"></a>プラットフォーム ログ

- [診断設定用の Resource Manager テンプレートのサンプル](essentials/resource-manager-diagnostic-settings.md) - アクティビティ ログの診断設定を修正します。
- [Azure portal を使用して Azure アクティビティ ログを Log Analytics ワークスペースに送信する](essentials/quick-collect-activity-log-portal.md) - 新しい記事。
- [Azure Resource Manager テンプレートを使用して Azure アクティビティ ログを Log Analytics ワークスペースに送信する](essentials/quick-collect-activity-log-arm.md) - 新しい記事。

プラットフォーム ログ コンテンツの再構築と統合による新規および更新された記事

- [Azure リソース ログをストレージ アカウントにアーカイブする](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure アクティビティ ログのイベント スキーマ](essentials/activity-log-schema.md)
- [Azure アクティビティ ログ](essentials/activity-log.md)
- [Azure Monitor CLI のサンプル](/cli-samples.md)
- [Azure Monitor PowerShell のサンプル](/powershell-samples.md)
- [Azure 監視 REST API のチュートリアル](essentials/rest-api-walkthrough.md)
- [Azure リソース ログでサポートされているサービスとスキーマ](./essentials/resource-logs-schema.md)
- [Azure リソース ログ](essentials/resource-logs.md)
- [Azure Monitor での Azure アクティビティ ログの収集と分析](./essentials/activity-log.md)
- [Log Analytics ワークスペースで Azure リソース ログを収集する](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](essentials/diagnostic-settings.md)
- [Azure アクティビティ ログをエクスポートする](./essentials/activity-log.md#legacy-collection-methods)
- [Azure プラットフォーム ログの概要](essentials/platform-logs-overview.md)
- [Azure プラットフォーム ログをイベント ハブにストリーム配信する](./essentials/resource-logs.md#send-to-azure-event-hubs)
- [Azure Monitor で Azure アクティビティ ログ イベントを表示する](./essentials/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>仮想マシン
- [Azure portal 内で VM insights を有効にする](./vm/vminsights-enable-portal.md) - Azure Arc を含めるように更新しました。
- [VM insights の有効化の概要](vm/vminsights-enable-overview.md) - Azure Arc を含めるように更新しました。
- [VM insights とは](vm/vminsights-overview.md) - Azure Arc を含めるように更新しました。


### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックのデータ ソース](visualize/workbooks-data-sources.md) - アラートとカスタム エンドポイントのセクションを追加しました。
- [Azure Monitor のブックベースの分析情報のトラブルシューティング](insights/troubleshoot-workbooks.md) - 新しい記事。
- [Log Analytics ダッシュボードの視覚化のアップグレード](logs/dashboard-upgrade.md) - 新しい記事。



## <a name="may-2020"></a>2020 年 5 月

### <a name="general"></a>全般

- [Azure Monitor についてよくあるご質問](faq.md) - メトリックのセクションを追加しました。
- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - 一般提供に向けたさまざまな変更。
- [Azure Monitor 用の Azure Policy 組み込みポリシー定義](./policy-reference.md) - 新しい記事。
- [ログ取り込み用の顧客所有のストレージ アカウント](logs/private-storage.md) - 新しい記事。
- [Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md) - クラスターの比例課金を追加しました。
- [Azure Private Link を使用して、ネットワークを Azure Monitor に安全に接続する](logs/private-link-security.md) - 新しい記事。


#### <a name="new-resource-manager-template-samples"></a>新しい Resource Manager テンプレートのサンプル 
- [Azure Monitor 用の Resource Manager テンプレートのサンプル](/resource-manager-samples.md)
- [アクション グループ用の Resource Manager テンプレートのサンプル](alerts/resource-manager-action-groups.md)
- [エージェント用の Resource Manager テンプレートのサンプル](agents/resource-manager-agent.md)
- [Container insights 用の Resource Manager テンプレートのサンプル](containers/resource-manager-container-insights.md)
- [VM insights 用の Resource Manager テンプレートのサンプル](vm/resource-manager-vminsights.md)
- [診断設定用の Resource Manager テンプレートのサンプル](essentials/resource-manager-diagnostic-settings.md)
- [Log Analytics ワークスペース用の Resource Manager テンプレートのサンプル](logs/resource-manager-workspace.md)
- [ログ クエリ用の Resource Manager テンプレートのサンプル](logs/resource-manager-log-queries.md)
- [ログ クエリ アラート ルール用の Resource Manager テンプレートのサンプル](alerts/resource-manager-alerts-log.md)
- [メトリック アラート ルール用の Resource Manager テンプレートのサンプル](alerts/resource-manager-alerts-metric.md)
- [ブック用の Resource Manager テンプレートのサンプル](visualize/resource-manager-workbooks.md)

### <a name="agents"></a>エージェント
- [Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](agents/diagnostics-extension-windows-install.md) - 診断構成の詳細を追加しました。
- [Log Analytics エージェントの概要](agents/log-analytics-agent.md) - サポートされている Linux バージョンを追加しました。

### <a name="application-insights"></a>Application Insights

- [Azure Monitor Application Insights で Azure Functions を監視する](app/monitor-functions.md) - 新しい記事。
- [Application Insights を使用して Node.js サービスとアプリを監視する](app/nodejs.md) - 以前のバージョンからの移行に関する新しいセクションを含む一般的な更新。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Webhook と米国政府用の IP アドレスを追加しました。
- [Application Insights を使用した Azure Kubernetes Service (AKS) 上のアプリケーションの監視 - Azure Monitor](app/kubernetes-codeless.md) - 新しい記事。
- [データが存在しない場合のトラブルシューティング - Application Insights for .NET](app/asp-net-troubleshoot-no-data.md) - dotnet-trace を使用したログの収集に関するセクションを追加しました。
- [Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける](app/change-analysis.md) - 変更分析機能の複数の更新。

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>ワークスペースベースのアプリケーションのプレビューに関する新規および更新された記事
- [Azure Monitor Application Insights のワークスペースベースのリソース スキーマ](app/apm-tables.md)
- [Azure Monitor Application Insights の新しいワークスペース ベースのリソースを作成する](app/create-workspace-resource.md)
- [Azure Monitor ログ クエリでの app() 式](logs/app-expression.md)
- [Azure Monitor Log Analytics のログ クエリのスコープ](logs/scope.md)
- [Azure Monitor でのリソース間のクエリ](logs/cross-workspace-query.md)
- [Azure Monitor ログ レコードの標準プロパティ](./logs/log-standard-columns.md)
- [Azure Monitor Logs の構造](./logs/data-platform-logs.md)





### <a name="containers"></a>Containers
- [Container insights を有効にする方法](containers/container-insights-onboard.md) - ファイアウォール構成テーブルを更新しました。
- [メトリック用に Container insights を更新する方法](containers/container-insights-update-metrics.md) - メトリックを収集するためにマネージド ID を使用する方法を更新しました。
- [Container insights の監視コスト](containers/container-insights-cost.md) - 新しい記事。
- [Container insights のライブ データ (プレビュー) の設定](containers/container-insights-livedata-setup.md) - 新しいクラスターの役割のバインドのサポート。

### <a name="insights"></a>洞察
- [Azure Monitor for Azure Cache for Redis (プレビュー)](insights/redis-cache-insights-overview.md) - 新しい記事。
- [Azure Monitor for Key Vault (プレビュー) によるキー コンテナーの監視](./insights/key-vault-insights-overview.md) - 新しい記事。

### <a name="logs"></a>ログ
- [PowerShell を使用して Log Analytics を作成および構成する](logs/powershell-workspace-configuration.md) - トラブルシューティング セクションを追加しました。
- [Azure portal で Log Analytics ワークスペースを作成する](logs/quick-create-workspace.md) - トラブルシューティング セクションを追加しました。
- [Azure CLI を使用して Log Analytics ワークスペースを作成する](logs/quick-create-workspace-cli.md) - トラブルシューティング セクションを追加しました。
- [Azure Log Analytics ワークスペースの削除と復旧](logs/delete-workspace.md) - 削除されたワークスペースの復旧に関する情報を更新しました。
- [Azure Monitor ログ クエリにおける関数](logs/functions.md) - 他の関数が含まれていない関数についての注を削除しました。
- [Azure Monitor Logs の構造](./logs/data-platform-logs.md) - Application Insights テーブルのプロパティの説明をわかりやすくしました。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](logs/logicapp-flow-connector.md) - 制限セクションを追加しました。
- [PowerShell を使用して Log Analytics ワークスペースを作成および構成する](logs/powershell-workspace-configuration.md) - トラブルシューティング セクションを追加しました。


### <a name="metrics"></a>メトリック
- [Azure Monitor でサポートされているメトリック (リソースの種類別)](essentials/metrics-supported.md) - ゲスト メトリックとメトリック ルーティングを明確にしました。 

### <a name="solutions"></a>ソリューション
- [Azure Monitor で Active Directory の環境を最適化する](insights/ad-assessment.md) - サポートされているバージョンに Windows Server 2019 を追加しました。
- [Azure Monitor で SQL Server の環境を最適化する](insights/sql-assessment.md) - サポートされている SQL Server のバージョンに追加しました。


### <a name="virtual-machines"></a>仮想マシン
- [VM insights の有効化の概要](vm/vminsights-enable-overview.md) - サポートされている Ubuntu Server のバージョンに追加しました。 Log Analytics ワークスペースにサポートされるリージョンを追加しました。
- [VM insights を使用してパフォーマンスをグラフ化する方法](vm/vminsights-performance.md) - 使用できないメトリックについての制限事項セクションを追加しました。

### <a name="visualizations"></a>視覚化
- [Azure Monitor ブックと Azure Resource Manager テンプレート](visualize/workbooks-automate.md) - ブック テンプレートをデプロイするための Resource Manager を追加しました。
- [Azure Monitor のブック グループ](./visualize/workbooks-groups.md) - 新しい記事。
- [Azure Monitor ブック - JSONPath を使用して JSON データを変換する](visualize/workbooks-jsonpath.md) - 新しい記事。


## <a name="april-2020"></a>2020 年 4 月

### <a name="general"></a>全般

- [Azure Monitor のカスタマー マネージド キー](logs/customer-managed-keys.md) - 非同期操作に関するセクションを追加しました
- [Azure Monitor で Log Analytics ワークスペースを管理する](logs/manage-access.md) - カスタム ログのセクションを更新しました。

### <a name="alerts"></a>警告

- [Azure Monitor アラートのアクション ルール](alerts/alerts-action-rules.md) - ビデオを追加しました。
- [Azure でのアラートと通知監視の概要](alerts/alerts-overview.md) - ビデオを追加しました。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights のアプリケーション マップ](app/app-map.md) - java エージェントのクラウド ロール名の構成を追加しました。
- [Azure Application Insights .NET エージェント API リファレンス](app/status-monitor-v2-api-reference.md) - API リファレンスを統合しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - App Insights と Log Analytics の API、アクショングループ webhook、Azure US Government の IP アドレスを更新しました。
- [任意の場所の Java アプリケーションの監視](app/java-standalone-config.md) - 新しい記事
- [任意の場所の Java アプリケーションを監視する](app/java-in-process-agent.md) - 新しい記事。
- [任意の環境で実行されている Java アプリケーションを監視する](app/java-standalone-arguments.md) - 新しい記事。
- [オンプレミスで実行されている Java アプリケーションの監視](app/java-on-premises.md) - 新しい記事。
- [Visual Studio での Application Insights の削除](app/remove-application-insights.md) - 新しい記事。
- [Azure Application Insights でのテレメトリ サンプリング](app/sampling.md) - Python の fixed-rate サンプルを修正。

### <a name="containers"></a>Containers

- [Container insights を使用して Azure Red Hat OpenShift v4.x を構成する](containers/container-insights-azure-redhat4-setup.md) - 新しい記事。
- [ServiceNow の同期に関する問題を手動で修正する方法](alerts/itsmc-resync-servicenow.md) - 新しい記事。
- [お使いの Azure および Red Hat OpenShift v4 クラスターの監視を停止する方法](containers/container-insights-optout-openshift-v4.md) - 新しい記事。
- [お使いの Azure Red Hat OpenShift v3 クラスターの監視を停止する方法](containers/container-insights-optout-openshift-v3.md) - 新しい記事。
- [お使いのハイブリッド Kubernetes クラスターの監視を停止する方法](containers/container-insights-optout-hybrid.md) - 新しい記事。

### <a name="insights"></a>洞察

- [Azure Monitor for Key Vault (プレビュー) による Azure キー コンテナーの監視](insights/key-vault-insights-overview.md) - 新しい記事。

### <a name="logs"></a>ログ

- [Azure Monitor サービスの制限](service-limits.md) - ユーザー クエリの調整を追加しました。
- [Azure Monitor ログの使用量とコストを管理する](logs/manage-cost-storage.md) - ログ クラスターの課金を追加しました。 Kusto クエリが追加されました。これにより、ノードごとのレガシ価格レベルを使用するお客様は、GB 単位または容量予約のレベルに移行する必要があるかどうかを判断できます。

### <a name="metrics"></a>メトリック

- [Azure メトリックス エクスプローラーの高度な機能](essentials/metrics-charts.md) - 集計のセクションを追加しました。

### <a name="workbooks"></a>Workbooks

- [Azure Monitor ブックと Azure Resource Manager テンプレート](visualize/workbooks-automate.md) - ブック テンプレートをデプロイするための Resource Manager テンプレートを追加しました。

## <a name="march-2020"></a>2020 年 3 月

### <a name="general"></a>全般

- [Azure Monitor の概要](overview.md) - Azure Monitor の概要ビデオを追加しました。
- [Azure Monitor のカスタマー マネージド キーの構成](logs/customer-managed-keys.md) - 全般的な更新。
- [Azure Monitor データ参照](/azure/azure-monitor/reference/) - 新しいサイト。

### <a name="alerts"></a>警告

- [Azure Monitor でアクティビティ ログ アラートを作成、表示、管理する](alerts/alerts-activity-log.md) -Resource Manager テンプレートの説明を追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](alerts/alerts-metric-overview.md) - 政府機関のサポート向けに更新されました。
- [Azure Monitor のアラートと通知のトラブルシューティング](alerts/alerts-troubleshoot.md) - 新しい記事。

### <a name="application-insights"></a>Application Insights

- [PowerShell での Azure Application Insights の自動化](app/powershell.md) - ARMClient の例を追加しました。
- [Application Insights からのテレメトリの連続エクスポート](app/export-telemetry.md) - エクスポート構造の詳細を含むテーブルを追加します。
- [Azure App Service で .NET アプリでスナップショット デバッガーを有効にする](app/snapshot-debugger-appservice.md) Resource Manager テンプレートの例を追加しました。
- [Azure Application Insights の使用量とコストを管理する](app/pricing.md) - データ上限アラートに関する情報を追加しました。
- [Azure Monitor を使用して Python アプリケーションを監視する (プレビュー)](app/opencensus-python.md) - 標準メトリックを追加しました。
- [JavaScript アプリケーションのソース マップのサポート - Azure Monitor Application Insights](app/source-map-support.md) - 新しい記事。

### <a name="containers"></a>Containers

- [Azure Monitor に関する FAQ](faq.md) - Container insights について更新しました。
- [Container insights で GPU の監視を構成する](containers/container-insights-gpu-monitoring.md) - 新しい記事。

### <a name="insights"></a>洞察

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 非推奨となる日が更新されました。

### <a name="logs"></a>ログ

- [Azure Monitor でログ クエリを最適化する](logs/query-optimization.md) - XML および JSON 解析のための CPU 状態について追加しました。
- [Azure Log Analytics ワークスペースの削除と復旧](logs/delete-workspace.md) - トラブルシューティングを追加しました。
- [Azure Logic Apps と Power Automate で Azure Monitor ログを使用する](logs/logicapp-flow-connector.md) - 新しい Azure Monitor コネクタ向けに更新しました。

### <a name="metrics"></a>メトリック

- [Azure portal でのディスク メトリックの非推奨](essentials/portal-disk-metrics-deprecation.md) - 新しい記事。
- [チュートリアル - Azure Monitor でのメトリック グラフの作成](essentials/tutorial-metrics-explorer.md) - ビデオを追加しました。

### <a name="platform-logs"></a>プラットフォーム ログ

- [Azure Monitor での Azure アクティビティ ログの収集と分析](./essentials/activity-log.md) - 診断設定を使用したアクティビティ ログの収集をより適切に説明するために書き直しました。

### <a name="virtual-machines"></a>仮想マシン

- [Azure Monitor を使用して Azure 仮想マシンを監視する](vm/monitor-vm-azure.md) - 新しい記事。
- [クイック スタート: Azure Monitor を使用して Azure 仮想マシンを監視する](vm/quick-monitor-azure-vm.md) - VM insights を追加して更新しました。
- [VM insights からのアラート](vm/vminsights-alerts.md) - 新しい記事。
- [VM insights の有効化の概要](vm/vminsights-enable-overview.md) - エージェントのダウンロード リンクを更新しました。

VM insights の一般提供に関する全般的な更新

- [VM insights とは](vm/vminsights-overview.md)
- [VM insights (GA) についてよく寄せられる質問](vm/vminsights-ga-release-faq.md) 
- [Azure Policy を使用して VM insights を有効にする](./vm/vminsights-enable-policy.md) 
- [VM insights を使用してパフォーマンスをグラフ化する方法](vm/vminsights-performance.md)
- [VM insights からログを照会する方法](vm/vminsights-log-search.md)
- [VM insights を使用してアプリの依存関係を表示する](vm/vminsights-maps.md) 

### <a name="visualizations"></a>視覚化

- [Azure Monitor からのデータを視覚化する](visualizations.md) - ビュー デザイナーの計画されている非推奨について記載するために更新しました。

## <a name="february-2020"></a>2020 年 2 月

### <a name="agents"></a>エージェント

診断拡張機能のコンテンツの書き直しの一環としての複数の更新。

- [Azure Monitor エージェントの概要](agents/agents-overview.md) - 各エージェントの固有の機能をより明確にするために表を再構築しました。
- [Azure Diagnostics 拡張機能の概要](agents/diagnostics-extension-overview.md) - 完全な書き直し。
- [Azure Monitor で IIS 用に Blob Storage を使用し、イベント用にテーブル ストレージを使用する](essentials/diagnostics-extension-logs.md) - 更新と明確化のための全体的な書き直し。
- [Windows Azure Diagnostics 拡張機能 (WAD) のインストールと構成](agents/diagnostics-extension-windows-install.md) - 新しい記事。 
- [Windows Diagnostics 拡張機能のスキーマ](agents/diagnostics-extension-schema-windows.md) - 再構成。
- [Windows Azure Diagnostics 拡張機能から Azure Event Hubs にデータを送信する](agents/diagnostics-extension-stream-event-hubs.md) - 完全な書き直しおよび更新。
- [Azure Storage への診断データの保存と表示](../cloud-services/diagnostics-extension-to-storage.md) - 完全な書き直しおよび更新。
- [Windows 用の仮想マシン拡張機能](../virtual-machines/extensions/oms-windows.md) - Log Analytics エージェントとの関係をより明確にします。
- [Linux 用の Azure Monitor 仮想マシン拡張機能](../virtual-machines/extensions/oms-linux.md) - Log Analytics エージェントとの関係をより明確にします。

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights の接続文字列](app/sdk-connection-string.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション

#### <a name="container-insights"></a>Container insights

- [Azure Active Directory と Azure Kubernetes Service を統合する](../aks/azure-ad-integration-cli.md) - Container insights をサポートするために Kubernetes RBAC 対応クラスターをサポートするクライアント アプリケーションの作成に関するメモを追加しました。

#### <a name="vm-insights"></a>VM insights

- [VM insights (GA) についてよく寄せられる質問](vm/vminsights-ga-release-faq.md) - パフォーマンス データの保存方法の変更。

#### <a name="office-365"></a>Office 365

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - 非推奨となる日が更新されました。


### <a name="logs"></a>ログ

- [Azure Monitor でログ クエリを最適化する](logs/query-optimization.md) - 新しい記事。
- [Azure Monitor ログの使用量とコストを管理する](logs/manage-cost-storage.md) - 使用量を理解するのに役立つサンプル クエリを改良しました。

### <a name="metrics"></a>メトリック

- [診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック](essentials/metrics-supported-export-diagnostic-settings.md) - null 値およびゼロ値に対する動作の変更に関するセクションを追加しました。

### <a name="visualizations"></a>視覚化

ビュー デザイナーからブックへの変換ガイドに関する複数の新しい記事。

- [Azure Monitor ビュー デザイナーからブックへの移行ガイド](visualize/view-designer-conversion-overview.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換オプション](visualize/view-designer-conversion-options.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブック タイルへの変換](visualize/view-designer-conversion-tiles.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の概要とアクセス](visualize/view-designer-conversion-access.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の一般的なタスク](visualize/view-designer-conversion-tasks.md) - 新しい記事。
- [Azure Monitor ビュー デザイナーからブックへの変換の例](visualize/view-designer-conversion-examples.md) - 新しい記事。

## <a name="january-2020"></a>2020 年 1 月

### <a name="general"></a>全般

- [Azure Monitor によって監視される内容](monitor-reference.md) - 新しい記事。

### <a name="agents"></a>エージェント

- [Azure Log Analytics エージェントを使用してログ データを収集する](agents/log-analytics-agent.md) - ネットワーク ファイアウォールの要件に関する表が更新されています。

### <a name="alerts"></a>警告

- [Azure portal でのアクション グループの作成および管理](alerts/action-groups.md) - 不要になった v2 関数用の設定を削除しました。
- [Resource Manager テンプレートでのメトリック アラートの作成](alerts/alerts-metric-create-templates.md) - *ignoreDataBefore* パラメーターの例を追加しました。  複数条件のルールに関する制約を追加しました。
- [Log Analytics のアラート REST API の使用](alerts/api-alerts.md) - JSON の例を修正しました。

### <a name="application-insights"></a>Application Insights

- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Azure ネットワーク セキュリティ グループを使用してトラフィックを許可するための受信ポート規則の追加方法に関して、「可用性テスト」セクションを更新しました。
- [Azure Application Insights Profiler に関する問題のトラブルシューティング](app/profiler-troubleshooting.md) - 一般的なトラブルシューティングを更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - お客様からのフィードバックに基づいて読みやすさを改善するために更新、改編しました。

### <a name="data-security"></a>データのセキュリティ

- [Azure Monitor のカスタマー マネージド キーの構成](logs/customer-managed-keys.md) - 新しい記事。

### <a name="insights-and-solutions"></a>分析情報とソリューション

#### <a name="container-insights"></a>Container insights

- [Container insights エージェントのデータ収集を構成する](containers/container-insights-agent-config.md) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [Container insights におけるパフォーマンス アラートを作成する](./containers/container-insights-log-alerts.md) - ワークスペースコンテキストのアラートを使用して、ワークスペースに格納されているパフォーマンス データに関するアラートを作成する方法についての情報を修正し、手順を更新しました。
- [Container insights での Kubernetes の監視](containers/container-insights-analyze.md) - Windows Kubernetes クラスターのサポートに関する概要記事と分析記事の両方を更新しました。
- [Azure Red Hat OpenShift クラスターに Container insights を構成する](containers/container-insights-azure-redhat-setup.md) - Azure Red Hat OpenShift におけるエージェントのアップグレードに関する詳しい情報を追加しました。また、エージェントのアップグレード方法を識別するための補足情報を追加しました。
- [ハイブリッド Kubernetes クラスターに Container insights を構成する](containers/container-insights-hybrid-setup.md) - Kubelet の cAdvisor を使用した secure port:10250 のサポート追加を反映するために更新しました。
- [Container insights エージェントを管理する方法](containers/container-insights-manage-agent.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレイピングの動作と構成に関する詳細情報を更新しました。
- [Container insights の Prometheus 統合を構成する](containers/container-insights-prometheus-integration.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレイピングの動作と構成に関する詳細情報を更新しました。
- [メトリックのための Container insights の更新方法](containers/container-insights-update-metrics.md) - Azure Red Hat OpenShift と他の種類の Kubernetes クラスターとを比較して、メトリック スクレイピングの動作と構成に関する詳細情報を更新しました。

#### <a name="vm-insights"></a>VM insights

- [VM insights (GA) についてよく寄せられる質問](vm/vminsights-ga-release-faq.md) - ワークスペースとエージェントの新バージョンへのアップグレードに関する情報を追加しました。

#### <a name="office-365"></a>Office 365

- [Azure の Office 365 管理ソリューション](insights/solution-office-365.md) - Azure Sentinel における Office 365 ソリューションへの移行に関する詳細および FAQ を追加しました。 オンボーディング セクションを削除しました。

### <a name="logs"></a>ログ

- [Azure Monitor で Log Analytics ワークスペースを管理する](logs/manage-access.md) - 「非アクション」を更新しました。
- [Azure Monitor ログの使用量とコストを管理する](logs/manage-cost-storage.md) - 「価格モデル」セクションにデータ量の計算についての説明を追加しました。
- [Azure Resource Manager テンプレートを使用して Log Analytics ワークスペースの作成と構成を行う](./logs/resource-manager-workspace.md) - 新しい価格レベルでテンプレートを更新しました。

### <a name="platform-logs"></a>プラットフォーム ログ

- [診断設定を使用して Azure アクティビティ ログを収集する - Azure Monitor](./essentials/activity-log.md) - 変更されたプロパティについての情報を追加しました。
- [Azure アクティビティ ログをエクスポートする](./essentials/activity-log.md#legacy-collection-methods) - UI の変更に合わせて更新しました。 

## <a name="december-2019"></a>2019 年 12 月

### <a name="agents"></a>エージェント

- [Linux コンピューターを Azure Monitor に接続する](agents/agent-linux.md) - 新しい記事。

### <a name="alerts"></a>警告

- [Resource Manager テンプレートでのメトリック アラートの作成](alerts/alerts-metric-create-templates.md) - カスタム メトリックの例を追加しました。
- [Azure Monitor で動的しきい値を使用したアラートを作成する](alerts/alerts-dynamic-thresholds.md) - 動的しきい値のグラフの解釈に関するセクションを追加しました。
- [Azure でのアラートと通知監視の概要](alerts/alerts-overview.md) - Resource Graph クエリを更新しました。
- [Azure Monitor のメトリック アラートでサポートされるリソース](alerts/alerts-metric-near-real-time.md) - サポートされるメトリックとディメンションについて更新しました。
- [従来の Log Analytics アラート API から新しい Azure Alerts API に切り替える](alerts/alerts-log-api-switch.md) - 変更されたアラート名についての注意を追加しました。
- [Azure Monitor でのメトリック アラートのしくみを理解する](alerts/alerts-metric-overview.md) - 大規模な監視でサポートされるリソースの種類を追加しました。

### <a name="application-insights"></a>Application Insights

- [ワーカー サービス アプリ (非 HTTP アプリ) 向け Application Insights](app/worker-service.md) - C# コードに既定のログ レベルを追加しました。 パッケージの参照バージョンを更新しました。
- [ApplicationInsights.config リファレンス - Azure](app/configuration-with-applicationinsights-config.md) - サンプル コードを更新しました。
- [PowerShell での Azure Application Insights の自動化](app/powershell.md) - Resource Manager テンプレートへの更新。
- [新しい Azure Application Insights リソースの作成](app/create-new-resource.md) - グローバルに一意の名前についての注意を追加しました。
- [Live Metrics Stream による診断 - Azure Application Insights](app/live-stream.md) - ASP.NET Core SDK バージョンの要件を更新しました。
- [Application Insights のイベント カウンター](app/eventcounters.md) - customMetrics のカテゴリとテーブルを更新しました。
- [Azure Application Insights を使用した Java トレース ログの探索](app/java-trace-logs.md) - Java エージェントのログのしきい値の構成を追加しました。
- [Application Insights および Log Analytics によって使用される IP アドレス](app/ip-addresses.md) - Live Metrics Stream の IP アドレスを更新しました。
- [Azure App Services のパフォーマンスを監視する](app/azure-web-apps.md) - ASP.NET Core 3.0 のサポートを追加しました。 
- [Azure Monitor を使用して Python アプリを監視する (プレビュー)](app/opencensus-python.md) - Azure Monitor スキーマに対する OpenCensus Python のスキーマ マッピングに関する説明を追加しました。
- [Azure Application Insights のリリース ノート](app/release-notes.md) - 以前のリリースに関する注意を追加しました。
- [Azure Application Insights におけるテレメトリ チャネル](app/telemetry-channels.md) - 長時間にわたる接続喪失時に破棄されるデータの期間を更新しました。
- [Azure Application Insights におけるテレメトリ サンプリング](app/sampling.md) - カスタム TelemetryInitializer のコード スニペットを修正しました。
- [Java Web プロジェクトでの Application Insights のトラブルシューティング](app/java-troubleshoot.md) - JDK 9 で依存関係の収集が未サポートであることに関する記述を削除しました。

### <a name="insights-and-solutions"></a>分析情報とソリューション

- [Container insights についてよく寄せられる質問](./faq.md) - Image フィールドと Name フィールドに関する質問を追加しました。
- [Azure Monitor の Azure SQL Analytics ソリューション](insights/azure-sql.md) - 「データベース待機」の「マネージド インスタンスのサポート」を更新しました。
- [Container insights エージェントのデータ収集を構成する](containers/container-insights-agent-config.md) - enrich_container_logs の設定を追加しました。
- [ハイブリッド Kubernetes クラスターに Container insights を構成する](containers/container-insights-hybrid-setup.md) - トラブルシューティング セクションを追加しました。
- [Azure Monitor で Active Directory のレプリケーション状態を監視する](insights/ad-replication-status.md) - .NET Framework の前提条件を更新しました。
- [Azure の Network Performance Monitor ソリューション](insights/network-performance-monitor.md) - サポートされるリージョンを追加しました。
- [Azure Monitor で Active Directory 環境を最適化する](insights/ad-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Monitor で SQL Server 環境を最適化する](insights/sql-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics で System Center Operations Manager 環境を最適化する](insights/scom-assessment.md) - .NET Framework の前提条件を更新しました。
- [Azure Log Analytics の IT Service Management Connector とのサポートされている接続](alerts/itsmc-connections.md) - 前提条件のクライアント ID とクライアント シークレットにニューヨークを追加しました。

### <a name="logs"></a>ログ

- [Azure Log Analytics ワークスペースの削除と復旧](logs/delete-workspace.md) - PowerShell メソッドを追加しました。
- [Azure Monitor ログのデプロイの設計](logs/design-logs-deployment.md) - ワークスペースのインジェスト率を増やしました。

### <a name="metrics"></a>メトリック

- [診断設定を使用してエクスポート可能な Azure Monitor プラットフォーム メトリック](essentials/metrics-supported-export-diagnostic-settings.md) - 新しい記事。

### <a name="platform-logs"></a>プラットフォーム ログ

プラットフォーム ログのコンテンツ再編成の一環として、診断設定を使用してアクティビティ ログを構成するための新機能に基づいて複数の記事を更新しました。

- [Azure リソース ログをストレージ アカウントにアーカイブする](./essentials/resource-logs.md#send-to-azure-storage)
- [Azure アクティビティ ログのイベント スキーマ](essentials/activity-log-schema.md)
- [Azure Monitor サービスの制限](service-limits.md)
- [Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](./essentials/activity-log.md)
- [診断設定を使用して Azure アクティビティ ログを収集する (プレビュー) - Azure Monitor](./essentials/activity-log.md)
- [Azure テナントにまたがる Log Analytics ワークスペースに Azure アクティビティ ログを収集する](./essentials/activity-log.md)
- [Log Analytics ワークスペースで Azure リソース ログを収集する](./essentials/resource-logs.md#send-to-log-analytics-workspace)
- [Azure で Resource Manager テンプレートを使用して診断設定を作成する](./essentials/resource-manager-diagnostic-settings.md)
- [Azure でログとメトリックを収集するための診断設定を作成する](essentials/diagnostic-settings.md)
- [Azure アクティビティ ログをエクスポートする](./essentials/activity-log.md#legacy-collection-methods)
- [Azure プラットフォーム ログの概要](essentials/platform-logs-overview.md)
- [イベント ハブへの Azure 監視データのストリーム配信](essentials/stream-monitoring-data-event-hubs.md)
- [Azure プラットフォーム ログをイベント ハブにストリーム配信する](./essentials/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>クイック スタートとチュートリアル

- [Azure Monitor でのメトリック グラフの作成](essentials/tutorial-metrics-explorer.md) - 新しい記事。
- [Azure リソースからリソース ログを収集し、Azure Monitor で分析する](essentials/tutorial-resource-logs.md) - 新しい記事。
- [Azure Monitor を使用して Azure リソースを監視する](essentials/quick-monitor-azure-resource.md) - 新しい記事。
   
## <a name="next-steps"></a>次のステップ

- Azure Monitor のドキュメント作成に協力していただける方は、[Docs 共同作成者ガイド](/contribute/)に関するページを参照してください。