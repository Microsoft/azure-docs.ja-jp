---
title: Azure Automation State Configuration による管理のためのマシンのオンボード
description: Azure Automation State Configuration による管理のためのマシンの設定方法
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: 89e86a6702be7314b99975cac90818252eb07df7
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046230"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Azure Automation State Configuration による管理のためのマシンのオンボード

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Azure Automation State Configuration でマシンを管理する理由

Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。
これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。
マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。
Azure Automation State Configuration サービスは、Azure Automation Runbook が PowerShell スクリプトに対応する DSC に対応します。
つまり、Azure Automation を使用して、PowerShell スクリプトを管理するのと同様に DSC 構成も管理できます。
Azure Automation State Configuration を使用した場合の利点については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。

以下のさまざまなマシンを管理する際に、Azure Automation State Configuration を使用できます。

- Azure の仮想マシン
- Azure Virtual Machines (クラシック)
- オンプレミスの物理または仮想 Windows マシン、または Azure 以外のクラウド (AWS EC2 インスタンスなど)
- オンプレミス、Azure、または Azure 以外のクラウド内の物理/仮想 Linux マシン

また、クラウドからコンピューター構成を管理する準備ができていない場合は、Azure Automation State Configuration をレポートのみのエンドポイントとして使用することもできます。
これにより、DSC を介して構成を設定 (プッシュ) し、Azure Automation でレポートの詳細を表示することができます。

> [!NOTE]
> インストールされている仮想マシン DSC 拡張機能が 2.70 より新しい場合は、追加料金なしで State Configuration を利用して Azure VM を管理できるようになっています。 詳細については、[**Automation 価格に関するページ**](https://azure.microsoft.com/pricing/details/automation/)を参照してください。

次のセクションでは、各種類のマシンを Azure Automation State Configuration にオンボードできる方法の概要を示します。

> [!NOTE]
>DSC を Linux ノードにデプロイするとき、`/tmp` フォルダーが使用されます。**nxAutomation** のようなモジュールが検証のために一時的にダウンロードされ、その後、適切な場所にインストールされます。 モジュールが確実に正しくインストールされるようにするためには、Linux 用 Log Analytics エージェントで `/tmp` フォルダーに読み取りおよび書き込みアクセス許可が必要になります。 Linux 用 Log Analytics エージェントは `omsagent` ユーザーとして実行されます。 
>
>`omsagent` ユーザーに書き込みアクセス許可を付与するには、コマンド `setfacl -m u:omsagent:rwx /tmp` を実行します。
>

## <a name="azure-virtual-machines"></a>Azure の仮想マシン

Azure Automation State Configuration では、Azure Portal、Azure Resource Manager テンプレート、または PowerShell を使用して、構成管理用に Azure Virtual Machines (クラシック) を簡単にオンボードすることができます。 Azure VM Desired State Configuration 拡張機能を使用することで、管理者が VM にリモート接続しなくても、内部で VM を Azure Automation State Configuration に登録できます。
Azure VM Desired State Configuration 拡張機能は非同期に実行されるため、その進行状況の追跡またはトラブルシューティングの手順については、後述の「[**Azure 仮想マシンのオンボードに関するトラブルシューティング**](#troubleshooting-azure-virtual-machine-onboarding)」を参照してください。

### <a name="azure-portal"></a>Azure portal

[Azure ポータル](https://portal.azure.com/)で、仮想マシンをオンボードする Azure Automation アカウントに移動します。 [状態の構成] ページの **[ノード]** タブで **[+ 追加]** をクリックします。

オンボードする Azure 仮想マシンを選択します。

マシンに PowerShell Desired State 拡張機能がインストールされておらず、[電源の状態] が [実行中] の場合は、 **[接続]** をクリックします。

**[登録]** で、ユース ケースに必要な [PowerShell DSC Local Configuration Manager の値](/powershell/scripting/dsc/managing-nodes/metaConfig)、および必要に応じて VM に割り当てるノード構成を入力します。

![オンボード](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート

Azure Virtual Machines は、Azure Resource Manager テンプレートを使用して Azure Automation State Configuration にデプロイおよびオンボードできます。 Azure Automation State Configuration に既存の VM をオンボードする例のテンプレートについては、「[Server managed by Desired State Configuration service (Desired State Configuration サービスによって管理されるサーバー)](https://azure.microsoft.com/resources/templates/101-automation-configuration/)」を参照してください。
仮想マシン スケール セットを管理する場合は、テンプレートの例について、「[Azure Automation によって管理される仮想マシンスケール セットの構成](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/)」を参照してください。

### <a name="powershell"></a>PowerShell

PowerShell を使用して Azure に仮想マシンをオンボードするには、[Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) コマンドレットを使用します。
ただし現在、このコマンドレットの実装で想定されているのは、Windows を実行するマシンだけです (このコマンドレットでトリガーされるのは Windows の拡張機能だけです)。

### <a name="registering-virtual-machines-across-azure-subscriptions"></a>Azure サブスクリプションにまたがる仮想マシンの登録

他の Azure サブスクリプションから仮想マシンを登録する最良の方法は、Azure Resource Manager デプロイ テンプレートで DSC 拡張機能を使用することです。
例については、「[Azure Resource Manager テンプレートを使用した Desired State Configuration 拡張機能](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template)」を参照してください。
このテンプレートのパラメーターとして使用する登録キーと登録 URL を見つける場合は、後述の「[**セキュリティで保護された登録**](#secure-registration)」を参照してください。

## <a name="physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>オンプレミスの物理または仮想 Windows マシン、または Azure 以外のクラウド (AWS EC2 インスタンスなど)

オンプレミスまたは他のクラウド環境で実行している Windows サーバーも、[Azure にアウトバウンド アクセス](automation-dsc-overview.md#network-planning)できる限り、Azure Automation State Configuration にオンボードできます。

1. Azure Automation State Configuration にオンボードするマシンに最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされていることを確認します。
1. 以下の「[**DSC メタ構成の生成**](#generating-dsc-metaconfigurations)」のセクションの指示に従って、必要な DSC メタ構成が含まれるフォルダーを生成します。
1. 以下を使用して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。 **このコマンドを実行するマシンには最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされている必要があります。**

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. PowerShell DSC のメタ構成をリモートで適用できない場合は、手順 2. のメタ構成フォルダーを、オンボードする各マシンにコピーします。 次に、オンボードする各マシンで **Set-DscLocalConfigurationManager** をローカルで呼び出します。
1. Azure Portal またはコマンドレットを使用し、オンボードするマシンが Azure Automation アカウントに登録されている State Configuration ノードとして示されていることを確認します。

## <a name="physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>オンプレミス、または Azure 以外のクラウド内の物理/仮想 Linux マシン

オンプレミスまたは他のクラウド環境で実行している Linux サーバーも、[Azure にアウトバウンド アクセス](automation-dsc-overview.md#network-planning)できる限り、Azure Automation State Configuration にオンボードできます。

1. Azure Automation State Configuration にオンボードするマシンに最新バージョンの [PowerShell Desired State Configuration for Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) がインストールされていることを確認します。
2. [PowerShell DSC Local Configuration Manager の既定値](/powershell/scripting/dsc/managing-nodes/metaConfig4) がユース ケースに適しており、 **両方とも** Azure Automation State Configuration とデータをやり取りするマシンをオンボードするには、次のようにします。

   - Azure Automation State Configuration にオンボードする各 Linux マシンで、`Register.py` と PowerShell DSC Local Configuration Manager の既定値を使用してオンボードします。

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Automation アカウントの登録キーと登録 URL を見つける場合は、後述の「[**セキュリティで保護された登録**](#secure-registration)」を参照してください。

     PowerShell DSC Local Configuration Manager の既定値 がユース ケースに適して**いない**場合、または Azure Automation State Configuration のみにレポートするようにマシンをオンボードする必要がある場合は、手順 3 から手順 6 に従ってください。 それ以外の場合は、手順 6 に直接進みます。

3. 以下の「[**DSC メタ構成の生成**](#generating-dsc-metaconfigurations)」セクションの指示に従って、必要な DSC メタ構成が含まれるフォルダーを生成します。
4. 以下を使用して、オンボードするマシンに PowerShell DSC メタ構成をリモートで適用します。

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

このコマンドを実行するマシンには最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされている必要があります。

1. PowerShell DSC メタ構成をリモートで適用できない場合は、手順 5. のフォルダーからそのマシンに対応するメタ構成を Linux マシンにコピーします。 次に、以下を使用して、Azure Automation State Configuration にオンボードする各 Linux マシンで `SetDscLocalConfigurationManager.py` をローカルで呼び出します。

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

2. Azure ポータルまたはコマンドレットを使用して、オンボードするマシンがこの時点で Azure Automation アカウントに登録されている DSC ノードとして示されていることを確認します。

## <a name="generating-dsc-metaconfigurations"></a>DSC メタ構成の生成

マシンを包括的に Azure Automation State Configuration にオンボードするために、Azure Automation State Configuration とデータをやり取りするように DSC エージェントに指示する [DSC メタ構成](/powershell/scripting/dsc/managing-nodes/metaConfig)を生成できます。 Azure Automation State Configuration の DSC メタ構成は、PowerShell DSC 構成または Azure Automation PowerShell コマンドレットのいずれかを使用して生成できます。

> [!NOTE]
> DSC メタ構成には Automation アカウントにマシンをオンボードするために必要な管理用の機密データが含まれています。 作成した DSC メタ構成は適切に保護し、使用後は削除してください。

### <a name="using-a-dsc-configuration"></a>DSC 構成の使用

1. ローカル環境のマシンで、管理者として VSCode (または、使い慣れたエディター) を開きます。 マシンには最新バージョンの [WMF 5](https://aka.ms/wmf5latest) がインストールされている必要があります。
1. 次のスクリプトをローカルにコピーします。 このスクリプトには、メタ構成を作成するための PowerShell DSC 構成と、メタ構成の作成を開始するためのコマンドが含まれています。

> [!NOTE]
> ポータルでは、State Configuration ノード構成名の大文字と小文字が区別されます。 大文字と小文字が一致しない場合、ノードは **[ノード]** タブ下に表示されません。

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. お使いの Automation アカウントの登録キーと URL のほか、オンボードするマシンの名前を入力します。 その他のパラメーターはすべて省略可能です。 Automation アカウントの登録キーと登録 URL を見つける場合は、後述の「[**セキュリティで保護された登録**](#secure-registration)」を参照してください。
1. マシンが Azure Automation State Configuration に DSC のステータス情報を送信する一方で、構成や PowerShell モジュールを取得しないようにするには、 **ReportOnly** パラメーターを true に設定します。
1. スクリプトを実行します。 作業ディレクトリに、(管理者として) マシンをオンボードするための PowerShell DSC メタ構成が含まれる **DscMetaConfigs** という名前のフォルダーが作成されます。

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="using-the-azure-automation-cmdlets"></a>Azure Automation コマンドレットの使用

PowerShell DSC Local Configuration Manager の既定値がユース ケースに適しており、Azure Automation State Configuration とデータをやり取りするマシンをオンボードする場合、Azure Automation コマンドレットが必要な DSC メタ構成を生成するための簡略化された手法を提供します。

1. ローカル環境のマシンで、管理者として PowerShell コンソールまたは VSCode を開きます。
2. `Connect-AzAccount` を使用して Azure Resource Manager に接続する
3. ノードをオンボードする Automation アカウントから、オンボードするマシンの PowerShell DSC メタ構成をダウンロードします。

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation Account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation Account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the meta configuration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. (管理者として) マシンをオンボードするための PowerShell DSC メタ構成が含まれる ***DscMetaConfigs*** という名前のフォルダーが作成されます。

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="secure-registration"></a>セキュリティで保護された登録

マシンは WMF 5 DSC 登録プロトコルを介して Azure Automation アカウントに安全にオンボードすることができます。これにより、DSC ノードを PowerShell DSC のプルまたはレポート サーバー (Azure Automation State Configuration を含む) に対して認証することができます。 ノードは**登録 URL** でサーバーに登録し、**登録キー**を使用して認証します。 登録時に、DSC ノードと DSC プル/レポート サーバーは、登録後のサーバーに対する認証に使用するこのノードの一意の証明書をネゴシエートします。 このプロセスは、ノードが侵害され、悪意のある動作を行っている場合などに、オンボードされたノードが別のノードの代わりに動作しないようにするためのものです。 登録後、登録キーは再度認証に使用されず、ノードから削除されます。

State Configuration 登録プロトコルに必要な情報は、Azure Portal の **[アカウント設定]** の **[キー]** から取得できます。 Automation アカウントの **[要点]** パネルにあるキー アイコンをクリックして、このブレードを開きます。

![Azure の自動化キーと URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- 登録 URL は、[キーの管理] ブレードの [URL] フィールドです。
- 登録キーは、[キーの管理] ブレードのプライマリ アクセス キーまたはセカンダリ アクセス キーです。 いずれかのキーを使用できます。

セキュリティ強化のため、Automation アカウントのプライマリおよびセカンダリ アクセス キーを ( **[キーの管理]** ページで) いつでも再生成して、以前のキーを使用して今後ノードが登録されないようにすることができます。

## <a name="certificate-expiration-and-re-registration"></a>証明書の有効期限と再登録

Azure Automation State Configuration に DSC ノードとしてマシンを登録した後も、さまざまな理由で、そのノードを再登録する必要があります。

- Windows Server 2019 より前のバージョンのWindows Server では、1 年後に有効期限が切れる認証用の一意の証明書を各ノードが自動的にネゴシエートします。 現時点では、PowerShell DSC 登録プロトコルは、有効期限が近づいたときに証明書を自動的に更新することはできないため、1 年後にノードを再登録する必要があります。 再登録する前に、各ノードで Windows Management Framework 5.0 RTM が実行されていることを確認します。 ノードの認証証明書の有効期限が切れるときにノードが再登録されない場合、ノードは Azure Automation と通信できなくなり、[反応なし] とマークされます。 証明書の有効期限が切れる 90 日以内または証明書の有効期限が切れた後で再登録を実行すると、新しい証明書が生成されて使用されます。  Windows Server 2019 以降には、この問題への解決策が含まれています。
- ConfigurationMode など、ノードの最初の登録時に設定した [PowerShell DSC Configuration Manager 値](/powershell/scripting/dsc/managing-nodes/metaConfig4)を変更する場合。 現在、DSC エージェント値を変更するには、再登録を使用する必要があります。 1 つの例外は、ノードに割り当てられたノード構成です。この場合、Azure Automation DSC で直接変更できます。

再登録は、このドキュメントで説明しているオンボード方法のいずれかを使用して、ノードを最初に登録したときと同じ方法で実行できます。 ノードを再登録する前に、Azure Automation State Configuration のノードの登録を解除する必要はありません。

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Azure 仮想マシンのオンボードに関するトラブルシューティング

Azure Automation State Configuration を使用すると、構成管理のための Azure Windows VM を簡単にオンボードできます。 内部で、Azure VM Desired State Configuration 拡張機能を使用して、VM を Azure Automation State Configuration に登録します。 Azure VM Desired State Configuration 拡張機能は非同期に実行されるため、その進行状況の追跡とその実行に関するトラブルシューティングが重要な場合があります。

> [!NOTE]
> Azure VM Desired State Configuration 拡張機能を使用する Azure Automation State Configuration への Azure Windows VM のオンボード方法では、ノードが Azure Automation に登録済みと表示されるまでに最大で 1 時間かかる場合があります。 これは、VM を Azure Automation State Configuration にオンボードするために必要な Windows Management Framework 5.0 を、Azure VM DSC 拡張機能が VM にインストールするためです。

トラブルシューティングを行う場合や、Azure VM Desired State Configuration 拡張機能の状態を表示する場合は、Azure Portal でオンボードされている VM に移動し、 **[設定]** の **[拡張機能]** をクリックします。 次に、お使いのオペレーティング システムに応じて **[DSC]** または **[DSCForLinux]** をクリックします。 詳細は、 **[詳細な状態の表示]** をクリックして表示できます。

トラブルシューティングの詳細については、「[Azure Automation Desired State Configuration (DSC) の問題をトラブルシューティングする](./troubleshoot/desired-state-configuration.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/az.automation#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
