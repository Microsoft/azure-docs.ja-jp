---
title: Cognitive Services の Azure セキュリティ ベースライン
description: Cognitive Services の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bde1ff4003ba69a4c5449996f4e18d646e6ecff6
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498346"
---
# <a name="azure-security-baseline-for-cognitive-services"></a>Cognitive Services の Azure セキュリティ ベースライン

Cognitive Services 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Cognitive Services は、多層型のセキュリティ モデルを採用しています。 このモデルでは、ネットワークの特定のサブセットに、Cognitive Services アカウントを固定することができます。 ネットワーク ルールを構成すると、指定したネットワークのセットを経由してデータを要求しているアプリケーションのみが、アカウントにアクセスできます。 要求フィルターを使用してリソースへのアクセスを制限して、Azure 仮想ネットワーク内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求のみを許可することができます。

Cognitive Services の仮想ネットワークとサービス エンドポイントのサポートは、特定のリージョンのセットに限定されます。

* [Azure Cognitive Services 仮想ネットワークを構成する方法](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)

* [Azure 仮想ネットワークの概要](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Cognitive Services コンテナーと同じ仮想ネットワークに仮想マシンがデプロイされている場合、ネットワーク セキュリティ グループ (NSG) を使用して、データ窃盗のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: コンテナー内で Cognitive Services を使用している場合は、悪意のあるトラフィックをフィルター処理し、エンドツーエンドの TLS 暗号化をサポートするフロントエンドの Web アプリケーション ファイアウォール ソリューションを使用して、コンテナーのデプロイを強化することができます。これにより、コンテナー エンドポイントがプライベートで安全な状態に維持されます。

Cognitive Services コンテナーでは、課金のために使用状況測定情報を送信する必要があることに注意してください。 唯一の例外は、オフライン コンテナーです。それらは、異なる課金方法に従っているからです。 Cognitive Services コンテナーが依存しているさまざまなネットワーク チャネルの一覧表示を許可しないと、コンテナーは動作しなくなります。 ホストは、ポート 443 と次のドメインの一覧表示を許可する必要があります。
- *.cognitive.microsoft.com
- cognitiveservices.azure.com

また、Cognitive Services コンテナーによって Microsoft サーバーに作成されるセキュリティで保護されたチャネルで、ファイアウォール ソリューションのディープ パケット インスペクションを無効にする必要があることにも注意してください。 そうしないと、コンテナーは正しく機能しません。

* [Azure Cognitive Services コンテナーのセキュリティについて](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Azure Cognitive Services コンテナーと同じ仮想ネットワークに仮想マシンがデプロイされている場合は、Azure Policy を使用して、関連するネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Cache for Redis インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.CognitiveServices" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用します。 次のように、組み込みのポリシー定義を使用することもできます。
- DDoS Protection Standard を有効にする必要がある

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

コンテナー内で Cognitive Services を使用している場合は、悪意のあるトラフィックをフィルター処理し、エンドツーエンドの TLS 暗号化をサポートするフロントエンドの Web アプリケーション ファイアウォール ソリューションを使用して、コンテナーのデプロイを強化することができます。これにより、コンテナー エンドポイントがプライベートで安全な状態に維持されます。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint を作成する方法](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

* [Azure Cognitive Services コンテナーのセキュリティについて](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Cognitive Services コンテナーと同じ仮想ネットワークに仮想マシンがデプロイされている場合、ネットワーク セキュリティ グループ (NSG) を使用して、データ窃盗のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: コンテナー内で Cognitive Services を使用している場合は、悪意のあるトラフィックをフィルター処理し、エンドツーエンドの TLS 暗号化をサポートするフロントエンドの Web アプリケーション ファイアウォール ソリューションを使用して、コンテナーのデプロイを強化することができます。これにより、コンテナー エンドポイントがプライベートで安全な状態に維持されます。 ペイロード検査を無効にする能力を備えた IDS 機能または IPS 機能をサポートする Azure Marketplace のプランを選択できます。

Cognitive Services コンテナーでは、課金のために使用状況測定情報を送信する必要があることに注意してください。 唯一の例外は、オフライン コンテナーです。それらは、異なる課金方法に従っているからです。 Cognitive Services コンテナーが依存しているさまざまなネットワーク チャネルの一覧表示を許可しないと、コンテナーは動作しなくなります。 ホストは、ポート 443 と次のドメインの一覧表示を許可する必要があります。
- *.cognitive.microsoft.com
- cognitiveservices.azure.com

また、Cognitive Services コンテナーによって Microsoft サーバーに作成されるセキュリティで保護されたチャネルで、ファイアウォール ソリューションのディープ パケット インスペクションを無効にする必要があることにも注意してください。 そうしないと、コンテナーは正しく機能しません。

* [Azure Cognitive Services コンテナーのセキュリティについて](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: コンテナー内で Cognitive Services を使用している場合は、悪意のあるトラフィックをフィルター処理し、エンドツーエンドの TLS 暗号化をサポートするフロントエンドの Web アプリケーション ファイアウォール ソリューションを使用して、コンテナーのデプロイを強化することができます。これにより、コンテナー エンドポイントがプライベートで安全な状態に維持されます。

Cognitive Services コンテナーでは、課金のために使用状況測定情報を送信する必要があることに注意してください。 唯一の例外は、オフライン コンテナーです。それらは、異なる課金方法に従っているからです。 Cognitive Services コンテナーが依存しているさまざまなネットワーク チャネルの一覧表示を許可しないと、コンテナーは動作しなくなります。 ホストは、ポート 443 と次のドメインの一覧表示を許可する必要があります。
- *.cognitive.microsoft.com
- cognitiveservices.azure.com

また、Cognitive Services コンテナーによって Microsoft サーバーに作成されるセキュリティで保護されたチャネルで、ファイアウォール ソリューションのディープ パケット インスペクションを無効にする必要があることにも注意してください。 そうしないと、コンテナーは正しく機能しません。

* [Azure Cognitive Services コンテナーのセキュリティについて](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#azure-cognitive-services-container-security)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ネットワーク セキュリティ グループ (NSG) または Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

また、アプリケーション セキュリティグループ (ASG) を使用して、複雑なセキュリティ構成を簡略化することもできます。 ASG を使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。

* [仮想ネットワーク サービス タグ](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [アプリケーション セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Cognitive Services コンテナーに関連するネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Cache for Redis インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.CognitiveServices" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用します。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint を作成する方法](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Cognitive Services コンテナーに関連付けられているネットワーク リソースを分類別に論理的に整理するために、それらのリソースにタグを使用します。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Cognitive Services コンテナーに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor でアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Microsoft では、Cognitive Services などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージア カウントにログを送信してアーカイブします。 アクティビティ ログは、コントロール プレーン レベルで Azure Cognitive Services コンテナーで実行された操作に関する分析情報を提供します。 Azure アクティビティ ログのデータを使用すると、Azure Cache for Redis インスタンスのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを確認できます。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

また、Azure Cognitive Services では、分析、アラート、レポートの目的で収集して使用できる診断イベントを送信します。 Azure portal を使用して、Cognitive Services コンテナーの診断設定を構成できます。 1 つ以上の診断イベントをストレージ アカウント、イベント ハブ、Log Analytics ワークスペースに送信できます。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Cognitive Services の診断設定を使用する](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 これらのログでは、問題の識別やデバッグに使用されるリソースの操作に関する豊富で頻繁なデータが提供されます。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、Azure Cognitive Services 用に収集された可能性があるアクティビティ ログ データに基づいて、多くの他の分析情報を提供します。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する方法](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Cognitive Services でサポートされているメトリックに対してアラートを生成するには、Azure Monitor の [Alerts &amp; Metrics]\(アラートとメトリック\) セクションに移動します。

Cognitive Services コンテナーの診断設定を構成し、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペース内で、事前に定義された一連の条件が発生したときに、アラートが発生するように構成します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Azure Monitor を使用してログ アラートを作成、表示、管理する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Cognitive Services では、マルウェア対策関連のログの処理や生成は行われません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Cognitive Services では、DNS 関連のログの処理や生成は行われません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Cognitive Services へのコントロール プレーン アクセスは、Azure Active Directory (AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

Azure Cognitive Services へのデータ プレーン アクセスは、アクセス キーを使用して制御されます。 これらのキーは、キャッシュに接続するクライアントによって使用され、いつでも再生成できます。

アプリケーションに既定のパスワードを組み込むことは推奨されません。 代わりに、パスワードを Azure Key Vault に格納し、Azure Active Directory を使用して取得することができます。

* [Azure Cache for Redis のアクセス キーを再生成する方法](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

* [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy を使用する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Cognitive Services では、アクセス キーを使用してユーザーが認証されます。データプ レーン レベルでのシングル サインオン (SSO) はサポートされていません。 Azure Cognitive Services のコントロール プレーンへのアクセスは、REST API 経由で行うことができ、SSO がサポートされています。 認証を行うには、Azure Active Directory から取得した要求の Authorization ヘッダーを JSON Web トークンに設定します。

* [Azure Cognitive Services REST API について](https://docs.microsoft.com/rest/api/cognitiveservices/)

* [Azure AD を使用した SSO の概要](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (AD) Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center で ID とアクセスを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

* [特権アクセス ワークステーションについて](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成します。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

* [Privileged Identity Management (PIM) をデプロイする方法](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan)

* [Azure AD のリスク検出の概要](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure Active Directory (AD) 条件付きアクセスのネームド ロケーションを、IP アドレス範囲または国/リージョンの特定の論理グループからのアクセスのみ許可するように構成します。

* [Azure でネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (AD) を中央認証および承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。 ユース ケースで AD 認証がサポートされている場合は、Azure AD を使用して Cognitive Services API への要求を認証します。

現時点では、Computer Vision API、Face API、Text Analytics API、Immersive Reader、Form Recognizer、Anomaly Detector、およびすべての Bing サービス (Bing Custom Search を除く) のみにおいて、Azure AD を使用した認証がサポートされています。

* [Cognitive Services に対する要求の認証方法](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-azure-active-directory)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、顧客は、Azure ID アクセス レビューを利用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

顧客は、API Management ユーザー アカウントのインベントリを維持し、必要に応じてアクセスを調整できます。 API Management では、開発者は、API Management を使用して公開された API のユーザーになります。 既定では、新しく作成された開発者アカウントは "アクティブ" になり、"開発者" グループに関連付けられます。 アクティブ状態の開発者アカウントを使用すると、サブスクリプションがあるすべての API にアクセスできます。

* [Azure API Management でユーザー アカウントを管理する方法](https://docs.microsoft.com/azure/api-management/api-management-howto-create-or-invite-developers)

* [API Management ユーザーの一覧を取得する方法](https://docs.microsoft.com/powershell/module/az.apimanagement/get-azapimanagementuser?view=azps-3.1.0)

* [Azure ID アクセス レビューの使用方法](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサードパーティの SIEM との統合が可能です。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:コントロール プレーンでのアカウント ログイン動作の偏差について、Azure Active Directory (AD) Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD の危険なサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: まだ使用できません。Azure Cognitive Services では、カスタマー ロックボックスはまだサポートされていません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、VNet/サブネットで分割し、適切にタグを付け、NSG または Azure Firewall で保護する必要があります。 機密データを格納または処理するリソースは、十分に分離する必要があります。 機密データを格納または処理する仮想マシンでは、使用されていないときにはオフにするためのポリシーとプロシージャを実装します。

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [仮想ネットワークを作成する方法](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

* [Azure Firewall をデプロイする方法l](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

* [Azure Firewall でアラートまたはアラートと拒否を構成する方法](https://docs.microsoft.com/azure/firewall/threat-intel)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: まだ使用できません。Azure Cognitive Services では、データの識別、分類、損失防止機能はまだ使用できません。

Microsoft では、Azure Cognitive Services 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 現在は使用できません

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: HTTP 経由で公開されるすべての Cognitive Services エンドポイントでは、TLS 1.2 が適用されます。 セキュリティ プロトコルが適用されている場合、Cognitive Services エンドポイントを呼び出そうとするコンシューマーは、次のガイドラインに従う必要があります。
- クライアント オペレーティング システム (OS) では、TLS 1.2 がサポートされている必要があります。
- HTTP の呼び出しに使用される言語 (およびプラットフォーム) では、要求の一部として TLS 1.2 を指定する必要があります (言語とプラットフォームによっては、TLS の指定は暗黙的または明示的に行われます)。

* [Azure Cognitive Services のトランスポート層セキュリティについて](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Cognitive Services では、データの識別、分類、損失防止の各機能はまだ使用できません。 機密情報が含まれているインスタンスにタグを付け、コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory (Azure AD) のロールベースのアクセス制御 (RBAC) を使用して、Azure Cognitive Services コントロール プレーン (つまり、Azure portal) へのアクセスを制御します。

* [Azure RBAC を構成する方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft では、Azure Cognitive Services 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Cognitive Services の保存時の暗号化は、使用されている特定のサービスに依存します。 ほとんどの場合、データは、FIPS 140-2 に準拠する 256 ビット AES 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードやアプリケーションを変更する必要はありません。

Azure Key Vault を使用してカスタマー マネージド キーを格納することもできます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。

* [保存時の情報を暗号化するサービスの一覧](https://docs.microsoft.com/azure/cognitive-services/encryption/cognitive-services-encryption-keys-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Cognitive Services の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Microsoft では、Azure Cognitive Services をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Microsoft では、Azure Cognitive Services をサポートしている基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された Asset Discovery ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Cache for Redis インスタンスと関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

さらに、Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Resource Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy を使用して、Azure Cognitive Services コンテナーの標準的なセキュリティ構成を定義して実装します。 Azure Cache for Redis インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.CognitiveServices" 名前空間で Azure Policy エイリアスを使用します。

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Azure Cognitive Services コンテナーおよび関連リソースにカスタムの Azure Policy 定義または Azure Resource Manager テンプレートを使用している場合は、Azure Repos を使用してコードを安全に格納して管理します。

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.Cache" 名前空間で Azure Policy エイリアスを使用して、システム構成のアラート通知、監査、適用を行うためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: "Microsoft.CognitiveServices" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタムの Azure Policy 定義を作成します。 Azure Policy の [audit]、[deny]、[deploy if not exist] を使用して、ご自分の Azure Cache for Redis インスタンスおよび関連リソースの構成を自動的に適用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure Cognitive Services API へのアクセスに使用されている Azure App Service で実行中の Azure 仮想マシンまたは Web アプリでは、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Cognitive Services のキー管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除が有効になっていることを確認します。

* [Azure マネージド ID と統合する方法](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

* [キー コンテナーを作成する方法](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

* [マネージド ID で Key Vault の認証を提供する方法](https://docs.microsoft.com/azure/key-vault/managed-identity)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure Cognitive Services API へのアクセスに使用されている Azure App Service で実行中の Azure 仮想マシンまたは Web アプリでは、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Cognitive Services のキー管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除を確実に有効にします。

Azure サービスに Azure Active Directory で自動的に管理される ID を提供するには、マネージド ID を使用します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

* [マネージド ID を構成する方法](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

* [Azure マネージド ID と統合する方法](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Cognitive Services など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Cache for Redis など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Cognitive Services など) をサポートする基になるホストで有効にされます。ただし、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常に自動的にレプリケートされます。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。

また、ライフサイクル管理機能を使用して、アーカイブ層にデータをバックアップすることもできます。 さらに、ストレージ アカウントに格納されているバックアップに対する論理的な削除を有効にします。

* [Azure Storage の冗長性とサービス レベル アグリーメントについて](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

* [Azure Blob Storage のライフサイクルの管理](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)

* [Azure Storage Blob の論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Resource Manager を使用して、Cognitive Services および関連リソースをデプロイします。 Azure Resource Manager には、テンプレートをエクスポートする機能が用意されています。これにより、開発ライフサイクル全体でソリューションを再デプロイでき、リソースが一貫した状態でデプロイされることを確信できます。 Azure Automation を使用して、Azure Resource Manager テンプレートのエクスポート API を定期的に呼び出します。 Azure Key Vault 内の事前共有キーをバックアップします。

* [Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)

* [Azure Resource Manager テンプレートを使用して Cognitive Services リソースを作成する方法](https://docs.microsoft.com/azure/cognitive-services/resource-manager-template?tabs=portal)

* [Azure portal のテンプレートへの単一および複数リソースのエクスポート](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

* [リソース グループ - テンプレートのエクスポート](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate)

* [Azure Automation の概要](https://docs.microsoft.com/azure/automation/automation-intro)

* [Azure 上でキー コンテナーのキーをバックアップする方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 必要に応じて、分離サブスクリプションに Azure Resource Manager テンプレートのデプロイを定期的に実行できることを確認します。 バックアップされた事前共有キーの復元をテストします。

* [ARM テンプレートと Azure portal でリソースをデプロイする](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal)

* [Azure でキー コンテナーのキーを復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:Azure DevOps を使用して、Azure Resource Manager テンプレートを安全に格納し、管理します。 Azure DevOps で管理するリソースを保護するには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。  ロールベースのアクセス制御を使用して、カスタマー マネージド キーを保護します。 Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。 

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure DevOps でのアクセス許可とグループについて](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

* [Key Vault で論理的な削除と消去保護を有効にする方法](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [Azure Security Center 内でワークフロー自動化を構成する方法](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [独自のインシデント対応計画の作成のために、NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます。](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: * [侵入テストが Microsoft のポリシーに違反していないことを確保するために、Microsoft の実施ルールに従ってください。](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
