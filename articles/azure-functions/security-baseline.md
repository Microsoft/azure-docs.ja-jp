---
title: Azure Functions 用の Azure セキュリティ ベースライン
description: Azure Functions 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32905383b046148893492640fe42fc721d1944dd
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810243"
---
# <a name="azure-security-baseline-for-azure-functions"></a>Azure Functions 用の Azure セキュリティ ベースライン

Azure Functions 用の Azure セキュリティ ベースラインには、ご自身のデプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Functions アプリを Azure 仮想ネットワークと統合します。 Premium プランで実行されている関数アプリには、"VNet 統合" 機能を含む、Azure App Service の Web アプリと同じホスティング機能があります。  Azure 仮想ネットワークを使用すると、Azure Functions などの Azure リソースの多くを、インターネットにルーティングできないネットワークに配置することができます。

- [Functions を Azure 仮想ネットワークに統合する方法](./functions-create-vnet.md)

- [Azure Functions と Azure App Service の Vnet 統合の概要](../app-service/web-sites-integrate-with-vnet.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure Functions アプリに関連するネットワーク リソースとネットワーク構成をセキュリティで保護します。

Azure Functions の実装にネットワーク セキュリティ グループ (NSG) を使用している場合は、NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:運用環境で、Azure 関数エンドポイントを完全に保護するには、次の関数アプリ レベルのセキュリティ オプションのいずれかの実装を検討してください。
- 関数アプリの App Service 認証および承認をオンにする
- 要求の認証に Azure API Management (APIM) を使用する、または
- Azure App Service Environment に関数アプリをデプロイする。

また、運用環境の Azure Functions のリモート デバッグが無効になっていることを確認します。 さらに、クロスオリジン リソース共有 (CORS) では、すべてのドメインが Azure Function アプリにアクセスすることを許可しないでください。 Azure 関数アプリの操作に必要なドメインのみを許可します。

受信トラフィックをさらに検査するために、ネットワーク構成の一部として Azure Web アプリケーション ファイアウォール (WAF) をデプロイすることを検討します。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。 

- [運用環境で Azure 関数エンドポイントをセキュリティで保護する方法](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 関数アプリに関連付けられた仮想ネットワーク上で DDoS Protection 標準を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のパブリック IP アドレスとの通信を拒否します。
さらに、すべての受信要求を認証し、悪意のあるトラフィックをフィルターで除外するように Azure Web アプリケーション ファイアウォールなどのフロントエンド ゲートウェイを構成します。 Azure Web アプリケーション ファイアウォールを使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃をブロックするために受信 Web トラフィックを検査することで、Azure 関数アプリを保護できます。 WAF を導入するには、App Service Environment またはプライベート エンドポイント (プレビュー) を使用する必要があります。 必ずプライベート エンドポイントが (プレビュー) 段階ではなくなってから、運用環境のワークロードで使用してください。

- [Azure Functions のネットワーク オプション](./functions-networking-options.md)

- [Azure Functions の Premium プラン](./functions-scale.md#premium-plan)

- [App Service Environment の概要](../app-service/environment/intro.md)

- [App Service Environment のネットワークの考慮事項](../app-service/environment/network-info.md)

- [DDoS 保護を構成する方法](../virtual-network/manage-ddos-protection.md)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/threat-protection.md)

- [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center の Just In Time ネットワーク アクセス制御について](../security-center/security-center-just-in-time.md)

- [Azure Functions へのプライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: Azure Functions の実装にネットワーク セキュリティ グループ (NSG) を使用している場合は、ネットワーク セキュリティ グループ フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:すべての受信要求を認証し、悪意のあるトラフィックをフィルターで除外するように Azure Web アプリケーション ファイアウォールなどのフロントエンド ゲートウェイを構成します。 Azure Web アプリケーション ファイアウォールを使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃をブロックするために受信 Web トラフィックを検査することで、関数アプリを保護できます。 WAF を導入するには、App Service Environment またはプライベート エンドポイント (プレビュー) を使用する必要があります。 必ずプライベート エンドポイントが (プレビュー) 段階ではなくなってから、運用環境のワークロードで使用してください。

また、IDS または IPS 機能を含め、Azure Marketplace で入手できる Barracuda WAF for Azure などのマーケットプレースの選択肢が複数あります。

- [Azure Functions のネットワーク オプション](./functions-networking-options.md)

- [Azure Functions の Premium プラン](./functions-scale.md#premium-plan)

- [App Service Environment の概要](../app-service/environment/intro.md)

- [App Service Environment のネットワークの考慮事項](../app-service/environment/network-info.md)

- [Azure Web アプリケーション ファイアウォールの概要](../web-application-firewall/index.yml)

- [Azure Functions へのプライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)

- [Barracuda WAF クラウド サービスの概要](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: エンドツーエンドの TLS 暗号化を使用して、Azure Web アプリケーション ファイアウォールなど、実際のネットワークに合わせてフロントエンド ゲートウェイを構成します。 WAF を導入するには、App Service Environment またはプライベート エンドポイント (プレビュー) を使用する必要があります。 必ずプライベート エンドポイントが (プレビュー) 段階ではなくなってから、運用環境のワークロードで使用してください。

- [Azure Functions のネットワーク オプション](./functions-networking-options.md)

- [Azure Functions の Premium プラン](./functions-scale.md#premium-plan)

- [App Service Environment の概要](../app-service/environment/intro.md)

- [App Service Environment のネットワークの考慮事項](../app-service/environment/network-info.md)

- [Azure Web アプリケーション ファイアウォールの概要](../web-application-firewall/index.yml)

- [ポータルで Application Gateway を使用してエンド ツー エンド TLS を構成する方法](../application-gateway/end-to-end-ssl-portal.md)

- [Azure Functions へのプライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソース フィールドまたはターゲット フィールドにサービス タグ名 (たとえば AzureAppService) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグの使用に関する詳細](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Functions に関連するネットワーク設定の標準的なセキュリティ構成を定義して実装します。 Azure Functions のネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Web" および "Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 また、次のように、Azure Functions 用の組み込みのポリシー定義を使用することもできます。
- CORS で関数アプリへのアクセスをすべてのリソースには許可しない
- Function App には HTTPS 経由でのみアクセスできるようにする
- 関数アプリでは最新の TLS バージョンを使用する必要がある

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Functions の実装にネットワーク セキュリティ グループ (NSG) を使用する場合は、ネットワーク セキュリティとトラフィック フローに関連する NSG およびその他のリソースのタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Functions デプロイに関連するネットワーク設定とリソースの変更を検出します。 重要なネットワーク設定とリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。 

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Microsoft では、Azure Functions などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

Azure Functions には、関数を監視するための Azure Application Insights とのビルトイン統合機能も用意されています。 Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 また、パフォーマンスの異常が自動的に検出されるほか、問題の診断や、関数がどのように使用されているかの理解に役立つ強力な分析ツールも含まれています。

Azure 関数アプリ内に組み込みのカスタム セキュリティおよび監査ログがある場合は、診断設定 "FunctionAppLogs" を有効にして、アーカイブのために Log Analytics ワークスペース、Azure イベントハブ、または Azure ストレージ アカウントにログを送信します。 

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

- [Azure Application Insights を使用して Azure Functions を設定する方法](./functions-monitoring.md)

- [Azure Functions に対して診断設定 (ユーザーが生成するログ) を有効にする方法](./functions-monitor-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

Azure 関数アプリ内に組み込みのカスタム セキュリティおよび監査ログがある場合は、診断設定 "FunctionAppLogs" を有効にして、アーカイブのために Log Analytics ワークスペース、Azure イベントハブ、または Azure ストレージ アカウントにログを送信します。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

- [Azure Functions に対して診断設定 (ユーザーが生成するログ) を有効にする方法](./functions-monitor-log-analytics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor で、組織のコンプライアンス規則に従って、Azure Functions アプリに関連付けられている Log Analytics ワークスペースのログの保持期間を設定します。

- [ログ保持期間のパラメーターを設定する方法](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定と Azure Functions アプリの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。

Azure Functions アプリの Application Insights を有効にすると、ログ、パフォーマンス、およびエラー データを収集できます。 Azure portal 内で Application Insights によって収集されたテレメトリ データを表示できます。

Azure 関数アプリ内に組み込みのカスタム セキュリティおよび監査ログがある場合は、診断設定 "FunctionAppLogs" を有効にして、アーカイブのために Log Analytics ワークスペース、Azure イベントハブ、または Azure ストレージ アカウントにログを送信します。 

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

- [Azure Functions に対して診断設定を有効にする方法](./functions-monitor-log-analytics.md)

- [Azure Application Insights で Azure Functions を設定し、テレメトリ データを表示する方法](./functions-monitoring.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure アクティビティ ログの診断設定と Azure Functions アプリの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。 Log Analytics ワークスペース クエリに基づいてアラートを作成できます。

Azure Functions アプリの Application Insights を有効にすると、ログ、パフォーマンス、およびエラー データを収集できます。 Azure portal 内で Application Insights によって収集されたテレメトリ データを確認し、アラートを作成することができます。

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/platform/activity-log.md)

- [Azure Functions に対して診断設定を有効にする方法](./functions-monitor-log-analytics.md)

- [Azure Functions に対して Application Insights を有効にする方法](./functions-monitoring.md#enable-application-insights-integration)

- [Azure 内でアラートを作成する方法](../azure-monitor/learn/tutorial-response.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Functions アプリでは、マルウェア対策関連のログが処理または生成されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Functions アプリでは、ユーザーがアクセスできる DNS 関連のログが処理または生成されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。 

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Functions へのコントロール プレーン アクセスは、Azure Active Directory (AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

データ プレーン アクセスは、承認キー、ネットワーク制限、AAD ID の検証など、いくつかの方法で制御できます。 承認キーは、Azure Functions HTTP エンドポイントに接続するクライアントによって使用され、いつでも再生成できます。 これらのキーは、既定で新しい HTTP エンドポイント用に生成されます。

関数アプリには複数の導入方法を使用できます。その一部は、生成された資格情報のセットを活用するものもあります。 アプリケーションに使用されるデプロイ方法を確認してください。

- [HTTP エンドポイントをセキュリティで保護する](./functions-bindings-http-webhook-trigger.md?tabs=csharp#secure-an-http-endpoint-in-production)

- [承認キーを取得して再生成する方法](./functions-bindings-http-webhook-trigger.md?tabs=csharp#obtaining-keys)

- [Azure Functions のデプロイ テクノロジ](./functions-deployment-technologies.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。サブスクリプションには複数の所有者を割り当てられる必要があります。所有者のアクセス許可を持つ、使用されていないアカウントをサブスクリプションから削除する必要があります。所有者のアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要があります。

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 可能な限り、関数アプリへのデータ アクセス用に個々のスタンドアロンの資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。 App Service の認証および承認機能を使用して、Azure Functions アプリにシングル サインオンを実装します。

- [Azure Functions での認証と承認の概要](../app-service/overview-authentication-authorization.md#identity-providers)

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (AD) Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

- [特権アクセス ワークステーションについて](/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成します。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Functions アプリの主要な認証および承認システムとして Azure Active Directory (AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

- [Azure AD ログインを使用するように Azure Functions アプリを構成する方法](../app-service/configure-authentication-provider-aad.md)

- [AAD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure 関数アプリの主要な認証および承認システムとして Azure Active Directory (AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure AD サインイン アクティビティ、監査、リスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサードパーティの SIEM と統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

- [Azure AD ログインを使用するように Azure Functions アプリを構成する方法](../app-service/configure-authentication-provider-aad.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Functions アプリの主要な認証および承認システムとして Azure Active Directory (AD) を使用します。 コントロール プレーン (Azure portal) でのアカウント ログイン動作の偏差について、Azure Active Directory (AD) Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 現在は使用できません。現在、Azure Functions ではカスタマー ロックボックスはサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure 関数アプリは、仮想ネットワーク (VNet) またはサブネットで分離し、適切にタグを付けるようにします。

プライベート エンドポイントを使用してネットワークの分離を実行することもできます。 Azure プライベート エンドポイントは、Azure Private Link を利用したサービス (例: Azure Functions アプリの HTTP エンドポイント) に非公開かつ安全に接続するネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。 Premium プランで実行されている関数アプリのプライベート エンドポイントは (プレビュー) 段階です。 必ずプライベート エンドポイントが (プレビュー) 段階ではなくなってから、運用環境のワークロードで使用してください。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure Functions のネットワーク オプション](./functions-networking-options.md)

- [Azure Functions の Premium プラン](./functions-scale.md#premium-plan)

- [プライベート エンドポイントの概要](../private-link/private-endpoint-overview.md)

- [Azure Functions へのプライベート エンドポイントの使用](../app-service/networking/private-endpoint.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする自動ツールをネットワーク境界にデプロイします。 

Microsoft では、Azure Functions 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure portal の Azure Function アプリで、[プラットフォーム機能: ネットワーク: SSL] の [HTTP のみ] 設定を有効にし、TLS の最小バージョンを 1.2 に設定します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 現在は使用できません。現在、Azure Functions では、データの識別、分類、損失防止機能を使用できません。 そのため、機密情報を処理する可能性のある関数アプリにはタグを付けます。また、コンプライアンスの目的で必要な場合はサードパーティ製のソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory (AD) ロールベースのアクセス制御 (RBAC) を使用して、Azure 関数コントロール プレーン (Azure portal) へのアクセスを制御します。 

- [Azure で RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

Microsoft では、Azure Functions 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:関数アプリを作成するときは、BLOB、キュー、テーブル ストレージをサポートする汎用の Azure Storage アカウントを作成またはリンクする必要があります。 これは、Functions ではトリガーの管理や関数実行のログ記録などの操作に Azure Storage を使用しているためです。 Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するには、BLOB およびファイル データの暗号化のためにカスタマー マネージド キーを用意します。 関数アプリからストレージ アカウントにアクセスできるように、これらのキーは Azure Key Vault 内に置かれている必要があります。

- [Azure Functions のストレージに関する考慮事項](./storage-considerations.md)

- [保存データに対する Azure Storage 暗号化の概要](../storage/common/storage-service-encryption.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:運用環境の Azure 関数アプリおよびその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure Functions アプリケーションをセキュリティで保護し、ライフサイクル全体を通じて可能な限り安全に保たれるように、DevSecOps プラクティスを採用します。 DevSecOps によって、組織のセキュリティ チームとその機能を DevOps プラクティスに組み込み、チームの全員がセキュリティを担当するようにします。

さらに、Azure Security Center の推奨事項に従って、Azure 関数アプリをセキュリティで保護します。

- [CI/CD パイプラインに継続的なセキュリティ検証を追加する方法](/azure/devops/migrate/security-validation-cicd-pipeline?view=azure-devops)

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/security-center-vulnerability-assessment-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="52-deploy-an-automated-operating-system-patch-management-solution"></a>5.2:オペレーティング システムの自動パッチ管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:Microsoft によって、Azure Functions をサポートする基盤となるシステムに対して脆弱性管理が実行されますが、お客様が Azure Security Center 内の推奨事項の重大度とセキュリティ スコアを使用して環境内のリスクを測定することもできます。 ご自身のセキュア スコアは、軽減した Security Center の推奨事項の数に基づきます。 最初に解決すべき推奨事項が優先されるよう、それぞれどれが重要であるか考えてください。

- [セキュリティの推奨事項のリファレンス ガイド](../security-center/recommendations-reference.md)

**Azure Security Center の監視**: はい

**責任**: 共有

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。許可されていないリソースの種類と許可されているリソースの種類

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアを定義します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。許可されていないリソースの種類と許可されているリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 機密性の高いまたはリスクの高い Azure 関数アプリの場合は、個別のサブスクリプションや管理グループを実装して分離を実現します。

リスクの高い Azure 関数アプリを独自の仮想ネットワーク (VNet) にデプロイします。 Azure Functions の境界セキュリティは、VNet によって実現されます。 Premium プランまたは App Service Environment (ASE) で実行されている関数は、VNet と統合できます。 ユース ケースに最適なアーキテクチャを選択します。

- [Azure Functions のネットワーク オプション](./functions-networking-options.md)

- [Azure Functions の Premium プラン](./functions-scale.md#premium-plan)

- [App Service Environment のネットワークの考慮事項](../app-service/environment/network-info.md)

- [外部 ASE を作成する方法](../app-service/environment/create-external-ase.md)

内部 ASE を作成する方法:

- [https://docs.microsoft.com/azure/app-service/environment/create-ilb-as](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy を使用して、Azure 関数アプリの標準的なセキュリティ構成を定義および実装します。 "Microsoft.Web" 名前空間の Azure Policy エイリアスを使用して、Azure Functions アプリの構成を監査または適用するカスタム ポリシーを作成します。 次のように、組み込みのポリシー定義を使用することもできます。
- 関数アプリではマネージド ID を使用する必要がある
- 関数アプリでリモート デバッグを無効にする必要がある
- Function App には HTTPS 経由でのみアクセスできるようにする

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。オンプレミスの関数をデプロイすることはできますが、このガイドラインは IaaS コンピューティング リソースを対象としています。 オンプレミスの関数をデプロイする際は、お客様が環境のセキュリティで保護された構成を実現する必要があります。

- [オンプレミスの関数の概要](./functions-runtime-install.md)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: ARM テンプレートとカスタム Azure ポリシーの定義をソース管理に安全に格納し、管理します。

- [コードとしてのインフラストラクチャとは](/azure/devops/learn/what-is-infrastructure-as-code)

- [コードとしてのポリシー ワークフローの設計](../governance/policy/concepts/policy-as-code.md)

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Web" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Web" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ご利用の Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:マネージド ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。 関数アプリにマネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [キー コンテナーを作成する方法](../key-vault/secrets/quick-create-portal.md)

- [App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md)

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/managed-identity.md)

- [App Service と Azure Functions の Key Vault 参照を使用する](../app-service/app-service-key-vault-references.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure AD で自動的に管理される ID を Azure 関数アプリに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [App Service と Azure Functions でマネージド ID を使用する方法](../app-service/overview-managed-identity.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Functions など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、データを格納するように設計された非コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Functions など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: バックアップと復元機能を使用して、アプリの定期的なバックアップをスケジュールします。 Premium プランで実行されている関数アプリには、"バックアップと復元" 機能を含む、Azure App Service の Web アプリと同じホスティング機能があります。

また、Azure Repos や Azure DevOps などのソース管理ソリューションを利用して、コードを安全に格納し、管理します。 Azure DevOps Services では、Azure Storage 機能の多くを活用して、ハードウェア障害、サービスの中断、またはリージョンの災害が発生した場合にデータの可用性を確保しています。 さらに、Azure DevOps チームは、データを偶発的または悪意のある削除から保護する手順に従っています。

- [Azure でのアプリのバックアップ](../app-service/manage-backup.md)

- [Azure DevOps のデータ可用性の概要](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: バックアップと復元機能を使用して、アプリの定期的なバックアップをスケジュールします。 Premium プランで実行されている関数アプリには、"バックアップと復元" 機能を含む、Azure App Service の Web アプリと同じホスティング機能があります。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

また、Azure Repos や Azure DevOps などのソース管理ソリューションを利用して、コードを安全に格納し、管理します。 Azure DevOps Services では、Azure Storage 機能の多くを活用して、ハードウェア障害、サービスの中断、またはリージョンの災害が発生した場合にデータの可用性を確保しています。 さらに、Azure DevOps チームは、データを偶発的または悪意のある削除から保護する手順に従っています。

- [Azure でのアプリのバックアップ](../app-service/manage-backup.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

- [Azure DevOps のデータ可用性の概要](/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップと復元機能から定期的に復元を実行できるようにします。 別のオフラインの場所を使用してコードをバックアップする場合は、完全な復元を実行できることを定期的に確認します。 バックアップされたカスタマー マネージド キーの復元をテストします。

- [バックアップから Azure にアプリを復元する](../app-service/web-sites-restore.md)

- [Azure でスナップショットからアプリケーションを復元する](../app-service/app-service-web-restore-snapshots.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: バックアップと復元機能からのバックアップには、サブスクリプションの Azure Storage アカウントを使用します。 Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するには、ストレージ データの暗号化のためにカスタマー マネージド キーを用意します。

カスタマー マネージド キーを使用している場合は、キーを偶発的または悪意のある削除から保護するために、Key Vault の論理的な削除が有効であることを確認します。

- [Azure Storage の保存時の暗号化](../storage/common/storage-service-encryption.md)

- [Key Vault で論理的な削除を有効にする方法](../storage/blobs/soft-delete-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: 共有

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: セキュリティ アラートやセキュリティに関する推奨事項に対して Logic Apps を使用して応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する