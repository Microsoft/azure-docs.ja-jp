---
title: 'Virtual WAN: さまざまなユーザー グループの Azure AD テナント: Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 4e667dcccd612873e8633c375bc3ce611e11c962
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485950"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 一連のユーザーがそれぞれ異なる VPN ゲートウェイに接続できるようにしたい場合、複数のアプリを AD に登録し、それらを異なる VPN ゲートウェイにリンクさせる方法が考えられます。

この記事では、P2S OpenVPN 認証用の Azure AD テナントを設定し、さまざまなユーザーとグループに異なるアクセスを許可するように、複数のアプリを作成して Azure AD に登録する方法について説明します。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="site"></a>6.新しい P2S 構成を作成する

P2S 構成には、リモート クライアントを接続するためのパラメーターが定義されています。

1. 次の変数を設定します。実際の環境に合わせて値は置き換えてください。

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. 次のコマンドを実行して構成を作成します。

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > 上記のコマンドでは、Azure VPN クライアントのアプリケーション ID を使用しないでください。それにより、すべてのユーザーに VPN ゲートウェイへのアクセス権が付与されます。 登録したアプリケーションの ID を使用します。

## <a name="hub"></a>7.ハブの割り当てを編集する

1. 仮想 WAN の下にある **[ハブ]** ブレードに移動します。

2. VPN サーバーの構成を関連付けるハブを選択し、省略記号 (...) をクリックします。

    ![新しいサイト](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. **[仮想ハブを編集する]** をクリックします。

4. **[ポイント対サイト ゲートウェイを含める]** チェック ボックスをオンにし、必要な **[ゲートウェイ スケール ユニット]** を選択します。

    ![新しいサイト](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. VPN クライアントの IP アドレスの割り当て元となる **[アドレス プール]** を入力します。

6. **[Confirm]\(確認\)** をクリックします。

7. この操作は、完了するまで最大 30 分かかることがあります。

## <a name="device"></a>8.VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

1. 仮想 WAN のページで、 **[User VPN configurations]\(ユーザー VPN の構成\)** をクリックします。

2. ページの上部にある **[Download user VPN config]\(ユーザー VPN 構成をダウンロードする\)** をクリックします。

3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。

4. プロファイル ファイルを使用して VPN クライアントを構成します。

5. ダウンロードした zip ファイルを解凍します。

6. 解凍された "AzureVPN" フォルダーを参照します。

7. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="9-configure-user-vpn-clients"></a>9.ユーザー VPN クライアントを構成する

接続するには、Azure VPN Client (プレビュー) をダウンロードし、前の手順でダウンロードした VPN クライアント プロファイルを、VNet に接続する各コンピューターでインポートします。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN Client をダウンロードするには

この[リンク](https://go.microsoft.com/fwlink/?linkid=2117554)を使用して、Azure VPN Client をダウンロードします。

#### <a name="import"></a>クライアント プロファイルをインポートするには

1. ページ上で、 **[インポート]** を選択します。

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. **[接続]** を選択して VPN に接続します。

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="delete"></a>クライアント プロファイルを削除するには

1. 削除するクライアント プロファイルの横にある省略記号 (...) を選択します。 **[削除]** を選択します。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. **[削除]** を選択して削除します。

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="diagnose"></a>接続の問題を診断するには

1. 接続の問題を診断するには、**診断**ツールを使用します。 診断する VPN 接続の横にある省略記号 (...) を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![診断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![診断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. 自分の資格情報でサインインします。

    ![診断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. 診断結果を確認します。

    ![診断](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>10.仮想 WAN を表示する

1. 仮想 WAN に移動します。

2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。

3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>11.リソースの正常性を表示する

1. WAN に移動します。

2. WAN のページの **[サポート + トラブルシューティング]** セクションで、 **[正常性]** をクリックしてリソースを表示します。

## <a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
