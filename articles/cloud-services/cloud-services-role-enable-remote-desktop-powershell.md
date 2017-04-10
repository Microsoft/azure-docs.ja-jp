---
title: "PowerShell を使用して Azure Cloud Services のロールでリモート デスクトップ接続を有効にする"
description: "PowerShell で Azure クラウド サービス アプリケーションを構成してリモート デスクトップ接続を許可する方法"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: a78fc57d264e9f8074c94b334b24bbf1b7871d08
ms.lasthandoff: 03/25/2017


---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>PowerShell を使用して Azure Cloud Services のロールでリモート デスクトップ接続を有効にする
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Azure クラシック ポータル](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)
>
>

リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。 リモート デスクトップ接続を使用すると、アプリケーションの実行中にそのアプリケーションの問題のトラブルシューティングや診断を行うことができます。

この記事では、PowerShell を使用して、クラウド サービスのロールでリモート デスクトップを有効にする方法について説明します。 この記事で求められる前提条件については、 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) に関するページを参照してください。 PowerShell では、リモート デスクトップ拡張機能を使用するため、アプリケーションのデプロイ後にリモート デスクトップを有効化できます。

## <a name="configure-remote-desktop-from-powershell"></a>PowerShell からリモート デスクトップを構成する
[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) コマンドレットを使用すると、クラウド サービス デプロイの指定したロールまたはすべてのロールでリモート デスクトップを有効にすることができます。 このコマンドレットでは、PSCredential オブジェクトを受け入れる *Credential* パラメーターを使用してリモート デスクトップ ユーザーのユーザー名とパスワードを指定できます。

PowerShell を対話形式で使用している場合は、 [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) コマンドレットを呼び出すことで、PSCredential オブジェクトを簡単に設定できます。

```
$remoteusercredentials = Get-Credential
```

このコマンドにより、リモート ユーザーのユーザー名とパスワードを安全な方法で入力できるダイアログ ボックスが表示されます。

PowerShell は自動化のシナリオで役立つため、ユーザー操作を必要としない方法で **PSCredential** オブジェクトを設定することもできます。 最初に、セキュリティで保護されたパスワードを設定する必要があります。 まずプレーンテキストのパスワードを指定し、 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)を使用してそのパスワードをセキュリティで保護された文字列に変換します。 次に、 [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx)を使用して、セキュリティで保護されたこの文字列を、暗号化された標準文字列に変換する必要があります。 これで、この暗号化された標準文字列を、 [Set-Content](https://technet.microsoft.com/library/ee176959.aspx)を使用してファイルに保存することができます。

パスワードを毎回手動で入力しなくても済むように、セキュリティで保護されたパスワード ファイルを作成することもできます。 また、セキュリティで保護されたパスワード ファイルはプレーン テキスト ファイルよりも適切です。 セキュリティで保護されたパスワード ファイルを作成するには、次の PowerShell を使用します。

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> パスワードを設定するときは、 [複雑さの要件](https://technet.microsoft.com/library/cc786468.aspx)を満たしていることを確認してください。
>
>

セキュリティで保護されたパスワード ファイルから資格情報オブジェクトを作成するには、ファイルの内容を読み取り、 [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)を使用して、セキュリティで保護された文字列にもう一度変換する必要があります。

[Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) コマンドレットは、ユーザー アカウントの期限が切れる *日時* を指定する **Expiration** パラメーターも受け入れます。 たとえば、現在の日時から数日後にアカウントの期限が切れるように設定することもできます。

次の PowerShell の例では、クラウド サービスでリモート デスクトップ拡張機能を設定する方法を示しています。

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
また、必要に応じて、リモート デスクトップを有効にするデプロイ スロットおよびロールを指定することもできます。 これらのパラメーターが指定されていない場合、コマンドレットは **運用環境** のデプロイ スロットに含まれるすべてのロールでリモート デスクトップを有効にします。

リモート デスクトップ拡張機能は、デプロイに関連付けられています。 サービスの新しいデプロイを作成した場合は、そのデプロイでリモート デスクトップを有効にする必要があります。 常にリモート デスクトップを有効にしておく必要がある場合は、PowerShell スクリプトをデプロイのワークフローに統合することを検討してください。

## <a name="remote-desktop-into-a-role-instance"></a>ロール インスタンスへのリモート デスクトップ接続
クラウド サービスの特定のロール インスタンスにリモート デスクトップ接続するには、 [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用します。 *LocalPath* パラメーターを使用すると、RDP ファイルをローカルにダウンロードできます。 *Launch* パラメーターを使用して、クラウド サービスのロール インスタンスにアクセスするための [リモート デスクトップ接続] ダイアログを直接起動することもできます。

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>サービスでリモート デスクトップ拡張機能が有効になっているかどうかを確認する
[Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) コマンドレットを使用すると、サービスのデプロイでリモート デスクトップが有効になっているか、無効になっているかが表示されます。 このコマンドレットによって、リモート デスクトップ ユーザーのユーザー名と、リモート デスクトップ拡張機能が有効になっているロールが返されます。 既定では、これはデプロイ スロットで行われるため、代わりにステージング スロットを使用することもできます。

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>リモート デスクトップ拡張機能をサービスから削除する
デプロイで既にリモート デスクトップ拡張機能を有効にしている状態で、リモート デスクトップ設定を更新する必要がある場合は、まず拡張機能を削除する必要があります。 次に、新しい設定でもう一度有効にします。 たとえば、リモート ユーザー アカウントに新しいパスワードを設定する必要がある場合や、ユーザー アカウントの有効期限が切れた場合などです。 これは、リモート デスクトップ拡張機能が有効になっている既存のデプロイで必要な操作です。 新しいデプロイの場合は、単に拡張機能を直接適用できます。

リモート デスクトップ拡張機能をデプロイから削除するには、 [Remove-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) コマンドレットを使用します。 また、必要に応じて、リモート デスクトップ拡張機能を削除するデプロイ スロットおよびロールを指定することもできます。

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> 拡張機能の構成を完全に削除するには、 *UninstallConfiguration* パラメーターを使用して **Remove** コマンドレットを呼び出す必要があります。
>
> **UninstallConfiguration** パラメーターを使用すると、サービスに適用されている拡張機能の構成がアンインストールされます。 拡張機能の構成は、いずれもサービスの構成に関連付けられています。 **UninstallConfiguration** を使用せずに *Remove* コマンドレットを呼び出すと、拡張機能の構成から<mark>デプロイ</mark>との関連付けが解除されるため、実質的には拡張機能が削除されることになります。 ただし、拡張機能の構成は、サービスに関連付けられたままになります。
>
>

## <a name="additional-resources"></a>その他のリソース

[Cloud Services の構成方法](cloud-services-how-to-configure.md)
[Cloud Services に関する FAQ - リモート デスクトップ](cloud-services-faq.md#remote-desktop)

