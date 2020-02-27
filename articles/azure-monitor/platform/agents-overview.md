---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 629b75963ba90a25d59c1601fcd479fce40c92e7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77467268"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor エージェントの概要 
仮想マシンおよびその他のコンピューティング リソースでは、ゲスト オペレーティング システムとワークロードのパフォーマンスと可用性を測定するために監視データを収集するエージェントが必要です。 この記事では、Azure Monitor で使用されるエージェントについて説明します。特定の環境の要件を満たす上で必要なエージェントを判断するのに役に立ちます。

> [!NOTE]
> Azure Monitor と Log Analytics が最近統合されたため、現在 Azure Monitor には複数のエージェントがあります。 機能に重複がある場合もありますが、それぞれに固有の機能があります。 要件によっては、仮想マシンに 1 つ以上のエージェントが必要になる場合があります。 

特定の仮想マシンに対して、1 つのエージェントでは完全に満たすことができない特定の要件セットがある場合があります。 たとえば、Azure Diagnostics 拡張機能を必要とするメトリック アラートを使用する必要があるときに、Log Analytics エージェントと依存関係エージェントを必要とする Azure Monitor for VM の機能も利用したい場合があります。 このような場合、複数のエージェントを使用できます。これは、それぞれの機能を必要とする顧客を対象にした一般的なシナリオです。

## <a name="summary-of-agents"></a>エージェントの概要
次の表は、Windows と Linux の Azure Monitor エージェントを簡単に比較したものです。 それぞれの詳細については、以下のセクションで説明します。 

### <a name="windows-agents"></a>Windows エージェント

| | 診断<br>拡張機能 (WAD) | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|
| サポートされている環境 | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | 
| エージェントの要件  | なし | なし | Log Analytics エージェントが必要 |
| 収集されるデータ | イベント ログ<br>ETW イベント<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>.NET アプリ ログ<br>クラッシュ ダンプ<br>エージェント診断ログ | イベント ログ<br>パフォーマンス<IIS logs><br>ファイル ベース ログ<br>分析情報とソリューション<br>その他のサービス | プロセスの詳細と依存関係<br>ネットワーク接続のメトリック |
| 送信されるデータ | Azure Storage<br>Azure Monitor メトリック<br>イベント ハブ | Azure Monitor ログ | Azure Monitor ログ |


### <a name="linux-agents"></a>Linux エージェント

| | 診断<br>拡張機能 (LAD) | Telegraf<br>エージェント | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|
| サポートされている環境 | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス |
| エージェントの要件  | なし | なし | なし | Log Analytics エージェントが必要 |
| 収集されるデータ | syslog<br>パフォーマンス | パフォーマンス | syslog<br>パフォーマンス| プロセスの詳細と依存関係<br>ネットワーク接続のメトリック |
| 送信されるデータ | Azure Storage<br>イベント ハブ | Azure Monitor メトリック | Azure Monitor ログ | Azure Monitor ログ |

## <a name="log-analytics-agent"></a>Log Analytics エージェント
[Log Analytics エージェント](log-analytics-agent.md)を使うと、Azure、他のクラウド プロバイダー、オンプレミスの仮想マシンのゲスト オペレーティング システムとワークロードから、監視データを収集できます。 データは Log Analytics ワークスペースに収集されます。 Log Analytics エージェントは、System Center Operations Manager によって使用されるものと同じであり、エージェント コンピューターをマルチホーム化して管理グループおよび Azure Monitor との通信を同時に行います。 このエージェントは、Azure Monitor の特定の分析情報およびソリューションにも必要です。


> [!NOTE]
> Windows 用 Log Analytics エージェントは、多くの場合、Microsoft Management Agent (MMA) と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、OMS エージェントと呼ばれます。



Log Analytics エージェントは、次のような場合に使用します。

* Azure の外部の仮想または物理マシンからログとパフォーマンス データを収集する。 
* データを Log Analytics ワークスペースに送信して、[ログ クエリ](../log-query/log-query-overview.md)など、[Azure Monitor ログ](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)でサポートされている機能を活用する。
* 仮想マシンを大規模に監視し、そのプロセスや他のリソースおよび外部プロセスに対する依存関係を監視できる [Azure Monitor for VMs](../insights/vminsights-overview.md) を使用する。  
* [Azure Security Center](../../security-center/security-center-intro.md) または [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用の仮想マシンのセキュリティを管理する。
* [Azure Automation Update Management](../../automation/automation-update-management.md)、[Azure Automation State Configuration](../../automation/automation-dsc-overview.md)、または [Azure Automation Change Tracking および Inventory](../../automation/change-tracking.md) を使用して、Azure VM の包括的な管理を提供する
* さまざまな[ソリューション](../monitor-reference.md#insights-and-core-solutions)を使用して、特定のサービスまたはアプリケーションを監視する。

Log Analytics エージェントの制限事項は次のとおりです。

- Azure Monitor メトリック、Azure Storage、または Azure Event Hubs にデータを送信することはできません。

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能
[Azure Diagnostics 拡張機能](diagnostics-extension-overview.md)によって、Azure 仮想マシンおよびその他のコンピューティング リソースのゲスト オペレーティング システムとワークロードから監視データが収集されます。 データは主に Azure Storage に収集されますが、データ シンクを定義して、Azure Monitor メトリックや Azure Event Hubs などの他の送信先にデータを送信することもできます。

Azure Diagnostics 拡張機能は、次のような場合に使用します。

- アーカイブのため、または [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールで分析したりするために Azure Storage にデータを送信する。
- [Azure Monitor メトリック](data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../../azure-monitor/platform/alerts-metric-overview.md)や[自動スケーリング](autoscale-overview.md) (Windows のみ) などの機能を活用したりする。
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [ブート診断](../../virtual-machines/troubleshooting/boot-diagnostics.md)を収集して VM ブートの問題を調査する。

Azure Diagnostics 拡張機能の制限事項は次のとおりです。

- Azure リソースでのみ使用できます。
- Azure Monitor ログにデータを送信する機能が制限されています。



## <a name="telegraf-agent"></a>Telegraf エージェント
[InfluxData Telegraf エージェント](collect-custom-metrics-linux-telegraf.md)は、Linux コンピューターから Azure Monitor メトリックにパフォーマンス データを収集するために使用されます。

Telegraf エージェントは、次のような場合に使用します。

* [Azure Monitor メトリック](data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../../azure-monitor/platform/alerts-metric-overview.md)や[自動スケーリング](autoscale-overview.md) (Linux のみ) などの機能を活用したりする。 



## <a name="dependency-agent"></a>依存関係エージェント
依存関係エージェントでは、仮想マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データが収集されます。 

依存関係エージェントは、次のような場合に使用します。

* マップ機能の [Azure Monitor for VMs](../insights/vminsights-overview.md) または [Service Map](../insights/service-map.md) ソリューションを使用する。


依存関係エージェントを使用する場合は、次の点を考慮してください。

- 依存関係エージェントを使用するには、Log Analytics エージェントが同じ仮想マシンにインストールされている必要があります。
- Linux VM の場合は、Azure 診断の拡張機能の前に Log Analytics エージェントをインストールする必要があります。


## <a name="extensions-compared-to-agents"></a>エージェントと比較した拡張機能
[Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics 拡張機能は、Azure 仮想マシンに Log Analytics エージェントをインストールします。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の Azure Monitor 依存関係拡張機能は、Azure 仮想マシンに依存関係エージェントをインストールします。 これらは上記と同じエージェントですが、これらは[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)を使用して管理できます。 可能な場合は、拡張機能を使用してエージェントをインストールおよび管理する必要があります。


## <a name="next-steps"></a>次のステップ
各エージェントの詳細については、以下を参照してください。

- [Log Analytics エージェントの概要](log-analytics-agent.md)
- [Azure Diagnostics 拡張機能の概要](diagnostics-extension-overview.md)
- [Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](collect-custom-metrics-linux-telegraf.md)
