---
title: Azure PowerShell Windows スクリプト サンプル - Azure Monitor ログ
description: Azure PowerShell のサンプル スクリプト - Azure Monitor ログ
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 5cef2c0d33d13f93d4614dd5cc8b97995bdb083a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077400"
---
# <a name="create-an-azure-monitor-vm-with-powershell"></a>PowerShell を使用して Azure Monitor VM を作成する 

このスクリプトでは、Azure 仮想マシンを作成し、Log Analytics エージェントをインストールして、システムを Log Analytics ワークスペースに登録します。 このスクリプトを実行すると、仮想マシンが Azure Monitor に表示されるようになります。 また、Log Analytics ワークスペース ID とワークスペース キーを更新する必要があります。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-monitor-oms/create-windows-vm-detailed-oms.ps1 "Create VM Log Analytics")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 また、このコマンドでは、ポート 80 を開いたり、管理者の資格情報を設定したりします。 |
| [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) | 仮想マシンに VM 拡張機能を追加します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
