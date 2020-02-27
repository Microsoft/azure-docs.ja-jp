---
title: 'VPN Gateway: さまざまなユーザー グループの Azure AD テナント: Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485726"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 一連のユーザーがそれぞれ異なる VPN ゲートウェイに接続できるようにしたい場合、複数のアプリを AD に登録し、それらを異なる VPN ゲートウェイにリンクさせる方法が考えられます。 この記事では、P2S OpenVPN 認証用の Azure AD テナントを設定し、ユーザーとグループにそれぞれ異なるアクセスを許可するように、複数のアプリを作成して Azure AD に登録する方法について説明します。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="enable-authentication"></a>6.ゲートウェイでの認証を有効にする

このステップでは、VPN ゲートウェイでの Azure AD 認証を有効にします。

1. 次のコマンドを実行して、VPN ゲートウェイでの Azure AD 認証を有効にします。 実際の環境に合わせてコマンドを変更してください。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > 上記のコマンドでは、Azure VPN クライアントのアプリケーション ID を使用しないでください。これにより、すべてのユーザーに VPN ゲートウェイへのアクセス権が付与されます。 登録したアプリケーションの ID を使用します。

2. 次のコマンドを実行してプロファイルを作成し、ダウンロードします。 -ResourcGroupName と -Name の値は実際の値に変更してください。

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. コマンドを実行すると、次のような結果が表示されます。 結果の URL をブラウザーにコピーして、プロファイルの ZIP ファイルをダウンロードします。

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. ダウンロードした zip ファイルを解凍します。

5. 解凍された "AzureVPN" フォルダーを参照します。

6. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
