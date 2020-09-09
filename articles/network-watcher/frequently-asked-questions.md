---
title: Azure Network Watcher についてよく寄せられる質問 (FAQ) | Microsoft Docs
description: この記事では、Azure Network Watcher サービスについてよく寄せられる質問に回答します。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2019
ms.author: damendo
ms.openlocfilehash: b48aab918b477f5c689a50ca476b0b1336642f0f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "77471858"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure Network Watcher に関してよく寄せられる質問 (FAQ)
[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) サービスは、Azure 仮想ネットワーク内のリソースの監視、診断、メトリックの表示、ログの有効化または無効化を行うツール スイートを提供します。 この記事では、そのサービスに関する一般的な質問への回答を示します。

## <a name="general"></a>全般

### <a name="what-is-network-watcher"></a>Network Watcher とは
Network Watcher は、Virtual Machines、Virtual Networks、アプリケーション ゲートウェイ、ロード バランサー、および Azure 仮想ネットワーク内のその他のリソースの IaaS (サービスとしてのインフラストラクチャ) コンポーネントのネットワーク正常性を監視および修復するように設計されています。 PaaS (サービスとしてのプラットフォーム) インフラストラクチャを監視したり、Web/モバイル分析を取得したりするためのソリューションではありません。

### <a name="what-tools-does-network-watcher-provide"></a>Network Watcher で提供されるツール
Network Watcher には、3 つの主要な機能セットがあります。
* 監視
  * [トポロジ ビュー](https://docs.microsoft.com/azure/network-watcher/view-network-topology)には、仮想ネットワーク内のリソースと、リソース間のリレーションシップが表示されます。
  * [接続モニター](https://docs.microsoft.com/azure/network-watcher/connection-monitor)では、VM と他のネットワーク リソースとの間の接続および待機時間を監視できます。
  * [Network Performance Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor) を使用すると、ハイブリッド ネットワーク アーキテクチャ、ExpressRoute 回線、サービス/アプリケーション エンドポイントの全体にわたって接続と待機時間を監視できます。  
* 診断
  * [IP フロー検証](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview)では、VM レベルでトラフィックのフィルター処理に関する問題を検出できます。
  * [次ホップ](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview)は、トラフィックのルートを検証してルーティングの問題を検出するのに役立ちます。
  * [接続のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-portal)では、VM と他のネットワーク リソースとの間で、1 回限りの接続と待機時間のチェックが有効になります。
  * [パケット キャプチャ](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview)を使用すると、仮想ネットワーク内の VM でのすべてのトラフィックをキャプチャできます。
  * [VPN のトラブルシューティング](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-overview)では、VPN ゲートウェイと接続に対して複数の診断チェックを実行し、問題のデバッグに役立てることができます。
* ログ記録
  * [NSG フロー ログ](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)では、[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) のすべてのトラフィックをログに記録できます。
  * [Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) では、NSG フロー ログのデータを処理して、ネットワーク トラフィックの視覚化、クエリ、分析、解釈を行うことができます。


詳細については、[Network Watcher の概要のページ](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)を参照してください。


### <a name="how-does-network-watcher-pricing-work"></a>Network Watcher の価格のしくみ
Network Watcher のコンポーネントと各コンポーネントの価格については、[価格のページ](https://azure.microsoft.com/pricing/details/network-watcher/)を参照してください。

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Network Watcher がサポートされている/利用できるリージョンはどれですか?
[Azure サービスの利用可能性のページ](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher)で、最新のリージョン別の利用可能性を確認できます。

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Network Watcher を使用するために必要なアクセス許可は何ですか?
[Network Watcher を使用するために必要な RBAC アクセス許可](https://docs.microsoft.com/azure/network-watcher/required-rbac-permissions)に関する記事にある一覧を参照してください。 リソースをデプロイするために、NetworkWatcherRG (下記参照) に対する共同作成者のアクセス許可が必要です。

### <a name="how-do-i-enable-network-watcher"></a>Network Watcher を有効化する方法
Network Watcher サービスは、すべてのサブスクリプションで[自動的に有効になります](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/)。

### <a name="what-is-the-network-watcher-deployment-model"></a>Network Watcher デプロイ モデルは何ですか?
Network Watcher の親リソースは、各リージョンで一意のインスタンスを使用してデプロイされます。 名前付け形式: NetworkWatcher_RegionName。 例:NetworkWatcher_centralus は、"米国中部" リージョンの Network Watcher リソースです。

### <a name="what-is-the-networkwatcherrg"></a>NetworkWatcherRG とは何ですか?
Network Watcher リソースは、自動的に作成される非表示の **NetworkWatcherRG** リソース グループに配置されます。 たとえば、NSG フロー ログ リソースは Network Watcher の子リソースであり、NetworkWatcherRG で有効になっています。

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Network Watcher 拡張機能をインストールする必要があるのはなぜですか? 
Network Watcher 拡張機能は、VM からトラフィックを生成またはインターセプトする必要があるすべての機能に必要です。 

### <a name="which-features-require-the-network-watcher-extension"></a>Network Watcher 拡張機能が必要なのはどの機能ですか?
パケット キャプチャ、接続のトラブルシューティング、接続モニターの機能では、Network Watcher 拡張機能が必要です。

### <a name="what-are-resource-limits-on-network-watcher"></a>Network Watcher でのリソース制限とは何ですか?
すべての制限については、[サービス制限の](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#network-watcher-limits)に関するページを参照してください。  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>リージョンごとに Network Watcher の 1 つのインスタンスしか許可されないのはなぜですか? 
機能が動作するためには、Network Watcher は 1 つのサブスクリプションに対して一度だけ有効にされる必要があります。これはサービスの上限ではありません。

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Network Watcher リソースを管理するにはどうすればよいですか？ 
Network Watcher リソースは、Network Watcher のバックエンドサービスを表しており、Azure によって完全に管理されています。 お客様はその管理を行う必要はありません。 移動などの操作は、リソースではサポートされていません。 ただし、[リソースを削除することはできます](https://docs.microsoft.com/azure/network-watcher/network-watcher-create#delete-a-network-watcher-in-the-portal)。 

## <a name="nsg-flow-logs"></a>NSG フロー ログ

### <a name="what-does-nsg-flow-logs-do"></a>NSG フロー ログの機能
Azure ネットワーク リソースは、[ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) を使用して結合および管理できます。 NSG フロー ログを使用すると、NSG を介してすべてのトラフィックに関する 5 組のフロー情報をログに記録できます。 未処理のフロー ログは Azure ストレージ アカウントに書き込まれ、必要に応じてさらに処理、分析、クエリ実行、またはエクスポートできます。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>ファイアウォールの背後にあるストレージ アカウントで NSG フロー ログを使用する方法

ファイアウォールの背後にあるストレージ アカウントを使用するには、信頼できる Microsoft サービスからストレージ アカウントにアクセスするための例外を指定する必要があります。

* ポータルのグローバル検索でストレージ アカウントの名前を入力するか、[[ストレージ アカウント] のページ](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts)から、ストレージ アカウントに移動します
* **[設定]** セクションで、 **[ファイアウォールと仮想ネットワーク]** を選択します
* [許可するアクセス元] で **[選択されたネットワーク]** を選択します。 次に、 **[例外]** 下で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** の横にあるボックスにチェックを付けます 
* 既に選択されている場合、変更は必要ありません。  
* [NSG フロー ログの概要ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)でターゲットの NSG を見つけて、上記のストレージ アカウントを選択した状態で NSG フロー ログを有効にします。

数分後にストレージ ログを確認できます。TimeStamp が更新されていることや新しい JSON ファイルが作成されていることがわかります。

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>サービス エンドポイントの背後にあるストレージ アカウントで NSG フロー ログを使用する方法

NSG フロー ログはサービス エンドポイントと互換性があります。追加の構成は不要です。 仮想ネットワークの[サービス エンドポイントを有効にする方法に関するチュートリアル](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources#enable-a-service-endpoint)を参照してください。


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>フロー ログのバージョン 1 と 2 の違い
フロー ログ バージョン 2 では、"*フロー状態*" という概念が導入されており、転送するバイトとパケットに関する情報が保存されます。 詳細については、[こちら](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview#log-file)を参照してください。

## <a name="next-steps"></a>次の手順
 - Network Watcher の使用を開始するためのいくつかのチュートリアルについては、[ドキュメントの概要のページ](https://docs.microsoft.com/azure/network-watcher/)を参照してください。
