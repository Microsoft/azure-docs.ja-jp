---
title: Batch で Key Vault に安全にアクセスする
description: Azure Batch を使用して、プログラムで Key Vault の資格情報にアクセスする方法について説明します。
ms.topic: how-to
ms.date: 02/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6938d0fcd2357efcf03053b0c9b2bde3954270b7
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079440"
---
# <a name="securely-access-key-vault-with-batch"></a>Batch で Key Vault に安全にアクセスする

この記事では、Azure Key Vault に格納されている資格情報に安全にアクセスできるように、Batch ノードを設定する方法について説明します。 管理者の資格情報を Key Vault に配置する一方で、スクリプトから Key Vault にアクセスするために資格情報をハードコーディングすることは無意味です。 これを解決するには、Key Vault へのアクセス権を Batch ノードに付与する証明書を使用します。 いくつかの手順を実行すると、Batch 用の安全なキー ストレージを実装できます。

Batch ノードから Azure Key Vault に対して認証を行うには、次のものが必要です。

- Azure Active Directory (Azure AD) の資格情報
- 証明書
- Batch アカウント
- 少なくとも 1 つのノードを含む Batch プール

## <a name="obtain-a-certificate"></a>証明書を取得する

まだ証明書がない場合、`makecert` コマンドライン ツールを使用して自己署名証明書を生成するのが最も簡単な方法です。

通常、`makecert` は次のパスにあります: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>`。 管理者としてコマンド プロンプトを開き、次の例を使用して `makecert` に移動します。

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

次に、`makecert` ツールを使用して、`batchcertificate.cer` および `batchcertificate.pvk` という自己署名証明書ファイルを作成します。 このアプリケーションでは、使用される共通名 (CN) は重要ではありませんが、証明書の用途がわかるような名前にすると便利です。

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Batch には `.pfx` ファイルが必要です。 [pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) ツールを使用して、`makecert` で作成した `.cer` および `.pvk` ファイルを単一の `.pfx` ファイルに変換します。

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

Key Vault へのアクセス権は、**ユーザー**または**サービス プリンシパル**に付与されます。 プログラムによって Key Vault にアクセスするには、前の手順で作成した証明書と共にサービス プリンシパルを使用します。

Azure サービス プリンシパルの詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください。

> [!NOTE]
> サービス プリンシパルは、Key Vault と同じ Azure AD テナントに存在する必要があります。

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

アプリケーションの URL は、Key Vault へのアクセスにのみ使用するため、重要ではありません。

## <a name="grant-rights-to-key-vault"></a>Key Vault への権限を付与する

前の手順で作成したサービス プリンシパルには、Key Vault からシークレットを取得するためのアクセス許可が必要です。 アクセス許可を付与するには、Azure portal を使用するか、次の PowerShell コマンドを使用します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>証明書を Batch アカウントに割り当てる

Batch プールを作成し、プールの [証明書] タブに移動して、作成した証明書を割り当てます。 これで、すべての Batch ノードに証明書が存在することになります。

次に、証明書を Batch アカウントに割り当てる必要があります。 証明書をアカウントに割り当てると、その証明書をプールに割り当てることができ、次に各ノードに割り当てることができます。 最も簡単にこれを行うには、ポータルで Batch アカウントに移動し、 **[証明書]** に移動して、 **[追加]** を選択します。 「[証明書を取得する](#obtain-a-certificate)」で生成した `.pfx` ファイルをアップロードし、パスワードを指定します。 完了すると、証明書が一覧に追加され、拇印を確認できるようになります。

これで、Batch プールを作成する際に、プール内で **[証明書]** に移動し、作成した証明書をそのプールに割り当てることができます。 これを行う場合は、ストアの場所として **[LocalMachine]** を選択してください。 証明書が、プール内のすべての Batch ノードに読み込まれます。

## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする

ノードで PowerShell スクリプトを使用して Key Vault にアクセスすることを計画している場合は、Azure PowerShell ライブラリをインストールしておく必要があります。 これを行うにはいくつかの方法があります。ノードに Windows Management Framework (WMF) 5 がインストールされている場合は、install-module コマンドを使用してダウンロードできます。 WMF 5 がインストールされていないノードを使用している場合、これをインストールする最も簡単な方法は、Azure PowerShell `.msi` ファイルをバッチ ファイルとひとまとめにし、バッチ起動スクリプトの最初の部分としてインストーラーを呼び出すことです。 詳細については、次の例を参照してください。

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault にアクセスします

これで、Batch ノードで実行されるスクリプトで Key Vault にアクセスするための設定が完了しました。 スクリプトから Key Vault にアクセスするには、スクリプトで証明書を使用して Azure AD に対する認証を行うだけです。 PowerShell でこれを行うには、次のコマンド例を使用します。 **拇印**の適切な GUID、**アプリ ID** (サービス プリンシパルの ID)、**テナント ID** (サービス プリンシパルが存在するテナント) を指定します。

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

認証されたら、通常どおり Key Vault にアクセスします。

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

これらはスクリプトで使用する資格情報です。
