---
title: Azure VMware Solutions (AVS) - オンプレミスと AVS プライベート クラウド間の VPN を構成する
description: オンプレミス ネットワークと AVS プライベート クラウド間でサイト対サイトまたはポイント対サイト VPN 接続を構成する方法について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8188fac270eadb6e09cc3561ddefb05aa59ba661
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087134"
---
# <a name="configure-a-vpn-connection-to-your-avs-private-cloud"></a>AVS プライベート クラウドへの VPN 接続を構成する

VPN ゲートウェイを使用すると、オンプレミス ネットワークおよびクライアント コンピューターからリモートで AVS ネットワークに接続できます。 この記事では、AVS ポータルから VPN ゲートウェイを設定する方法について説明します。 オンプレミス ネットワークと AVS ネットワーク間の VPN 接続により、AVS プライベート クラウド上の vCenter とワークロードにアクセスできます。 AVS では、ポイント対サイト VPN ゲートウェイとサイト対サイト VPN ゲートウェイの両方がサポートされています。

## <a name="vpn-gateway-types"></a>VPN ゲートウェイの種類

* **ポイント対サイト VPN** 接続は、お使いのコンピューターから自分の AVS プライベート クラウドに接続する最も簡単な方法です。 AVS プライベート クラウドにリモートで接続するには、ポイント対サイト VPN 接続を使用します。
* **サイト対サイト VPN** 接続を使用すると、オンプレミスのサービスにアクセスするように AVS プライベート クラウドのワークロードを設定することができます。 AVS プライベート クラウドの vCenter に対して認証を行うための ID ソースとして、オンプレミスの Active Directory を使用することもできます。 現時点では、**ポリシーベースの VPN** の種類がサポートされています。

リージョンでは、1 つのサイト対サイト VPN ゲートウェイと 1 つのポイント対サイト VPN ゲートウェイを作成できます。

## <a name="point-to-site-vpn"></a>ポイント対サイト VPN

ポイント対サイト VPN ゲートウェイを作成するには、「[ポイント対サイト VPN ゲートウェイを作成する](vpn-gateway.md#create-point-to-site-vpn-gateway)」を参照してください。

### <a name="connect-to-avs-using-point-to-site-vpn"></a>ポイント対サイト VPN を使用して AVS に接続する

お使いのコンピューターから AVS に接続するには、VPN クライアントが必要です。 Windows 用の [OpenVPN クライアント](https://openvpn.net/community-downloads/)または macOS および OS X 用の [Viscosity](https://www.sparklabs.com/viscosity/download/) をダウンロードします。

1. AVS ポータルを起動し、 **[ネットワーク]** を選択します。
2. **[VPN Gateway]\(VPN ゲートウェイ\)** を選択します。
3. VPN ゲートウェイの一覧で、ポイント対サイト VPN ゲートウェイをクリックします。
4. **[ユーザー]** を選択します。
5. **[Download my VPN configuration]\(VPN 構成をダウンロードする\)** をクリックします

    ![VPN 構成をダウンロードする](media/download-p2s-vpn-configuration.png)

6. VPN クライアントで構成をインポートします

    * [Windows クライアントで構成をインポートする](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)方法
    * [macOS または OS X で構成をインポートする](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)方法

7. AVS VPN ゲートウェイに接続します。

次の例で、**Viscosity クライアント**を使用した接続のインポートを示します。

#### <a name="import-connection-on-viscosity-client"></a>Viscosity クライアントの接続をインポートする

1. ダウンロードした .zip ファイルから VPN 構成の内容を抽出します。

2. コンピューターで Viscosity を開きます。

3. **+** アイコンをクリックし、 **[Import connection]\(接続のインポート\)**  >  **[From File]\(ファイルから\)** を選択します。

    ![ファイルから VPN 構成をインポートする](media/import-p2s-vpn-config.png)

4. 使用するプロトコル用の OpenVPN 構成ファイル (.ovpn) を選択し、 **[Open]\(開く\)** をクリックします。

    ![VPN](media/import-p2s-vpn-config-choose-ovpn.png)

これで、[Viscosity] メニューに接続が表示されます。

#### <a name="connect-to-the-vpn"></a>VPN に接続します

Viscosity OpenVPN クライアントを使用して VPN に接続するには、メニューから接続を選択します。 メニュー アイコンが更新され、接続が確立されたことが示されます。

![VPN](media/vis03.png)

### <a name="connecting-to-multiple-avs-private-clouds"></a>複数の AVS プライベート クラウドへの接続

ポイント対サイト VPN 接続では、作成した最初の AVS プライベート クラウドの DNS 名が解決されます。 他の AVS プライベート クラウドにアクセスする場合は、VPN クライアント上の DNS サーバーを更新する必要があります。

1. [AVS ポータル](access-cloudsimple-portal.md)を起動します。

2. **[リソース]**  >  **[AVS プライベート クラウド]** に移動し、接続先の AVS プライベート クラウドを選択します。

3. AVS プライベート クラウドの **[概要]** ページの **[基本情報]** で、AVS プライベート クラウドの DNS サーバーの IP アドレスをコピーします。

    ![AVS プライベート クラウドの DNS サーバー](media/private-cloud-dns-server.png)

4. コンピューターのシステム トレイの Viscosity アイコンを右クリックし、 **[Preferences]\(ユーザー設定\)** を選択します。

    ![VPN](media/vis00.png)

5. AVS VPN 接続を選択します。

    ![VPN 接続](media/viscosity-client.png)

6. **[編集]** をクリックして、接続プロパティを変更します。

    ![VPN 接続を編集する](media/viscosity-edit-connection.png)

7. **[ネットワーク]** タブをクリックし、AVS プライベート クラウドの DNS サーバーの IP アドレスをコンマまたはスペースで区切って入力し、ドメインを ```az.cloudsimple.io``` として入力します。 **[VPN サーバーによって送信される DNS 設定を無視する]** を選択します。

    ![VPN ネットワーク](media/viscosity-edit-connection-networking.png)

> [!IMPORTANT]
> 最初の AVS プライベート クラウドに接続するには、これらの設定を削除して VPN サーバーに接続します。

## <a name="site-to-site-vpn"></a>サイト対サイト VPN

サイト対サイト VPN ゲートウェイを作成するには、「[サイト間 vpn ゲートウェイを作成する](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)」を参照してください。 オンプレミス ネットワークから AVS プライベート クラウドへのサイト対サイト VPN 接続には、次の利点があります。 

* オンプレミス ネットワーク内の任意のワークステーションからの AVS プライベート クラウド vCenter へのアクセス
* vCenter ID ソースとしてのオンプレミスの Active Directory Domain Services の使用
* オンプレミス リソースから AVS プライベート クラウド vCenter への VM テンプレート、ISO イメージ、その他のファイルの簡易な転送
* AVS プライベート クラウドで実行されているワークロードへのオンプレミス ネットワークからのアクセス

オンプレミス VPN ゲートウェイを高可用性モードで設定する方法については、[高可用性 VPN 接続の構成](high-availability-vpn-connection.md)に関する記事を参照してください。

> [!IMPORTANT]
>    1. VPN デバイスでの TCP MSS クランプは 1,200 で設定します。 お使いの VPN デバイスで MSS クランプがサポートされていない場合は、別の方法として、トンネル インターフェイスの MTU を 1,240 バイトに設定できます。
> 2. サイト対サイト VPN がセットアップされたら、*.cloudsimple.io に対する DNS 要求を AVS プライベート クラウドの DNS サーバーに転送します。 [オンプレミスの DNS のセットアップ](on-premises-dns-setup.md)に関する記事の手順に従います。
