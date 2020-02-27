---
title: Azure AD での Azure AD Connect クラウド プロビジョニングの前提条件
description: この記事では、クラウド プロビジョニングに必要な前提条件とハードウェア要件について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e5c58d29f01cadcc3ea2e8ec48ae67e58c4180
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909042"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Azure AD Connect クラウド プロビジョニングの前提条件
この記事では、ID ソリューションとして Azure Active Directory (Azure AD) クラウド プロビジョニングを選択して使用する方法に関するガイダンスを示します。



## <a name="cloud-provisioning-agent-requirements"></a>クラウド プロビジョニング エージェントの要件
Azure AD Connect クラウド プロビジョニングを使用するには、次のものが必要です。
    
- Azure AD テナントの全体管理者アカウント
- Windows 2012 R2 以降を搭載した、プロビジョニング エージェント用のオンプレミス サーバー
- オンプレミスのファイアウォールの構成

>[!NOTE]
>現在、プロビジョニング エージェントは、英語の言語サーバーにのみインストールできます。 英語以外のサーバーに英語の言語パックをインストールすることは有効な回避策ではなく、エージェントのインストールが失敗します。 

ここからは、これらの前提条件に関する詳細な手順について説明します。

### <a name="in-the-azure-active-directory-admin-center"></a>Azure Active Directory 管理センター

1. Azure AD テナントで、クラウド専用のグローバル管理者アカウントを作成します。 その方法を採用すると、オンプレミス サービスが利用できなくなった場合にテナントの構成を管理できます。 クラウド専用のグローバル管理者アカウントを追加する方法については、[こちら](../active-directory-users-create-azure-portal.md)をご覧ください。 テナントからロックアウトされないようにするには、この手順を実行する必要があります。
1. 1 つ以上の[カスタム ドメイン名](../active-directory-domains-add-azure-portal.md)を Azure AD テナントに追加します。 ユーザーは、このドメイン名のいずれかを使用してサインインできます。

### <a name="in-your-on-premises-environment"></a>オンプレミスの環境の場合

1. 4 GB 以上の RAM と .NET 4.7.1 以降のランタイムを搭載した、Windows Server 2012 R2 以降が実行されているドメイン参加済みホスト サーバーを特定します。

1. サーバーと Azure AD の間にファイアウォールがある場合は、次の項目を構成します。
   - エージェントが次のポートを介して Azure AD に "*送信*" 要求を発行できるようにします。

        | ポート番号 | 用途 |
        | --- | --- |
        | **80** | SSL 証明書を検証する際に証明書失効リスト (CRL) をダウンロードします。  |
        | **443** | サービスを使用したすべての送信方向の通信を処理します。 |
        | **8080** (省略可能) | ポート 443 が使用できない場合、エージェントは、ポート 8080 経由で 10 分おきにその状態をレポートします。 この状態は Azure AD ポータルに表示されます。 |
     
   - ご利用のファイアウォールが送信元ユーザーに応じて規則を適用している場合は、ネットワーク サービスとして実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。
   - ファイアウォールまたはプロキシで安全なサフィックスの指定が許可されている場合は、\*.msappproxy.net および \*.servicebus.windows.net への接続を追加します。 そうでない場合は、毎週更新される [Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可します。
   - エージェントは、初期登録のために login.windows.net と login.microsoftonline.com にアクセスする必要があります。 これらの URL にもファイアウォールを開きます。
   - 証明書の検証のために、URL mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80、www\.microsoft.com:80 のブロックを解除します。 他の Microsoft 製品でもこれらの URL を証明書の検証に使用しているので、URL のブロックを既に解除している可能性もあります。

### <a name="verify-the-port"></a>ポートを確認する
Azure によってポート 443 がリッスンされていて、エージェントとこのポートとの通信が可能であることを確認するには、次の URL を使用します。

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

このテストでは、ポート 443 を介してエージェントと Azure との通信が可能であることを確認します。 ブラウザーを開き、エージェントがインストールされているサーバーから前の URL に移動します。

![ポートの到達可能性の確認](media/how-to-install/verify2.png)

### <a name="additional-requirements"></a>その他の要件
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS の要件

>[!NOTE]
>トランスポート層セキュリティ (TLS) は、セキュリティで保護された通信を規定するプロトコルです。 TLS 設定を変更すると、フォレスト全体に影響します。 詳細については、「[Windows の WinHTTP における既定の安全なプロトコルとして TLS 1.1 および TLS 1.2 を有効化する更新プログラム](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)」を参照してください。

Azure AD Connect クラウド プロビジョニング エージェントをホストする Windows サーバーでは、インストールする前に TLS 1.2 を有効にする必要があります。

TLS 1.2 を有効にするには、次の手順に従います。

1. 次のレジストリ キーを設定します。
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. サーバーを再起動します。


## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニングとは](what-is-cloud-provisioning.md)

