---
title: CloudSimple による Azure VMware ソリューション クイック スタート - プライベート クラウドを作成する
description: CloudSimple による Azure VMware ソリューションでプライベート クラウドを作成して構成する方法を学習する
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1bd84a40236b54d799efcf04eae707aea9c6c945
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828947"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>クイックスタート - プライベート クラウドの環境を構成する

この記事では、CloudSimple プライベート クラウドを作成し、プライベート クラウド環境を設定する方法について学習します。

## <a name="before-you-begin"></a>開始する前に

[ネットワークの前提条件](cloudsimple-network-checklist.md)を確認してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

プライベート クラウドは、ESXi ホスト、vCenter、vSAN、NSX をサポートする分離 VMware スタックです。

プライベート クラウドは CloudSimple ポータル経由で管理されます。 独自の管理ドメインに独自の vCenter サーバーがあります。 スタックは専用ノードと分離ベア メタル ハードウェア ノードで実行されます。

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
3. プライベート クラウドを作成する CloudSimple サービスを選択します。
4. **[概要]** から、 **[Create Private Cloud]\(プライベート クラウドの作成\)** をクリックして CloudSimple ポータルの新しいブラウザー タブを開きます。  メッセージが表示されたら、Azure サインインの資格情報でサインインします。  

    ![Azure からプライベート クラウドを作成する](media/create-private-cloud-from-azure.png)

5. CloudSimple ポータルで、プライベート クラウドの名前を指定します。
6. プライベート クラウドの**場所**を選択します。
7. Azure で購入したものと一貫性のある**ノードの種類**を選択します。
8. **[ノード数]** を指定します。  プライベート クラウドを作成するには、少なくとも 3 つのノードが必要です。

    ![プライベート クラウドを作成する - 基本情報](media/create-private-cloud-basic-info.png)

9. **[次へ: 詳細オプション]** をクリックします。
10. vSphere/vSAN サブネットの CIDR 範囲を入力します。 CIDR 範囲がオンプレミスのサブネット、他の Azure サブネット (仮想ネットワーク)、またはゲートウェイのサブネットのいずれとも重複していないことを確認します。

    **CIDR 範囲オプション:** /24、/23、/22、または /21。 /24 CIDR 範囲は最大で 26 つのノードをサポートし、/23 CIDR 範囲は最大で 58 のノードをサポートし、/22 および /21 CIDR 範囲は 64 のノード (プライベート クラウドのノードの最大数) をサポートします。  VLAN とサブネットの詳細は、「[VLAN とサブネットの概要](cloudsimple-vlans-subnets.md)」を参照してください。

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 範囲の IP アドレスは、プライベート クラウド インフラストラクチャで使用するために予約されています。  どの仮想マシンでもこの範囲の IP アドレスは使用しないでください。

11. **[次へ: 確認と作成]** を選択します。
12. 設定を確認します。 設定を変更する必要がある場合は、 **[前へ]** をクリックします。
13. **Create** をクリックしてください。

プライベート クラウドのプロビジョニング プロセスが開始されます。  プライベート クラウドのプロビジョニングには、最大で 2 時間かかる場合があります。

## <a name="launch-cloudsimple-portal"></a>CloudSimple ポータルを起動する

Azure portal から CloudSimple ポータルにアクセスすることができます。  CloudSimple ポータルは、シングル サインオン (SSO) を使って Azure サインインの資格情報で起動されます。  CloudSimple ポータルにアクセスするには、**CloudSimple Service Authorization** アプリケーションを認可する必要があります。  アクセス許可の付与の詳細については、「[CloudSimple Service Authorization アプリケーションに同意する](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)」を参照してください。

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
3. プライベート クラウドを作成する CloudSimple サービスを選択します。
4. 概要から、 **[Go to the CloudSimple portal]\(CloudSimple ポータルに移動する\)** をクリックして CloudSimple ポータルの新しいブラウザー タブを開きます。  メッセージが表示されたら、Azure サインインの資格情報でサインインします。  

    ![CloudSimple ポータルを起動する](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>ポイント対サイト VPN を作成する

ポイント対サイト VPN 接続は、ご利用のコンピューターからプライベート クラウドに接続する最も簡単な方法です。 プライベート クラウドにリモート接続する場合は、ポイント対サイト VPN 接続を使用します。  プライベート クラウドにすばやくアクセスするには、次の手順のようにします。  オンプレミス ネットワークから CloudSimple リージョンへのアクセスは、[サイト間 VPN](vpn-gateway.md) または[Azure ExpressRoute](on-premises-connection.md) を使って行うことができます。

### <a name="create-gateway"></a>ゲートウェイの作成

1. CloudSimple ポータルを起動し、 **[Network]\(ネットワーク\)** を選択します。
2. **[VPN Gateway]\(VPN ゲートウェイ\)** を選択します。
3. **[New VPN Gateway]\(新しい VPN ゲートウェイ\)** をクリックします。

    ![VPN ゲートウェイを作成する](media/create-vpn-gateway.png)

4. **[Gateway configuration]\(ゲートウェイの構成\)** で次の設定を指定して、 **[次へ]** をクリックします。

    * ゲートウェイの種類として **[Point-to-Site VPN]\(ポイント対サイト VPN\)** を選択します。
    * ゲートウェイを識別する名前を入力します。
    * CloudSimple サービスをデプロイする Azure の場所を選択します。
    * ポイント対サイト ゲートウェイ用のクライアント サブネットを指定します。  接続するときは、このサブネットから DHCP アドレスが提供されます。

5. **[Connection/User]\(接続/ユーザー\)** で次の設定を指定して、 **[次へ]** をクリックします。

    * 現在および将来のすべてのユーザーがこのポイント対サイト ゲートウェイ経由でプライベート クラウドにアクセスするのを自動的に許可するには、 **[Automatically add all users]\(すべてのユーザーを自動的に追加する\)** を選択します。 このオプションを選択すると、[User]\(ユーザー\) 一覧のすべてのユーザーが自動的に選択されます。 一覧で個々のユーザーの選択を解除して、自動オプションをオーバーライドできます。
    * 個々のユーザーのみを選択するには、[User]\(ユーザー\) 一覧でチェック ボックスをオンにします。

6. [VLANs/Subnets]\(VLAN/サブネット\) セクションでは、ゲートウェイと接続に対する管理およびユーザーの VLAN/サブネットを指定できます。

    * **[Automatically add]\(自動的に追加する\)** オプションでは、このゲートウェイに対するグローバル ポリシーが設定されます。 現在のゲートウェイに設定が適用されます。 **[Select]\(選択\)** 領域で設定をオーバーライドできます。
    * **[Add management VLANs/Subnets of Private Clouds]\(プライベート クラウドの管理 VLAN/サブネットを追加する\)** を選択します。
    * ユーザー定義のすべての VLAN/サブネットを追加するには、 **[Add user-defined VLANs/Subnets]\(ユーザー定義の VLAN/サブネットを追加する\)** をクリックします。
    * **[Automatically add]\(自動的に追加する\)** のグローバル設定は、 **[Select]\(選択\)** の設定によってオーバーライドされます。

7. **[次へ]** をクリックして、設定を確認します。 変更するには [編集] アイコンをクリックします。
8. **[作成]** をクリックして、VPN ゲートウェイを作成します。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>ポイント対サイト VPN を使用して CloudSimple に接続する

お使いのコンピューターから CloudSimple に接続するには、VPN クライアントが必要です。  Windows 用の [OpenVPN クライアント](https://openvpn.net/community-downloads/)または macOS および OS X 用の [Viscosity](https://www.sparklabs.com/viscosity/download/) をダウンロードします。

1. CloudSimple ポータルを起動し、 **[Network]\(ネットワーク\)** を選択します。
2. **[VPN Gateway]\(VPN ゲートウェイ\)** を選択します。
3. VPN ゲートウェイの一覧で、ポイント対サイト VPN ゲートウェイをクリックします。
4. **[ユーザー]** を選択します。
5. **[Download my VPN configuration]\(VPN 構成をダウンロードする\)** をクリックします。

    ![VPN 構成をダウンロードする](media/download-p2s-vpn-configuration.png)

6. VPN クライアントで構成をインポートします。

    * [Windows クライアントで構成をインポートする](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)方法
    * [macOS または OS X で構成をインポートする](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)方法

7. CloudSimple に接続します。

## <a name="create-a-vlan-for-your-workload-vms"></a>ワークロード VM 用の VLAN を作成する

プライベート クラウドを作成した後は、ワークロード/アプリケーション VM をデプロイする VLAN を作成します。

1. CloudSimple ポータルで、 **[ネットワーク]** を選択します。
2. **[VLAN/Subnets]\(VLAN/サブネット\)** をクリックします。
3. **[Create VLAN/Subnet]\(VLAN/サブネットの作成\)** をクリックします。

    ![VLAN/サブネットを作成する](media/create-new-vlan-subnet.png)

4. 新しい VLAN/サブネットの **[Private Cloud]\(プライベート クラウド\)** を選択します。
5. 一覧から VLAN ID を選択します。  
6. サブネットを識別するサブネット名を入力します。
7. サブネットの CIDR 範囲とマスクを指定します。  この範囲は、既存のサブネットと重複できません。
8. **[送信]** をクリックします。

    ![VLAN/サブネットの作成の詳細](media/create-new-vlan-subnet-details.png)

VLAN/サブネットが作成されます。  この VLAN ID を使って、プライベート クラウドの vCenter に分散ポート グループを作成できるようになります。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>環境を Azure 仮想ネットワークに接続する

CloudSimple では、プライベート クラウドに対する ExpressRoute 回線が提供されます。 Azure 上の仮想ネットワークを ExpressRoute 回線に接続できます。 接続の設定の詳細については、「[Azure Virtual Network Connection using ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/)」 (ExpressRoute を使用した Azure 仮想ネットワークの接続) をご覧ください。

## <a name="sign-in-to-vcenter"></a>vCenter にサインインする

vCenter にサインインして、仮想マシンとポリシーを設定できるようになりました。

1. vCenter にアクセスするには、CloudSimple ポータルから始めます。 ホーム ページの **[Common Tasks]\(一般タスク\)** で、 **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。  プライベート クラウドを選択し、プライベート クラウドで **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。

    ![vSphere Client を起動する](media/launch-vcenter-from-cloudsimple-portal.png)

2. 優先する vSphere クライアントを選択して vCenter にアクセスし、ユーザー名とパスワードでサインインします。  既定値は次のとおりです。
    * ユーザー名: **CloudOwner@cloudsimple.local**
    * Password (パスワード):**CloudSimple123!**  

次の手順での vCenter の画面は、vSphere (HTML5) クライアントのものです。

## <a name="change-your-vcenter-password"></a>vCenter のパスワードを変更する

CloudSimple では初めて vCenter にサインインするときに、パスワードを変更することが推奨されています。  
設定するパスワードは、次の要件を満たしている必要があります。

* 最長有効期間: パスワードは 365 日ごとに変更する必要があります
* 再使用の制限: ユーザーは、以前の 5 つのパスワードを再使用することはできません
* 長さ: 8 から 20 文字
* 特殊文字: 少なくとも 1 つの特殊文字
* アルファベット文字: 少なくとも 1 つの大文字 (A - Z) と、少なくとも 1 つの小文字 (a - z)
* 数字: 少なくとも 1 つの数字 (0 - 9)
* 同じ文字が連続できる最大長: 3

    例:CC または CCC はパスワードの一部として許容されますが、CCCC は許容されません。

要件を満たさないパスワードを設定した場合:

* vSphere Flash Client を使っている場合は、エラーが報告されます
* HTML5 クライアントを使っている場合は、エラーは報告されません。 クライアントでは変更が受け付けられず、古いパスワードが引き続き使用されます。

## <a name="change-nsx-administrator-password"></a>NSX 管理者のパスワードを変更する

NSX Manager は、既定のパスワードでデプロイされます。  プライベート クラウドを作成した後、そのパスワードを変更することをお勧めします。

* ユーザー名: **admin**
* Password (パスワード):**CloudSimple123!**

CloudSimple ポータルで、NSX Manager の完全修飾ドメイン名 (FQDN) と IP アドレスを検索できます。

1. CloudSimple ポータルを起動し、 **[Resources]\(リソース\)** を選択します。
2. 使用するプライベート クラウドをクリックします。
3. **[vSphere management network]\(vSphere 管理ネットワーク\)** を選択します
4. **NSX Manager** の FQDN または IP アドレスを使用し、Web ブラウザーを使って接続します。

    ![NSX Manager の FQDN を検索する](media/private-cloud-nsx-manager-fqdn.png)

パスワードを変更するには、「[NSX Manager Installation ](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)」(NSX Manager のインストール) の手順に従ってください。

## <a name="create-a-port-group"></a>ポート グループを作成する

vSphere で分散ポート グループを作成するには:

1. 「[vSphere Networking Guide](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)」 (vSphere ネットワーク ガイド) の「Add a distributed port group」 (分散ポート グループを追加する) の手順に従ってください。
2. 分散ポート グループを設定するときは、「[ワークロード VM 用の VLAN を作成する](#create-a-vlan-for-your-workload-vms)」で作成した VLAN ID を指定します。

## <a name="next-steps"></a>次の手順

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute を使用してオンプレミスのネットワークに接続する](on-premises-connection.md)
* [オンプレミスからのサイト間 VPN を設定する](vpn-gateway.md)
