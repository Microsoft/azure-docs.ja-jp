---
title: チュートリアル:ポート フォワーディングの構成 - Azure portal
titleSuffix: Azure Load Balancer
description: このチュートリアルでは、Azure Load Balancer を使用してポート フォワーディングを構成し、Azure 仮想ネットワーク内の VM への接続を作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: dcb151c8be0ab3a2393d0659b75985a92ac60507
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "82207889"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>チュートリアル:ポータルを使用して Azure Load Balancer のポート フォワーディングを構成する

ポート フォワーディングを使用すると、Azure Load Balancer のパブリック IP アドレスとポート番号を使って Azure 仮想ネットワーク内の仮想マシン (VM) に接続できます。 

このチュートリアルでは、Azure ロード バランサーのポート フォワーディングを設定します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数の VM にネットワーク トラフィックを分散するパブリック Standard ロード バランサ―を作成する。 
> * ネットワーク セキュリティ グループ (NSG) 規則を使用して仮想ネットワークと VM を作成する。 
> * ロード バランサーのバックエンド アドレス プールに VM を追加する。
> * ロード バランサーの正常性プローブとトラフィック規則を作成する。
> * ロード バランサーの受信 NAT ポート フォワーディング規則を作成する。
> * IIS を VM にインストールして構成し、負荷分散とポート フォワーディングの動作を確認する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

このチュートリアルのすべての手順は、Azure portal ([https://portal.azure.com](https://portal.azure.com)) にサインインして行います。

## <a name="create-a-standard-load-balancer"></a>Standard ロード バランサ―を作成する

まず、複数の VM にトラフィックを分散できるパブリック Standard ロード バランサ―を作成します。 Standard ロード バランサ―でサポートされるのは Standard パブリック IP アドレスだけです。 Standard ロード バランサ―を作成するときに、新しい Standard パブリック IP アドレスも作成します。これがロード バランサーのフロントエンドとして構成され、既定で **LoadBalancerFrontEnd** という名前になります。 

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[ロード バランサー]** の順にクリックします。
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | 設定                 | Value                                              |
    | ---                     | ---                                                |
    | サブスクリプション               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択して、テキスト ボックスに「*MyResourceGroupLB*」と入力します。|
    | 名前                   | *myLoadBalancer*                                   |
    | リージョン         | **[西ヨーロッパ]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | **[Standard]** を選択します。                          |
    | パブリック IP アドレス | **[新規作成]** を選択します。 |
    | パブリック IP アドレス名              | テキスト ボックスに「*myPublicIP*」と入力します。   |
    |可用性ゾーン| **[ゾーン冗長]** を選択します。    |
     
    >[!NOTE]
     >お客様のロード バランサーとそのすべてのリソースは、必ず Availability Zones がサポートされている場所に作成してください。 詳細については、「[可用性ゾーンをサポートしているリージョン](../availability-zones/az-region.md)」を参照してください。 

3. **[確認と作成]** タブで、 **[作成]** をクリックします。  
  
## <a name="create-and-configure-back-end-servers"></a>バックエンド サーバーを作成して構成する

2 つの仮想マシンを含んだ仮想ネットワークを作成し、お客様のロード バランサーのバックエンド プールに VM を追加します。 

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (既存のリソース グループを選択) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 西ヨーロッパ      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>VM を作成してロード バランサーのバックエンド プールに追加する

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2016 Datacenter]** の順に選択します。 
   
1. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : ドロップダウンから **[MyResourceGroupLB]** を選択します。
   - **[仮想マシン名]** : 「*MyVM1*」と入力します。
   - **[リージョン]** : **[西ヨーロッパ]** を選択します。 
   - **[ユーザー名]** : 「*azureuser*」と入力します。
   - **パスワード**:「*Azure1234567*」と入力します。 
     **パスワードの確認**フィールドに、パスワードを再度入力します。
   
1. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。 
   
   以下が選択されていることを確認します。
   - **仮想ネットワーク**:**MyVNet**
   - **サブネット**:**MyBackendSubnet**
   
1. **[パブリック IP]** で **[新規作成]** を選択し、**[パブリック IP アドレスの作成]** ページで **[Standard]** を選択して、**[OK]** を選択します。 
   
1. **[ネットワーク セキュリティ グループ]** の **[詳細設定]** を選択し、ファイアウォールの一種であるネットワーク セキュリティ グループ (NSG) を新しく作成します。 
   1. **ネットワーク セキュリティ グループの構成**フィールドで、 **[新規作成]** を選択します。 
   1. 「*MyNetworkSecurityGroup*」と入力し、 **[OK]** を選択します。 
   
   >[!NOTE]
   >NSG にはあらかじめ、リモート デスクトップ (RDP) のポート 3389 を開放する受信規則が既定で含まれていることに注目してください。
   
1. お客様が作成するロード バランサーのバックエンド プールに VM を追加します。
   
   1. **[負荷分散]**  >  **[Place this virtual machine behind an existing load balancing solution?]\(この仮想マシンを既存の負荷分散ソリューションの内側に配置しますか?\)** で **[はい]** を選択します。 
   1. **[負荷分散のオプション]** で、ドロップダウンから **[Azure Load Balancer]** を選択します。 
   1. **[Select a load balancer]\(ロード バランサーの選択\)** で、ドロップダウンから **[MyLoadBalancer]** を選択します。 
   1. **[Select a backend pool]\(バックエンド プールの選択\)** で **[新規作成]** を選択し、「*MyBackendPool*」と入力して、**[作成]** を選択します。 
   
   ![仮想ネットワークの作成](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. **[管理]** タブまたは **[次へ]**  >  **[管理]** を選択します。 **[監視]** で **[ブート診断]** を **[オフ]** に設定します。
   
1. **[Review + create]\(レビュー + 作成\)** を選択します。
   
1. 設定を確認し、検証が成功したら、**[作成]** を選択します。 

1. 手順に従って *MyVM2* という名前の 2 つ目の VM を作成します。他のすべての設定は MyVM1 と同じです。 
   
   **[ネットワーク セキュリティ グループ]** で **[詳細設定]** を選択した後、お客様が既に作成した **[MyNetworkSecurityGroup]** をドロップダウンから選択します。 
   
   **[Select a backend pool]\(バックエンド プールの選択\)** で、**[MyBackendPool]** が選択されていることを確認します。 

### <a name="create-an-nsg-rule-for-the-vms"></a>VM に対する NSG 規則を作成する

VM に対してインバウンド インターネット (HTTP) 接続を許可するネットワーク セキュリティ グループ (NSG) 規則を作成します。

>[!NOTE]
>NSG にはあらかじめ、リモート デスクトップ (RDP) のポート 3389 を開放する規則が既定で含まれています。

1. 左側のメニューで、**[すべてのリソース]** を選択します。 リソースの一覧から、**MyResourceGroupLB** リソース グループの **MyNetworkSecurityGroup** を選択します。
   
1. **[設定]** で **[受信セキュリティ規則]** を選択し、 **[追加]** を選択します。
   
1. **[受信セキュリティ規則の追加]** ダイアログで、以下を入力または選択します。
   
   - **[ソース]**: **[Service Tag]\(サービス タグ\)** を選択します。  
   - **[ソース サービス タグ]**: **[インターネット]** を選択します。 
   - **[宛先ポート範囲]**: 「*80*」と入力します。
   - **プロトコル**: **[TCP]** を選択します。 
   - **[アクション]**: **[許可]** を選択します。  
   - **[優先度]**: 「*100*」と入力します。 
   - **[名前]**: 「*MyHTTPRule*」を入力します。 
   - **[説明]**: 「*HTTP を許可する*」と入力します。 
   
1. **[追加]** を選択します。 
   
   ![NSG 規則を作成する](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、ロード バランサーのバックエンド プールを調べて、ロード バランサ―の正常性プローブとトラフィック規則を構成します。

### <a name="view-the-back-end-address-pool"></a>バックエンド アドレス プールを確認する

ロード バランサーでは、複数の VM にトラフィックを分散するためにバックエンド アドレス プールが使用されます。ここには、ロード バランサーに接続された仮想ネットワーク インターフェイス (NIC) の IP アドレスが含まれています。 

お客様はご自分のロード バランサーのバックエンド プールを作成しました。また VM を作成したとき、それに VM を追加しました。 バックエンド プールの作成と VM の追加/削除は、ロード バランサーの **[バックエンド プール]** ページで行うこともできます。 

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[バックエンド プール]** を選択します。
   
1. **[バックエンド プール]** ページで、 **[MyBackendPool]** を展開し、 **[VM1]** と **[VM2]** の両方が表示されていることを確認します。

1. **[MyBackendPool]** を選択します。 
   
   **[MyBackendPool]** ページの **[仮想マシン]** と **[IP アドレス]** で、利用可能な VM をプールから削除したり、プールに追加したりすることができます。

新しいバックエンド プールは、**[バックエンド プール]** ページの **[追加]** を選択することで作成できます。

### <a name="create-a-health-probe"></a>正常性プローブの作成

ロード バランサーで VM の状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[正常性プローブ]** 、 **[追加]** の順に選択します。
   
1. **[正常性プローブの追加]** ページで、以下の値を入力または選択します。
   
   - **Name**:「*MyHealthProbe*」と入力します。
   - **プロトコル**:ドロップダウンから **[HTTP]** を選択します。 
   - **ポート**:「*80*」と入力します。 
   - **パス**:既定の URI の */* をそのまま使用します。 この値は任意の別の URI に置き換えることができます。 
   - **間隔**: 「*15*」と入力します。 [間隔] は、プローブの試行の間隔を示す秒数です。
   - **[異常のしきい値]** : 「*2*」と入力します。 この値は、プローブの連続する失敗回数です (この回数を超えると、VM は異常と見なされます)。
   
1. **[OK]** を選択します。
   
   ![プローブの追加](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、VM に対するトラフィックの分散方法を定義します。 この規則で、着信トラフィック用のフロントエンド IP 構成、トラフィックを受信するためのバックエンド IP プール、必要な発信元ポートと宛先ポートを定義します。 

**MyLoadBalancerRule** という名前のロード バランサー規則は、フロントエンド **LoadBalancerFrontEnd** のポート 80 をリッスンしています。 この規則は、ネットワーク トラフィックをバックエンド アドレス プール **MyBackendPool** に、またポート 80 上で送信します。 

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[負荷分散規則]** 、 **[追加]** の順に選択します。
   
1. **[負荷分散規則の追加]** ページで、次の値を入力または選択します。
   
   - **Name**:「*MyLoadBalancerRule*」と入力します。
   - **プロトコル**: **[TCP]** を選択します。
   - **ポート**:「*80*」と入力します。
   - **[バックエンド ポート]** :「*80*」と入力します。
   - **[バックエンド プール]** : **[MyBackendPool]** を選択します。
   - **[正常性プローブ]** : **[MyHealthProbe]** を選択します。 
   
1. **[OK]** を選択します。
   
   ![ロード バランサー規則の追加](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>受信 NAT ポート フォワーディング規則を作成する

フロントエンド IP アドレスの特定のポートからバックエンド VM の特定のポートにトラフィックを転送する、ロード バランサーの受信ネットワーク アドレス変換 (NAT) 規則を作成します。

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で **[受信 NAT 規則]** を選択し、**[追加]** を選択します。 
   
1. **[受信 NAT 規則の追加]** ページで、次の値を入力または選択します。
   
   - **Name**:「*MyNATRuleVM1*」と入力します。
   - **ポート**:「*4221*」と入力します。
   - **[ターゲット仮想マシン]** : ドロップ ダウンから **[MyVM1]** を選択します。
   - **[ネットワーク IP 構成]** : ドロップ ダウンから **[ipconfig1]** を選択します。
   - **[ポート マッピング]** : **[カスタム]** を選択します。
   - **[ターゲット ポート]** : 「*3389*」と入力します。
   
1. **[OK]** を選択します。
   
1. 手順を繰り返して、"*MyNATRuleVM2*" という名前の受信 NAT 規則を追加します。 **[ポート]** には *4222* を、 **[ターゲット仮想マシン]** には **MyVM2** を使用してください。

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

このセクションでは、バックエンド サーバーにインターネット インフォメーション サービス (IIS) をインストールし、既定の Web ページをカスタマイズしてマシン名が表示されるようにします。 その後、ロード バランサーのパブリック IP アドレスを使用してロード バランサーをテストします。 

各バックエンド VM は、既定の IIS Web ページの異なるバージョンを提供するため、ロード バランサーが 2 つの VM 間で要求を分散していることがわかります。

### <a name="connect-to-the-vms-with-rdp"></a>RDP を使用して VM に接続する

リモート デスクトップ (RDP) を使用して各 VM に接続します。 

1. ポータルの左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧の **MyResourceGroupLB** リソース グループから各 VM を選択します。
   
1. **[概要]** ページで **[接続]** を選択してから **[RDP ファイルのダウンロード]** を選択します。 
   
1. ダウンロードした RDP ファイルを開き、 **[接続]** を選択します。
   
1. [Windows セキュリティ] ウィンドウで、 **[その他]** 、 **[別のアカウントを使用する]** の順に選択します。 
   
   ユーザー名に「*azureuser*」、パスワードに「*Azure1234567*」を入力して **[OK]** を選択します。
   
1. 証明書のプロンプトが表示されたら **[はい]** を選択します。 
   
   VM デスクトップが新しいウィンドウに開きます。 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>IIS をインストールして既定の IIS Web ページを置き換える 

PowerShell を使用して IIS をインストールし、既定の IIS Web ページを、VM の名前が表示されるページに置き換えます。

1. MyVM1 と MyVM2 上で、 **[スタート]** メニューから **Windows PowerShell** を起動します。 

2. 次のコマンドを実行して IIS をインストールし、既定の IIS Web ページを置き換えます。
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. **[切断]** を選択して、MyVM1 と MyVM2 との RDP 接続を閉じます。 VM をシャットダウンしないでください。

### <a name="test-load-balancing"></a>負荷分散をテストする

1. ポータルにある **MyLoadBalancer** の **[概要]** ページで、**[パブリック IP アドレス]** のパブリック IP アドレスをコピーします。 アドレスにカーソルを置き、 **[コピー]** アイコンを選択してコピーします。 この例では **40.67.218.235** です。 
   
1. ロード バランサーのパブリック IP アドレス (*40.67.218.235*) をご自分のインターネット ブラウザーのアドレス バーに貼り付けるか入力します。 
   
   カスタマイズされた IIS Web サーバーの既定のページがブラウザーに表示されます。 **Hello World from MyVM1** または **Hello World from MyVM2** というメッセージが表示されます。
   
   ![新しい IIS の既定のページ](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. ブラウザーを更新して、ロード バランサーによって VM 間でトラフィックが分散されていることを確認します。 ロード バランサーによって要求が各バックエンド VM に分散されている場合、**MyVM1** ページが表示されるときと、**MyVM2** ページが表示されるときがあります。
   
   >[!NOTE]
   >場合によっては試行する間に、ブラウザーのキャッシュをクリアするか、新しいブラウザー ウィンドウを開く必要があります。

## <a name="test-port-forwarding"></a>ポート フォワーディングをテストする

ポート フォワーディングを使用すると、ロード バランサーの IP アドレスと、NAT 規則で定義されたフロントエンド ポートの値を使って、バックエンド VM にリモート デスクトップ接続できます。 

1. ポータルにある **MyLoadBalancer** の **[概要]** ページで、そのパブリック IP アドレスをコピーします。 アドレスにカーソルを置き、 **[コピー]** アイコンを選択してコピーします。 この例では **40.67.218.235** です。 
   
1. コマンド プロンプトを開き、次のコマンドを使用して、MyVM2 とのリモート デスクトップ セッションを作成します。ロード バランサーのパブリック IP アドレスと、お客様が VM の NAT 規則で定義したフロントエンド ポートを使用します。 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. ダウンロードされた RDP ファイルを開き、 **[接続]** を選択します。
   
1. [Windows セキュリティ] ウィンドウで、 **[その他]** 、 **[別のアカウントを使用する]** の順に選択します。 
   
   ユーザー名に「*azureuser*」、パスワードに「*Azure1234567*」を入力して **[OK]** を選択します。
   
1. 証明書のプロンプトが表示されたら **[はい]** を選択します。 
   
   MyVM2 デスクトップが新しいウィンドウで開きます。 

受信 NAT 規則 **MyNATRuleVM2** によって、ロード バランサーのフロントエンド ポート 4222 から MyVM2 のポート 3389 (RDP ポート) へとトラフィックが誘導されるため、RDP 接続は成功します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったときにロード バランサーと関連するすべてのリソースを削除するには、**MyResourceGroupLB** リソース グループを開き、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Standard パブリック ロード バランサーを作成しました。 ネットワーク リソース、バックエンド サーバー、正常性プローブ、およびロードバランサーの規則を作成および構成しました。 バックエンド VM に IIS をインストールし、ロード バランサーのパブリック IP アドレスを使用してロード バランサーをテストしました。 ロード バランサーの指定されたポートからバックエンド VM のポートへのポート フォワーディングを設定し、テストしました。 

Azure Load Balancer についてさらに学習するには、Load Balancer の他のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
