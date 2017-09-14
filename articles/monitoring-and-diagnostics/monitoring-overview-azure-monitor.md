---
title: "Azure Monitor の概要 | Microsoft Docs"
description: "Azure Monitor は、アラート、webhook、自動スケール、およびオートメーションで使用するための統計を収集します。 この記事では、その他の Microsoft 監視オプションも示します。"
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: robb
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 619a004b9aff99be68988e1f7be3ccad400a8a0e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="overview-of-azure-monitor"></a>Azure Monitor の概要
この記事では、Microsoft Azure の Azure Monitor サービスの概要を説明します。 Azure Monitor の動作について説明し、Azure Monitor の使用方法に関するその他の情報の参照先を紹介します。  概要ビデオをご希望の場合は、この記事の最後にある「次のステップ」のリンクを参照してください。 

## <a name="why-monitor-your-application-or-system"></a>アプリケーションまたはシステムを監視する理由
クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。 さらに、監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。


## <a name="azure-monitor-and-microsofts-other-monitoring-products"></a>Azure Monitor と Microsoft のその他の監視製品
Azure Monitor では、Microsoft Azure のほとんどのサービス向けにベース レベルのインフラストラクチャのメトリックおよびログを提供します。 Azure Monitor にまだデータを格納していない Azure サービスは、今後データを格納する予定です。

Microsoft は、開発者、DevOps、または IT オペレーター向けの追加の監視機能が備わっている製品やサービスを他にも提供しています。これらの機能には、オンプレミスのインストールも用意されています。 このようなさまざまな製品やサービスの連携方法の概要と理解については、[Microsoft Azure での監視](monitoring-overview.md)に関する記事を参照してください。

## <a name="monitoring-sources---compute"></a>監視のソース - コンピューティング

![非コンピューティング リソースに対する監視と診断のモデル](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-compute_v6.png)

コンピューティング サービスには次のものが含まれます。 
- Cloud Services 
- Virtual Machines 
- 仮想マシン スケール セット 
- Service Fabric

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>アプリケーション - 診断ログ、アプリケーション ログ、メトリック
コンピューティング リソース用のモデルに示したとおり、アプリケーションはゲスト OS で実行できます。 各アプリケーションからは、それぞれに固有のログとメトリックのセットが出力されます。 Azure Monitor は、Azure 診断拡張機能 (Windows または Linux) を利用して、アプリケーション レベルのメトリックとログの大半を収集します。 種類には次のものが含まれます。

* パフォーマンス カウンター
* Application Logs
* Windows イベント ログ
* .NET イベント ソース
* IIS ログ
* マニフェスト ベースの ETW
* クラッシュ ダンプ
* カスタム エラー ログ

診断拡張機能を使用しない場合、使用できるメトリックはごくわずかです (CPU 使用率など)。 

### <a name="host-and-guest-vm-metrics"></a>ホスト VM とゲスト VM のメトリック
上記のコンピューティング リソースには、対話相手となる専用のホスト VM とゲスト OS が存在します。 ホスト VM およびゲスト OS は、Hyper-V ハイパーバイザー モデルのルート VM およびゲスト VM に相当するものです。 この両方でメトリックを収集できます。 また、ゲスト OS では診断ログを収集することもできます。   

### <a name="activity-log"></a>アクティビティ ログ
リソースの情報については、Azure インフラストラクチャで確認できるようなアクティビティ ログ (旧称、操作ログまたは監査ログ) を検索できます。 このログには、リソースが作成された時点や破棄された時点などの情報が記載されています。  詳細については、[アクティビティ ログの概要](monitoring-overview-activity-logs.md)に関する記事を参照してください。 

## <a name="monitoring-sources---everything-else"></a>監視のソース - その他すべて

![コンピューティング リソースに対する監視と診断のモデル](./media/monitoring-overview-azure-monitor/Monitoring_Azure_Resources-non-compute_v6.png)


### <a name="resource---metrics-and-diagnostics-logs"></a>リソース - メトリックと診断ログ
収集可能なメトリックと診断ログは、リソースの種類によって異なります。 たとえば、Web Apps は、ディスク IO と CPU 使用率に関する統計情報を提供します。 このようなメトリックは、Service Bus キューのためには存在しません。その代わり、このキューには、キューのサイズやメッセージのスループットのようなメトリックが用意されています。 各リソースの収集可能なメトリックの一覧については、[サポートされるメトリック](monitoring-supported-metrics.md)に関する記事を参照してください。 

### <a name="host-and-guest-vm-metrics"></a>ホスト VM とゲスト VM のメトリック
リソースと特定のホスト VM またはゲスト VM の間に必ずしも 1 対 1 のマッピングが存在するわけではありません。そのため、メトリックは使用できません。

### <a name="activity-log"></a>アクティビティ ログ
アクティビティ ログは、コンピューティング リソースの場合と同じです。  

## <a name="uses-for-monitoring-data"></a>監視データの用途
データを収集したら、Azure Monitor ではそのデータを使用して次のことを実行できます。

### <a name="route"></a>ルート
監視データは他の場所にリアルタイムでストリーミングできます。

たとえば、次のようになります。

- Application Insights に送信すると、Application Insights のより優れた視覚化ツールや分析ツールを使用できます。
- Event Hubs に送信すると、サード パーティ製のツールにルーティングできます。 

### <a name="store-and-archive"></a>保存とアーカイブ
一部の監視データは既に保存されており、Azure Monitor で一定期間使用できます。 
- メトリックは 30 日間保存されます。 
- アクティビティ ログのエントリは 90 日間保存されます。 
- 診断ログは一切保存されません。 

上記の期間よりも長くデータを保存したい場合は、Azure Storage を使用できます。 監視データは、設定した保持ポリシーに基づいてストレージ アカウントで保持されます。 Azure Storage でこのデータが占有する容量に対して料金が発生します。 

このデータを使用する方法はいくつかあります。

- データが書き込まれたら、それを Azure の内部または外部の他のツールに読み込ませ、処理する。
- データをローカルにダウンロードしてアーカイブするか、クラウドのアイテム保持ポリシーを変更して、データを長期間保持する。  
- アーカイブ目的で、期限を定めることなく Azure Storage にデータを置いておく。 

### <a name="query"></a>クエリ
Azure Monitor REST API、クロス プラットフォーム コマンド ライン インターフェイス (CLI) コマンド、PowerShell コマンドレット、または .NET SDK を使えば、システム ストレージまたは Azure ストレージ内のデータにアクセスできます。

たとえば、次のようになります。

* 独自に作成したカスタム監視アプリケーションのためのデータを取得する
* カスタム クエリを作成し、そのデータをサードパーティ製アプリケーションに送信する

### <a name="visualize"></a>視覚化
グラフィックスやグラフで監視データを視覚化すると、データ自体を見るよりも格段に早く傾向を把握できます。  

視覚化の方法には、次のようなものがあります。

* Azure ポータルの使用
* Azure Application Insights にデータをルーティングする
* Microsoft PowerBI にデータをルーティングする
* ライブ ストリーミングを使ってサード パーティ製の視覚化ツールにデータをルーティングするか、ツールに Azure Storage のアーカイブからデータを読み込ませる


### <a name="automate"></a>自動化
アラートはもとより、プロセス全体をトリガーする場合にも、監視データを利用できます。 たとえば、次のようになります。

* データを使用し、アプリケーションの負荷に応じてコンピューティング インスタンスを自動でスケールアップまたはスケールダウンする
* メトリックが事前に定義されているしきい値に達したときにメールを送信する
* Web URL (webhook) を呼び出して Azure 外部のシステムでアクションを実行する
* Azure Automation で Runbook を開始し、さまざまなタスクを実行する

## <a name="methods-of-accessing-azure-monitor"></a>Azure Monitor にアクセスする方法
一般に、次のいずれかの方法で、データの追跡、ルーティング、および取得の操作を行うことができます。 ただし、アクションやデータの種類によっては利用できない方法もあります。

* [Azure ポータル](https://portal.azure.com)
* [PowerShell](insights-powershell-samples.md)  
* [クロスプラットフォーム コマンド ライン インターフェイス (CLI)](insights-cli-samples.md)
* [REST API](https://docs.microsoft.com/rest/api/monitor/)
* [.NET SDK](http://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

## <a name="next-steps"></a>次のステップ
項目ごとに詳しい情報を確認できます。
- Azure Monitor のビデオ チュートリアルは、  
「[Get Started with Azure Monitor](https://channel9.msdn.com/Blogs/Azure-Monitoring/Get-Started-with-Azure-Monitor)」 (Azure Monitor を使ってみる) をご覧ください。 Azure Monitor の使用シナリオについて説明する追加ビデオ「[Explore Microsoft Azure monitoring and diagnostics (Microsoft Azure 監視と診断の詳細)](https://channel9.msdn.com/events/Ignite/2016/BRK2234)」および [Azure Monitor の紹介ビデオ (Ignite 2016 より)](https://myignite.microsoft.com/videos/4977) もあります。
- Azure Monitor インターフェイスについては、「[Azure Monitor の使用](monitoring-get-started.md)」をご覧ください。
- クラウド サービス、仮想マシン、仮想マシン スケール セット、または Service Fabric アプリケーションの問題を診断する場合は、[Azure 診断拡張機能](../azure-diagnostics.md)を設定してください。
- [Application Insights](https://azure.microsoft.com/documentation/services/application-insights/) App Service Web アプリの問題を診断する場合には、こちらをご覧ください。
- [Azure Storage のトラブルシューティング](../storage/common/storage-e2e-troubleshooting.md) Storage Blobs、テーブル、またはキューを使用している場合には、こちらをご覧ください。
- [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) と [Operations Management Suite](https://www.microsoft.com/oms/)

