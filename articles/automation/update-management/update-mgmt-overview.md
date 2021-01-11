---
title: Azure Automation Update Management の概要
description: この記事では、Windows および Linux マシンの更新プログラムを実装する Update Management 機能について概要を説明します。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0fd416c844ac93ffb77eded98448b2e93e9acd30
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660910"
---
# <a name="update-management-overview"></a>Update Management の概要

Azure Automation の Update Management を使用すると、Azure、オンプレミス環境、およびその他のクラウド環境で、Azure での Windows と Linux マシンに対するオペレーティング システムの更新プログラムを管理できます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。

VM の Update Management は、次の方法で有効にできます。

* 1 台以上の Azure マシンに対しては、お使いの [Azure Automation アカウント](update-mgmt-enable-automation-account.md) から。
* Azure 以外のマシンに対しては手動で。
* 単一の Azure VM に対しては、Azure portal の [仮想マシン] ページから。 このシナリオは、[Linux](../../virtual-machines/linux/tutorial-config-management.md#enable-update-management) VM 用と [Windows](../../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM 用があります。
* [複数の Azure VM](update-mgmt-enable-portal.md) に対しては、Azure portal の [仮想マシン] ページからそれらを選択することで。

> [!NOTE]
> Update Management では、Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 サポートされているリージョンの確定的な一覧については、[Azure でのワークスペースのマッピング](../how-to/region-mappings.md)に関する記事をご覧ください。 リージョン マッピングは、Automation アカウントとは別のリージョンの VM を管理する機能には影響しません。

[Azure Resource Manager テンプレート](update-mgmt-enable-template.md)を使用すると、お使いのサブスクリプション内の新しいまたは既存の Automation アカウントと Log Analytics ワークスペースに Update Management をデプロイするのに役立ちます。

> [!NOTE]
> Update Management で構成されたコンピューターを使用して Azure Automation からカスタム スクリプトを実行することはできません。 このコンピューターで実行できるのは、Microsoft が署名した更新プログラム スクリプトのみです。

## <a name="about-update-management"></a>Update Management について

Update Management で管理されるマシンでは、評価の実行とデプロイの更新に次の構成が使用されます。

* Windows または Linux 用の Log Analytics エージェント
* PowerShell Desired State Configuration (DSC) (Linux の場合)
* Automation Hybrid Runbook Worker
* Microsoft Update または Windows Server Update Services (WSUS) (Windows マシンの場合)

次の図に、ワークスペース内の接続されたすべての Windows Server および Linux マシンに対する、Update Management による評価の実行方法とセキュリティ更新プログラムの適用方法を示します。

![Update Management ワークフロー](./media/update-mgmt-overview/update-mgmt-updateworkflow.png)

Update Management を使用して、同じテナント内の複数のサブスクリプションにマシンをネイティブにデプロイできます。

パッケージがリリースされた後、Linux マシンの評価用に修正プログラムが表示されるまで 2 時間から 3 時間かかります。 Windows マシンの場合、リリースされてから評価用に修正プログラムが表示されるまで 12 時間から 15 時間かかります。

マシンで更新プログラムのコンプライアンスを確認するためのスキャンが完了すると、エージェントによって情報が Azure Monitor ログに一括転送されます。 Windows マシンでは、コンプライアンス スキャンは既定で 12 時間ごとに実行されます。

このスキャン スケジュールに加えて、Log Analytics エージェントの再起動後 15 分以内、更新プログラムのインストール前、および更新プログラムのインストール後に、更新プログラムのコンプライアンスを確認するためのスキャンが開始されます。

Linux マシンでは、コンプライアンス スキャンは既定で 1 時間ごとに実行されます。 Log Analytics エージェントを再起動した場合、コンプライアンス スキャンは 15 分以内に開始されます。

Update Management では、同期先として構成されたソースに基づいて、マシンがどの程度最新の状態であるかがレポートされます。 WSUS にレポートするように Windows マシンが構成されている場合、WSUS の Microsoft Update との最後の同期のタイミングによっては、Microsoft Updates で示されるものと結果が一致しないことがあります。 この動作は、パブリック リポジトリではなくローカル リポジトリにレポートするように構成されている Linux マシンでも同様です。

> [!NOTE]
> サービスに正しく報告するためには、Update Management で、特定の URL とポートを有効にする必要があります。 これらの要件の詳細については、[ネットワーク構成](../automation-hybrid-runbook-worker.md#network-planning)に関する記事を参照してください。

スケジュールされたデプロイを作成することで、更新が必要なマシンへのソフトウェア更新プログラムのデプロイとインストールを実行できます。 Windows マシンの場合、オプションに分類されている更新プログラムはデプロイの範囲に含まれません。 デプロイの範囲には、必須の更新プログラムのみが含まれています。

スケジュールされたデプロイでは、適用可能な更新プログラムを受け取るターゲット マシンが定義されます。 これを行うには、特定のマシンを明示的に指定するか、特定のマシン セットのログ検索 (または指定した条件に基づいて動的に Azure VM を選択する [Azure クエリ](update-mgmt-view-logs.md)) に基づいて[コンピューター グループ](../../azure-monitor/platform/computer-groups.md)を選択します。 これらのグループは、Update Management を有効にするために構成を受け取るコンピューターのターゲット設定を制御するために使用される[スコープ構成](../../azure-monitor/insights/solution-targeting.md)とは異なります。 これにより、更新プログラムのコンプライアンスを実行および報告したり、承認された必要な更新プログラムをインストールしたりできなくなります。

デプロイを定義するときに、更新プログラムをインストールできる期間を承認および設定するスケジュールも指定します。 この期間は、メンテナンス期間と呼ばれます。 再起動が必要な場合、適切な再起動オプションを選択していれば、再起動のために 20 分間のメンテナンス期間が予約されます。 パッチ適用に予想よりも時間がかかり、メンテナンス期間の残りが 20 分を切った場合、再起動は行われません。

更新プログラムは、Azure Automation の Runbook によってインストールされます。 これらの Runbook は表示できません。また、これらは構成不要です。 更新プログラムのデプロイを作成すると、対象に含めたマシンに対して、指定した時間にマスター更新 Runbook を開始するスケジュールが作成されます。 このマスター Runbook は、必須の更新プログラムをインストールする子 Runbook を各エージェントで開始します。

更新プログラムのデプロイで指定された日時に、ターゲット マシンでデプロイが並列で実行されます。 まず、スキャンが実行され、その更新プログラムが依然として必須であることが確認されてからインストールされます。 WSUS クライアント マシンの場合、更新プログラムが WSUS で承認されていないと、更新プログラムのデプロイは失敗します。

1 つのマシンを複数の Log Analytics ワークスペースに Update Management 用に登録すること (マルチホームとも言われます) は、サポートされていません。

## <a name="clients"></a>クライアント

### <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、更新プログラムの評価および修正プログラムの適用でサポートされているオペレーティング システムの一覧です。 修正プログラムを適用するには、Hybrid Runbook Worker が必要です。 Hybrid Runbook Worker の要件の詳細については、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md)」と「[Linux Hybrid Runbook Worker を展開する](../automation-linux-hrw-install.md)」を参照してください。

> [!NOTE]
> Linux マシンの更新プログラムの評価は、Automation アカウントと Log Analytics ワークスペースの[マッピング テーブル](../how-to/region-mappings.md#supported-mappings)に記載されている特定のリージョンでのみサポートされています。 

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter、Datacenter Core、Standard)<br><br>Windows Server 2016 (Datacenter、Datacenter Core、Standard)<br><br>Windows Server 2012 R2 (Datacenter、Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2 (RTM および SP1 Standard)| Update Management では、このオペレーティング システムの評価および修正プログラムの適用がサポートされます。 Windows Server 2008 R2 では、[Hybrid Runbook Worker](../automation-windows-hrw-install.md) がサポートされています。 |
|CentOS 6 (x86/x64) および 7 (x64)      | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。 分類に基づく修正プログラムでは、CentOS の RTM リリースには含まれていないセキュリティ データを返すための `yum` が必須です。 分類に基づく CentOS への修正プログラムの適用の詳細については、[Linux の更新プログラムの分類](update-mgmt-view-update-assessments.md#linux)に関する記事を参照してください。          |
|Red Hat Enterprise 6 (x86/x64) および 7 (x64)     | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。        |
|SUSE Linux Enterprise Server 11 (x86/x64) および 12 (x64)     | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。        |
|Ubuntu 14.04 LTS、16.04 LTS、18.04 (x86/x64)      |Linux エージェントでは、更新リポジトリへのアクセス権が必要です。         |

> [!NOTE]
> Azure 仮想マシン スケール セットは、Update Management を使用して管理できます。 Update Management は、基本イメージではなくインスタンス自体で動作します。 すべての VM インスタンスを一度に更新しないように、段階的に更新をスケジュールする必要があります。 仮想マシン スケールセットのノードを追加するには、「[Azure 以外のマシンを Change Tracking とインベントリに追加する](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory)」の手順に従ってください。

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

次の表は、サポートされていないオペレーティング システムの一覧です。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows クライアント     | クライアント オペレーティング システム (Windows 7 や Windows 10 など) はサポートされません。<br> Azure Windows Virtual Desktop (WVD) の場合、<br> 更新プログラムを管理するには、Windows 10 クライアント コンピューターの修正プログラム管理用の [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) をお勧めします。 |
|Windows Server 2016 Nano Server     | サポートされていません。       |
|Azure Kubernetes Service ノード | サポートされていません。 「[Azure Kubernetes Service (AKS) の Linux ノードにセキュリティとカーネルの更新を適用する](../../aks/node-updates-kured.md)」で説明されている修正プログラム適用プロセスを使用します。|

### <a name="client-requirements"></a>クライアントの要件

オペレーティング システム固有のクライアント要件については、以下の情報を参照してください。 追加のガイダンスについては、「[ネットワークの計画](#ports)」を参照してください。 TLS 1.2 のクライアント要件を理解するには、「[Azure Automation に対する TLS 1.2 の強制](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)」のセクションを参照してください。

#### <a name="windows"></a>Windows

WSUS サーバーと通信するように Windows エージェントを構成するか、Microsoft Update へのアクセスが必要です。 Windows 用 Log Analytics エージェントをインストールする方法については、「[Windows コンピューターを Azure Monitor に接続する](../../azure-monitor/platform/agent-windows.md)」を参照してください。

Microsoft Endpoint Configuration Manager は、Update Management と組み合わせて使用できます。 統合シナリオの詳細については、「[Update Management を使用して、Microsoft Endpoint Configuration Manager クライアントに更新プログラムを展開する](update-mgmt-mecmintegration.md)」を参照してください。 Configuration Manager 環境のサイトによって管理されている Windows サーバーでは、[Windows 用の Log Analytics エージェント](../../azure-monitor/platform/agent-windows.md)が必要です。 

既定では、Azure Marketplace からデプロイされた Windows VM は、Windows Update Service から自動更新を受信するように設定されています。 Windows VM をワークスペースに追加しても、この動作は変わりません。 Update Management を使用して更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

> [!NOTE]
> グループ ポリシーを変更して、システムではなくユーザーだけがマシンの再起動を実行できるようにすることができます。 Update Management にユーザーによる手動操作なしでマシンを再起動する権限がない場合、管理対象のマシンが停止する可能性があります。 詳しくは、「[自動更新のグループ ポリシー設定を構成する](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)」をご覧ください。

#### <a name="linux"></a>Linux

Linux の場合、マシンでは、プライベートまたはパブリックの更新リポジトリへのアクセス権が必要になります。 Update Management と対話するには、TLS 1.1 または TLS 1.2 が必要です。 Update Management では、複数の Log Analytics ワークスペースにレポートするように構成されている Linux 用 Log Analytics エージェントはサポートされていません。 マシンには Python 2.x もインストールされている必要があります。

> [!NOTE]
> Linux マシンの更新プログラムの評価は、特定のリージョンでのみサポートされています。 Automation アカウントと Log Analytics ワークスペースの[マッピング テーブル](../how-to/region-mappings.md#supported-mappings)を参照してください。

Linux 用 Log Analytics エージェントをインストールして最新バージョンをダウンロードする方法の詳細については、[Linux 用 Log Analytics エージェント](../../azure-monitor/platform/agent-linux.md)に関するページを参照してください。

Azure Marketplace から入手できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した VM は、Azure にデプロイされた [Red Hat Update Infrastructure (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) にアクセスするよう登録されています。 その他の Linux ディストリビューションは、そのディストリビューションでサポートされている方法を使用して、ディストリビューションのオンライン ファイル リポジトリから更新する必要があります。

## <a name="permissions"></a>アクセス許可

更新プログラムのデプロイを作成および管理するには、特定のアクセス許可が必要です。 これらのアクセス許可の詳細については、[Update Management へのロールベースのアクセス](../automation-role-based-access-control.md#update-management-permissions)に関するページをご覧ください。

## <a name="update-management-components"></a>Update Management コンポーネント

Update Management では、このセクションで説明されているリソースが使用されます。 Update Management を有効にすると、これらのリソースが Automation アカウントに自動的に追加されます。

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ

Update Management を有効にすると、Update Management に含まれている Runbook をサポートするために、Log Analytics ワークスペースに直接接続された Windows マシンが自動的に Hybrid Runbook Worker として構成されます。

Update Management で管理されている各 Windows マシンは、Automation アカウントの [システム ハイブリッド worker グループ] として、[ハイブリッド worker グループ] ペインに表示されます。 グループでは、`Hostname FQDN_GUID` の名前付け規則が使用されます。 アカウントの Runbook でこれらのグループを対象として指定することはできません。 指定しようとすると、失敗します。 これらのグループは、Update Management のみをサポートすることを目的としています。

Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用すると、Windows マシンを Automation アカウントの Hybrid Runbook Worker グループに追加して Automation Runbook をサポートすることができます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 で追加されました。

### <a name="management-packs"></a>管理パック

Operations Manager 管理グループが [Log Analytics ワークスペースに接続](../../azure-monitor/platform/om-agents.md)されている場合は、以下の管理パックが Operations Manager にインストールされます。 これらの管理パックは、直接接続されている Windows マシン上の Update Management にもインストールされます。 これらの管理パックを構成または管理する必要はありません。

* Microsoft System Center Advisor 更新プログラム評価インテリジェンス パック (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 更新プログラムの展開 MP

> [!NOTE]
> ログ データを収集するように管理グループに構成されたエージェントを使用して Log Analytics ワークスペースに接続される Operations Manager 1807 または 2019 管理グループがある場合、**Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 規則でパラメーター `IsAutoRegistrationEnabled` をオーバーライドして True に設定する必要があります。

管理パックの更新プログラムの詳細については、[Azure Monitor ログへの Operations Manager の接続](../../azure-monitor/platform/om-agents.md)に関する記事を参照してください。

> [!NOTE]
> Log Analytics エージェントを使用して Update Management でマシンを完全に管理するには、Windows 用の Log Analytics エージェント、または Linux 用 Log Analytics エージェントに更新する必要があります。 エージェントを更新する方法については、[Operations Manager エージェントのアップグレード方法](/system-center/scom/deploy-upgrade-agents)に関する記事を参照してください。 Operations Manager を使用する環境では、System Center Operations Manager 2012 R2 UR 14 以降を実行している必要があります。

## <a name="data-collection"></a>データ コレクション

### <a name="supported-sources"></a>サポートされているソース

次の表では、Update Management でサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
| --- | --- | --- |
| Windows エージェント |はい |Update Management は、Windows エージェントからシステムの更新プログラムに関する情報を収集した後、必要な更新プログラムのインストールを開始します。 |
| Linux エージェント |はい |Update Management は、Linux エージェントからシステムの更新プログラムに関する情報を収集した後、サポート対象のディストリビューションに対して必要な更新プログラムのインストールを開始します。 |
| Operations Manager 管理グループ |はい |Update Management は、接続された管理グループ内のエージェントからシステムの更新プログラムに関する情報を収集します。<br/><br/>Operations Manager エージェントから Azure Monitor ログへの直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |

### <a name="collection-frequency"></a>収集の頻度

Update Management では、次のルールを使用して、管理対象のマシンのデータのスキャンが行われます。 管理対象のマシンの更新されたデータがダッシュボードに表示されるまでに、30 分から 6 時間かかる可能性があります。

* 各 Windows マシン - Update Management では、各マシンで 1 日に 2 回スキャンが行われます。 15 分ごとに Windows API が呼び出され、最後の更新時間を照会することで、状態が変化したかどうかが確認されます。 状態が変更された場合、Update Management で対応スキャンが開始されます。

* 各 Linux マシン - Update Management では 1 時間ごとにスキャンが行われます。

Update Management を使用しているマシンでの Azure Monitor ログによる平均データ使用量は、1 か月あたり約 25 MB です。 この値は概数にすぎず、環境によって異なることがあります。 正確な使用量を把握するために、環境を監視することをお勧めします。 Azure Monitor ログのデータ使用量を分析する方法の詳細については、[使用量とコストの管理](../../azure-monitor/platform/manage-cost-storage.md)に関するページを参照してください。

## <a name="network-planning"></a><a name="ports"></a>ネットワークの計画

Update Management には次のアドレスが明示的に必要です。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

ネットワーク グループのセキュリティ規則を作成する場合、または Automation サービスと Log Analytics ワークスペースへのトラフィックを許可するように Azure Firewall を構成する場合は、[サービス タグ](../../virtual-network/service-tags-overview.md#available-service-tags)の **GuestAndHybridManagement** と **AzureMonitor** を使用します。 これにより、ネットワーク セキュリティ規則の継続的な管理が簡単になります。 Azure VM から安全かつプライベートに Automation サービスに接続するには、[Azure Private Link の使用](../how-to/private-link-security.md)に関するページを確認してください。 現在のサービス タグと範囲情報を、オンプレミスのファイアウォール構成の一部として取得して含めるには、[ダウンロード可能な JSON ファイル](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)に関するページを参照してください。

Windows マシンでは、Windows Update で必要なすべてのエンドポイントへのトラフィックも許可する必要があります。 必要なエンドポイントの更新された一覧は、「[HTTP またはプロキシに関連する問題](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)」で確認できます。 ローカル環境に [Windows Update サーバー](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)がある場合は、[WSUS キー](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)で指定されているサーバーへのトラフィックも許可する必要があります。

Red Hat Linux マシンで必要なエンドポイントについては、「[RHUI コンテンツ配信サーバーの IP アドレス](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)」をご覧ください。 他の Linux ディストリビューションについては、プロバイダーのドキュメントをご覧ください。

Hybrid Runbook Worker で必要なポートの詳細については、「[Hybrid Runbook Worker の Update Management アドレス](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)」をご覧ください。

インターネットに接続するネットワーク上のマシンが IT セキュリティ ポリシーで許可されていない場合、[Log Analytics ゲートウェイ](../../azure-monitor/platform/gateway.md)を設定し、マシンをゲートウェイ経由で Azure Automation および Azure Monitor に接続するように構成できます。

## <a name="update-classifications"></a>更新プログラムの分類

次の表では、Update Management が Windows 更新プログラムでサポートされる分類が定義されています。 

|分類  |説明  |
|---------|---------|
|緊急更新プログラム     | セキュリティに関連しない重大なバグを修正する、特定の問題に対する更新プログラムです。        |
|セキュリティ更新プログラム     | 製品固有のセキュリティに関連する問題に対する更新プログラムです。        |
|更新プログラムのロールアップ     | 容易なデプロイのためにパッケージにまとめられた修正プログラムの累積セットです。        |
|Feature Pack     | 製品リリース外で配布される製品の新機能です。        |
|Service Pack     | アプリケーションに適用される修正プログラムの累積セットです。        |
|定義の更新     | ウイルスまたは他の定義ファイルに対する更新プログラムです。        |
|ツール     | 1 つまたは複数のタスクを完了するのに役立つユーティリティまたは機能です。        |
|更新プログラム     | 現在インストールされているアプリケーションまたはファイルに対する更新プログラムです。        |

次の表では、Linux 更新プログラムでサポートされる分類が定義されています。

|分類  |説明  |
|---------|---------|
|緊急更新プログラムとセキュリティ更新プログラム     | 特定の問題または製品固有のセキュリティに関連する問題に対する更新プログラムです。         |
|他の更新プログラム     | 本質的に重要ではない、またはセキュリティ更新プログラムではない、他のすべての更新プログラムです。        |

>[!NOTE]
>Linux マシン用の更新プログラムの分類は、サポートされている Azure パブリック クラウド リージョンで使用される場合にのみ使用できます。 以下の国内クラウド リージョンで Update Management を使用する場合、
>* Azure US Government
>* 中国の 21Vianet
>
> Linux の更新プログラムの分類はなく、 **[他の更新プログラム]** カテゴリの下でレポートされます。 Update Management では、サポートされているディストリビューションによって発行されたデータが使用されます。具体的には、リリースされた [OVAL](https://oval.mitre.org/) (Open Vulnerability and Assessment Language) ファイルです。 インターネット アクセスはこれらの国内クラウドから制限されているため、Update Management はこれらのファイルにアクセスして使用することはできません。

Linux の場合、クラウドでのデータ エンリッチメントにより、Update Management は、評価データを表示しながら、クラウド内で重要な更新プログラムとセキュリティ更新プログラムを識別できます。 修正プログラムの場合、Update Management はマシン上にある分類データを使用します。 他のディストリビューションとは異なり、RTM バージョンの CentOS ではこの情報は使用できません。 CentOS マシンで、次のコマンドに対してセキュリティ データを返すように構成されている場合、Update Management は分類に基づいて修正プログラムを適用できます。

```bash
sudo yum -q --security check-update
```

CentOS 上でネイティブ分類データを使用できるようにするためのサポートされている方法は現在ありません。 現時点では、お客様がこの機能をご自身で使用可能にした場合には、できる範囲内のサポートのみを提供しています。

Red Hat Enterprise バージョン 6 の更新プログラムを分類するには、yum-security プラグインをインストールする必要があります。 Red Hat Enterprise Linux 7 では、プラグインは既に yum 自体の一部であるため、何もインストールする必要はありません。 詳細については、次の Red Hat の[ナレッジ記事](https://access.redhat.com/solutions/10021)を参照してください。

## <a name="integrate-update-management-with-configuration-manager"></a>Configuration Manager と Azure Update Management を統合する

PC、サーバー、モバイル デバイスを管理するために Microsoft Endpoint Configuration Manager に投資してきたお客様は、Configuration Manager の強みと成熟度を活用してソフトウェア更新プログラムを管理できます。 Update Management を Configuration Manager と統合する方法については、「[Configuration Manager と Azure Update Management を統合する](update-mgmt-mecmintegration.md)」を参照してください。

## <a name="third-party-updates-on-windows"></a>Windows でのサードパーティの更新プログラム

Update Management では、ローカルで構成された更新リポジトリに依存して、サポートされている Windows システム (WSUS または Windows Update) を更新します。 [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) などのツールを使用すると、WSUS を使用してカスタム更新プログラムをインポートして発行することができます。 このシナリオでは、サードパーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で更新プログラムを適用できます。 Updates Publisher を構成する方法については、「[Install Updates Publisher](/configmgr/sum/tools/install-updates-publisher)」(Updates Publisher のインストール) を参照してください。

## <a name="enable-update-management"></a>Update Management の有効化

Azure [Resource Manager テンプレート](update-mgmt-enable-template.md)を使用すると、お使いのサブスクリプション内の新しいまたは既存の Automation アカウントと Azure Monitor Log Analytics ワークスペースに Update Management ソリューションをデプロイするのに役立ちます。 管理する必要があるマシンのスコープは構成されません。これは、テンプレートを使用した後の個別の手順として実行されます。

Update Management を有効にし、管理するマシンを選択するには、次の方法を使用します。

* [仮想マシンから](update-mgmt-enable-vm.md)
* [複数のマシンを参照することから](update-mgmt-enable-portal.md)
* [Azure Automation アカウントから行う方法](update-mgmt-enable-automation-account.md)

## <a name="next-steps"></a>次のステップ

* Update Management で作業する詳細については、[VM の更新プログラムの管理](update-mgmt-manage-updates-for-vm.md)に関するページを参照してください。

* Update Management についてよく寄せられる質問については、「[Azure Automation についてよく寄せられる質問](../automation-faq.md)」でご確認ください。
