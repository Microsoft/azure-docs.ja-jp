---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c696bea902a25571c173d04e0eaa7304ed657151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610443"
---
1. 最新の [Azure PowerShell バージョン](/powershell/azure/install-az-ps)がインストールされており、Connect-AzAccount を使用して Azure アカウントにサインインしていることを確認します。

1. Azure Key Vault と暗号化キーのインスタンスを作成します。

    Key Vault インスタンスを作成する場合、論理的な削除と消去保護を有効にする必要があります。 論理的な削除では、Key Vault は削除されたキーを特定の保持期間 (既定では90日) にわたって保持します。 消去保護では、保持期間が経過するまで、削除されたキーを完全に削除できないようになります。 これらの設定は、誤って削除したためにデータが失われるのを防ぎます。 これらの設定は、Key Vault を使用してマネージド ディスクを暗号化する場合は必須です。
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    DiskEncryptionSet のインスタンスを作成します。 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。

        > [!NOTE]
        > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```
