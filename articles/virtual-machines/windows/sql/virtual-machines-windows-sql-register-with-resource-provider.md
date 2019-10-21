---
title: Azure 内の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する | Microsoft Docs
description: SQL Server VM を SQL VM リソースプロバイダーに登録して管理性を向上させます。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b0a7221107f05ff2239bd77cc18e7ffedc18efc1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72023600"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Azure 内の SQL Server 仮想マシンを SQL VM リソースプロバイダーに登録する

この記事では、Azure 内の SQL Server 仮想マシン (VM) を SQL VM リソースプロバイダーに登録する方法について説明します。 

Azure portal を介して SQL Server VM の Azure Marketplace イメージをデプロイすると、その SQL Server VM が自動的にリソースプロバイダーに登録されます。 Azure Marketplace からイメージを選択するのではなく Azure 仮想マシンに SQL Server を自分でインストールすることを選択した場合や、SQL Server を含むカスタム VHD から Azure VM をプロビジョニングする場合は、以下の目的で、お使いの SQL Server VM をリソースプロバイダーに登録する必要があります。

- **ライセンスの管理の簡略化**Microsoft 製品の利用規約に従って、お客様は [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を利用している場合に Microsoft にレポートする必要があります。 SQL VM リソース プロバイダーに登録すると、SQL Server ライセンスの管理が簡略化されるほか、[ポータル](virtual-machines-windows-sql-manage-portal.md)または Az CLI の Azure ハイブリッド特典を使用して SQL Server VM をすばやく識別することができます。 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **機能面の利点**: お使いの SQL Server VM をリソースプロバイダーに登録すると、[自動修正](virtual-machines-windows-sql-automated-patching.md)、[自動バックアップ](virtual-machines-windows-sql-automated-backup-v2.md)、監視と管理機能が利用できるようになります。 また、[ライセンス](virtual-machines-windows-sql-ahb.md)や[エディション](virtual-machines-windows-sql-change-edition.md)の柔軟性も実現されます。 これらの機能は、以前は Azure Marketplace の SQL Server VM イメージでしか利用できませんでした。

- **無料の管理**: SQL VM リソース プロバイダーに登録すると、すべての管理モードが完全に無料になります。 リソース プロバイダーまたは管理モードの変更に関連する追加のコストは発生しません。 

SQL VM リソースプロバイダーを利用するには、SQL VM リソースプロバイダーをサブスクリプションに登録することも必要です。 これは Azure portal、Azure CLI、または PowerShell を使用して実行できます。 

  > [!NOTE]
  > リソース プロバイダーへの登録に関連する追加のライセンス要件はありません。 SQL VM リソース プロバイダーに登録すると、各リソースのライセンス登録フォームを管理する代わりに Azure ハイブリッド特典が有効にされたことを Microsoft に通知する要件を満たすための簡単な方法が提供されます。 

SQL VM リソース プロバイダーを使用する利点の詳細については、次の [Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) のビデオをご覧ください。 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Azure で SQL Server を自己インストールするときの SQL VM リソース プロバイダーの利点 - Microsoft Channel 9 のビデオ"></iframe>


## <a name="prerequisites"></a>前提条件

SQL Server VM をリソースプロバイダーに登録するには、以下のものが必要になります。 

- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 
- [Azure CLI](/cli/azure/install-azure-cli) または [PowerShell](/powershell/azure/new-azureps-module-az) の最新バージョン。 


## <a name="register-with-sql-vm-resource-provider"></a>SQL VM リソースプロバイダーに登録する
[SQL Server IaaS Agent 拡張機能](virtual-machines-windows-sql-server-agent-extension.md)が VM にインストールされていない場合は、軽量 SQL 管理モードを指定することによって SQL VM リソースプロバイダーに登録できます。 

登録プロセスで軽量が指定された場合、SQL VM リソースプロバイダーによって SQL IaaS 拡張機能が[軽量モード](#change-management-modes)で自動インストールされ、SQL Server インスタンス メタデータが検証されます。その際、SQL Server サービスの再起動は行われません。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。

軽量モードで SQL VM リソースプロバイダーに登録することにより、コンプライアンスが確保され、柔軟なライセンス管理と SQL Server エディションのインプレース更新が可能になります。 フェールオーバー クラスター インスタンスとマルチインスタンス デプロイは、軽量モードでのみ SQL VM リソースプロバイダーに登録できます。 完全管理モードにいつでも[アップグレード](#change-management-modes)できますが、その場合は SQL Server サービスが再起動されます。 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
SQL Server IaaS 拡張機能が既に VM にインストールされている場合は、次のコード スニペットを使用して SQL VM リソースプロバイダーに登録します。 SQL VM リソースプロバイダーへの登録時に必要な SQL Server ライセンスの種類として、従量課金制 (`PAYG`) または Azure ハイブリッド特典 (`AHUB`) を指定する必要があります。 

次の PowerShell コード スニペットを使用して SQL Server VM を登録します。

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

有料エディション (Enterprise または Standard) の場合:

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

無料エディション (Developer、Web、Express) の場合:

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

SQL IaaS 拡張機能が手動で既に VM にインストールされている場合、Microsoft.SqlVirtualMachine/SqlVirtualMachines という種類のメタデータ リソースを作成することによって、SQL VM リソースプロバイダーに完全モードで登録できます。 以下は、SQL IaaS 拡張機能が既に VM にインストールされている場合に SQL VM リソースプロバイダーへの登録を行うコード スニペットです。 必要な SQL Server ライセンスの種類として "PAYG" または "AHUB" を指定する必要があります。 完全管理モードで登録するには、次の PowerShell コマンドを使用します。

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Windows Server 2008 VM 上の SQL Server 2008 または 2008 R2 を登録する

Windows Server 2008 にインストールされている SQL Server 2008 および 2008 R2 は、[エージェントなし](#change-management-modes)モードで SQL VM リソースプロバイダーに登録できます。 このオプションにより、コンプライアンスが確保され、SQL Server VM を Azure portal の限られた機能で監視できるようになります。

次の表では、登録時に指定するパラメーターに使用可能な値を詳細に示しています。

| パラメーター | 使用可能な値                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` または `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` または `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Windows Server 2008 インスタンス上の SQL Server 2008 または 2008 R2 インスタンスを登録するには、次の Powershell または Az CLI のコード スニペットを使用します。  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>登録状態を確認する
お使いの SQL Server VM が既に SQL VM リソースプロバイダーに登録されているかどうかは、Azure portal、Azure CLI、または PowerShell を使用して確認できます。 

### <a name="azure-portal"></a>Azure ポータル 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. お使いの [SQL Server 仮想マシン](virtual-machines-windows-sql-manage-portal.md)に移動します。
1. 一覧から SQL Server VM を選択します。 お使いの SQL Server VM がここに表示されていない場合は、SQL VM リソースプロバイダーに登録されていない可能性があります。 
1. **[状態]** の値を確認します。 **[状態]** が **[成功]** の場合は、その SQL Server VM が正常に SQL VM リソースプロバイダーに登録されています。 

![SQL RP 登録による状態を確認する](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>コマンド ライン

Az CLI または PowerShell を使用して現在の SQL Server VM の登録状態を確認します。 登録が成功していれば、`ProvisioningState` に `Succeeded` と表示されます。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

エラーは、SQL Server VM がリソースプロバイダーに登録されていないことを示します。 

## <a name="change-management-modes"></a>管理モードを変更する

SQL Server IaaS 拡張機能には 3 つの無料管理モードがあります。 

- **フル** モードでは、すべての機能が提供されますが、SQL Server の再起動とシステム管理者権限が必要です。 これは、既定でインストールされるオプションです。 単一のインスタンスがある SQL Server VM を管理する場合に使用します。 フル モードでは、メモリと CPU に与える影響が最小の 2 つの Windows サービスがインストールされます。これらは、タスク マネージャーを通して監視できます。 完全管理モードの使用に関連するコストはありません。 

- **軽量**では SQL Server の再起動は必要ありませんが、サポートされるのはライセンスの種類と SQL Server のエディションの変更のみです。 このオプションは、複数のインスタンスを備えているかフェールオーバー クラスター インスタンス (FCI) に参加している SQL Server VM で使用します。 軽量モードを使用する場合、メモリまたは CPU への影響はありません。 軽量管理モードの使用に関連するコストはありません。 

- **NoAgent** は Windows Server 2008 にインストールされた SQL Server 2008 および SQL Server 2008 R2 の専用モードです。 NoAgent モードを使用する場合、メモリまたは CPU への影響はありません。 NoAgent 管理者モードの使用に関連するコストはありません。 

PowerShell を使用して、次のように SQL Server IaaS エージェントの現在のモードを確認できます。 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

"*軽量*" の IaaS 拡張機能がインストールされている SQL Server VM の場合、Azure portal を使用してモードを "_フル_" にアップグレードできます。 _No-Agent_ モードの SQL Server VM は、OS が Windows 2008 R2 以降にアップグレードされ後で "_フル_" にアップグレードできます。 ダウングレードはできません。これを行うには、SQL IaaS 拡張機能を完全にアンインストールし、再度インストールする必要があります。 

エージェントのモードを "フル" にアップグレードするには: 


### <a name="azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [SQL 仮想マシン](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) リソースに移動します。 
1. 対象の SQL Server 仮想マシンを選択し、 **[概要]** を選択します。 
1. IaaS モードが NoAgent または軽量である SQL Server VM では、**SQL IaaS 拡張機能で利用できるのはライセンスの種類とエディションの更新のみ**という内容のメッセージを選択します。

   ![ポータルでモードを変更するための選択](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. **[仮想マシンで SQL Server サービスを再起動することに同意します]** チェック ボックスをオンにし、 **[確認]** を選択して IaaS モードを "フル" にアップグレードします。 

    ![仮想マシンで SQL Server サービスを再起動することに同意するためのチェック ボックス](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>コマンド ライン

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

次の Az CLI コード スニペットを実行します。

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

次の PowerShell コード スニペットを実行します。

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>SQL VM リソースプロバイダーをサブスクリプションに登録する 

お使いの SQL Server VM を SQL VM リソースプロバイダーに登録するには、そのリソースプロバイダーを自分のサブスクリプションに登録する必要があります。 これは Azure portal、Azure CLI、または PowerShell を使用して実行できます。

### <a name="azure-portal"></a>Azure ポータル

1. Azure portal を開き、 **[すべてのサービス]** に移動します。 
1. **[サブスクリプション]** に移動し、目的のサブスクリプションを選択します。  
1. **[サブスクリプション]** ページで、 **[リソースプロバイダー]** に移動します。 
1. フィルター内に「**sql**」と入力し、SQL 関連のリソースプロバイダーを表示します。 
1. 必要なアクションに応じて、**Microsoft.SqlVirtualMachine** プロバイダーで **[登録]** 、 **[再登録]** 、または **[登録解除]** を選択します。 

![プロバイダーの変更](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>コマンド ライン

Az CLI または PowerShell を使用して、SQL VM リソースプロバイダーを自分の Azure サブスクリプションに登録します。 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
次のコード スニペットは、SQL VM リソース プロバイダーを Azure サブスクリプションに登録します。 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>解説

- SQL VM リソースプロバイダーでサポートされているのは、Azure Resource Manager を介してデプロイされた SQL Server VM のみです。 クラシック モデルを介してデプロイされた SQL Server VM はサポートされません。 
- SQL VM リソースプロバイダーでサポートされているのは、パブリック クラウドにデプロイされた SQL Server VM のみです。 プライベート クラウドや政府機関向けクラウドにデプロイされている場合はサポートされません。 
 

## <a name="frequently-asked-questions"></a>よく寄せられる質問 

**Azure Marketplace の SQL Server イメージからプロビジョニングされた SQL Server VM を登録する必要はありますか?**

No. Azure Marketplace の SQL Server イメージからプロビジョニングされた VM は自動的に登録されます。 SQL VM リソースプロバイダーへの登録が必要になるのは、VM が Azure Marketplace の SQL Server イメージからプロビジョニングされて "*おらず*"、SQL Server を自分でインストールした場合のみです。

**SQL VM リソース プロバイダーは、すべての顧客が使用できますか。** 

はい。 お客様がご自身の SQL Server VM を SQL VM リソースプロバイダーにインストールする必要があるのは、Azure Marketplace の SQL Server イメージを使用せずに自分で SQL Server をインストールした場合やカスタム VHD を購入した場合です。 すべての種類のサブスクリプション (Direct、Enterprise Agreement、Cloud Solution Provider) で所有されているVM は、SQL VM リソースプロバイダーに登録できます。

**SQL Server VM に SQL Server IaaS 拡張機能が既にインストールされている場合、SQL VM リソースプロバイダーに登録する必要はありますか?**

SQL Server VM が自分でインストールしたもので、Azure Marketplace の SQL Server イメージからプロビジョニングしたものではない場合は、SQL Server IaaS 拡張機能をインストール済みであっても SQL VM リソースプロバイダーに登録する必要があります。 SQL VM リソースプロバイダーに登録すると、Microsoft.SqlVirtualMachines という種類の新しいリソースが作成されます。 SQL Server IaaS 拡張機能をインストールしても、そのリソースは作成されません。

**SQL VM リソースプロバイダーへの登録時の既定の管理モードは何ですか?**

SQL VM リソースプロバイダーに登録する際の既定の管理モードは "*完全*" です。 SQL VM リソースプロバイダーへの登録時に SQL Server 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 SQL Server IaaS 拡張機能が VM にインストールされていることが、完全管理モードで SQL VM リソースプロバイダーに登録するための前提条件になります。

**SQL VM リソースプロバイダーに登録するための前提条件は何ですか?**

軽量モードまたはエージェントなしモードで SQL VM リソース プロバイダーに登録するための前提条件はありません。 完全モードで SQL VM リソースプロバイダーに登録するための前提条件は、SQL Server IaaS 拡張機能が VM にインストールされていることです。

**SQL Server IaaS 拡張機能を VM にインストールしていない場合、SQL VM リソースプロバイダーに登録できますか?**

はい。SQL Server IaaS 拡張機能を VM にインストールしていない場合は、軽量管理モードで SQL VM リソースプロバイダーに登録できます。 軽量モードの場合、SQL VM リソースプロバイダーはコンソール アプリを使用して、その SQL Server インスタンスのバージョンとエディションを確認します。 

SQL VM リソースプロバイダーへの登録時の既定の SQL 管理モードは "_完全_" です。 SQL VM リソース プロバイダーに登録したときに SQL 管理プロパティが設定されていない場合、このモードは完全管理として設定されます。 SQL IaaS 拡張機能を VM にインストールすることが、完全管理モードで SQL VM リソース プロバイダーに登録するための前提条件です。

**SQL VM リソースプロバイダーに登録すると、VM にエージェントがインストールされますか?**

No. SQL VM リソースプロバイダーに登録した場合は、新しいメタデータ リソースのみが作成されます。 VM にエージェントがインストールされることはありません。

SQL Server IaaS 拡張機能は、完全管理を有効にする場合のみ必要です。 管理モードを軽量から完全にアップグレードすると、SQL Server IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL Server VM リソースプロバイダーに登録すると、VM 上の SQL Server は再起動されますか?**

No. SQL VM リソースプロバイダーに登録した場合は、新しいメタデータ リソースのみが作成されます。 VM 上の SQL Server が再起動されることはありません。 

SQL Server の再起動は、SQL Server IaaS 拡張機能をインストールする場合にのみ必要です。 また、SQL Server IaaS 拡張機能は、完全管理を有効にする場合に必要になります。 管理モードを軽量から完全にアップグレードすると、SQL Server IaaS 拡張機能がインストールされ、SQL Server が再起動されます。

**SQL VM リソースプロバイダーに登録するときの軽量モードとエージェントなし管理モードの違いは何ですか?** 

エージェントなし管理モードは、Windows Server 2008 上の SQL Server 2008 と SQL Server 2008 R2 のみに使用できます。 これは、これらのバージョンに唯一使用できる管理モードです。 その他すべてのバージョンの SQL Server に使用可能な 2 つの管理モードとして軽量モードと完全モードがあります。 

エージェントなしモードでは、SQL Server のバージョンとエディションのプロパティをお客様が設定する必要があります。 軽量モードでは、VM が照会され、SQL Server インスタンスのバージョンとエディションが確認されます。

**Azure CLI を使用することで、軽量モードまたはエージェントなしモードで SQL VM リソースプロバイダーに登録できますか?**

No. この管理モード プロパティを使用できるのは、Azure PowerShell を使用して SQL VM リソースプロバイダーに登録する場合のみです。 Azure CLI では、SQL Server 管理プロパティの設定はサポートされていません。 常に、既定の完全管理モードで SQL VM リソースプロバイダーに登録します。

**SQL Server のライセンスの種類を指定せずに SQL VM リソースプロバイダーに登録できますか?**

No. SQL VM リソースプロバイダーに登録している場合、SQL Server のライセンスの種類は省略可能なプロパティではありません。 Azure CLI と PowerShell の両方を使用してすべての管理モード (エージェントなし、軽量、完全) で SQL VM リソースマネージャーに登録するときに、SQL Server のライセンスの種類を従量課金制または Azure ハイブリッド特典として設定する必要があります。

**SQL Server IaaS 拡張機能をエージェントなしモードから完全モードにアップグレードできますか?**

No. エージェントなしモードでは、管理モードを完全または軽量にアップグレードすることはできません。 これは Windows Server 2008 の技術的な制限です。

**SQL Server IaaS 拡張機能を軽量モードから完全モードにアップグレードできますか?**

はい。 管理モードを軽量から完全にアップグレードすることは、PowerShell または Azure portal でサポートされています。 これには、SQL Server の再起動が必要です。

**SQL Server IaaS 拡張機能を完全モードからエージェントなしまたは軽量管理モードにダウングレードすることはできますか?**

No. SQL Server IaaS 拡張機能の管理モードのダウングレードはサポートされていません。 この管理モードは、完全モードから軽量またはエージェントなしモードにダウングレードできません。また、軽量モードからエージェントなしモードにダウングレードすることもできません。 

管理モードを完全管理から変更するには、SQL Server IaaS 拡張機能を削除してください。 その後、Microsoft.SqlVirtualMachine リソースをドロップし、SQL Server VM を SQL VM リソースプロバイダーに再登録します。

**Azure portal から SQL VM リソースプロバイダーに登録できますか?**

No. Azure portal では SQL VM リソースプロバイダーへの登録を利用できません。 完全管理モードでの SQL VM リソースプロバイダーへの登録は、Azure CLI または PowerShell でサポートされています。 軽量またはエージェントなし管理モードでの SQL VM リソースプロバイダーへの登録は、Azure PowerShell API のみでサポートされています。

**SQL Server をインストールする前に、VM を SQL VM リソースプロバイダーに登録できますか?**

No. SQL VM リソースプロバイダーへの登録を成功させるには、VM に少なくとも 1 つの SQL Server インスタンスが必要です。 VM 上に SQL Server インスタンスが存在しない場合、新しい Microsoft.SqlVirtualMachine リソースはエラー状態になります。

**複数の SQL Server インスタンスがある場合、VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 SQL VM リソースプロバイダーが登録する SQL Server インスタンスは 1 つだけです。 複数のインスタンスがある場合、SQL VM リソースプロバイダーは既定の SQL Server インスタンスを登録します。 既定のインスタンスがない場合、軽量モードでの登録のみがサポートされます。 軽量から完全管理モードにアップグレードするには、既定の SQL Server インスタンスが存在するか、VM に名前付き SQL Server インスタンスが 1 つだけ存在する必要があります。

**SQL Server フェールオーバー クラスター インスタンスを SQL VM リソースプロバイダーに登録できますか?**

はい。 Azure VM 上の SQL Server フェールオーバー クラスター インスタンスは SQL VM リソースプロバイダーに軽量モードで登録できます。 ただし、SQL Server フェールオーバー クラスター インスタンスを完全管理モードにアップグレードすることはできません。

**Always On 可用性グループが構成されている場合、自分の VM を SQL VM リソースプロバイダーに登録できますか?**

はい。 Always On 可用性グループ構成に参加している場合、Azure VM 上の SQL Server インスタンスを SQL VM リソースプロバイダーに登録することに制限はありません。

**SQL VM リソース プロバイダーに登録する場合、または完全管理モードにアップグレードする場合、どのようなコストがかかりますか?**
なし。 SQL VM リソース プロバイダーへの登録に関連する料金も、3 種類のいずれの管理モードの使用に関連する料金もかかりません。 ご利用の SQL Server VM をリソース プロバイダーを使用して管理する場合は、完全に無料です。 

**さまざまな管理モードを使用すると、パフォーマンスにどのような影響がありますか?**
*NoAgent* および*軽量*管理性モードを使用する場合、影響はありません。 OS にインストールされている 2 つのサービスから、*完全*管理モードを使用する場合の影響は最小限に抑えられます。 これらは、タスク マネージャーを通して監視できます。 

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [Windows VM における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上の SQL Server に関する FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上の SQL Server の価格ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上の SQL Server のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
