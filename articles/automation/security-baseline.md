---
title: Azure Automation 用の Azure セキュリティ ベースライン
description: Automation 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e78f4133e7f722870f6c84de2ab7e784cd151d79
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562686"
---
# <a name="azure-security-baseline-for-automation"></a>Automation 用の Azure セキュリティ ベースライン

Automation 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Automation アカウントでは、サービスへのアクセスをプライベート エンドポイント経由に制限する Azure Private Link がまだサポートされていません。 Azure のリソースに対して認証および実行される Runbook は、Azure サンドボックスで実行され、共有バックエンド リソース (Microsoft が相互を分離する責任を負います) を利用します。そのネットワークは制限されておらず、パブリック リソースにアクセスできます。 Azure Automation には現在、プライベート ネットワークの仮想ネットワーク統合として Hybrid Runbook Worker のサポートを超えるものはありません。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Runbook の分離性をさらに高めるために、Azure 仮想マシンで実行されている Hybrid Runbook Worker を使用できます。 Azure 仮想マシン を作成する場合は、仮想ネットワーク (VNet) を作成するか、既存の VNet を使用してサブネットで VM を構成する必要があります。 デプロイされたすべてのサブネットに、アプリケーションの信頼されたポートおよびソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認します。 サービス固有の要件については、その特定のサービスのセキュリティに関する推奨事項を参照してください。

または、特定の要件がある場合は、それを満たすために Azure Firewall を使用することもできます。

* [Azure における仮想ネットワークと仮想マシン](../virtual-machines/network-overview.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

* [Runbook の実行環境](./automation-runbook-execution.md#runbook-execution-environment)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Automation には現在、プライベート ネットワークの仮想ネットワーク統合として Hybrid Runbook Worker のサポートを超えるものはありません。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、それらのワーカーを含むサブネットがネットワーク セキュリティ グループ (NSG) を使用して有効になっていることを確認し、トラフィック監査のためにログをストレージ アカウントに転送するようにフロー ログを構成してください。 NSG フロー ログを Log Analytics ワークスペースに転送し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG ルールとユーザー定義ルートはプライベート エンドポイントには適用されませんが、送信接続の NSG フロー ログと監視情報は引き続きサポートされており、使用できます。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Azure Automation には現在、プライベート ネットワークの仮想ネットワーク統合として Hybrid Runbook Worker のサポートを超えるものはありません。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、Hybrid Runbook Worker をホストしている仮想ネットワークで分散型サービス拒否 (DDoS) Standard 保護を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用することにより、既知の悪意のある IP アドレスとの通信を拒否できます。 各 Virtual Network セグメントの Azure Firewall を構成して、脅威インテリジェンスを有効にし、悪意のあるネットワーク トラフィックに対して**警告して拒否**するように構成します。

Azure Security Center の Just In Time ネットワーク アクセスを使用すると、限られた期間について、承認された IP アドレスへの Windows 仮想マシンの公開を制限できます。 また、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限するために、NSG 構成に Azure Security Center のアダプティブ ネットワーク強化の推奨事項を使用します。

* [DDoS 保護を構成する方法](../virtual-network/manage-ddos-protection.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/threat-protection.md)

* [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../security-center/security-center-adaptive-network-hardening.md)

* [Azure Security Center の Just In Time ネットワーク アクセス制御について](../security-center/security-center-just-in-time.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:Azure Automation には現在、プライベート ネットワークの仮想ネットワーク統合として Hybrid Runbook Worker のサポートを超えるものはありません。Hybrid Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、NSG フロー ログをストレージ アカウントに記録して、runbook worker として機能する Azure Virtual Machines のフロー レコードを生成できます。 異常なアクティビティを調査するときに、Network Watcher パケット キャプチャを有効にして、通常とは異なる異常なアクティビティがないかネットワーク トラフィックを確認できるようにすることができます。

* [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

* [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: Azure Automation には現在、プライベート ネットワークの仮想ネットワーク統合として Hybrid Runbook Worker のサポートを超えるものはありません。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシン上でホストされる Hybrid Runbook Worker を使用している場合は、Network Watcher によって提供されるパケット キャプチャとオープン ソース IDS ツールを組み合わせて、それらの worker マシンに対するさまざまな脅威についてネットワーク侵入検出を実行できます。 また、必要に応じて、Virtual Network セグメントに Azure Firewall を デプロイし、脅威インテリジェンスを有効にして、悪意のあるネットワーク トラフィックに対して "警告して拒否する" ように構成できます。

* [Network Watcher とオープン ソース ツールを使用したネットワーク不正侵入検出の実行](../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Virtual Network サービス タグを使用して、Azure に構成されているネットワーク セキュリティ グループまたは Azure Firewall のうち、Automation のリソースへのアクセスを必要とするものに対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (GuestAndHybridManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

* [サービス タグとその使用方法の概要](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Automation によって使用されるネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境アーティファクトを単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [ネットワークに関する Azure Policy のサンプル](/azure/governance/policy/samples/#network)

* [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワークのセキュリティおよびトラフィック フローに関連する NSG およびその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、リソース構成を監視し、ネットワーク リソースに対する変更を検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/platform/activity-log.md#view-the-activity-log)

* [Azure Monitor でアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は、Azure リソースのタイム ソースを保持しています。 ただし、Windows 仮想マシンで実行されている Hybrid Runbook Worker の時刻同期設定をユーザー側で管理することもできます。

* [Azure コンピューティング リソースの時刻同期を構成する方法](../virtual-machines/windows/time-sync.md)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: ログ データを Azure Monitor ログに転送し、Azure Automation リソースによって生成されるセキュリティ データを集計します。 Azure Monitor 内で、ログ クエリを使用して検索と分析を行い、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。 Azure Automation は、Runbook ジョブの状態、ジョブ ストリーム、Automation の状態の構成データ、更新管理、および変更追跡またはインベントリのログを Log Analytics ワークスペースに送信できます。 この情報は、Azure portal、Azure PowerShell、および Azure Monitor Logs API から参照できます。これにより、簡単な調査を実行できます。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

* [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/platform/diagnostic-settings.md)

* [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

* [Azure Monitor ログに Azure Automation のジョブ データを転送する](./automation-manage-send-joblogs-log-analytics.md)

* [DSC を Azure Monitor ログと統合する](./automation-dsc-diagnostics.md)

* [リンクされた Log Analytics ワークスペースでサポートされるリージョン](./how-to/region-mappings.md)

* [Update Management ログにクエリを実行する](./update-management/update-mgmt-query-logs.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:監査およびアクティビティのログ (イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素を含みます) にアクセスするために、Azure Monitor を有効にします。

* [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](../azure-monitor/platform/diagnostic-settings.md)

* [Azure アクティビティ ログ イベントを表示および取得する](../azure-monitor/platform/activity-log.md#view-the-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:マルチテナント runbook worker で Azure Automation を使用する場合、この制御は適用されず、基になる仮想マシンがプラットフォームによって処理されます。

Hybrid Runbook Worker 機能を使用する場合は、Azure Security Center で Windows 仮想マシンのセキュリティ イベント ログの監視が行われます。 組織でそのセキュリティ イベント ログ データを保持する場合は、データ収集レベル内で格納でき、その後 Log Analytics でそのクエリを実行できます。 さまざまなレベルがあります:最小、共通、およびすべて。これらの詳細は、次のリンクに記載されています。

* [Azure Security Center でのデータ収集レベルを構成する](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics でデータ保持期間を変更する](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

* [Automation アカウントのデータ保持の詳細](./automation-managing-data.md#data-retention)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 ログの確認と、ログ データに対するクエリの実行には、Azure Monitor ログ クエリを使用します。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

* [Azure Monitor のログ クエリについて](../azure-monitor/log-query/get-started-portal.md)

* [Azure Monitor でカスタム クエリを実行する方法](../azure-monitor/log-query/get-started-queries.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: セキュリティ ログやイベントで検出される異常なアクティビティに対する監視およびアラート送信のために、Azure Monitor と共に Azure Security Center を使用します。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

* [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

* [Azure Monitor ログ データに関するアラートを送信する方法](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: マルチテナント runbook worker で Azure Automation を使用する場合、この制御は適用されず、基になる仮想マシンがプラットフォームによって処理されます。

ただし、Hybrid Runbook Worker 機能を使用する場合は、Azure Cloud Services と仮想マシンに対して Microsoft Antimalware を使用できます。 Azure Storage アカウントに対するイベントをログするように仮想マシンを構成します。 Storage アカウントからイベントを取り込み、必要に応じてアラートを作成するように、Log Analytics ワークスペースを構成します。 Azure Security Center の"コンピューティングとアプリ" の推奨事項に従います。

* [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md)

* [仮想マシンのゲストレベルの監視を有効にする方法](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: マルチテナント runbook worker で Azure Automation を使用する場合、この制御は適用されず、基になる仮想マシンがプラットフォームによって処理されます。

ただし、Hybrid Runbook Worker 機能を使用する場合は、Azure Security Center で Azure 仮想マシンのセキュリティ イベント ログの監視が行われます。 Security Center は、自動プロビジョニングが有効になっている場合、サポートされているすべての Azure VM と作成された新規のものに対して Log Analytics エージェントをプロビジョニングします。 または、このエージェントを手動でインストールすることができます。 エージェントにより、プロセス作成イベント 4688 と、イベント 4688 内の commandline フィールドが有効になります。 VM に作成された新しいプロセスは、イベント ログに記録され、Security Center の検出サービスによって監視されます。

* [Azure Security Center でのデータ収集](../security-center/security-center-enable-data-collection.md#data-collection-tier)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: 明示的な割り当てとクエリの実行が可能である Azure Active Directory 組み込みの管理者ロールを使用します。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。 Automation アカウントの実行アカウントを Runbook に使用する場合は常に、これらのサービス プリンシパルもインベントリ内で追跡されるようにします。多くの場合、それらは管理者特権のアクセス許可を持つためです。 未使用の実行アカウントを削除して、攻撃にさらされる領域を最小限にします。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [実行アカウントまたはクラシック実行アカウントの削除](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation の実行アカウントを管理する](./manage-runas-account.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Automation アカウントには、既定のパスワードの概念がありません。 お客様は、サービスまたはその Hybrid Runbook Worker 上で動作する、既定のパスワードを使用する可能性があるサードパーティ アプリケーションおよびマーケット プレース サービスに対して責任があります。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。 Automation アカウントの実行アカウントを Runbook に使用する場合は常に、これらのサービス プリンシパルもインベントリ内で追跡されるようにします。多くの場合、それらは管理者特権のアクセス許可を持つためです。 Runbook により自動プロセスが正常に実行されるために必要な最低限の特権アクセス許可を指定して、これらの ID のスコープを設定します。 未使用の実行アカウントを削除して、攻撃にさらされる領域を最小限にします。

また、Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure Resource Manager を使用して、Just-In-Time/Just-Enough-Access を有効にすることもできます。

* [Privileged Identity Management について](../active-directory/privileged-identity-management/index.yml)

* [実行アカウントまたはクラシック実行アカウントの削除](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation の実行アカウントを管理する](./manage-runas-account.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory で SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

* [Azure Active Directory でのアプリケーションへのシングル サインオン](../active-directory/manage-apps/what-is-single-sign-on.md)

* [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

* [Azure AD を使用して Azure に対する認証を行う](./automation-use-azure-ad.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD の多要素認証 (MFA) を有効にし、Azure Security Center の ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

* [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用のマシンを使用する

**ガイダンス**: 多要素認証が構成されている PAW を使用して、運用環境で Azure Automation アカウント リソースにログインして構成します。

* [特権アクセス ワークステーションについて](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 必要に応じて、お客様は Azure Security Center のリスク検出アラートを Azure Monitor に転送し、カスタムのアラートまたは通知を構成できます。これには、アクション グループを使用します。

* [Azure Security Center のリスク検出 (疑わしいアクティビティ) について](../active-directory/identity-protection/overview-identity-protection.md)

* [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

* [カスタムのアラートおよび通知用にアクション グループを構成する方法](../azure-monitor/platform/action-groups.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可することをお勧めします。

* [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure AD を中央認証および承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 Hybrid Runbook Worker を使用している場合は、実行アカウントの代わりにマネージド ID を利用して、よりシームレスで安全なアクセス許可を有効にできます。

* [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

* [マネージド ID で Runbook 認証を使用する](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 Automation アカウントの実行アカウントを Runbook に使用する場合は常に、これらのサービス プリンシパルもインベントリ内で追跡されるようにします。多くの場合、それらは管理者特権のアクセス許可を持つためです。 未使用の実行アカウントを削除して、攻撃にさらされる領域を最小限にします。

* [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

* [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

* [実行アカウントまたはクラシック実行アカウントの削除](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation の実行アカウントを管理する](./manage-runas-account.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM/監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD のリスクおよび Identity Protection 機能を使用して、ネットワーク リソースのユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

* [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Azure Automation アカウントの場合、Microsoft サポートは、サポート ケースの開いている間は他のツールを使用せずにプラットフォーム リソースのメタデータにアクセスできます。

ただし、Azure 仮想マシンによる Hybrid Runbook Worker を使用していて、サードパーティが顧客データにアクセスする必要がある場合 (サポート リクエスト中など) は、Azure 仮想マシンのカスタマー ロックボックス (プレビュー) を使用して、顧客データへのアクセス要求を確認し、承認または拒否します。

* [カスタマー ロックボックスについて](../security/fundamentals/customer-lockbox-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure Automation リソースを追跡しやすくするために、タグを使用します。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 個別の Automation アカウント リソースを使用して、環境を分離します。 Hybrid Runbook Worker などのリソースは、仮想ネットワークまたはサブネットで分離し、適切にタグ付けし、さらにネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティ保護する必要があります。 機密データを格納または処理する仮想マシンでは、使用されていないときにはオフにするためのポリシーと手順を実装します。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

* [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

* [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

* [Azure Firewall でアラートまたはアラートと拒否を構成する方法](../firewall/threat-intel.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Hybrid Runbook Worker 機能を使用する場合は、Azure Marketplace のサードパーティ ソリューションをネットワーク境界で利用することにより、機密情報の不正転送を監視し、そのような転送をブロックすると同時に情報セキュリティ担当者にアラートを送信します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure 仮想ネットワーク内の Azure リソースに接続しているクライアントが TLS 1.2 以降を確実にネゴシエートできるようにします。 Azure Automation では、(Webhook、DSC ノード、ハイブリッド runbook worker を介した) すべての外部 HTPS エンドポイントに対して、トランスポート層 (TLS) 1.2 以降のバージョンとすべてのクライアント呼び出しが完全にサポートおよび適用されます。

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

* [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

* [Azure Automation の TLS 1.2 の適用](https://azure.microsoft.com/updates/azure-automation-tls12-enforcement/)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報インベントリを更新します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure AD RBAC を使用して、組み込みのロール定義を利用して Azure Automation リソースへのアクセスを制御し、最小特権または ' 必要最低限' のアクセス モデルに従って Automation リソースにアクセスするユーザーにアクセス権を割り当てます。 Hybrid Runbook Worker を使用する場合は、これらの仮想マシンのマネージド ID を利用して、サービス プリンシパルの使用を回避します。マルチテナントまたは Hybrid Runbook Worker の両方を使用する場合は、runbook worker の ID に対して、適切なスコープが設定された RBAC アクセス許可を確実に適用してください。

* [Azure で RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

* [Hybrid Runbook Worker に対する Runbook のアクセス許可](./automation-hybrid-runbook-worker.md#runbook-permissions-for-a-hybrid-runbook-worker)

* [ロールのアクセス許可とセキュリティの管理](./automation-role-based-access-control.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンを公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、サードパーティのホストベースのデータ損失防止ソリューションを使用して、ホストされている Hybrid Runbook Worker 仮想マシンにアクセス制御を適用する必要があります。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Automation でカスタマー マネージド キーを使用します。 Azure Automation では、資格情報、証明書、接続、暗号化された変数など、使用されるすべての "セキュリティで保護された資産" を暗号化するために、カスタマー マネージド キーの使用がサポートされています。 意図しない公開を防ぐために、すべての永続的な変数参照のニーズに合わせて、暗号化された変数を Runbook で利用します。

Hybrid Runbook Worker を使用する場合、仮想マシン上の仮想ディスクは、サーバー側の暗号化または Azure Disk Encryption (ADE) を使用して、保存時に暗号化されます。 Azure Disk Encryption では、Windows の BitLocker 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。 カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

* [Azure Managed Disks のサーバー側暗号化](../virtual-machines/windows/disk-encryption.md)

* [Windows VM 用の Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Azure Automation で変数を管理する](./shared-resources/variables.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、ネットワーク コンポーネント、Azure Automation アカウント、Runbook などの重要な Azure リソースに変更が加えられたときのアラートを作成します。

* [ネットワーク セキュリティ グループの診断ログ](../private-link/private-link-overview.md#logging-and-monitoring)

* [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure リソースに対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います

* [Azure Security Center でのセキュリティに関する推奨事項](../security-center/security-center-recommendations.md)

* [Security Center の推奨事項のリファレンス](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンを公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、Azure Update Management を使用して、仮想マシンの更新プログラムおよび修正プログラムを管理します。 Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。

* [Azure における Update Management](./update-management/update-mgmt-overview.md)

* [VM の更新プログラムと修正プログラムの管理](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンを公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Azure 仮想マシンによる Hybrid Runbook Worker を使用している場合は、Azure Update Management を使用して、仮想マシンの更新プログラムおよび修正プログラムを管理できます。 Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。

* [Azure の Update Management ソリューション](./update-management/update-mgmt-overview.md)

* [Azure VM の更新プログラムとパッチの管理](./update-management/update-mgmt-manage-updates-for-vm.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: スキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合、お客様は選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用して、検出された脆弱性の修復に優先順位を付けることができます。

* [Azure Security Center のセキュリティ スコアについて](../security-center/secure-score-security-controls.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された Asset Discovery ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべての Azure Automation リソースのクエリ実行と検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

* [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

* [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure Automation リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。 未使用の実行アカウントを削除して、攻撃にさらされる領域を最小限にします。

* [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

* [管理グループを作成する方法](../governance/management-groups/create.md)

* [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

* [実行アカウントまたはクラシック実行アカウントの削除](./manage-runas-account.md#delete-a-run-as-or-classic-run-as-account)

* [Azure Automation の実行アカウントを管理する](./manage-runas-account.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成する必要があります。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

さらに、Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリ実行または検出を行います。 これは、ストレージ アカウントを使用する環境など、高セキュリティ ベースの環境に役立ちます。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

* [Azure Automation 用の Azure Policy サンプルの組み込み](./policy-samples.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure Automation オファリングは現在、基になるマルチテナント runbook worker の仮想マシンを公開していないため、これはプラットフォームによって処理されます。 Hybrid Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。 ただし、PowerShell、または Runbook がポータルまたはコマンドレットを使用してアクセスできる Python モジュールをインストール、削除、管理することは可能です。 Runbook の未承認または古いモジュールは、削除または更新する必要があります。

Azure Virtual Machines による Hybrid Runbook Worker を使用している場合は、Azure Automation によって、ワークロードとリソースのデプロイ、運用、および使用停止を完全に制御できます。 Azure 仮想マシンのインベントリを利用して、Virtual Machines 上のすべてのソフトウェアに関する情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、お客様はゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込む必要があります。

* [Azure Automation の概要](./automation-intro.md)

* [Azure VM インベントリを有効にする方法](./automation-tutorial-installed-software.md)

* [Azure Automation でモジュールを管理する](./shared-resources/modules.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: お客様は、自社のガイドラインに従って Azure Policy を使用して、リソースの作成や使用を防ぐことができます。 承認されていないリソースを削除するための独自のプロセスを実装できます。 Azure Automation オファリング内で、PowerShell、または Runbook がポータルまたはコマンドレットを使用してアクセスできる Python モジュールをインストール、削除、管理できます。 Runbook の未承認または古いモジュールは、削除または更新する必要があります。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation でモジュールを管理する](./shared-resources/modules.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: Hybrid Runbook Worker 機能を使用する場合は、Azure Security Center の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみが実行され、承認されていないすべてのソフトウェアの実行がブロックされるようにできます。

* [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: Hybrid Runbook Worker 機能を使用する場合は、ハイブリッド worker 仮想マシンで Azure Security Center 適応型アプリケーション制御機能を使用できます。

適応型アプリケーション制御は、お使いの Azure および Azure 以外のコンピューター (Windows および Linux) 上でどのアプリケーションが実行できるかを制御できる、Azure Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 これが組織の要件を満たしていない場合は、サードパーティ ソリューションを実装します。

* [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセス ポリシーを使用して Azure Resource Manager を操作するユーザーの機能を制限するには、セキュリティで保護されていないか、承認されていない場所またはデバイスからの "Microsoft Azure 管理" アプリに対する [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: Hybrid Runbook Worker 機能を使用する場合は、スクリプトの種類に基づき、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限できます。 また、Azure Security Center の適応型アプリケーション制御を利用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

* [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

* [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: Azure 環境にデプロイされたリスクの高いアプリケーションは、仮想ネットワーク、サブネット、サブスクリプション、管理グループなどの構成体を使用する個別のネットワークおよびリソース コンテナーを使用して分離でき、Azure Firewall、Web アプリケーション ファイアウォール (WAF)、またはネットワーク セキュリティ グループ (NSG) を使用して十分に保護できます。

* [Azure における仮想ネットワークと仮想マシン](../virtual-machines/network-overview.md)

* [Azure Firewall の概要](../firewall/overview.md)

* [Azure Web アプリケーション ファイアウォールの概要 ](../web-application-firewall/overview.md)

* [ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)

* [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

* [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)

* [サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: ご利用の Azure Automation および関連リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からの推奨事項を使用することもできます。

* [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation 用の Azure Policy サンプルの組み込み](./policy-samples.md)

* [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

* [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していません。 これは、プラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、Azure Security Center の推奨事項 [Remediate Vulnerabilities in Security Configurations on your Virtual Machines] (仮想マシンのセキュリティ構成の脆弱性を修復する) を使用して、仮想マシンのセキュリティ構成を維持します。

* [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md)

* [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure Automation に関連付けられている Azure リソースを安全に構成するには、Azure Resource Manager テンプレートと Azure Policy を使用します。 Azure Resource Manager テンプレートは、Azure リソースのデプロイに使用される JSON ベースのファイルです。カスタム テンプレートはすべてコード リポジトリ内に安全に格納され、維持される必要があります。 ソース管理の統合機能を使用して、ソース管理リポジトリ内のスクリプトで Automation アカウントの Runbook を最新の状態に維持します。 Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

* [ソース管理の統合を使用する](./source-control-integration.md)

* [Azure Resource Manager テンプレートの作成に関する情報](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Policy の効果について](../governance/policy/concepts/effects.md)

* [Azure Resource Manager テンプレートを使用して Automation アカウントをデプロイする](./quickstart-create-automation-account-template.md#deploy-the-template)

* [Azure Automation 用の Azure Policy サンプルの組み込み](./policy-samples.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、デプロイ用に Azure 仮想マシンのセキュリティで保護された構成を維持するためのオプションがいくつかあります。

- Azure Resource Manager テンプレート:これらは、Azure portal から VM をデプロイするために使用される JSON ベースのファイルであり、カスタム テンプレートは管理する必要があります。 基本テンプレートの管理は、Microsoft が行います。
- カスタム仮想ハードディスク (VHD):他の方法で管理できない複雑な環境を扱う場合など、状況によっては、カスタム VHD ファイルを使用することが必要な場合があります。
- Azure Automation State Configuration:ベース OS がデプロイされたら、これを使用して設定をより細かく制御し、オートメーション フレームワークを通じて適用できます。

ほとんどのシナリオで、Microsoft ベース VM テンプレートと Azure Automation State Configuration を組み合わせると、セキュリティ要件を満たして維持することができます。

* [VM テンプレートをダウンロードする方法に関する情報](../virtual-machines/windows/download-template.md)

* [Resource Manager テンプレートの作成に関する情報](../virtual-machines/windows/ps-template.md)

* [カスタム VM VHD を Azure にアップロードする方法](/azure-stack/operator/azure-stack-add-vm-image?view=azs-1910)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするために、アクセス許可の付与または拒否を、特定のユーザー、組み込みのセキュリティ グループ、または Azure Active Directory (Azure DevOps に統合されている場合) あるいは Active Directory (TFS に統合されている場合) で定義されたグループに対して行えます。 ソース管理の統合機能を使用して、ソース管理リポジトリ内のスクリプトで Automation アカウントの Runbook を最新の状態に維持します。

* [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

* [ソース管理の統合を使用する](./source-control-integration.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、ストレージ アカウントにあるカスタム OS イメージへのアクセスが、承認されたユーザーのみがアクセスできるように適切に制限されていることを確認してください。

* [Azure での RBAC の概要](../role-based-access-control/rbac-and-directory-admin-roles.md)

* [Azure で RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [エイリアスを使用する方法](../governance/policy/concepts/definition-structure.md#aliases)

* [Azure Automation 用の Azure Policy サンプルの組み込み](./policy-samples.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、任意のクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの構成管理サービスである Azure Automation State Configuration を runbook worker で使用します。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。

* [Azure Automation State Configuration による管理のためのマシンのオンボード](./automation-dsc-onboarding.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Policy を使用して Azure リソース構成をアラート送信および監査します。ポリシーを使用して、プライベート エンドポイントで構成されていない特定のリソースを検出できます。

Hybrid Runbook Worker 機能を使用する場合は、Azure Security Center を利用して Azure 仮想マシンのベースライン スキャンを実行します。 自動構成の他の方法には、Azure Automation State Configuration があります。

* [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

* [Azure Automation State Configuration の使用開始](./automation-dsc-getting-started.md)

* [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

* [Azure Automation 用の Azure Policy サンプルの組み込み](./policy-samples.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Automation オファリングは現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、任意のクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの構成管理サービスである Azure Automation State Configuration を runbook worker に使用します。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。

* [Azure Automation State Configuration による管理のためのマシンのオンボード](./automation-dsc-onboarding.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [マネージド ID で Runbook 認証を使用する](./automation-hrw-run-runbooks.md#runbook-auth-managed-identities)

* [キー コンテナーを作成する方法](../key-vault/secrets/quick-create-portal.md)

* [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/managed-identity.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

* [マネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure Automation オファリングは現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、Azure Windows 仮想マシンに対して Microsoft Antimalware を使用して、runbook worker リソースを継続的に監視および保護します。

* [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: 適用できません。サービスとしての Azure Automation にファイルは格納されません。 Microsoft Antimalware は、Azure のサービス (Azure Automation など) をサポートする、基になっているホストで有効になっていますが、ユーザーのコンテンツに対しては実行されません。

* [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure Automation は現在、基になるマルチテナント runbook worker の仮想マシンまたは OS を公開していないため、これはプラットフォームによって処理されます。 Hybrid Runbook Worker なしですぐに使用できるサービスを使用している場合、この制御は適用されません。

Hybrid Runbook Worker 機能を使用する場合は、Azure 向けの Microsoft Antimalware を使用して、最新の署名、プラットフォーム、およびエンジンの更新プログラムを既定で runbook worker に自動的にインストールします。 Azure Security Center の"計算とアプリ" の推奨事項に従って、すべてのエンドポイントが最新の署名を備え、最新の状態になっているようにします。 Azure Security Center と統合された Microsoft Defender Advanced Threat Protection サービスを使用して、ウイルスやマルウェアベースの攻撃のリスクを限定する追加のセキュリティによって、Windows OS の保護を強化できます。

* [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware をデプロイする方法](../security/fundamentals/antimalware.md)

* [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:Azure Resource Manager を使用して、Azure Automation アカウントおよび関連リソースをデプロイします。 Azure Resource Manager には、Azure Automation アカウントと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能が用意されています。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。

ソース管理の統合機能を使用して、ソース管理リポジトリ内のスクリプトで Automation アカウントの Runbook を最新の状態に維持します。

* [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

* [Azure Automation リソースの Azure Resource Manager テンプレートのリファレンス](/azure/templates/microsoft.automation/allversions)

* [Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](./quickstart-create-automation-account-template.md)

* [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

* [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation の概要](./automation-intro.md)

* [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [ソース管理の統合を使用する](./source-control-integration.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Resource Manager を使用して、Azure Automation アカウントおよび関連リソースをデプロイします。 Azure Resource Manager には、Azure Automation アカウントと関連リソースを復元するためのバックアップとして使用できるテンプレートをエクスポートする機能が用意されています。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。 Azure portal または PowerShell を使用して、Runbook をスクリプト ファイルにエクスポートできます。

* [Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)

* [Azure Automation リソースの Azure Resource Manager テンプレートのリファレンス](/azure/templates/microsoft.automation/allversions)

* [Azure Resource Manager テンプレートを使用して Automation アカウントを作成する](./quickstart-create-automation-account-template.md)

* [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

* [リソース グループ - テンプレートのエクスポート](/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation の概要](./automation-intro.md)

* [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

* [Automation アカウントの Azure データ バックアップ](./automation-managing-data.md#data-backup)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:必要に応じて、分離サブスクリプションに Azure Resource Manager テンプレートのデプロイを定期的に実行できるようにします。 バックアップされたカスタマー マネージド キーの復元をテストします。

* [ARM テンプレートと Azure portal でリソースをデプロイする](../azure-resource-manager/templates/deploy-portal.md)

* [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Automation アカウントのカスタマー マネージド キーの使用](./automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure DevOps を使用して、Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 Azure DevOps で管理するリソースを保護するために、アクセス許可の付与または拒否を、特定のユーザー、組み込みのセキュリティ グループ、または Azure Active Directory (Azure DevOps に統合されている場合) あるいは Active Directory (TFS に統合されている場合) で定義されたグループに対して行えます。

ソース管理の統合機能を使用して、ソース管理リポジトリ内のスクリプトで Automation アカウントの Runbook を最新の状態に維持します。

* [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

* [ソース管理の統合を使用する](./source-control-integration.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

* [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

* [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

* [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

* [連続エクスポートを構成する方法](../security-center/continuous-export.md)

* [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

* [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

* [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
