---
title: 仮想マシンのコンテンツの監査を学習する
description: Azure Policy がゲスト構成エージェントを使用して仮想マシン内の設定を監査するしくみについて説明します。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 73f986774fc13ac8c69cd800c977c909b591a74c
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369745"
---
# <a name="understand-azure-policys-guest-configuration"></a>Azure Policy のゲストの構成の理解

Azure リソースの監査と[修復](../how-to/remediate-resources.md)以外に、Azure Policy ではマシン内の設定を監査することができます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 クライアントを介した拡張機能によって、次のような設定が検証されます。

- オペレーティング システムの構成
- アプリケーションの構成または存在
- 環境設定

現時点での Azure Policy ゲスト構成では、マシン内の設定の監査のみが行われます。 構成は適用されません。

## <a name="extension-and-client"></a>拡張機能とクライアント

マシン内の設定を監査するには、[仮想マシン拡張機能](../../../virtual-machines/extensions/overview.md)を有効にします。 拡張機能は、適用可能なポリシーの割り当てと、対応する構成定義をダウンロードします。

### <a name="limits-set-on-the-extension"></a>拡張機能に設定されている制限

マシン内で実行されているアプリケーションに対する拡張機能の影響を制限するため、ゲスト構成の CPU 使用率は 5% を超えることはできません。 この制限は、組み込み定義とカスタム定義の両方に存在します。

## <a name="register-guest-configuration-resource-provider"></a>ゲスト構成リソース プロバイダーの登録

ゲストの構成を使用するには、リソース プロバイダーを登録する必要があります。 ポータルまたは PowerShell を使用して登録できます。 ポータルを使用してゲスト構成ポリシーを割り当てる場合、リソース プロバイダーは自動的に登録されます。

### <a name="registration---portal"></a>登録ポータル

Azure portal からゲストの構成用のリソース プロバイダを登録するには、次の手順を実行します。

1. Azure portal を起動し、 **[すべてのサービス]** をクリックします。 **サブスクリプション**を検索して選択します。

1. ゲスト構成を有効にするために必要とするサブスクリプションを検索し、クリックします。

1. **サブスクリプション** ページ上の左側のメニューで、**リソース プロバイダー** をクリックします。

1. **Microsoft.GuestConfiguration** が見つかるまでフィルター処理するか、またはスクロールして、同じ行に**登録**をクリックします。

### <a name="registration---powershell"></a>登録 - PowerShell

PowerShell を使ってゲスト構成用にリソース プロバイダーを登録するには、次のコマンドを実行します。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>検証ツール

マシン内では、ゲスト構成クライアントによりローカル ツールを使用して監査が実行されます。

次の表では、サポートされている各オペレーティング システムで使用するローカルのツールの一覧を示します。

|オペレーティング システム|検証ツール|Notes|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| ゲスト構成拡張機能によって、Ruby、Python がインストールされます。 |

### <a name="validation-frequency"></a>検証の頻度

ゲスト構成クライアントは、新しいコンテンツを 5 分ごとにチェックします。 ゲスト割り当てを受信すると、設定が 15 分間隔でチェックされます。 監査が完了するとすぐに、結果がゲスト構成リソース プロバイダーに送信されます。 ポリシー[評価トリガー](../how-to/get-compliance-data.md#evaluation-triggers)が発生すると、マシンの状態がゲスト構成リソース プロバイダーに書き込まれます。 この更新により、Azure Policy によって Azure Resource Manager のプロパティが評価されます。 オンデマンドの Azure Policy 評価により、ゲスト構成リソース プロバイダーから最新の値が取得されます。 ただし、マシン内の構成の新しい監査はトリガーされません。

## <a name="supported-client-types"></a>サポートされているクライアントの種類

次の表は、Azure イメージでサポートされているオペレーティング システムの一覧を示します。

|Publisher|名前|バージョン|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、2012 R2 Datacenter、2016 Datacenter、2019 Datacenter|
|Microsoft|Windows クライアント|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> ゲスト構成は、サポートされる OS を実行しているノードを監査できます。 カスタム イメージを使用する仮想マシンを監査する場合は、**DeployIfNotExists** の定義を複製し、**If** セクションを変更してイメージのプロパティを含める必要があります。

### <a name="unsupported-client-types"></a>サポートされていないクライアントの種類

Windows Server Nano Server はどのバージョンでもサポートされていません。

## <a name="guest-configuration-extension-network-requirements"></a>ゲスト構成拡張機能のネットワーク要件

Azure のゲスト構成リソース プロバイダーと通信するには、マシンはポート **443** で Azure データセンターに対してアウトバウンド アクセスを行う必要があります。 アウトバウンド トラフィックが許可されないプライベート仮想ネットワークを Azure で使用している場合は、[ネットワーク セキュリティ グループ](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)規則で例外を構成する必要があります。
[サービス タグ](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" を使用して、ゲスト構成サービスを参照できます。

## <a name="guest-configuration-definition-requirements"></a>ゲスト構成定義の要件

ゲスト構成によって実行される各監査には、**DeployIfNotExists** 定義と **AuditIfNotExists** 定義の 2 つのポリシー定義が必要です。 **DeployIfNotExists** 定義は、[検証ツール](#validation-tools)をサポートするようにゲスト構成エージェントおよびその他のコンポーネントを使用してマシンを準備するために使用されます。

**DeployIfNotExists** ポリシー定義が検証し、次の項目を修正します。

- マシンに評価する構成が割り当てられていることを検証します。 割り当てが現在存在しない場合は、割り当てを取得し、次のようにしてマシンを準備します。
  - [マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) を使用して、マシンへの認証を行う
  - **Microsoft.GuestConfiguration** 拡張機能の最新バージョンをインストールする
  - 必要とする場合、[検証ツール](#validation-tools)と依存関係をインストールする

**DeployIfNotExists** の割り当てが準拠していない場合は、[修復タスク](../how-to/remediate-resources.md#create-a-remediation-task)を使用できます。

**DeployIfNotExists** の割り当てが準拠している場合は、**AuditIfNotExists** ポリシーの割り当てによってローカル検証ツールが使用され、構成の割り当てが準拠なのか非準拠なのかが判断されます。 この検証ツールは、結果をゲスト構成クライアントに提供します。 クライアントは、その結果をゲストの拡張機能に転送します。それにより、その結果がゲスト構成のリソース プロバイダー全体で使用可能になります。

Azure Policy は、ゲスト構成リソースプロバイダーの **complianceStatus** プロパティを使用して**コンプライアンス** ノードでコンプライアンスを報告します。 詳細については、[コンプライアンス データを取得する](../how-to/get-compliance-data.md)を参照してください。

> [!NOTE]
> **AuditIfNotExists** ポリシーから結果を返すには、**DeployIfNotExists** ポリシーが必要です。 **DeployIfNotExists** がない場合、**AuditIfNotExists** ポリシーは状態として"0 of 0" のリソースを示します。

割り当てで使用するための定義をグループ化するためのイニシアティブには、ゲストの構成のすべての組み込みポリシーが含まれます。 " _\[[プレビュー]\]: Linux および Windows マシン内のパスワードのセキュリティ設定の監査_" という名前の組み込みイニシアティブには、18 のポリシーが含まれています。 Windows のために **DeployIfNotExists** と **AuditIfNotExists** の 6  つのペアがあって、Linux 用に 3 つのペアがあります。 [ポリシー定義](definition-structure.md#policy-rule)のロジックでは、対象のオペレーティング システムのみが評価されることが検証されます。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>業界の基準に従ってオペレーティング システムの設定を監査する

Azure Policy で利用できるイニシアチブの 1 つは、Microsoft の "ベースライン" に従って、仮想マシン内のオペレーティング システムの設定を監査する機能を提供します。 _\[[プレビュー]\]: Azure セキュリティ ベースライン設定と一致しない Windows VM を監査する_という定義には、Active Directory グループ ポリシーからの設定に基づく監査規則の完全なセットが含まれています。

ほとんどの設定は、パラメーターとして使用できます。 この機能を使用すると、監査対象をカスタマイズして、組織の要件に合わせてポリシーを調整したり、業界の規制標準などのサードパーティの情報にポリシーをマップしたりすることができます。

一部のパラメーターは、整数の値範囲をサポートしています。 たとえば、[パスワードの有効期間] パラメーターを範囲演算子を使用して設定し、コンピューターの所有者に柔軟に割り当てることができます。 ユーザーにパスワードの変更を要求する有効なグループ ポリシー設定は、70 日以内である必要がありますが、1 日未満にすることはできません。 パラメーターの情報バブルで説明されているように、このビジネス ポリシーを有効な監査値にするには、値を「1, 70」に設定します。

Azure Resource Manager デプロイ テンプレートを使用してポリシーを割り当てる場合は、パラメーター ファイルを使用して、ソース管理からこれらの設定を管理できます。 Git などのツールを使用して、チェックインのたびにコメントを含む監査ポリシーへの変更を管理すると、その割り当てが予期される値に対して例外となってしまう理由の証拠がドキュメントに記録されます。

#### <a name="applying-configurations-using-guest-configuration"></a>ゲスト構成を使用して構成を適用する

Azure Policy の最新の機能によって、コンピューター内の設定が構成されます。 _[Windows コンピューターでタイムゾーンを構成]_ する定義は、タイムゾーンを構成することによって、マシンに変更を加えます。

_[構成]_ で始まる定義を割り当てるとき、 _[前提条件を展開して Windows VM でゲスト構成ポリシーを有効にする]_ 定義も割り当てる必要があります。 必要に応じて、これらの定義をイニシアチブで結合させることができます。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Azure 外部のコンピューターにポリシーを割り当てる

ゲスト構成に使用できる監査ポリシーには、**Microsoft.HybridCompute/machines** という種類のリソースが含まれます。 ポリシー割り当てのスコープ内にある[サーバー向け Azure Arc](../../../azure-arc/servers/overview.md) にオンボードされているすべてのマシンが自動的に追加されます。

### <a name="multiple-assignments"></a>複数の割り当て

ポリシー割り当てで異なるパラメーターが使用されている場合でも、現在、ゲスト構成ポリシーでは、同じゲスト割り当てはマシンごとに一度の割り当てのみがサポートされています。

## <a name="built-in-resource-modules"></a>組み込みリソース モジュール

ゲスト構成拡張機能をインストールするとき、最新バージョンの DSC リソース モジュールには "GuestConfiguration" PowerShell モジュールが含まれています。 このモジュールは、PowerShell ギャラリーのモジュール ページ [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/) の [Manual Download] リンクを使用してダウンロードできます。 ".nupkg'" ファイル形式の名前を ".zip" に変更することで、圧縮を解除して確認できます。

## <a name="client-log-files"></a>クライアントのログ ファイル

ゲスト構成拡張機能は、ログ ファイルを次の場所に書き込みます。

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

`<version>` は現在のバージョン番号を示します。

### <a name="collecting-logs-remotely"></a>ログのリモート収集

ゲスト構成の構成またはモジュールのトラブルシューティングでは最初に、`Test-GuestConfigurationPackage` コマンドレットを使用し、「[ゲスト構成パッケージをテストする](../how-to/guest-configuration-create.md#test-a-guest-configuration-package)」の手順に従う必要があります。
それでうまくいかない場合は、クライアント ログを収集すると問題の診断に役立ちます。

#### <a name="windows"></a>Windows

Azure VM の実行コマンド機能を使用して Windows マシンのログ ファイルから情報をキャプチャするには、次の PowerShell スクリプトの例が役立ちます。 詳しくは、「[実行コマンドを使用して Windows VM で PowerShell スクリプトを実行する](../../../virtual-machines/windows/run-command.md)」をご覧ください。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Azure VM の実行コマンド機能を使用して Linux マシンのログ ファイルから情報をキャプチャするには、次の Bash スクリプトの例が役立ちます。 詳しくは、「[実行コマンドを使用して Linux VM でシェル スクリプトを実行する](../../../virtual-machines/linux/run-command.md)」をご覧ください

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>ゲスト構成のサンプル

Policy ゲスト構成の組み込みのイニシアチブのソースは、次の場所にあります。

- [組み込みのポリシー定義 - ゲスト構成](../samples/built-in-policies.md#guest-configuration)
- [組み込みのイニシアチブ - ゲスト構成](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy サンプルの GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](definition-structure.md)」を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。
