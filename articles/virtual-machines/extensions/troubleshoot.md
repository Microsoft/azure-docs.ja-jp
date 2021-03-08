---
title: Windows VM 拡張機能のエラーのトラブルシューティング
description: Azure Windows VM 拡張機能のエラーのトラブルシューティングについて説明します
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 343ddb109de41a0959533b16b11762841b5b1105
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676761"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 拡張機能のエラーのトラブルシューティング
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>拡張機能の状態表示
Azure リソース マネージャー テンプレートは、Azure Powershell から実行できます。 このテンプレートを実行すると、Azure Resource Explorer またはコマンド ライン ツールから拡張機能の状態を表示できます。

たとえば次のようになります。

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

出力例を次に示します。

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>拡張機能のエラーのトラブルシューティング

### <a name="rerun-the-extension-on-the-vm"></a>VM での拡張機能の再実行
VM でカスタム スクリプト拡張機能を使用してスクリプトを実行している場合、VM は正常に作成されたがスクリプトは失敗するというエラーが発生することがあります。 このような状況でこのエラーから回復するためにお勧めする方法は、拡張機能を削除してもう一度テンプレートを実行することです。
注:将来は、この機能が強化され、拡張機能をアンインストールする必要はなくなります。

#### <a name="remove-the-extension-from-azure-powershell"></a>Azure Powershell から拡張機能を削除する
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

拡張機能を削除した後、テンプレートを再実行して、VM 上でスクリプトを実行できます。

### <a name="trigger-a-new-goalstate-to-the-vm"></a>新しい GoalState を VM にトリガーする
拡張機能が実行されていないか、"Windows Azure CRP Certificate Generator" がないために実行できないことに気づくことがあります (この証明書は、拡張機能の保護された設定の転送をセキュリティで保護するために使用されます)。
その証明書は、仮想マシン内から Windows ゲスト エージェントを再起動することで自動的に再生成されます。
- タスク マネージャーを開く
- [詳細] タブに移動する
- WindowsAzureGuestAgent.exe プロセスを見つける
- 右クリックして [タスクの終了] を選択する。 プロセスは自動的に再起動されます


"VM の再適用" を実行して、VM に新しい GoalState をトリガーすることもできます。 VM の[再適用](/rest/api/compute/virtualmachines/reapply)は、2020 年に導入された API であり、VM の状態を再適用します。 VM の短時間のダウンタイムを許容できるタイミングでこれを行うことをお勧めします。 再適用自体では VM は再起動されず、また再適用を呼び出しても大抵の場合は VM が再起動されることはありませんが、再適用によって新しい目標状態がトリガーされたときに VM モデルに対する他の一部の保留中の更新が適用され、他の変更で再起動が必要になるという非常に小さなリスクがあります。 

Azure portal:

ポータルで VM を選択し、左ペインの **[サポート + トラブルシューティング]** で、 **[Redeploy + reapply]\(再デプロイ + 再適用\)** を選択し、続いて **[再適用]** を選択します。


Azure PowerShell *(RG 名と VM 名を実際の値に置き換えてください)* :

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(RG 名と VM 名を実際の値に置き換えてください)* :

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

"VM の再適用" が機能しなかった場合は、Azure の管理ポータルから VM に新しい空のデータ ディスクを追加し、証明書が再び追加された後でそれを削除できます。