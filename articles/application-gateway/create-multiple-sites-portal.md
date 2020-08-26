---
title: チュートリアル:Azure portal を使用して複数の Web サイトをホストする
titleSuffix: Azure Application Gateway
description: このチュートリアルでは、Azure portal を使用して、複数の Web サイトをホストするアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: victorh
ms.openlocfilehash: c73e09e241baff7c4719acfd4257f537e27b010a
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236189"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用して複数の Web サイトをホストするアプリケーション ゲートウェイを作成し、構成します。

[アプリケーション ゲートウェイ](overview.md)を作成するときに、Azure Portal を使用して[複数の Web サイト](multiple-site-overview.md)のホスティングを構成できます。 このチュートリアルでは、仮想マシンを使用してバックエンド アドレス プールを定義します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するように、所有するドメインに基づいてリスナーと規則を構成します。 このチュートリアルでは、複数のドメインを所有していることを前提として、*www.contoso.com* と *www.fabrikam.com* の例を使用します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * ルーティング規則の作成
> * ドメインの CNAME レコードの作成

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="複数サイト アプリケーション ゲートウェイ":::

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

1. Azure portal の左側のメニューにある **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。

2. **[ネットワーク]** を選択し、**おすすめ**のリストで **[Application Gateway]** を選択します。

### <a name="basics-tab"></a>[基本] タブ

1. **[基本]** タブで、次のアプリケーション ゲートウェイの設定に以下の値を入力します。

   - **[リソース グループ]** :リソース グループには、**myResourceGroupAG** を選択します。 存在しない場合は、 **[新規作成]** を選択して作成します。
   - **[アプリケーション ゲートウェイ名]** :アプリケーション ゲートウェイの名前として「*myAppGateway*」と入力します。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="アプリケーション ゲートウェイの作成":::

2.  お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、アプリケーション ゲートウェイの作成と同時に新しい仮想ネットワークを作成します。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

    **[仮想ネットワークの構成]** で、 **[新規作成]** を選択して新しい仮想ネットワークを作成します。 **[仮想ネットワークの作成]** ウィンドウが開いたら、次の値を入力して、仮想ネットワークと 2 つのサブネットを作成します。

    - **Name**:仮想ネットワークの名前として「*myVNet*」と入力します。

    - **[サブネット名]** (Application Gateway サブネット): **[サブネット]** グリッドには、 *[既定]* という名前のサブネットが表示されます。 このサブネットの名前を *myAGSubnet* に変更します。<br>アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。

    - **[サブネット名]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目で、 **[サブネット名]** 列に「*myBackendSubnet*」と入力します。

    - **[アドレス範囲]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目に、*myAGSubnet* のアドレス範囲と重複しないアドレス範囲を入力します。 たとえば、*myAGSubnet* のアドレス範囲が 10.0.0.0/24 の場合は、*myBackendSubnet* のアドレス範囲として「*10.0.1.0/24*」と入力します。

    **[OK]** を選択して **[仮想ネットワークの作成]** ウィンドウを閉じ、仮想ネットワークの設定を保存します。

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="VNet を作成します":::
    
3. **[基本]** タブで、その他の設定の既定値をそのまま使用し、 **[次へ:フロントエンド]** を選択します。

### <a name="frontends-tab"></a>[フロントエンド] タブ

1. **[フロントエンド]** タブで、 **[フロントエンド IP アドレスの種類]** が **[パブリック]** に設定されていることを確認します。 <br>ユース ケースに従って、フロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。
   > [!NOTE]
   > Application Gateway v2 SKU では、 **[パブリック]** フロントエンド IP 構成のみを選択できます。 プライベート フロントエンド IP 構成は、この v2 SKU では現在有効になっていません。

2. **[パブリック IP アドレス]** として **[新規作成]** を選択し、パブリック IP アドレス名として「*myAGPublicIPAddress*」と入力し、 **[OK]** を選択します。 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="VNet を作成します":::

3. **バックエンド** を選択します。

### <a name="backends-tab"></a>[バックエンド] タブ

バックエンド プールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンド プールには、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドがあります。 この例では、アプリケーション ゲートウェイを使用して空のバックエンド プールを作成し、バックエンド ターゲットをバックエンド プールに追加します。

1. **[バックエンド]** タブで、 **[+ バックエンド プールの追加]** を選択します。

2. 開いた **[バックエンド プールの追加]** ウィンドウで、次の値を入力して空のバックエンド プールを作成します。

    - **Name**:バックエンド プールの名前として「*contosoPool*」と入力します。
    - **[ターゲットを持たないバックエンド プールを追加します]** : **[はい]** を選択して、ターゲットを持たないバックエンド プールを作成します。 アプリケーション ゲートウェイを作成した後で、バックエンド ターゲットを追加します。

3. **[バックエンド プールの追加]** ウィンドウで、 **[追加]** を選択してバックエンド プールの構成を保存し、 **[バックエンド]** タブに戻ります。
4. 次に、*fabrikamPool* という名前の別のバックエンド プールを追加します。

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="バックエンドを作成します":::

4. **[バックエンド]** タブで、 **[次へ:構成]** を選択します。

### <a name="configuration-tab"></a>[構成] タブ

**[構成]** タブで、ルーティング規則を使用して作成したフロントエンドとバックエンド プールを接続します。

1. **[ルーティング規則]** 列で **[規則を追加する]** を選択します。

2. 開いた **[ルーティング規則の追加]** ウィンドウで、 **[規則名]** に「*contosoRule*」と入力します。

3. ルーティング規則にはリスナーが必要です。 **[ルーティング規則の追加]** ウィンドウ内の **[リスナー]** タブで、リスナーの次の値を入力します。

    - **[リスナー名]** :リスナーの名前として「*contosoListener*」と入力します。
    - **[フロントエンド IP]** : **[パブリック]** を選択して、フロントエンド用に作成したパブリック IP を選択します。

   **[追加設定]** :
   - **[リスナーの種類]** :複数のサイト
   - **[ホスト名]** : **www.contoso.com**

   **[リスナー]** タブで他の設定の既定値をそのまま使用し、 **[バックエンド ターゲット]** タブを選択して、ルーティング規則の残りの部分を構成します。

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="ルーティング規則を作成します":::

4. **[バックエンド ターゲット]** タブで、 **[バックエンド ターゲット]** として **[contosoPool]** を選択します。

5. **[HTTP 設定]** には **[新規作成]** を選択して新しい HTTP 設定を作成します。 HTTP 設定によって、ルーティング規則の動作が決まります。 開いた **[HTTP 設定の追加]** ウィンドウで、 **[HTTP 設定名]** として「*contosoHTTPSetting*」と入力します。 **[HTTP 設定の追加]** ウィンドウで他の設定の既定値をそのまま使用し、 **[追加]** を選択して **[ルーティング規則の追加]** ウィンドウに戻ります。 

6. **[ルーティング規則の追加]** ウィンドウで **[追加]** を選択してルーティング規則を保存し、 **[構成]** タブに戻ります。
7. **[ルールの追加]** を選択し、Fabrikam 用に同様のルール、リスナー、バックエンドターゲット、および HTTP 設定を追加します。

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam 規則":::

7. **タグ**、**次へ:確認と作成** をクリックします。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認と作成 ]** タブの設定を確認し、 **[作成]** を選択して、仮想ネットワーク、パブリック IP アドレス、およびアプリケーションゲートウェイを作成します。 Azure によるアプリケーション ゲートウェイの作成には数分かかる場合があります。

次のセクションに進む前に、デプロイが正常に完了するまで待機します。

## <a name="add-backend-targets"></a>バックエンド ターゲットの追加

この例では、ターゲットのバックエンドとして仮想マシンを使用します。 既存の仮想マシンを使用することも、新しい仮想マシンを作成することもできます。 Azure がアプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。

バックエンド ターゲットを追加するには、次のことを行います。

1. バックエンド サーバーとして使用される 2 つの新しい VM (*contosoVM* と *fabrikamVM*) を作成します。
2. IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。
3. バックエンド サーバーをバックエンド プールに追加します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure portal で、 **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
2. **[コンピューティング]** を選択し、 **[人気順]** の一覧で **[Windows Server 2016 Datacenter]** を選択します。 **[仮想マシンの作成]** ページが表示されます。<br>Application Gateway は、バックエンド プールで使用されているあらゆる種類の仮想マシンにトラフィックをルートできます。 この例では、Windows Server 2016 Datacenter を使用します。
3. **[基本]** タブで、次の仮想マシンの設定に以下の値を入力します。

    - **[リソース グループ]** :リソース グループ名には、**myResourceGroupAG** を選択します。
    - **[仮想マシン名]** : 仮想マシンの名前として「*contosoVM*」と入力します。
    - **[ユーザー名]** : 管理者のユーザー名として「*azureuser*」と入力します。
    - **パスワード**:管理者パスワードとして「*Azure123456!* 」と入力します 。
4. 他の既定値をそのまま使用し、 **[Next: ディスク]** を選択します。  
5. **[ディスク]** タブの既定値をそのまま使用し、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。
6. **[ネットワーク]** タブで、 **[仮想ネットワーク]** に **myVNet** が選択されていること、および **[サブネット]** が **myBackendSubnet** に設定されていることを確認します。 他の既定値をそのまま使用し、 **[Next: 管理]** を選択します。<br>Application Gateway は、それが存在している仮想ネットワークの外部にあるインスタンスと通信できますが、IP 接続があることを確認する必要があります。
7. **[管理]** タブで、 **[ブート診断]** を **[オフ]** に設定します。 他の既定値をそのまま使用し、 **[確認および作成]** を選択します。
8. **[確認および作成]** タブで、設定を確認し、検証エラーを修正してから、 **[作成]** を選択します。
9. 仮想マシンの作成が完了するのを待って先に進みます。

### <a name="install-iis-for-testing"></a>テスト用の IIS のインストール

この例では、Azure がアプリケーション ゲートウェイを正常に作成したことを確認するためにのみ、仮想マシンに IIS をインストールします。

1. [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)を開きます。 そのためには、Azure portal の上部のナビゲーション バーで **[Cloud Shell]** を選択して、ドロップダウン リストで **[PowerShell]** を選択します。 

    ![カスタム拡張機能のインストール](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 2 つ目の仮想マシンを作成し、お客様が先ほど完了した手順を使用して IIS をインストールします。 仮想マシンの名前と、**Set-AzVMExtension** コマンドレットの **VMName** 設定には、*fabrikamVM* を使用します。

### <a name="add-backend-servers-to-backend-pools"></a>バックエンド プールヘのバックエンド サーバーの追加

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[バックエンド プール]** を選択します。

3. **[contosoPool]** を選択します。

4. **[ターゲット]** の下のドロップダウン リストで **[仮想マシン]** を選択します。

5. **[仮想マシン]** と **[ネットワーク インターフェイス]** で、**contosoVM** 仮想マシンとその関連するネットワーク インターフェイスをドロップダウン リストから選択します。

    ![バックエンド サーバーの追加](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. **[保存]** を選択します。
7. 手順を繰り返し、*fabrikamVM* とインターフェイスを *fabrikamPool* に追加します。

デプロイが完了するまで待ってから次の手順に進んでください。

## <a name="create-a-www-a-record-in-your-domains"></a>実際のドメイン内の www A レコードの作成

パブリック IP アドレスを使用してアプリケーション ゲートウェイを作成した後は、IP アドレスを取得し、これを使用して実際のドメインに A レコードを作成できます。 

1. **[すべてのリソース]** 、 **[myAGPublicIPAddress]** の順にクリックします。

    ![アプリケーション ゲートウェイの DNS アドレスの記録](./media/create-multiple-sites-portal/public-ip.png)

2. IP アドレスをコピーし、これを実際のドメイン内の新しい *www* A レコードの値として使用します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. ブラウザーのアドレス バーにドメイン名を入力します。 `http://www.contoso.com` など。

    ![アプリケーション ゲートウェイの contoso サイトをテストする](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. アドレスをもう 1 つのドメインに変更します。次の例のように表示されます。

    ![アプリケーション ゲートウェイの fabrikam サイトをテストする](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除する際は、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。

リソース グループを削除するには:

1. Azure portal の左側のメニューにある **[リソース グループ]** を選択します。
2. **[リソース グループ]** ページで、リストの **myResourceGroupAG** を探して選択します。
3. **[リソース グループ] ページ**で、 **[リソース グループの削除]** を選択します。
4. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「*myResourceGroupAG*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Application Gateway で実行できる操作の詳細を確認する](application-gateway-introduction.md)