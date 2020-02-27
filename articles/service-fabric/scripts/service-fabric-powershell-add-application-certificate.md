---
title: PowerShell でクラスターにアプリケーション証明書を追加する
description: Azure PowerShell のサンプル スクリプト - アプリケーション証明書の Service Fabric クラスターへの追加。
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 61c22a3949008d61bbe3472f601d2d0dd597a0ac
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114340"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>アプリケーション証明書の Service Fabric クラスターへの追加

このサンプル スクリプトでは、Key Vault に証明書を作成して、クラスターが実行されている仮想マシン スケール セットのいずれかにデプロイする方法について説明します。 このシナリオは、Service Fabric を直接使用しませんが、Key Vault と仮想マシン スケール セットに依存します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzAccount` を実行して、Azure との接続を作成します。 

## <a name="create-a-certificate-in-key-vault"></a>Key Vault に証明書を作成する

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>あるいは既存の証明書を Key Vault にアップロードする

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $PathToPFX, $CertPassword

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>証明書を使用して仮想マシン スケール セットのプロファイルを更新する

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore
$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>仮想マシン スケール セットを更新する
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> クラスター内の複数のノードの種類に証明書を配置する場合は、このスクリプトの 2 番目と 3 番目の部分を、証明書を保持する必要があるノードの種類ごとに繰り返す必要があります。

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [New-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | 証明書を表すインメモリ ポリシーを作成します |
| [Add-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| ポリシーを Key Vault にデプロイします |
| [New-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | VM 内の証明書を表すインメモリ構成を作成します |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Add-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | 仮想マシン スケール セットのインメモリ定義に証明書を追加します |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | 仮想マシン スケール セットの新しい定義をデプロイします |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure Service Fabric 用 Azure PowerShell サンプルは、[Azure PowerShell サンプル](../service-fabric-powershell-samples.md)のページにあります。
