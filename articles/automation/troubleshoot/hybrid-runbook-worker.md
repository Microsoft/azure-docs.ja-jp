---
title: トラブルシューティング - Azure Automation Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker のトラブルシューティングについて説明します。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 11/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4d804499116631be6f922f67f8b8f6c7063a6d5c
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030729"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Hybrid Runbook Worker のトラブルシューティング

この記事では、Hybrid Runbook Worker での問題のトラブルシューティングについて説明します。

## <a name="general"></a>全般

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うためにエージェントに依存しています。 Windows では、このエージェントは Microsoft Monitoring Agent (MMA) とも呼ばれる Windows 用 Log Analytics エージェントです。 Linux の場合は、Linux 用 Log Analytics エージェントです。

### <a name="runbook-execution-fails"></a>シナリオ:Runbook の実行が失敗する

#### <a name="issue"></a>問題

Runbook の実行が失敗し、次のエラーを受け取ります。

```error
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Runbook は 3 回実行を試みると、短時間中断されます。 Runbook の完了を妨げる可能性がある状況が存在します。 関連するエラー メッセージに追加情報が含まれていない場合があります。

#### <a name="cause"></a>原因

以下の原因が考えられます。

* Runbook がローカル リソースで認証できない。

* Hybrid Worker がプロキシまたはファイアウォールの内側にある。

* Hybrid Runbook Worker の機能を実行するように構成されているコンピューターが、ハードウェアの最小要件を満たしていない。

#### <a name="resolution"></a>解決策

コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするようにワーカーを構成する前に、ハードウェアの最小要件を満たしている必要があります。 Runbook とそれらが使用するバック グラウンド プロセスによってシステムが過剰に使用され、それが原因で Runbook ジョブの遅延またはタイムアウトが発生する可能性があります。

Hybrid Runbook Worker の機能を実行するコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU の負荷は、リソースのアップグレードが必要であることを示している可能性があります。 最小要件を満たす別のコンピューティング リソースを選択し、ワークロードの需要によって増加が必要であることが示された時点でスケーリングすることもできます。

**Microsoft-SMA** のイベント ログで " *Win32 Process Exited with code [4294967295]* " という説明の対応するイベントを確認します。 このエラーの原因は、Runbook で認証が構成されていないか、または Hybrid Worker グループの Run As 資格情報が指定されていないことです。 「[Runbook のアクセス許可](../automation-hrw-run-runbooks.md#runbook-permissions)」を参照して、Runbook の認証を正しく構成してあることを確認します。

### <a name="no-cert-found"></a>シナリオ:Hybrid Runbook Worker の証明書ストア内に証明書が見つからない

#### <a name="issue"></a>問題

Hybrid Runbook Worker で実行される Runbook が次のエラー メッセージで失敗します。

```error
Connect-AzureRmAccount : No certificate was found in the certificate store with thumbprint 0000000000000000000000000000000000000000
At line:3 char:1
+ Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Connect-AzureRmAccount], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```
#### <a name="cause"></a>原因

このエラーは、Hybrid Runbook Worker 上で実行される Runbook 内で[実行アカウント](../manage-runas-account.md)を使用しようとしたが、その実行アカウントの証明書が存在しない場合に発生します。 既定では、Hybrid Runbook Worker には、実行アカウントを正常に動作させるために必要なローカルの証明書資産はありません。

#### <a name="resolution"></a>解決策

Hybrid Runbook Worker が Azure VM の場合は、[Azure リソース用のマネージド ID](../automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) を代わりに使用できます。 このシナリオでは、実行アカウントの代わりに Azure VM のマネージド ID を使用して Azure リソースへの認証を可能にすることで、認証を簡素化します。 Hybrid Runbook Worker がオンプレミスのコンピューターである場合は、実行アカウント証明書をコンピューターにインストールする必要があります。 証明書をインストールする方法については、「[Hybrid Runbook Worker での Runbook の実行](../automation-hrw-run-runbooks.md)」で PowerShell Runbook Export-RunAsCertificateToHybridWorker を実行するための手順を参照してください。

### <a name="error-403-on-registration"></a>シナリオ:Hybrid Runbook Worker の登録中にエラー 403 が発生した

#### <a name="issue"></a>問題

ワーカーの初期登録フェーズが失敗し、次のエラー (403) が表示されます。

```error
"Forbidden: You don't have permission to access / on this server."
```

#### <a name="cause"></a>原因

以下の原因が考えられます。
* エージェントの設定で、ワークスペース ID かワークスペース キー (プライマリ) に入力ミスがあります。 
* Hybrid Runbook Worker で構成をダウンロードできず、アカウント リンク エラーが発生します。 Azure では、ソリューションを有効にした場合、Log Analytics ワークスペースと Automation アカウントをリンクするために特定のリージョンのみがサポートされます。 また、コンピューターで誤った日付や時刻が設定されている可能性もあります。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。

#### <a name="resolution"></a>解決策

##### <a name="mistyped-workspace-idkey"></a>ワークスペース ID/キーの入力ミス
エージェントのワークスペース ID またはワークスペース キーに入力ミスがあるかどうかを確認するには、Windows エージェントの場合は「[ワークスペースの追加または削除 - Windows エージェント](../../azure-monitor/platform/agent-manage.md#windows-agent)」、Linux エージェントの場合は「[ワークスペースの追加または削除 - Linux エージェント](../../azure-monitor/platform/agent-manage.md#linux-agent)」を参照してください。  Azure portal から完全な文字列を選択し、コピーと貼り付けを慎重に行う必要があります。

##### <a name="configuration-not-downloaded"></a>構成がダウンロードされていない

Log Analytics ワークスペースと Automation アカウントは、リンクされたリージョン内にある必要があります。 サポートされているリージョンの一覧については、[Azure Automation と Log Analytics ワークスペースのマッピング](../how-to/region-mappings.md)に関する記事を参照してください。

また、コンピューターの日付またはタイムゾーンを更新する必要がある場合もあります。 カスタムの時間範囲を選択する場合は、その範囲が UTC であることを確認してください。これは、ローカル タイムゾーンとは異なる場合があります。

## <a name="linux"></a>Linux

Linux Hybrid Runbook Worker は、[Linux 用 Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="oms-agent-not-running"></a>シナリオ:Linux 用 Log Analytics エージェントが実行されていない

#### <a name="issue"></a>問題

Linux 用 Log Analytics エージェントが実行されていない

#### <a name="cause"></a>原因

エージェントが実行されていない場合、Linux Hybrid Runbook Worker は Azure Automation と通信できません。 さまざまな理由で、エージェントが実行されていない可能性があります。

#### <a name="resolution"></a>解決策

 次のコマンドを入力して、このエージェントが実行されていることを確認します: `ps -ef | grep python`。 次のように、**nxautomation** ユーザー アカウントの python プロセスが出力されます。 Update Management または Azure Automation ソリューションが有効でない場合、次のどのプロセスも実行されません。

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

次の一覧は、Linux Hybrid Runbook Worker のために開始されるプロセスを示します。 これらはすべて `/var/opt/microsoft/omsagent/state/automationworker/` ディレクトリにあります。

* **oms.conf** - worker マネージャー プロセスです。 これは、DSC から直接開始されます。

* **worker.conf** - 自動登録ハイブリッド worker プロセスです。 worker マネージャーによって開始されます。 このプロセスは Update Management によって使用され、ユーザーに透過的です。 Update Management ソリューションがコンピューター上で有効でない場合、このプロセスは存在しません。

* **diy/worker.conf** - DIY ハイブリッド worker プロセスです。 DIY ハイブリッド ワーカー プロセスは、Hybrid Runbook Worker でユーザーの Runbook を実行するために使用されます。 キーの詳細での自動登録ハイブリッド worker プロセスとの違いは、異なる構成を使用していることだけです。 Azure Automation ソリューションが無効であり、DIY Linux ハイブリッド worker が登録されていない場合、このプロセスは存在しません。

エージェントが実行されていない場合、次のコマンドを実行してサービスを開始します。`sudo /opt/microsoft/omsagent/bin/service_control restart`

### <a name="class-does-not-exist"></a>シナリオ:指定したクラスが存在しない

「**指定されたクラスが存在しません**」というエラーが `/var/opt/microsoft/omsconfig/omsconfig.log` で表示された場合、Linux 用 Log Analytics エージェントを更新する必要があります。 エージェントを再インストールするには、次のコマンドを実行します。

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Windows Hybrid Runbook Worker は、[Windows 用 Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md) を使用することにより、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、状態の報告を行います。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="mma-not-running"></a>シナリオ:Microsoft Monitoring Agent が実行されていません。

#### <a name="issue"></a>問題

`healthservice` サービスが Hybrid Runbook Worker マシンで実行されていません。

#### <a name="cause"></a>原因

Microsoft Monitoring Agent の Microsoft サービスが実行されていない場合、この状態により Hybrid Runbook Worker は Azure Automation と通信できません。

#### <a name="resolution"></a>解決策

PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="event-4502"></a>シナリオ:Operations Manager ログのイベント 4502

#### <a name="issue"></a>問題

**アプリケーションとサービス ログ\Operations Manager** のイベント ログで、イベント 4502 と、**Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** および次の説明を含む EventMessage が表示されます。*サービス \<wsid\>.oms.opinsights.azure.com によって提示された証明書は、Microsoft サービスで使用する証明機関によって発行されたものではありません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。*

#### <a name="cause"></a>原因

この問題は、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。 

#### <a name="resolution"></a>解決策

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログにあるかどうかを確認できます。 Log Analytics エージェントに関する問題のトラブルシューティングの詳細については、[Log Analytics Windows エージェントの問題のトラブルシューティング](../../azure-monitor/platform/agent-windows-troubleshoot.md)に関するページを参照してください。

[Runbook の出力とメッセージ](../automation-runbook-output-and-messages.md) は、クラウドで実行される Runbook ジョブと同様に、Hybrid Worker から Azure Automation に送られます。 他の Runbook と同じ方法で、Verbose および Progress ストリームも有効にできます。

### <a name="corrupt-cache"></a>シナリオ:Hybrid Runbook Worker の報告がない

#### <a name="issue"></a>問題

Hybrid Runbook Worker マシンは動作していますが、ワークスペース内のマシンのハートビート データが表示されません。

次のクエリの例は、ワークスペース内のマシンとそれらの最後のハートビートを示しています:

```loganalytics
// Last heartbeat of each computer
Heartbeat
| summarize arg_max(TimeGenerated, *) by Computer
```

#### <a name="cause"></a>原因

この問題は、Hybrid Runbook Worker 上のキャッシュの破損が原因である可能性があります。

#### <a name="resolution"></a>解決策

この問題を解決するには、Hybrid Runbook Worker にサインインし、次のスクリプトを実行します。 このスクリプトは Microsoft Monitoring Agent を停止し、そのキャッシュを削除し、サービスを再起動します。 この操作により、Hybrid Runbook Worker の構成が Azure Automation から強制的に再ダウンロードされます。

```powershell
Stop-Service -Name HealthService

Remove-Item -Path 'C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State' -Recurse

Start-Service -Name HealthService
```

### <a name="already-registered"></a>シナリオ:Hybrid Runbook Worker を追加できない

#### <a name="issue"></a>問題

`Add-HybridRunbookWorker` コマンドレットを使用して Hybrid Runbook Worker を追加しようとすると、次のメッセージが表示されます。

```error
Machine is already registered
```

#### <a name="cause"></a>原因

この問題は、マシンが既に別の Automation アカウントに登録されている場合や、マシンから Hybrid Runbook Worker を削除した後にそれを再度追加しようとした場合に発生する可能性があります。

#### <a name="resolution"></a>解決策

この問題を解決するには、次のレジストリ キーを削除し、`HealthService` を再起動した後、もう一度 `Add-HybridRunbookWorker` コマンドレットを試してください。

`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\HybridRunbookWorker`

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。