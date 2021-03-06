---
title: Azure AD Domain Services をセキュリティで保護する | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインの弱い暗号、古いプロトコル、および NTLM パスワード ハッシュ同期を無効にする方法を説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: 5fa19e23767af0e121d07872970199a2a1705ea8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104951940"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services マネージド ドメインをセキュリティで保護するために、弱い暗号およびパスワード ハッシュ同期を無効にする

既定では、Azure Active Directory Domain Services (Azure AD DS) で、NTLM v1 や TLS v1 などの暗号が使用できます。 これらの暗号は、一部のレガシ アプリケーションで必要になる場合がありますが、脆弱と見なされており、不要であれば無効にできます。 Azure AD Connect を使用したオンプレミスのハイブリッド接続がある場合は、NTLM パスワード ハッシュ同期も無効にできます。

この記事では、NTLM v1 と TLS v1 の暗号を無効にする方法と、NTLM パスワード ハッシュ同期を無効にする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下のリソースが必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要に応じて、[Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。

## <a name="use-security-settings-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>セキュリティ設定を使用して脆弱な暗号と NTLM パスワード ハッシュ同期を無効します

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure AD Domain Services** を検索して選択します。
1. 目的のマネージド ドメインを選択します (例: *aaddscontoso.com* )。
1. 左側で、**セキュリティの設定** を選択します。
1. 次の設定に対して **無効** をクリックします。
   - **TLS 1.2 のみモード**
   - **NTLM 認証**
   - **オンプレミスからの NTLM パスワード同期**

   ![脆弱な暗号と NTLM パスワード ハッシュ同期を無効するセキュリティの設定のスクリーンショット](media/secure-your-domain/security-settings.png)

## <a name="use-powershell-to-disable-weak-ciphers-and-ntlm-password-hash-sync"></a>PowerShell を使用して脆弱な暗号と NTLM パスワード ハッシュ同期を無効にします

必要に応じて、[Azure PowerShell をインストールして構成します](/powershell/azure/install-az-ps)。 必ず [Connect-AzAccount][Connect-AzAccount] コマンドレットを使用して Azure サブスクリプションにサインインしてください。 

また、必要に応じて、[Azure AD PowerShell をインストールして構成します](/powershell/azure/active-directory/install-adv2)。 必ず [Connect-AzureAD][Connect-AzureAD] コマンドレットを使用して Azure AD テナントにサインインしてください。

弱い暗号スイートと NTLM 資格情報ハッシュの同期を無効にするには、Azure アカウントにサインインしてから、[Get-AzResource][Get-AzResource] コマンドレットを使用して Azure AD DS リソースを取得します。

> [!TIP]
> [Get-AzResource][Get-AzResource] コマンドを使用して "*Microsoft.AAD/DomainServices* resource doesn't exist (Microsoft.AAD/DomainServices リソースが存在しません)" というエラーを受け取った場合は、[アクセス権を昇格して、すべての Azure サブスクリプションと管理グループを管理できるようにします][global-admin]。

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

次に、*DomainSecuritySettings* を定義して、以下のセキュリティ オプションを構成します。

1. NTLM v1 のサポートを無効にする。
2. オンプレミスの AD からの NTLM パスワード ハッシュの同期を無効にする。
3. TLS v1 を無効にする。

> [!IMPORTANT]
> Azure AD DS マネージド ドメインで NTLM パスワード ハッシュ同期を無効にしている場合、ユーザーおよびサービス アカウントは LDAP 簡易バインドを実行できません。 LDAP 簡易バインドを実行する必要がある場合は、次のコマンドで、 *"SyncNtlmPasswords"="Disabled";* セキュリティ構成オプションを設定しないでください。

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

最後に、[Set-AzResource][Set-AzResource] コマンドレットを使用して、定義済みのセキュリティ設定をマネージド ドメインに適用します。 最初の手順の Azure AD DS リソースと、前の手順のセキュリティ設定を指定します。

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

セキュリティ設定がマネージド ドメインに適用されるまでに、しばらく時間がかかります。

> [!IMPORTANT]
> NTLM を無効にした後、Azure AD Connect で完全なパスワード ハッシュ同期を実行して、マネージド ドメインからすべてのパスワード ハッシュを削除します。 NTLM を無効にしてもパスワード ハッシュ同期を強制しない場合は、ユーザー アカウントの NTLM パスワード ハッシュが削除されるのは、次回のパスワード変更時のみになります。 この動作により、認証方法として NTLM が使用されるシステムにキャッシュされた資格情報があれば、ユーザーは引き続きサインインすることができます。
>
> NTLM パスワード ハッシュが Kerberos パスワード ハッシュと異なっていると、NTLM へのフォールバックは機能しません。 VM にマネージド ドメイン コントローラーへの接続がある場合は、キャッシュされた資格情報も機能しなくなります。  

## <a name="next-steps"></a>次のステップ

同期プロセスの詳細について学習するには、[マネージド ドメイン内でのオブジェクトと資格情報の同期のしくみ][synchronization]に関するページを参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
