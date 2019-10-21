---
title: ハイブリッド環境での Azure Monitor (プレビュー) の有効化 |Microsoft Docs
description: この記事では、1 つまたは複数の仮想マシンを含むハイブリッド クラウド環境において、Azure Monitor for VMs を有効にする方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: f4c483c36dc7a19e3e16dcaabab10af03cdfe17e
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72515505"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>ハイブリッド環境での Azure Monitor for VMs (プレビュー) の有効化

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

この記事では、お使いのデータセンターまたは他のクラウド環境でホストされている仮想マシンまたは物理コンピューターに対して、Azure Monitor for VMs (プレビュー) を有効にする方法について説明します。 このプロセスの最後に、ご利用の環境にある仮想マシンの監視が正常に開始され、何らかのパフォーマンスや可用性の問題が発生しているかどうかを確認できるようになります。 

開始する前に、必ず[前提条件](vminsights-enable-overview.md)を確認し、ご利用のサブスクリプションおよびリソースが要件を満たしていることを確認してください。 [Log Analytics Linux および Windows エージェント](../../log-analytics/log-analytics-agent-overview.md)の要件とデプロイ方法を確認します。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>Azure Monitor for VMs マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。 マップ データは、Log Analytics エージェントによって常に Azure Monitor サービスに直接、または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[Operations Management Suite ゲートウェイ](../../azure-monitor/platform/gateway.md)を経由して送信されます。

以下に、このタスクを完了するための手順をまとめています。

1. Windows または Linux 用の Log Analytics エージェントをインストールします。 エージェントをインストールする前に、システムの前提条件とデプロイ メソッドを理解するために、[Log Analytics エージェントの概要](../platform/log-analytics-agent.md)に関する記事を確認してください。

2. [Windows](https://aka.ms/dependencyagentwindows) または [Linux](https://aka.ms/dependencyagentlinux) 用の Azure Monitor for VMs Map Dependency Agent をダウンロードしてインストールします。

3. パフォーマンス カウンターの収集を有効にします。

4. Azure Monitor for VMs をデプロイします。

>[!NOTE]
>この記事で説明する Dependency Agent のデプロイに関する情報は、[Service Map ソリューション](service-map.md)にも適用できます。  

## <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする

`InstallDependencyAgent-Windows.exe` を実行することで、Dependency Agent を Windows コンピューターに手動でインストールできます。 オプションを指定せずにこの実行可能ファイルを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でエージェントをインストールできます。

>[!NOTE]
>エージェントをインストールまたはアンインストールするには、"*管理者*" 特権が必要です。

次の表に、コマンド ラインからのエージェントのセットアップでサポートされているパラメーターを示します。

| パラメーター | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を返します。 |
| /S | ユーザーの操作を必要とせずに、サイレント インストールを実行します。 |

たとえば、`/?` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Windows.exe /?** 」と入力します。

Windows Dependency Agent のファイルは、既定で *C:\Program Files\Microsoft Dependency Agent* にインストールされます。 セットアップの完了後に Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは、 *%Programfiles%\Microsoft Dependency Agent\logs* にあります。

## <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする

Dependency Agent は、*InstallDependencyAgent-Linux64.bin* (自己解凍バイナリ ファイルを含むシェル スクリプト) から Linux サーバーにインストールされます。 `sh` を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

>[!NOTE]
> エージェントをインストールまたは構成するには、ルート アクセスが必要です。
>

| パラメーター | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

たとえば、`-help` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Linux64.bin -help**」と入力します。

コマンド `sh InstallDependencyAgent-Linux64.bin` を実行し、Linux Dependency Agent を root としてインストールします。

Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、 */var/opt/microsoft/dependency-agent/log* にあります。

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | Location |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>インストール スクリプトの例

Dependency Agent を同時に多数のサーバーに対して簡単にデプロイするために、次のスクリプトの例が提供されています。このスクリプトを使用すると、Dependency Agent をダウンロードして Windows または Linux にインストールできます。

### <a name="powershell-script-for-windows"></a>Windows 用 PowerShell スクリプト

```powershell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Linux 用シェル スクリプト

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="desired-state-configuration"></a>Desired State Configuration

Desired State Configuration (DSC) を使用して Dependency Agent をデプロイするには、xPSDesiredStateConfiguration モジュールと次に示すコード例を使用できます。

```powershell
configuration ServiceMap {

    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    $DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

    Node localhost
    {
        # Download and install the Dependency agent
        xRemoteFile DAPackage 
        {
            Uri = "https://aka.ms/dependencyagentwindows"
            DestinationPath = $DAPackageLocalPath
        }

        xPackage DA
        {
            Ensure="Present"
            Name = "Dependency Agent"
            Path = $DAPackageLocalPath
            Arguments = '/S'
            ProductId = ""
            InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
            InstalledCheckRegValueName = "DisplayName"
            InstalledCheckRegValueData = "Dependency Agent"
            DependsOn = "[xRemoteFile]DAPackage"
        }
    }
}
```

## <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする

ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかでこれを行うことができます。
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します

## <a name="deploy-azure-monitor-for-vms"></a>Azure Monitor for VMs をデプロイする

この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。

テンプレートを使用してリソースをデプロイする方法がわからない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI を使用するには、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードするには、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

### <a name="create-and-execute-a-template"></a>テンプレートを作成して実行する

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. このファイルを *installsolutionsforvminsights.json* としてローカル フォルダーに保存します。

1. *WorkspaceName*、*ResourceGroupName*、および *WorkspaceLocation* の値を取り込みます。 *WorkspaceName* の値は Log Analytics ワークスペースの名前です。 *WorkspaceLocation* の値は、ワークスペースが定義されているリージョンです。

1. これで、次の PowerShell コマンドを使用して、このテンプレートをデプロイする準備ができました。

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定の変更が完了するまで数分かかる場合があります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    provisioningState       : Succeeded
    ```
   監視を有効にしてから、ハイブリッド コンピューターの正常性の状態とメトリックが表示されるまで、約 10 分かかる場合があります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="vm-doesnt-appear-on-the-map"></a>VM がマップに表示されない

Dependency Agent のインストールに成功しても、マップにご利用のコンピューターが表示されない場合は、次の手順に従って問題を診断してください。

1. Dependency Agent は正しくインストールされているのでしょうか? これについては、サービスがインストールされているか、実行中かを確認することで検証できます。

    **Windows**:"Microsoft Dependency Agent" というサービスを探します。 

    **Linux**:"microsoft-dependency-agent" という実行中のプロセスを探します。

2. 現在、利用されているのは[無料の価格レベルの Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions) ですか? 無料プランでは、一意のコンピューターを最大 5 台まで使用できます。 前の 5 台からデータが送信されなくなった場合でも、以降のコンピューターはマップに表示されません。

3. コンピューターからログとパフォーマンス データが Azure Monitor ログに送信されていますか? コンピューターに対して次のクエリを実行します。 

    ```Kusto
    Usage | where Computer == "computer-name" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

    1 つ以上の結果が返されましたか? そのデータは最近のものですか? そうである場合は、Log Analytics エージェントは正しく動作しており、サービスと通信しています。 そうでない場合は、ご利用のサーバー上でエージェントを確認してください ([Windows 用 Log Analytics エージェントのトラブルシューティング](../platform/agent-windows-troubleshoot.md) または [Linux 用 Log Analytics エージェントのトラブルシューティング](../platform/agent-linux-troubleshoot.md)に関するページを参照)。

#### <a name="computer-appears-on-the-map-but-has-no-processes"></a>マップにコンピューターは表示されるがプロセスが表示されない

マップにサーバーは表示されるがプロセスや接続データが表示されない場合は、Dependency Agent がインストールされて実行されているがカーネル ドライバーがロードされなかったことを意味しています。 

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log ファイル (Windows) または /var/opt/microsoft/dependency-agent/log/service.log ファイル (Linux) を確認します。 ファイルの最後の行に、カーネルがロードされなかった原因が示されています。 たとえば、カーネルを更新した場合にそのカーネルが Linux でサポートされないことがあります。


## <a name="next-steps"></a>次の手順

これで、仮想マシンに対する監視が有効になったので、この情報を Azure Monitor for VMs での分析に使用できます。
 
- 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。

- VM のパフォーマンスでのボトルネックや全体的な使用率を識別するには、[Azure VM のパフォーマンスの表示](vminsights-performance.md)に関する記事をご覧ください。
