---
title: 内部ロード バランサー (ILB) エンドポイントを構成する
titleSuffix: Azure Application Gateway
description: この記事では、プライベート フロントエンド IP アドレスを使用して Application Gateway を構成する方法に関する情報を提供します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 01/30/2020
ms.author: victorh
ms.openlocfilehash: f56929e14aef34f675139782328ed5c559df12c7
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198600"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>内部ロード バランサー (ILB) エンドポイントでアプリケーション ゲートウェイを構成する

Azure Application Gateway は、インターネットに接続する VIP のほか、インターネットに接続されていない内部エンドポイントを使用して構成できます。 内部エンドポイントは、フロントエンドのプライベート IP アドレスを使用します。これは、*内部ロードバランサー (ILB) エンドポイント* とも呼ばれます。

フロントエンド プライベート IP アドレスを使用したゲートウェイの構成は、インターネットに接続されていない基幹業務アプリケーションで便利です。 また、セキュリティの境界でインターネットに接続されていない多階層アプリケーション内のサービスや階層でも便利ですが、ラウンド ロビンの負荷分散、セッションの持続性、または Secure Sockets Layer (SSL) ターミネーションが必要です。

この記事では、Azure portal からフロントエンド プライベート IP アドレスを使用してアプリケーション ゲートウェイを構成する手順について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure portal (<https://portal.azure.com>) にサインインする

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、新しい仮想ネットワークを作成します。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

1. ポータルメニューを展開し、**リソースの作成** を選択します。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. アプリケーション ゲートウェイの名前として「*myAppGateway*」を、新しいリソース グループとして「*myResourceGroupAG*」を入力します。
4. **リージョン**については、 **(US) Central US** を選択します。
5. **階層**については、 **[標準]** を選択します。
6. **[仮想ネットワークの選択]** で、 **[新規作成]** を作成してから、次の仮想ネットワークの値を入力します。
   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。
   - *myBackendSubnet* - バックエンド サブネットの名前です。
   - *10.0.1.0/24* - バックエンド サブネットのアドレス空間です。

    ![Create virtual network](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. **[OK]** を選択して、仮想ネットワークとサブネットを作成します。
7. **Next:Frontends** を選択します。
8. **フロントエンド IP アドレスの種類**については、 **[プライベート]** を選択します。

   デフォルトでは、動的な IP アドレスの割り当てになります。 構成されたサブネットで使用できる最初のアドレスは、フロントエンド IP アドレスとして割り当てられます。
   > [!NOTE]
   > 一度割り当てられると、IP アドレスの種類 (静的または動的) を後で変更することはできません。
9. **Next: Backends** を選択します。
10. **バックエンド プールの追加** を選択します。
11. **名前**には、*appGatewayBackendPool*と入力します。
12. **ターゲットを持たないバックエンド プールを追加する**については、 **[はい]** を選択します。 後でターゲットを追加します。
13. **[追加]** を選択します。
14. **[次:構成]** を選択します。
15. **ルーティング規則**で、 **[規則の追加]** を選択します。
16. **ルール名**には、*Rrule-01*と入力します。
17. **リスナー名**には、*Listener-01*と入力します。
18. **フロントエンドIP**には、 **[プライベート]** を選択します。
19. 残りのデフォルトを受け入れて、**バックエンド ターゲット**タブを選択します。
20. **ターゲットタイプ**の場合、 **[バックエンドプール]** を選択してから、 **[appGatewayBackendPool]** を選択します。
21. **HTTP設定**には、 **[新規作成]** を選択します。
22. **HTTP設定名**には、*http-setting-01*と入力します。
23. **バックエンドプロトコル**には、 **[HTTP]** を選択します。
24. **バックエンドポート**には、*80*と入力します。
25. 残りのデフォルトを受け入れ、 **[追加]** を選択します。
26. **ルーティング規則の追加**ページで、 **[追加]** を選択します。
27. **タグ**を選択します。
28. **確認と作成** をクリックします。
29. 概要ページで設定を確認し、 **[作成]** を選択して、ネットワークリソースとアプリケーションゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。 次のセクションに進む前に、デプロイが正常に完了するまで待機します。

## <a name="add-backend-pool"></a>バックエンド プールを追加する

バックエンド プールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンドは、NIC、仮想マシンスケールセット、パブリック IP アドレス、内部 IP アドレス、完全修飾ドメイン名（FQDN）、および Azure App Service のようなマルチテナント バックエンドで構成できます。 この例では、ターゲットのバックエンドとして仮想マシンを使用します。 既存の仮想マシンを使用することも、新しい仮想マシンを作成することもできます。 この例では、Azure によってアプリケーション ゲートウェイのバックエンド サーバーとして使用される 2 つの仮想マシンを作成します。

このためには、次の手順に従います。

1. バックエンドサーバーとして使用される 2 つの新しい仮想マシン*myVM*および*myVM2*を作成します。
2. IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。
3. バックエンド サーバーをバックエンド プールに追加します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. **[リソースの作成]** を選択します。
2. **[計算]** を選択し、次に **[仮想マシン]** を選択します。
4. 次の仮想マシンの値を入力します。
   - **リソースグループ**に対して *[myResourceGroupAG]* を選択します。
   - *myVM* - **仮想マシン名**です。
   - **Image**に **[Windows Server 2019 Datacenter]** を選択します。
   - *azureadmin* - **ユーザー名**です。
   - *Azure123456!* **パスワード**用。
5. 残りのデフォルトを受け入れて **[次へ:ディスク]** を選択します。
6. デフォルトを受け入れて **[次へ:ネットワーク]** を選択します。
7. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。
8. 残りのデフォルトを受け入れて、 **[次へ:管理]** を選択します。
9. **[オフ]** を選択して、ブート診断を無効にします。
10. 残りのデフォルトを受け入れて、 **[次へ:高度]** を選択します。
11. **次へ :タグ**を選択します。
12. **[次へ :確認と作成]** をクリックします。
13. 概要ページの設定を確認して、 **[作成]** を選択します。 VMの作成には数分かかる場合があります。 次のセクションに進む前に、デプロイが正常に完了するまで待機します。

### <a name="install-iis"></a>IIS のインストール

1. Cloud Shell を開き、**PowerShell**に設定されていることを確認します。
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. 2 番目の仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 Set-AzVMExtension の名前と VMName に myVM2 を入力します。

### <a name="add-backend-servers-to-backend-pool"></a>バックエンド プールヘのバックエンド サーバーの追加

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。
2. **[バックエンド プール]** を選択します。 **[appGatewayBackendPool]** を選択します。
3. **ターゲットタイプ**で **[仮想マシン]** を選択し、**ターゲット**で myVM に関連付けられた vNIC を選択します。
4. 繰り返して MyVM2 を追加します。
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. **[保存]** を選択します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. ポータルの**Frontend IP Configurations**ページをクリックして、割り当てられたフロントエンド IP を確認します。
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. プライベート IP アドレスをコピーし、同じ VNet またはこのVNet に接続しているオンプレミスの VM のブラウザーアドレスバーに貼り付けて、Application Gateway にアクセスしてみます。

## <a name="next-steps"></a>次のステップ

バックエンドのヘルスを監視する場合は、[Application Gateway のバックエンドヘルスおよび診断ログ](application-gateway-diagnostics.md)を参照してください。
