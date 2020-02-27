---
title: Azure Monitor のメトリック | Microsoft Docs
description: ほぼリアルタイムのシナリオをサポートできる軽量な監視データである、Azure Monitor のメトリックについて説明します。
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: cd30803735c5453c286788b8669a3d2f02c418a5
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77468050"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor のメトリック

> [!NOTE]
> Azure Monitor データ プラットフォームは、2 つの基本的なデータの種類に基づいています。メトリックとログです。 この記事では、メトリックについて説明します。 ログの詳細な説明については、「[Logs in Azure Monitor](data-platform-logs.md)」(Azure Monitor のログ) を参照し、これら 2 つの比較については、「[Azure Monitor data platforn](data-platform.md)」(Azure Monitor データ プラットフォーム) を参照してください。

Azure Monitor のログは軽量であり、ほぼリアルタイムのシナリオをサポートできるため、アラートと問題の迅速な検出に特に役立ちます。 この記事では、メトリックの構造と、メトリックを使用してできること、およびメトリックにデータを保存できるさまざまなデータ ソースについて説明します。

## <a name="what-are-metrics"></a>メトリックとは
メトリックは、特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは定期的に収集されます。これらは頻繁にサンプリングでき、比較的単純なロジックですばやくアラートを生成できるため、アラートを発行するときに役に立ちます。

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor のメトリックでできること
次の表に、Azure Monitor でメトリック データを使用できるさまざまな方法を示します。

|  |  |
|:---|:---|
| 分析 | [メトリックス エクスプローラー](metrics-charts.md)を使用して、収集されたメトリックをグラフで分析し、異なるリソースからのメトリックを比較します。 |
| 視覚化 | メトリックス エクスプローラーのグラフを [Azure ダッシュボード](../learn/tutorial-app-dashboards.md)にピン留めします。<br>[ブック](../app/usage-workbooks.md)を作成して、複数のデータのセットを対話型のレポートにまとめます。クエリの結果を [Grafana](grafana-plugin.md) にエクスポートし、そのダッシュボードを利用して他のデータ ソースと組み合わせます。 |
| アラート: | メトリックがしきい値を超えたときに、通知を送信または[自動化されたアクション](action-groups.md)を実行する[メトリック アラート ルール](alerts-metric.md)を構成します。 |
| 自動化 |  [自動スケーリング](autoscale-overview.md)を使用して、しきい値を超えるメトリック値に基づいてリソースを増加または減少させます。 |
| [エクスポート] | [メトリックをログにルーティング](resource-logs-collect-storage.md)して、Azure Monitor メトリックのデータと Azure Monitor ログのデータを一緒に分析し、93 日間より長くメトリック値を保存します。<br>メトリックを [Event Hub](stream-monitoring-data-event-hubs.md) にストリーミングして、外部システムにルーティングします。 |
| 取得 | [PowerShell コマンドレット](https://docs.microsoft.com/powershell/module/az.applicationinsights)を使用して、コマンド ラインからメトリック値にアクセスします。<br>[REST API](rest-api-walkthrough.md) を使用して、カスタム アプリケーションからメトリック値にアクセスします。<br>[CLI](/cli/azure/monitor/metrics) を使用して、コマンド ラインからメトリック値にアクセスします。 |
| アーカイブ | コンプライアンス、監査、オフライン レポートの目的で、リソースのパフォーマンスや正常性の履歴を[アーカイブ](..//learn/tutorial-archive-data.md)します。 |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor メトリックのデータの構造
Azure Monitor メトリックによって収集されるデータは、タイムスタンプ付きのデータの分析用に最適化された時系列データベースに保存されます。 メトリック値の各セットは、次のプロパティを持つ時系列です。

* 値が収集された時刻
* 値が関連付けられているリソース
* メトリックのカテゴリのように機能する名前空間
* メトリック名
* 値そのもの
* 「[多次元メトリック](#multi-dimensional-metrics)」で説明されているように、一部のメトリックは複数のディメンションを持ちます。 カスタム メトリックは最大 10 個のディメンションを持つことができます。

## <a name="multi-dimensional-metrics"></a>多次元メトリック
メトリック データの課題の 1 つは、収集された値のコンテキストを提供するための情報が限定されている場合が多いことです。 Azure Monitor では、多次元メトリックを使用してこの課題に対処します。 メトリックのディメンションは、メトリックの値を記述するための追加データを保持する名前と値のペアです。 たとえば、_使用可能なディスク領域_ メトリックは、_C:_ 、_D:_ という値がある _ドライブ_ と呼ばれるディメンションを持つことができ、これらの値を使用して、すべてのドライブまたは個別のドライブで使用可能なディスク領域を表示できます。

次の例は、_ネットワーク スループット_ という名前の架空のメトリック用の 2 つのデータセットを示しています。 最初のデータセットにはディメンションがありません。 2 番目のデータセットは、_IP アドレス_ と _方向_ という 2 つのディメンションの値を示しています。

### <a name="network-throughput"></a>ネットワーク スループット

| Timestamp     | メトリック値 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8 Kbps |
| 8/9/2017 8:15 | 1,141.4 Kbps |
| 8/9/2017 8:16 | 1,110.2 Kbps |

このディメンションのないメトリックは、「特定の時点でのネットワークのスループットは?」のような基本的な質問にのみ答えることができます。

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>ネットワーク スループット + 2 つのディメンション ("IP" と "方向")

| Timestamp     | ディメンション "IP"   | ディメンション "方向" | メトリック値|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "送信"    | 646.5 Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 方向 = "受信" | 420.1 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "送信"    | 150.0 Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 方向 = "受信" | 115.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "送信"    | 515.2 Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 方向 = "受信" | 371.1 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "送信"    | 155.0 Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 方向 = "受信" | 100.1 Kbps |

このメトリックは、「各 IP アドレスのネットワーク スループットは?」 や 「データの送信量と受信量?」といった質問に応えることができます。 多次元メトリックは、ディメンションを持たないメトリックに比べ、分析と診断に使用できる多数の値を提供します。

## <a name="interacting-with-azure-monitor-metrics"></a>Azure Monitor メトリックの操作
[メトリックス エクスプ ローラー](metrics-charts.md)を使用して、メトリック データベース内のデータを対話的に分析し、一定期間にわたる複数のメトリックの値をグラフにします。 グラフをダッシュボードにピン留めして、他の視覚化と一緒に表示できます。 [Azure monitoring REST API](rest-api-walkthrough.md) を使用してメトリックを取得することもできます。

![メトリックス エクスプローラー](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor メトリックのソース
Azure Monitor によって収集されるメトリックのソースには、基本的なものが 3 つあります。 Azure Monitor メトリック データベースでこれらのメトリックが収集されると、そのソースとは無関係に一緒に評価できます。

**プラットフォームのメトリック**は Azure リソースによって作成され、リソースの正常性とパフォーマンスについての可視化を提供します。 リソースの種類ごとに[別個のメトリックのセット](metrics-supported.md)が作成され、必要な構成はありません。 メトリックの定義で特に指定がない限り、プラットフォーム メトリックは 1 分間隔で Azure リソースから収集されます。 

**ゲスト OS メトリック**は、仮想マシンのゲスト オペレーティング システムから収集されます。 Windows 仮想マシンの場合は [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) を使用し、Linux 仮想マシンの場合は [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を使用して、ゲスト OS メトリックを有効にします。

**アプリケーション メトリック**は、監視対象のアプリケーションについて Application Insights によって作成され、パフォーマンスの問題を検出し、アプリケーションがどのように使用されているかの傾向を追跡する助けになります。 これには、_サーバー応答時間_と_ブラウザー例外_などの値が含まれます。

**カスタム メトリック**は、自動的に利用できる標準メトリックに加えて、ユーザーが定義するメトリックです。 Application Insights によって監視される[カスタム メトリックをアプリケーション内で定義](../app/api-custom-events-metrics.md)したり、[カスタム メトリック API](metrics-store-custom-rest-api.md) を使用して Azure サービスのカスタム メトリックを作成したりできます。

## <a name="retention-of-metrics"></a>メトリックの保有期間
Azure の多くのリソースでは、メトリックは 93 日間保存されます。 ただし、例外があります。

**ゲスト OS メトリック**
-   **クラシック ゲストの OS メトリック**。 これらは [Windows Diagnostic Extension (WAD)](../platform/diagnostics-extension-overview.md) または [Linux Diagnostic Extension (LAD)](../../virtual-machines/extensions/diagnostics-linux.md) によって収集され、Azure ストレージ アカウントにルーティングされるパフォーマンス カウンターです。 これらのメトリックの保有期間は 14 日です。
-   **Azure Monitor メトリックに送信されるゲスト OS メトリック**。 これらは [Windows Diagnostic 拡張機能 (WAD)](diagnostics-extension-overview.md) によって収集され、[Azure Monitor データ シンク](diagnostics-extension-overview.md#data-destinations)に送信されるか、Linux マシン上の [InfluxData Telegraf エージェント](https://www.influxdata.com/time-series-platform/telegraf/)を介して収集されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 93 日です。
-   **Log Analytics エージェントによって収集されるゲスト OS メトリック**。 これらは、Log Analytics エージェントによって収集され、Log Analytics ワークスペースに送信されるパフォーマンス カウンターです。 これらのメトリックの保有期間は 31 日で、最大 2 年間まで延長できます。

**Application Insights ログベースのメトリック** 
- バック グラウンドで[ログ ベースのメトリック](../app/pre-aggregated-metrics-log-metrics.md)をログ クエリに変換します。 そのリテンション期間は、基になるログのイベントのリテンション期間と一致します。 Application Insights リソースの場合、ログは 90 日間保存されます。


> [!NOTE]
> 長期的な傾向を見るために、[Azure Monitor リソースのプラットフォーム メトリックを Log Analytics ワークスペースに送信](resource-logs-collect-storage.md)できます。





## <a name="next-steps"></a>次のステップ

- [Azure Monitor データ プラットフォーム](data-platform.md)の詳細を確認します。
- [Azure Monitor のログ データ](data-platform-logs.md)について確認します。
- Azure のさまざまなリソースで[入手できる監視データ](data-sources.md)を確認します。
