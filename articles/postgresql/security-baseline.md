---
title: Azure Database for PostgreSQL 単一サーバーの Azure セキュリティ ベースライン
description: Azure Database for PostgreSQL 単一サーバーの Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 475933bcc9219808087671d49778cc3696d8c815
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500795"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーの Azure セキュリティ ベースライン

Azure Database for PostgreSQL 単一サーバーの Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を向上させるのに役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: プライベート エンドポイントを使用して、Azure Database for PostgreSQL 用に Private Link を構成します。 Private Link を使用すると、プライベート エンドポイントを経由して Azure 内のさまざまな PaaS サービスに接続できます。 Azure Private Link は基本的に、プライベート仮想ネットワーク (VNet) 内に Azure サービスを提供します。 仮想ネットワークと PostgreSQL インスタンスの間のトラフィックは、Microsoft のバックボーンネットワークを経由します。

また、仮想ネットワーク サービス エンドポイントを使用して、Azure Database for PostgreSQL の実装へのネットワーク アクセスを保護および制限することもできます。 仮想ネットワーク規則は、Azure Database for PostgreSQL サーバーで仮想ネットワーク内の特定のサブネットから送信される通信を受け入れるかどうかを制御する、1 つのファイアウォール セキュリティ機能です。

ファイアウォール規則を使用して、Azure Database for PostgreSQL サーバーをセキュリティで保護することもできます。 サーバー ファイアウォールでは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを防ぎます。 ファイアウォールを構成するには、受け入れ可能な IP アドレスの範囲を指定するファイアウォール規則を作成します。 ファイアウォール規則はサーバー レベルで作成できます。

Azure Database for PostgreSQL 用に Private Link を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Azure Database for PostgreSQL で VNet サービス エンドポイントおよび VNet 規則を作成して管理する方法: https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Azure Database for PostgreSQL のファイアウォール規則を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Database for PostgreSQL インスタンスがプライベート エンドポイントに対してセキュリティで保護されている場合は、同じ仮想ネットワークに仮想マシンをデプロイできます。 ネットワーク セキュリティ グループ (NSG) を使用して、データ流出のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

Azure Database for PostgreSQL 用に Private Link を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Database for PostgreSQL 用の Advanced Threat Protection を使用します。 Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。

Azure Database for PostgreSQL インスタンスに関連付けられている仮想ネットワークで DDoS Protection Standard を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

Azure Database for PostgreSQL 用に Advanced Threat Protection を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

DDoS 保護を構成する方法: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**:Azure Database for PostgreSQL インスタンスがプライベート エンドポイントに対してセキュリティで保護されている場合は、同じ仮想ネットワークに仮想マシンをデプロイできます。 その後、データ流出のリスクを軽減するために、ネットワーク セキュリティ グループ (NSG) を構成することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Database for PostgreSQL 用の Advanced Threat Protection を使用します。 Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。

Azure Database for PostgreSQL 用に Advanced Threat Protection を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Database for PostgreSQL インスタンスへのアクセスが必要なリソースについては、仮想ネットワーク サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 (SQL.WestUs など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

注:Azure Database for PostgreSQL では、"Microsoft.Sql" サービス タグが使用されます。

サービス タグの使用の詳細については、 https://docs.microsoft.com/azure/virtual-network/service-tags-overview を参照してください

Azure Database for PostgreSQL のサービス タグの使用方法について: https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Database for PostgreSQL インスタンスに関連付けられているネットワーク設定とネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Database for PostgreSQL インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft. DBforPostgreSQL" および "Microsoft. Network" 名前空間で Azure Policy エイリアスを使用します。 次のように、ネットワークまたは Azure Database for PostgreSQL インスタンスに関連する組み込みのポリシー定義を使用することもできます。

- DDoS Protection Standard を有効にする必要がある

- PostgreSQL データベース サーバーに対して TLS 接続の強制を有効にする必要がある

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

ネットワークに関する Azure Policy のサンプル: https://docs.microsoft.com/azure/governance/policy/samples/

Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure Database for PostgreSQL インスタンスのネットワーク セキュリティとトラフィック フローに関連するリソースにタグを使用すると、メタデータで論理的にリソースを整理することができます。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、Azure Database for PostgreSQL インスタンスに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する方法: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft では、Azure Database for PostgreSQL などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: 診断設定とサーバー ログを有効にし、ログを取り込み、Azure Database for PostgreSQL インスタンスによって生成されるセキュリティ データを集約します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

Azure Database for PostgreSQL のサーバー ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Azure Database for PostgreSQL の監査ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/concepts-audit

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 監査、セキュリティ、およびリソース ログにアクセスするために、Azure Database for PostgreSQL インスタンスの診断設定を有効にします。 PostgreSQL 監査ログが明示的に有効になっていることを確認します。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。 また、Azure アクティビティ ログの診断設定を有効にし、同じ Log Analytics ワークスペースまたはストレージ アカウントにログを送信することもできます。

Azure Database for PostgreSQL のサーバー ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Azure Database for PostgreSQL の監査ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/concepts-audit

Azure アクティビティ ログ用に診断設定を構成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、Azure Database for PostgreSQL ログを保持するために使用される Log Analytics ワークスペースについて、組織のコンプライアンス規則に従って保有期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

Log Analytics ワークスペースのログ保持パラメーターを設定する方法: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Azure ストレージ アカウントでのリソース ログの格納: https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Database for PostgreSQL インスタンスからのログを分析および監視して、異常な動作がないか確認します。 ログを確認し、ログ データに対してクエリを実行するには、Azure Monitor の Log Analytics を使用します。 または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Log Analytics の詳細については、 https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal を参照してください

Azure Monitor でカスタム クエリを実行する方法: https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Database for PostgreSQL 用の Advanced Threat Protection を有効にします。 Advanced Threat Protection では、データベースにアクセスしたり、データベースを悪用したりしようとする、通常とは異なる、害を及ぼす可能性のある試行を示す異常なアクティビティを検出します。

さらに、PostgreSQL のサーバー ログと診断設定を有効にし、Log Analytics ワークスペースにログを送信することもできます。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。これは、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるためです。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。

Azure Database for PostgreSQL 用の Advanced Threat Protection を有効にする方法: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Azure Database for PostgreSQL のサーバー ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Azure Database for PostgreSQL の監査ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/concepts-audit

Azure アクティビティ ログ用に診断設定を構成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用なし。Azure Database for PostgreSQL では、マルウェア対策関連のログの処理や生成は行われません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用なし。Azure Database for PostgreSQL では、DNS 関連のログの処理や生成は行われません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Database for PostgreSQL インスタンスのコントロール プレーン (Azure portal など) への管理アクセス権を持つユーザー アカウントのインベントリを保持します。 さらに、Azure Database for PostgreSQL インスタンスの (データベース自体内の) データ プレーンへのアクセス権を持つ管理アカウントのインベントリを保持します (PostgreSQL サーバーを作成する場合は、管理者ユーザーの資格情報を指定します。 この管理者を使用して、追加の PostgreSQL ユーザーを作成することができます)。

Azure Database for PostgreSQL では、組み込みのロールベースのアクセス制御はサポートされませんが、特定のリソース プロバイダーの操作に基づいてカスタム ロールを作成することはできます。

Azure サブスクリプションのカスタム ロールについて: https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Azure Database for PostgreSQL リソース プロバイダーの操作について: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

Azure Database for PostgreSQL のアクセス管理について: https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory および Azure Database for PostgreSQL には、既定のパスワードという概念はありません。

Azure Database for PostgreSQL リソース自体の作成時に、Azure では強力なパスワードを使用する管理ユーザーの作成が強制されます。 しかし、PostgreSQL インスタンスが作成されたら、作成した最初のサーバー管理者アカウントを使用して、追加のユーザーを作成し、それらに管理アクセス権を付与することができます。 これらのアカウントを作成するときは、必ず、アカウントごとに異なる強力なパスワードを構成してください。

Azure Database for PostgreSQL 用に追加のアカウントを作成する方法: https://docs.microsoft.com/azure/postgresql/howto-create-users

管理者パスワードを更新する方法: https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Database for PostgreSQL インスタンスにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。 

Azure Security Center ID とアクセスについて: https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Azure Database for PostgreSQL で管理者ユーザーを作成する方法について: https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Database for PostgreSQL へのサインインでは、データベースで直接構成されたユーザー名とパスワードの両方の使用がサポートされます。また、Azure Active Directory (AD) ID を使用することも、Azure AD トークンを利用して接続することもできます。 Azure AD トークンを使用する場合、Azure AD ユーザー、Azure AD グループ、データベースに接続している Azure AD アプリケーションなど、さまざまな方法がサポートされます。

これとは別に、PostgreSQL のコントロール プレーン アクセスは、REST API 経由で利用でき、SSO がサポートされます。 認証を行うには、Azure Active Directory から取得した要求の Authorization ヘッダーを JSON Web トークンに設定します。

Azure Database for PostgreSQL での認証に Azure Active Directory を使用する https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Azure Database for PostgreSQL の REST API について: https://docs.microsoft.com/rest/api/postgresql/

Azure AD を使用した SSO について: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory の Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。 Azure AD トークンを利用してデータベースにサインインする場合、データベースのサインインに対して多要素認証を要求することができます。

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Database for PostgreSQL での認証に Azure Active Directory を使用する https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Azure Security Center 内で ID とアクセスを監視する方法: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

特権アクセス ワークステーションについて: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Database for PostgreSQL 用の Advanced Threat Protection を有効にして、疑わしいアクティビティに関するアラートを生成します。

さらに、Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいまたは安全でないアクティビティが発生したときにログとアラートを生成できます。

Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

Azure Database for PostgreSQL 用の Advanced Threat Protection を設定する方法: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Privileged Identity Management (PIM) をデプロイする方法: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD のリスク検出の概要: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: ポータルや Azure Resource Manager での IP アドレス範囲または国と地域の特定の論理グループからのアクセスのみを許可するには、条件付きアクセスのネームド ロケーションを使用します。

Azure でネームド ロケーションを構成する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (AD) を中央認証および承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure Database for PostgreSQL にサインインする場合は、Azure AD を使用することと、Azure AD トークンを使って接続することをお勧めします。 Azure AD トークンを使用する場合、Azure AD ユーザー、Azure AD グループ、データベースに接続している Azure AD アプリケーションなど、さまざまな方法がサポートされます。

Azure AD 資格情報は、PostgreSQL 管理者アカウントを制御するための管理プレーン レベル (Azure portal など) での管理にも使用できます。

Azure Database for PostgreSQL での認証に Azure Active Directory を使用する https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory ログを確認します。これは、Azure Database for PostgreSQL 管理ロールを持つアカウントを含めることができる古いアカウントを検出するのに役立ちます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、Azure Database for PostgreSQL へのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的 (たとえば、90 日ごと) に確認し、適切なユーザーだけが継続してアクセスできるようにする必要があります。

Azure AD のレポートについて: https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure ID アクセスレビューの使用方法: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

PostgreSQL ユーザーと割り当てられたロールを確認する: https://www.postgresql.org/docs/current/database-roles.html

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Database for PostgreSQL および Azure Active Directory の診断設定を有効にし、すべてのログを Log Analytics ワークスペースに送信します。 Log Analytics 内で、必要なアラート (認証試行の失敗など) を構成します。

Azure Database for PostgreSQL のサーバー ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Azure Database for PostgreSQL の監査ログの構成およびアクセス方法: https://docs.microsoft.com/azure/postgresql/concepts-audit

Azure アクティビティ ログを Azure Monitor に統合する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Database for PostgreSQL 用の Advanced Threat Protection を有効にして、疑わしいアクティビティに関するアラートを生成します。

検出された疑わしいアクションに対する自動応答を構成するには、Azure Active Directory の Identity Protection とリスク検出機能を使用します。 Azure Sentinel で自動応答を有効にして、組織のセキュリティ対応を実装することができます。

さらに調査するために、Azure Sentinel にログを取り込むこともできます。

Azure Database for PostgreSQL 用の Advanced Threat Protection を設定する方法: https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Azure AD Identity Protection の概要: https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Azure AD の危険なサインインを表示する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 現在使用できません。カスタマー ロックボックスは Azure Database for PostgreSQL ではまだサポートされていません。

カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:タグを使用すると、機密情報を格納または処理する Azure Database for PostgreSQL インスタンスや関連リソースの追跡に役立ちます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Private Link、サービスエンド ポイント、またはファイアウォール規則の組み合わせを使用して、Azure Database for PostgreSQL インスタンスへのネットワーク アクセスを分離して制限します。

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

Azure Database for PostgreSQL 用に Private Link を構成する方法: https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Azure Database for PostgreSQL で VNet サービス エンドポイントおよび VNet 規則を作成して管理する方法: https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Azure Database for PostgreSQL のファイアウォール規則を構成する方法: https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Azure Security Center の監視**: 使用不可

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Virtual Machines を使用して Azure Database for PostgreSQL インスタンスにアクセスする場合は、Private Link、PostgreSQL ネットワーク構成、ネットワーク セキュリティ グループ、およびサービス タグを利用して、データ流出の可能性を軽減します。

Microsoft では、Azure Database for PostgreSQL 用の基になるインフラストラクチャを管理しており、顧客データの損失や漏えいを防ぐために厳重な管理を行っています。

Azure Database for PostgreSQL のデータ流出を軽減する方法: https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Azure での顧客データの保護について: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:Azure Database for PostgreSQL では、以前は Secure Sockets Layer (SSL) と呼ばれていたトランスポート層セキュリティ (TLS) を使用した、PostgreSQL サーバーのクライアント アプリケーションへの接続がサポートされます。 お使いのデータベース サーバーとクライアント アプリケーション間に TLS 接続を強制すると、サーバーとアプリケーション間のデータ ストリームが暗号化され、中間者攻撃から保護するのに役立ちます。 Azure portal で、すべての Azure Database for PostgreSQL インスタンスに対して [SSL 接続を強制する] が既定で有効になっていることを確認します。

現在、Azure Database for PostgreSQL でサポートされている TLS のバージョンは、TLS 1.0、TLS 1.1、TLS 1.2 です。

Azure Database for PostgreSQL 用に転送中の暗号化を構成する方法: https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:データの識別、分類、および損失防止機能は、Azure Database for PostgreSQL ではまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Database for PostgreSQL コントロール プレーン (Azure portal など) へのアクセスを制御します。 (データベース自体内の) データ プレーン アクセスの場合は、SQL クエリを使用してユーザーを作成し、ユーザーのアクセス許可を構成します。 RBAC は、データベース内のユーザーのアクセス許可には影響しません。

Azure で RBAC を構成する方法: https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

Azure Database for PostgreSQL の SQL を使用してユーザー アクセスを構成する方法: https://docs.microsoft.com/azure/postgresql/howto-create-users

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft では、Azure Database for PostgreSQL 用の基になるインフラストラクチャを管理しており、顧客データの損失や漏えいを防ぐために厳重な管理を行っています。

Azure での顧客データの保護について: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Database for PostgreSQL サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 バックアップを含むデータは、クエリの実行中に作成された一時ファイルを除き、ディスク上で暗号化されます。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます。 ストレージの暗号化は常にオンになっており、無効にすることはできません。

Azure Database for PostgreSQL 単一サーバーのカスタマー マネージド キー (CMK) によるデータ暗号化では、保存データの保護に Bring Your Own Key (BYOK) を使用できます。 現時点では、この機能を使用するにはアクセス権をリクエストする必要があります。 これを行う場合は、以下にお問い合わせください。

AskAzureDBforPostgreSQL@service.microsoft.com.

Azure Database for PostgreSQL の保存時の暗号化について: https://docs.microsoft.com/azure/postgresql/concepts-security

カスタマー マネージド キーを使用した Azure Database for PostgreSQL の保存時の暗号化について: https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Database for PostgreSQL の運用インスタンスやその他の重要なまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

Azure アクティビティ ログ イベントのアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:現在使用できません。Azure Security Center では、Azure Database for PostgreSQL の脆弱性評価はまだサポートされていません。

Azure Security Center の Azure PaaS サービスを対象とする機能: https://docs.microsoft.com/azure/security-center/features-paas

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Microsoft では、Azure Database for PostgreSQL をサポートする基になるシステムで脆弱性の管理を行います。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (Azure Database for PostgreSQL インスタンスを含む) のクエリや検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

Azure Resource Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC の概要: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure Database for PostgreSQL インスタンスやその他の関連リソースにタグを適用し、論理的に分類してまとめます。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、Azure Database for PostgreSQL インスタンスと関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**:適用できません。この推奨事項は、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、機密情報を含む Azure Database for PostgreSQL のインスタンスなど、高セキュリティ環境内でのリソースの作成と変更を防ぐことができます。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**ガイダンス**: Azure Policy を使用して、Azure Database for PostgreSQL インスタンスの標準的なセキュリティ構成を定義して実装します。 Azure Database for PostgreSQL インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.DBforPostgreSQL" 名前空間で Azure Policy エイリアスを使用します。 次のように、Azure Database for PostgreSQL インスタンスに関連する組み込みのポリシー定義を使用することもできます。

- PostgreSQL データベース サーバーに対して TLS 接続の強制を有効にする必要がある

- PostgreSQL データベース サーバーでは接続のログが有効でなければならない

使用可能な Azure Policy エイリアスを表示する方法: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果の概要: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:Azure Database for PostgreSQL インスタンスと関連リソースにカスタム Azure Policy 定義を使用する場合は、Azure Repos を使ってコードを安全に格納および管理します。

Azure DevOps でコードを格納する方法: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: システム構成のアラート生成、監査、および適用のためのカスタム ポリシーを作成するには、"Microsoft.DBforPostgreSQL" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: システム構成のアラート生成、監査、および適用のためのカスタム ポリシーを作成するには、"Microsoft.DBforPostgreSQL" 名前空間で Azure Policy エイリアスを使用します。 Azure Policy の [audit]、[deny]、[deploy if not exist] を使用して、Azure Database for PostgreSQL インスタンスおよび関連リソースの構成を自動的に適用します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Database for PostgreSQL インスタンスへのアクセスに使用される Azure App Service で実行されている Azure Virtual Machines または Web アプリケーションの場合は、Azure Key Vault と組み合わせてマネージド サービス ID を使用して、Azure Database for PostgreSQL シークレットの管理を簡素化およびセキュリティで保護します。 Key Vault の論理的な削除が有効になっていることを確認します。

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法: https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法: https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure Database for PostgreSQL サーバーでは、データベースにアクセスするための Azure Active Directory 認証がサポートされています。  Azure Database for PostgreSQL サーバーを作成するときに、管理者ユーザーの資格情報を指定します。 この管理者は、追加のデータベース ユーザーを作成するために使用できます。  

Azure Database for PostgreSQL サーバーへのアクセスに使用される Azure App Service で実行されている Azure Virtual Machines または Web アプリケーションの場合は、Azure Key Vault と組み合わせてマネージド サービス ID を使用して、Azure Database for PostgreSQL サーバーの資格情報を格納および取得します。 Key Vault の論理的な削除が有効になっていることを確認します。

マネージド ID を使用して、Azure Active Directory (AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

マネージド ID を構成する方法: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft マルウェア対策は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Database for PostgreSQL など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage、Azure Database for PostgreSQL などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

Microsoft のマルウェア対策は、Azure サービス (Azure Database for PostgreSQL など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Database for PostgreSQL では、データ ファイルとトランザクション ログのバックアップが作成されます。 サポートされている最大ストレージ サイズに応じて、完全バックアップと差分バックアップ (最大 4 TB のストレージ サーバー) またはスナップショット バックアップ (最大 16 TB のストレージ サーバー) を使用します。 これらのバックアップを使用すると、サーバーを、バックアップの構成済みリテンション期間内の任意の時点に復元できます。 バックアップの既定のリテンション期間は 7 日です。 これは、必要に応じて、最大 35 日に設定できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

Azure Database for PostgreSQL のサーバーをバックアップする方法: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Azure Database for PostgreSQL の初期構成について: https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Database for PostgreSQL では、サーバーのバックアップを自動的に作成し、ユーザーの選択に応じて、ローカル冗長または geo 冗長ストレージに格納します。 バックアップを使用すると、サーバーを特定の時点に復元できます。 不慮の破損または削除からデータを保護するバックアップと復元は、ビジネス継続性戦略の最も重要な部分です。

Azure Database for PostgreSQL インスタンスの資格情報を格納するために Azure Key Vault を使用する場合は、必ず、キーの定期的な自動バックアップを行ってください。

Azure Database for PostgreSQL のサーバーをバックアップする方法: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Key Vault のキーをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Azure Database for PostgreSQL で復元を実行すると、元のサーバーのバックアップから新しいサーバーが作成されます。 使用できる復元には 2 つの種類があります。ポイントインタイム リストアと geo リストアです。 ポイントインタイム リストアは、いずれのバックアップ冗長オプションでも使用でき、元のサーバーと同じリージョンに新しいサーバーが作成されます。 geo リストアは、サーバーを geo 冗長ストレージ用に構成した場合にのみ使用でき、ご利用のサーバーを別のリージョンに復元できます。

復旧の推定所要時間は、データベースのサイズ、トランザクション ログのサイズ、ネットワーク帯域幅、同じリージョン内で同時に復旧するデータベースの合計数など、複数の要因によって異なります。 通常は 12 時間もかかりません。

Azure Database for PostgreSQL インスタンスの復元を定期的にテストします。

Azure Database for PostgreSQL のサーバーをバックアップする方法: https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure Database for PostgreSQL では、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが作成されます。 これらのバックアップを使用すると、サーバーを、バックアップの構成済みリテンション期間内の任意の時点に復元できます。 バックアップの既定のリテンション期間は 7 日です。 これは、必要に応じて、最大 35 日に設定できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

Azure Database for PostgreSQL でのバックアップと復元について: https://docs.microsoft.com/azure/postgresql/concepts-backup

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center のインシデントの構造: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます: https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT の計画と機能に関するテスト、トレーニング、演習プログラムのガイド: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

Azure Security Center のセキュリティ連絡先を設定する方法: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

連続エクスポートを構成する方法: https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

ワークフローの自動化と Logic Apps を構成する方法: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の活動規則に従ってください: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
