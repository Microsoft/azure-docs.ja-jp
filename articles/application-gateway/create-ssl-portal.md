---
title: チュートリアル - SSL ターミネーションでアプリケーション ゲートウェイを構成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して、アプリケーション ゲートウェイを構成し、SSL ターミネーションの証明書を追加する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/31/2019
ms.author: victorh
ms.openlocfilehash: b558384fe6bc86cd7b0ebd640407557e23f68ddd
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194565"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用して SSL ターミネーションでアプリケーション ゲートウェイを構成する

Azure Portal を使用して、バックエンド サーバーに仮想マシンを使用する SSL ターミネーションの証明書で、[アプリケーション ゲートウェイ](overview.md) を構成することができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * 証明書でのアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * アプリケーション ゲートウェイのテスト

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

このセクションでは、[New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) を使用して、自己署名証明書を作成します。 アプリケーション ゲートウェイのリスナーを作成するときに、証明書を Azure portal にアップロードします。

ローカル コンピューターでは、管理者として Windows PowerShell ウィンドウを開きます。 次のコマンドを実行して、証明書を作成します。

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

次のような応答が表示されます。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

[Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) と返されたサムプリントを使用して、pfx ファイルを証明書からエクスポートします。

> [!NOTE]
> .pfx ファイルのパスワードに特殊文字は一切使用しないでください。 サポートされるのは英数字のみです。

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

1. Azure portal の左側のメニューにある **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。

2. **[ネットワーク]** を選択し、**おすすめ**のリストで **[Application Gateway]** を選択します。

### <a name="basics-tab"></a>[基本] タブ

1. **[基本]** タブで、次のアプリケーション ゲートウェイの設定に以下の値を入力します。

   - **[リソース グループ]** :リソース グループには、**myResourceGroupAG** を選択します。 存在しない場合は、 **[新規作成]** を選択して作成します。
   - **[アプリケーション ゲートウェイ名]** :アプリケーション ゲートウェイの名前として「*myAppGateway*」と入力します。

     ![新しいアプリケーション ゲートウェイの作成:基本](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、アプリケーション ゲートウェイの作成と同時に新しい仮想ネットワークを作成します。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

    **[仮想ネットワークの構成]** で、 **[新規作成]** を選択して新しい仮想ネットワークを作成します。 **[仮想ネットワークの作成]** ウィンドウが開いたら、次の値を入力して、仮想ネットワークと 2 つのサブネットを作成します。

    - **[名前]** :仮想ネットワークの名前として「*myVNet*」と入力します。

    - **[サブネット名]** (Application Gateway サブネット): **[サブネット]** グリッドには、 *[既定]* という名前のサブネットが表示されます。 このサブネットの名前を *myAGSubnet* に変更します。<br>アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。

    - **[サブネット名]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目で、 **[サブネット名]** 列に「*myBackendSubnet*」と入力します。

    - **[アドレス範囲]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目に、*myAGSubnet* のアドレス範囲と重複しないアドレス範囲を入力します。 たとえば、*myAGSubnet* のアドレス範囲が 10.0.0.0/24 の場合は、*myBackendSubnet* のアドレス範囲として「*10.0.1.0/24*」と入力します。

    **[OK]** を選択して **[仮想ネットワークの作成]** ウィンドウを閉じ、仮想ネットワークの設定を保存します。

     ![新しいアプリケーションゲートウェイの作成: 仮想ネットワーク](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. **[基本]** タブで、その他の設定の既定値をそのまま使用し、 **[次へ:フロントエンド]** を選択します。

### <a name="frontends-tab"></a>[フロントエンド] タブ

1. **[フロントエンド]** タブで、 **[フロントエンド IP アドレスの種類]** が **[パブリック]** に設定されていることを確認します。 <br>ユース ケースに従って、フロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。
   > [!NOTE]
   > Application Gateway v2 SKU では、 **[パブリック]** フロントエンド IP 構成のみを選択できます。 プライベート フロントエンド IP 構成は、この v2 SKU では現在有効になっていません。

2. **[パブリック IP アドレス]** として **[新規作成]** を選択し、パブリック IP アドレス名として「*myAGPublicIPAddress*」と入力し、 **[OK]** を選択します。 

     ![新しいアプリケーション ゲートウェイの作成: フロントエンド](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. **[次へ:バックエンド]** を選択します。

### <a name="backends-tab"></a>[バックエンド] タブ

バックエンド プールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 この例では、アプリケーション ゲートウェイを使用して空のバックエンド プールを作成し、バックエンド ターゲットをバックエンド プールに追加します。

1. **[バックエンド]** タブで、 **[+ バックエンド プールの追加]** を選択します。

2. 開いた **[バックエンド プールの追加]** ウィンドウで、次の値を入力して空のバックエンド プールを作成します。

    - **[名前]** :バックエンド プールの名前として「*myBackendPool*」と入力します。
    - **[ターゲットを持たないバックエンド プールを追加します]** : **[はい]** を選択して、ターゲットを持たないバックエンド プールを作成します。 アプリケーション ゲートウェイを作成した後で、バックエンド ターゲットを追加します。

3. **[バックエンド プールの追加]** ウィンドウで、 **[追加]** を選択してバックエンド プールの構成を保存し、 **[バックエンド]** タブに戻ります。

     ![新しいアプリケーション ゲートウェイの作成: バックエンド](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. **[バックエンド]** タブで、 **[次へ:構成]** を選択します。

### <a name="configuration-tab"></a>[構成] タブ

**[構成]** タブで、ルーティング規則を使用して作成したフロントエンドとバックエンド プールを接続します。

1. **[ルーティング規則]** 列で **[規則を追加する]** を選択します。

2. 開いた **[ルーティング規則の追加]** ウィンドウで、 **[規則名]** に「*myRoutingRule*」と入力します。

3. ルーティング規則にはリスナーが必要です。 **[ルーティング規則の追加]** ウィンドウ内の **[リスナー]** タブで、リスナーの次の値を入力します。

    - **[リスナー名]** :リスナーの名前として「*myListener*」と入力します。
    - **[フロントエンド IP]** : **[パブリック]** を選択して、フロントエンド用に作成したパブリック IP を選択します。
    - **プロトコル**: **[HTTPS]** を選択します。
    - **ポート**:ポートに対して 443 が入力されていることを確認します。

   **[HTTPS 証明書]** で以下の操作を行います。

   - **[PFX 証明書ファイル]** - 前に作成した c:\appgwcert.pfx ファイルを参照して選択します。
   - **[証明書名]** - 証明書の名前として「*mycert1*」と入力します。
   - **[パスワード]** - 「*Azure123456!* 」と 入力します。
  
      **[リスナー]** タブで他の設定の既定値をそのまま使用し、 **[バックエンド ターゲット]** タブを選択して、ルーティング規則の残りの部分を構成します。

   ![新しいアプリケーション ゲートウェイの作成: リスナー](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. **[バックエンド ターゲット]** タブで、 **[バックエンド ターゲット]** の **[myBackendPool]** を選択します。

5. **[HTTP 設定]** には **[新規作成]** を選択して新しい HTTP 設定を作成します。 HTTP 設定によって、ルーティング規則の動作が決まります。 開いた **[HTTP 設定の追加]** ウィンドウで、 **[HTTP 設定名]** として「*myHTTPSetting*」と入力します。 **[HTTP 設定の追加]** ウィンドウで他の設定の既定値をそのまま使用し、 **[追加]** を選択して **[ルーティング規則の追加]** ウィンドウに戻ります。 

     ![新しいアプリケーション ゲートウェイの作成:HTTP 設定](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. **[ルーティング規則の追加]** ウィンドウで **[追加]** を選択してルーティング規則を保存し、 **[構成]** タブに戻ります。

     ![新しいアプリケーションゲートウェイの作成: ルーティング規則](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. **[次へ:タグ]** 、 **[次へ:確認と作成]** をクリックします。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認と作成 ]** タブの設定を確認し、 **[作成]** を選択して、仮想ネットワーク、パブリック IP アドレス、およびアプリケーションゲートウェイを作成します。 Azure によるアプリケーション ゲートウェイの作成には数分かかる場合があります。 次のセクションに進む前に、デプロイが正常に完了するまで待機します。

## <a name="add-backend-targets"></a>バックエンド ターゲットの追加

この例では、ターゲットのバックエンドとして仮想マシンを使用します。 既存の仮想マシンを使用することも、新しい仮想マシンを作成することもできます。 Azure がアプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。

これを行うには、次を実行します。

1. バックエンド サーバーとして使用される 2 つの新しい VM (*myVM* と *myVM2*) を作成します。
2. IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。
3. バックエンド サーバーをバックエンド プールに追加します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure portal で、 **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
2. **[人気順]** の一覧で **[Windows Server 2016 Datacenter]** を選択します。 **[仮想マシンの作成]** ページが表示されます。

   Application Gateway は、バックエンド プールで使用されているあらゆる種類の仮想マシンにトラフィックをルートできます。 この例では、Windows Server 2016 Datacenter を使用します。

1. **[基本]** タブで、次の仮想マシンの設定に以下の値を入力します。

    - **[リソース グループ]** :リソース グループ名には、**myResourceGroupAG** を選択します。
    - **[仮想マシン名]** : 仮想マシンの名前として「*myVM*」と入力します。
    - **[ユーザー名]** : 管理者のユーザー名として「*azureuser*」と入力します。
    - **Password**:管理者パスワードとして「*Azure123456!* 」と入力します 。
4. 他の既定値をそのまま使用し、 **[Next: ディスク]** を選択します。  
5. **[ディスク]** タブの既定値をそのまま使用し、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。
6. **[ネットワーク]** タブで、 **[仮想ネットワーク]** に **myVNet** が選択されていること、および **[サブネット]** が **myBackendSubnet** に設定されていることを確認します。 他の既定値をそのまま使用し、 **[Next: 管理]** を選択します。

   Application Gateway は、それが存在している仮想ネットワークの外部にあるインスタンスと通信できますが、IP 接続があることを確認する必要があります。
1. **[管理]** タブで、 **[ブート診断]** を **[オフ]** に設定します。 他の既定値をそのまま使用し、 **[確認および作成]** を選択します。
2. **[確認および作成]** タブで、設定を確認し、検証エラーを修正してから、 **[作成]** を選択します。
3. 続行する前に、デプロイが完了するまで待ちます。

### <a name="install-iis-for-testing"></a>テスト用の IIS のインストール

この例では、Azure がアプリケーション ゲートウェイを正常に作成したことを確認するためにのみ、仮想マシンに IIS をインストールします。

1. [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)を開きます。 そのためには、Azure portal の上部のナビゲーション バーで **[Cloud Shell]** を選択して、ドロップダウン リストで **[PowerShell]** を選択します。 

    ![カスタム拡張機能のインストール](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 2 番目の仮想マシンを作成し、お客様が先ほど完了した手順を使用して IIS をインストールします。 仮想マシンの名前と、**Set-AzVMExtension** コマンドレットの **VMName** 設定には、*myVM2* を使用します。

### <a name="add-backend-servers-to-backend-pool"></a>バックエンド プールヘのバックエンド サーバーの追加

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[バックエンド プール]** を選択します。

3. **[myBackendPool]** を選択します。

4. **[ターゲット]** の下のドロップダウン リストで **[仮想マシン]** を選択します。

5. **[仮想マシン]** と **[ネットワーク インターフェイス]** で、**myVM** 仮想マシンおよび **myVM2** 仮想マシンとそれらの関連するネットワーク インターフェイスをドロップダウン リストから選択します。

    ![バックエンド サーバーの追加](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. **[保存]** を選択します。

7. デプロイが完了するまで待ってから次の手順に進んでください。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]** を選択し、 **[myAGPublicIPAddress]** を選択します。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/create-ssl-portal/application-gateway-ag-address.png)

2. ブラウザーのアドレス バーに「*https://\<自分のアプリケーション ゲートウェイ IP アドレス\>* 」と入力します。

   自己署名証明書を使用した場合、セキュリティ警告を受け入れるには **[詳細]** (Chrome では **[Advanced]\(詳細設定\)** ) を選択し、Web ページへ移動します。

    ![セキュリティに関する警告](./media/create-ssl-portal/application-gateway-secure.png)

    セキュリティで保護された IIS Web サイトは、次の例のように表示されます。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Application Gateway SSL サポートの詳細を学習します](ssl-overview.md)