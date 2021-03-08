---
title: VM の可用性セットの変更
description: Azure PowerShell を使用して仮想マシンの可用性セットを変更する方法について説明します。
ms.service: virtual-machines
author: cynthn
ms.topic: how-to
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 65bf2dea6f3bc2e33ec10dc75b1678466401c10b
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184476"
---
# <a name="change-the-availability-set-for-a-vm"></a>VM の可用性セットを変更する
以下の手順では、Azure PowerShell を使用して VM の可用性セットを変更する方法について説明します。 VM を可用性セットに追加できるのは、VM の作成時のみです。 可用性セットを変更するには、仮想マシンを削除してから作成し直す必要があります。 

この記事は、Linux と Windows の両方の VM に適用されます。

この記事は、2019 年 2 月 12 日に [Azure Cloud Shell](https://shell.azure.com/powershell) と [Az PowerShell モジュール](/powershell/azure/install-az-ps) バージョン 1.2.0 を使用して最後にテストされました。

この例では、VM がロード バランサーに接続されているかどうかは確認されません。 VM がロード バランサーに接続されている場合は、そのケースを処理するようスクリプトを更新する必要があります。 


## <a name="change-the-availability-set"></a>可用性セットを変更する 

次のスクリプトでは、必要な情報を収集し、元の VM を削除してから新しい可用性セット内でその VM を作り直す例を示します。

```powershell
# Set variables
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    $newAvailSetName = "myAvailabilitySet"

# Get the details of the VM to be moved to the Availability Set
    $originalVM = Get-AzVM `
       -ResourceGroupName $resourceGroup `
       -Name $vmName

# Create new availability set if it does not exist
    $availSet = Get-AzAvailabilitySet `
       -ResourceGroupName $resourceGroup `
       -Name $newAvailSetName `
       -ErrorAction Ignore
    if (-Not $availSet) {
    $availSet = New-AzAvailabilitySet `
       -Location $originalVM.Location `
       -Name $newAvailSetName `
       -ResourceGroupName $resourceGroup `
       -PlatformFaultDomainCount 2 `
       -PlatformUpdateDomainCount 2 `
       -Sku Aligned
    }
    
# Remove the original VM
    Remove-AzVM -ResourceGroupName $resourceGroup -Name $vmName    

# Create the basic configuration for the replacement VM. 
    $newVM = New-AzVMConfig `
       -VMName $originalVM.Name `
       -VMSize $originalVM.HardwareProfile.VmSize `
       -AvailabilitySetId $availSet.Id
 
# For a Linux VM, change the last parameter from -Windows to -Linux 
    Set-AzVMOSDisk `
       -VM $newVM -CreateOption Attach `
       -ManagedDiskId $originalVM.StorageProfile.OsDisk.ManagedDisk.Id `
       -Name $originalVM.StorageProfile.OsDisk.Name `
       -Windows

# Add Data Disks
    foreach ($disk in $originalVM.StorageProfile.DataDisks) { 
    Add-AzVMDataDisk -VM $newVM `
       -Name $disk.Name `
       -ManagedDiskId $disk.ManagedDisk.Id `
       -Caching $disk.Caching `
       -Lun $disk.Lun `
       -DiskSizeInGB $disk.DiskSizeGB `
       -CreateOption Attach
    }
    
# Add NIC(s) and keep the same NIC as primary; keep the Private IP too, if it exists. 
    foreach ($nic in $originalVM.NetworkProfile.NetworkInterfaces) {    
    if ($nic.Primary -eq "True")
    {
            Add-AzVMNetworkInterface `
               -VM $newVM `
               -Id $nic.Id -Primary
               }
           else
               {
                 Add-AzVMNetworkInterface `
                -VM $newVM `
                 -Id $nic.Id 
                }
      }

# Recreate the VM
    New-AzVM `
       -ResourceGroupName $resourceGroup `
       -Location $originalVM.Location `
       -VM $newVM `
       -DisableBginfoExtension
```

## <a name="next-steps"></a>次のステップ

[データ ディスク](attach-managed-disk-portal.md)を追加して、記憶域を VM にさらに追加します。
