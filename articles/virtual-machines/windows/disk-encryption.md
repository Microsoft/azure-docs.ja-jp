---
title: Azure Managed Disks のサーバー側暗号化 - PowerShell
description: Azure Storage では、保存時に暗号化してデータを保護してから、ストレージ クラスターに保存します。 マネージド ディスクの暗号化には Microsoft のマネージド キーを使用できます。また、カスタマー マネージド キーを使用し、独自のキーを使って暗号化を管理できます。
author: roygara
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: bc15ee42fd7ef8e41b332104b28af808c336789f
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430410"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Azure Managed Disks のサーバー側暗号化

Azure マネージド ディスクは、データをクラウドに永続化するときに、既定で自動的にデータを暗号化します。 サーバー側の暗号化によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 Azure マネージド ディスク内のデータは、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化され、FIPS 140-2 に準拠しています。   

暗号化はマネージド ディスクのパフォーマンスに影響しません。 暗号化に追加コストはかかりません。

Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

プラットフォーム マネージド キーを利用してお使いのマネージド ディスクを暗号化することも、お使いの独自のキーを使用して暗号化を管理することもできます。 独自のキーを使用して暗号化を管理する場合は、マネージド ディスク内のすべてのデータの暗号化と暗号化解除に使用する*カスタマー マネージド キー*を指定できます。 

以降のセクションでは、キー管理の各オプションについてさらに詳しく説明します。

## <a name="platform-managed-keys"></a>プラットフォーム マネージド キー

既定では、マネージド ディスクはプラットフォーム マネージド暗号化キーを使用します。 2017 年 6 月 10 日をもって、新しいすべてのマネージド ディスク、スナップショット、イメージ、および既存のマネージド ディスクに書き込まれる新しいデータは、プラットフォーム マネージド キーで保存時に自動的に暗号化されます。 

## <a name="customer-managed-keys"></a>カスタマー マネージド キー

ユーザー独自のキーを使用して、各マネージド ディスクのレベルで暗号化を管理できます。 カスタマー マネージド キーを使用したサーバー側でのマネージド ディスクの暗号化により、Azure Key Vault との統合されたエクスペリエンスが提供されます。 [ご使用の RSA キー](../../key-vault/key-vault-hsm-protected-keys.md)を Key Vault にインポートするか、Azure Key Vault で新しい RSA キーを生成することができます。 Azure マネージド ディスクは[エンベロープ暗号化](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique)を使用して、暗号化と暗号化解除を完全に透過的な方法で処理します。 これは、[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 ベースのデータ暗号化キー (DEK) を使用してデータを暗号化します。このキーは、ご使用のキーを使用して保護されます。 DEK の暗号化と暗号化解除にキーを使用するための、Key Vault 内のマネージド ディスクへのアクセス権の付与を行う必要があります。 これにより、データとキーを完全に制御できます。 任意の時点で、キーを無効にしたり、マネージド ディスクへのアクセスを取り消したりすることができます。 また、Azure Key Vault 監視で暗号化キーの使用状況を監査して、マネージド ディスクまたは他の信頼された Azure サービスのみがキーにアクセスしていることを確認することもできます。

次の図は、マネージド ディスクで Azure Active Directory と Azure Key Vault を使用して、カスタマー マネージド キーを使って要求を行う方法を示しています。

![マネージド ディスクとカスタマー マネージド キーのワークフロー。 管理者は Azure Key Vault を作成し、ディスク暗号化セットを作成してから、ディスク暗号化セットを設定します。 このセットは VM に関連付けられています。これにより、ディスクは Azure AD を使用して認証を行うことができます。](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


次の一覧では、図についてさらに詳しく説明します。

1. Azure Key Vault の管理者が Key Vault リソースを作成します。
1. Key Vault 管理者は、RSA キーを Key Vault にインポートするか、Key Vault 内で新しい RSA キーを生成します。
1. この管理者は、Azure Key Vault ID とキー URL を指定して、ディスク暗号化セット リソースのインスタンスを作成します。 ディスク暗号化セットは、マネージド ディスクのキー管理を簡略化するために導入された新しいリソースです。 
1. ディスク暗号化セットを作成すると、[システムによって割り当てられたマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) が Azure Active Directory (AD) に作成され、ディスク暗号化セットに関連付けられます。 
1. Azure Key Vault の管理者はその後、Key Vault 内での操作を実行するためのアクセス許可をマネージド ID に付与します。
1. VM ユーザーは、ディスク暗号化セットにディスクを関連付けることによってディスクを作成します。 また、VM ユーザーは、既存のリソースに対するカスタマー マネージド キーを使用したサーバー側の暗号化を、ディスク暗号化セットへのリソースの関連付けによって有効にすることもできます。 
1. マネージド ディスクは、マネージド ID を使用して Azure Key Vault に要求を送信します。
1. データの読み取りまたは書き込みを行うために、マネージド ディスクは Azure Key Vault に要求を送信して、データの暗号化と暗号化解除を実行するためにデータ暗号化キーを暗号化 (ラップ) および暗号化解除 (ラップ解除) します。 

カスタマー マネージド キーへのアクセスを取り消すには、[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) および [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) に関する記事をご覧ください。 アクセスを取り消すと、Azure Storage が暗号化キーにアクセスできなくなるため、ストレージ アカウント内の全データへのアクセスが事実上ブロックされます。

### <a name="supported-regions"></a>サポートされているリージョン

現在サポートされているリージョンは次のとおりです。

- 米国東部、米国西部 2、米国中南部、英国南部の各リージョンでは GA オファリングとしてご利用いただけます。
- 米国中西部、米国東部 2、カナダ中部、北ヨーロッパの各リージョンではパブリック プレビューとしてご利用いただけます。

### <a name="restrictions"></a>制限

現在、カスタマー マネージド キーには次の制限があります。

- サイズ 2080 の ["ソフト" と "ハード" の RSA キー](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types)のみがサポートされており、その他のキーまたはサイズはサポートされていません。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたカスタム イメージから作成されたディスクは、同じカスタマー マネージド キーを使用して暗号化する必要があり、同じサブスクリプション内に存在する必要があります。
- サーバー側の暗号化とカスタマー マネージド キーで暗号化されたディスクから作成されたスナップショットは、同じカスタマー マネージド キーを使用して暗号化する必要があります。
- サーバー側の暗号化とカスタマー マネージド キーを使用して暗号化されたカスタム イメージは、共有イメージ ギャラリーでは使用できません。
- お使いのカスタマー マネージド キー (Azure Key Vault、ディスク暗号化セット、VM、ディスク、およびスナップショット) に関連するすべてのリソースは、同じサブスクリプションとリージョンに存在する必要があります。
- カスタマー マネージド キーで暗号化されたディスク、スナップショット、およびイメージは、別のサブスクリプションに移動できません。
- Azure portal を使用してディスク暗号化セットを作成する場合、現時点ではスナップショットを使用できません。

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault と DiskEncryptionSet の設定

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

1.  DiskEncryptionSet のインスタンスを作成します。 
    
    ```powershell
    $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned

    $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
    ```

1.  DiskEncryptionSet リソースに Key Vault へのアクセス権を付与します。

    > [!NOTE]
    > Azure がお使いの Azure Active Directory にご自分の DiskEncryptionSet の ID を作成するのには数分かかる場合があります。 次のコマンドを実行しているときに "Active Directory オブジェクトが見つかりません" のようなエラーが表示された場合は、数分待ってから再試行してください。
    
    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
     
    New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
    ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace イメージを使用して VM を作成し、カスタマー マネージド キーで OS とデータ ディスクを暗号化する

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>カスタマー マネージド キーを使用したサーバー側の暗号化で暗号化された空のディスクを作成し、VM に接続する

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-unattached-managed-disks"></a>既存の接続されていないマネージド ディスクを暗号化する 

既存のディスクは、次のスクリプトを使用して暗号化するために、実行中の VM に接続することはできません。

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Marketplace イメージを使用して仮想マシン スケール セットを作成し、カスタマー マネージド キーで OS とデータ ディスクを暗号化する

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>サーバー側の暗号化と Azure ディスク暗号化の比較

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) では、Windows の [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。  カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

## <a name="next-steps"></a>次のステップ

- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../../key-vault/key-vault-overview.md)
- [カスタマー マネージド キー対応ディスクを含むコンピューターをレプリケートする](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell を使用して Azure への VMware VM のディザスター リカバリーを設定する](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell と Azure Resource Manager を使用して Azure への Hyper-V VM のディザスター リカバリーを設定する](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
