---
title: IaaS Agent 拡張機能を使用して管理タスクを自動化する
description: この記事では、SQL Server IaaS Agent 拡張機能を管理して、SQL Server の特定の管理タスクを自動化する方法について説明します。 これらには、自動バックアップ、自動修正、および Azure Key Vault の統合が含まれます。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 638a7fa96f4112f9805fd46618dc0acd1c2f382f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87063239"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent 拡張機能を使用して Azure 仮想マシン上で管理タスクを自動化する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


SQL Server IaaS Agent 拡張機能 (SQLIaaSExtension) は、管理タスクを自動化するために Azure 仮想マシン上で実行されます。 この記事では、拡張機能でサポートされているサービスの概要について説明します。 この記事では、拡張機能のインストール、状態、および削除の手順についても説明します。

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

この記事のクラシック バージョンを確認するには、[SQL Server VM 用 SQL Server IaaS Agent 拡張機能 (クラシック)](../../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md) に関する記事をご覧ください。


## <a name="supported-services"></a>サポートされているサービス
SQL Server IaaS Agent 拡張機能は、次の管理タスクをサポートします。

| 管理機能 | 説明 |
| --- | --- |
| **SQL Server の自動バックアップ** |VM 上の SQL Server の既定のインスタンスまたは[適切にインストールされた](frequently-asked-questions-faq.md#administration)名前付きインスタンスについて、すべてのデータベースのバックアップを自動的にスケジュールします。 詳細については、[Azure 仮想マシンでの SQL Server の自動バックアップ (Resource Manager)](automated-backup-sql-2014.md) に関する記事を参照してください。 |
| **SQL Server の自動修正** |VM で重要な更新プログラムを実行できるメンテナンス期間を構成します。これにより、ワークロードのピーク時の更新を回避できます。 詳細については、「[Azure Virtual Machines での SQL Server の自動修正 (Resource Manager)](automated-patching.md)」をご覧ください。 |
| **Azure Key Vault の統合** |SQL Server VM に Azure Key Vault を自動的にインストールして構成できます。 詳細については、「[Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)](azure-key-vault-integration-configure.md)」をご覧ください。 |

SQL Server Iaas Agent 拡張機能をインストールして実行すると、管理機能が使用できるようになります。

* Azure portal の仮想マシンの [SQL Server] パネル上と、Azure Marketplace の SQL Server イメージ用の Azure PowerShell を通して。
* 拡張機能を手動でインストールするための Azure PowerShell を通して。 

## <a name="prerequisites"></a>前提条件
VM で SQL Server IaaS Agent 拡張機能を使用するための要件を次に示します。

**[オペレーティング システム]** :

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server のバージョン**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [最新の Azure PowerShell コマンドをダウンロードして構成します](/powershell/azure/)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


##  <a name="installation"></a>インストール
SQL Server VM を [SQL Server VM リソース プロバイダー](sql-vm-resource-provider-register.md)に登録すると、SQL Server IaaS 拡張機能がインストールされます。 必要に応じて、以下の PowerShell コマンドを使用して、SQL Server IaaS エージェントを手動でインストールできます。 

  ```powershell-interactive
    Set-AzVMSqlServerExtension -VMName "sql2017" `
    -ResourceGroupName "LabsqlIAASagent" -Name "SQLIaasExtension" `
    -Version "2.0" -Location "Central US";  
  ```

> [!NOTE]
> 拡張機能をインストールすると、SQL Server サービスが再起動されます。 


### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>単一の名前付き SQL Server インスタンスを備えた VM にインストールする
SQL Server IaaS 拡張機能は、既定のインスタンスがアンインストールされて IaaS 拡張機能が再インストールされた場合に、SQL Server 上の名前付きインスタンスと連携します。

SQL Server の名前付きインスタンスを使用するには、次の手順を実行します。
   1. Azure Marketplace から SQL Server VM をデプロイします。 
   1. [Azure portal](https://portal.azure.com) から、IaaS 拡張機能をアンインストールします。
   1. SQL Server VM 内で SQL Server を完全にアンインストールします。
   1. SQL Server VM 内の名前付きインスタンスで SQL Server をインストールします。 
   1. Azure portal から、IaaS 拡張機能をインストールします。  


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>SQL Server IaaS 拡張機能の状態を取得する
拡張機能がインストールされていることを確認する 1 つの方法は、Azure ポータルにエージェントの状態を表示することです。 仮想マシンのウィンドウで **[すべての設定]** を選択し、 **[拡張機能]** を選択します。 **SqlIaasExtension** 拡張機能が一覧表示されます。

![Azure portal での SQL Server IaaS Agent 拡張機能の状態](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)

**Get-AzVMSqlServerExtension** Azure PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

前のコマンドでは、エージェントがインストールされていることが確認され、全般的な状態情報が提供されます。 次のコマンドを使用して、自動バックアップと自動修正に関する特定の状態情報を取得できます。

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>削除
Azure portal で、仮想マシンのプロパティの **[拡張機能]** ウィンドウにある省略記号を選択することで、拡張機能をアンインストールできます。 次に、 **[削除]** を選択します。

![Azure portal での SQL Server IaaS Agent 拡張機能のアンインストール](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-uninstall.png)

**Remove-AzVMSqlServerExtension** PowerShell コマンドレットを使用することもできます。

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>次のステップ
拡張機能によってサポートされるいずれかのサービスの使用を開始します。 詳細については、この記事の「[サポートされているサービス](#supported-services)」セクションで参照されているトピックをご覧ください。

Azure Virtual Machines で SQL Server を実行する方法の詳細については、「[Azure Virtual Machines における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。
