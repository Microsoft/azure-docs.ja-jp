---
title: "Azure Virtual Machines 用の一般的な PowerShell コマンド | Microsoft Docs"
description: "Azure で Windows VM の作成および管理を開始する際に使用される一般的な PowerShell コマンド。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 7744f0bef5969a41130c09635d608df1a2952993
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Azure Virtual Machines の作成および管理に使用される一般的な PowerShell コマンド

この記事では、Azure サブスクリプション内の仮想マシンの作成および管理に使用できる Azure PowerShell コマンドの一部について説明します。  具体的なコマンド ライン スイッチやオプションについて詳しくは、**Get-Help** *コマンド* を使用します。

最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/overview)」を参照してください。

この記事内のコマンドを複数実行する場合は、変数が役に立つことがあります。

- $location: 仮想マシンの場所。 [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) を使用して、適切な[地理的リージョン](https://azure.microsoft.com/regions/)を検索できます。
- $myResourceGroup: 仮想マシンを含むリソース グループの名前。
- $myVM: 仮想マシンの名前。

## <a name="create-a-vm"></a>VM の作成

| タスク | コマンド |
| ---- | ------- |
| VM 構成の作成 |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>VM 構成は、仮想マシンの設定の定義または更新に使用します。 この構成は、VM の名前とその [サイズ](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) で初期化します。 |
| 構成設定の追加 |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>[資格情報](https://technet.microsoft.com/library/hh849815.aspx) を含むオペレーティング システムの設定が、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 |
| ネットワーク インターフェイスの追加 |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>仮想ネットワークで通信するには、VM に [ネットワーク インターフェイス](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) がある必要があります。 [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) を使用して既存のネットワーク インターフェイス オブジェクトを取得することもできます。 |
| プラットフォーム イメージの指定 |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "発行元名" -Offer "発行元のプラン" -Skus "製品SKU" -Version "最新"<BR></BR><BR></BR>[イメージ情報](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) が、New-AzureRmVMConfig を使用してあらかじめ作成した構成オブジェクトに追加されます。 このコマンドから返されるオブジェクトは、プラットフォーム イメージを使用するために OS ディスクを設定する場合にのみ使用されます。 |
| プラットフォーム イメージを使用するように OS ディスクを設定 |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>オペレーティング システム ディスクの名前と [ストレージ](../../storage/common/storage-powershell-guide-full.md) 内での場所が、あらかじめ作成した構成オブジェクトに追加されます。 |
| 一般化されたイメージを使用するように OS ディスクを設定 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>オペレーティング システム ディスクの名前、ソース イメージの場所、ディスクを追加する [ストレージ](../../storage/common/storage-powershell-guide-full.md) 内での場所が構成オブジェクトに追加されます。 |
| 特殊なイメージを使用するように OS ディスクを設定 |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| VM を作成します |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>すべてのリソースを[リソース グループ](../../azure-resource-manager/powershell-azure-resource-manager.md)に作成する必要があります。 このコマンドを実行する前に、New-AzureRmVMConfig、Set-AzureRmVMOperatingSystem、Set-AzureRmVMSourceImage、Add-AzureRmVMNetworkInterface、および Set-AzureRmVMOSDisk を実行します。 |

## <a name="get-information-about-vms"></a>VM に関する情報の取得

| タスク | コマンド |
| ---- | ------- |
| サブスクリプション内の VM の一覧表示 |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| リソース グループ内の VM の一覧表示 |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>サブスクリプションのリソース グループのリストを取得するには、[Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup) を使用します。 |
| VM に関する情報の取得 |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>VM の管理
| タスク | コマンド |
| --- | --- |
| VM の起動 |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の停止 |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| 実行中の VM の再起動 |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の削除 |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| VM の汎用化 |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>Save-AzureRmVMImage を実行する前に、このコマンドを実行します。 |
| VM のキャプチャ |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>仮想マシンをイメージの作成に使用するには、[準備、シャット ダウン、一般化](prepare-for-upload-vhd-image.md)が必要です。 このコマンドを実行する前に、Set-AzureRmVm を実行します。 |
| VM の更新 |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Get-AzureRmVM を使用して現在の VM の構成を取得し、VM オブジェクトの構成設定を変更してから、このコマンドを実行します。 |
| VM へのデータ ディスクの追加 |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Get-AzureRmVM を使用して VM オブジェクトを取得します。 LUN の数とディスクのサイズを指定します。 Update-AzureRmVM を実行して、構成の変更を VM に適用します。 追加するディスクは初期化されていません。 |
| VM からのデータ ディスクの削除 |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Get-AzureRmVM を使用して VM オブジェクトを取得します。 Update-AzureRmVM を実行して、構成の変更を VM に適用します。 |
| VM への拡張機能の追加 |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>インストールする拡張機能の適切な[構成情報](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions)を含めて、このコマンドを実行します。 |
| VM 拡張機能の削除 |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>次のステップ
* 仮想マシンを作成するための基本的な手順について「[Resource Manager と PowerShell を使用して Windows VM を作成する](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。


