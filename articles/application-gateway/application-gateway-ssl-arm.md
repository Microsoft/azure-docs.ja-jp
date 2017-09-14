---
title: "SSL オフロードの構成 - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "この記事では、Azure Resource Manager を使用して、SSL オフロード用のアプリケーション ゲートウェイを作成する方法について説明します"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4bcd002a805f1584323f5baf75f791f2fb64201f
ms.contentlocale: ja-jp
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Azure リソース マネージャーを使用した SSL オフロード用の Application Gateway の構成

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-ssl-arm.md)
> * [Azure クラシック PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway をゲートウェイでの Secure Sockets Layer (SSL) セッションを停止するように構成し、Web ファーム上で発生するコストのかかる SSL 暗号化解除タスクを回避することができます。 また、SSL オフロードはフロントエンド サーバーのセットアップと Web アプリケーションの管理も簡素化します。

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。 仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。 Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. アプリケーション ゲートウェイを使用するように構成するサーバーが存在している必要があります。または、それらのエンドポイントが仮想ネットワーク内に作成されているか、パブリック IP または仮想 IP アドレス (VIP) が割り当てられている必要があります。

## <a name="what-is-required-to-create-an-application-gateway"></a>Application Gateway の作成に必要な構成

* **バックエンド サーバー プール**: バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
* **バックエンド サーバー プールの設定**: すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート**: このポートは、アプリケーション ゲートウェイで開かれるパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー**: リスナーには、フロントエンド ポート、プロトコル (Http または Https。大文字小文字の区別あり)、および SSL 証明書名 (SSL オフロードを構成する場合) があります。
* **ルール**: ルールはリスナーとバックエンド サーバー プールを結び付け、特定のリスナーにヒットしたときにトラフィックが送られるバックエンド サーバー プールを定義します。 現在、 *basic* ルールのみサポートされます。 *basic* ルールは、ラウンド ロビンの負荷分散です。

**構成に関する追加の注意**

SSL 証明書の構成では、 **HttpListener** のプロトコルを **Https** (大文字小文字の区別あり) に変更する必要があります。 **SslCertificate** 要素を、SSL 証明書用に構成された変数値を設定して、**HttpListener** に追加します。 フロントエンド ポートは **443** に更新する必要があります。

**Cookie ベースのアフィニティを有効にするには**: クライアント セッションからの要求が常に Web ファーム内の同じ VM に送られるようにアプリケーション ゲートウェイを構成できます。 これを実現するには、ゲートウェイがトラフィックを適切に送ることを可能にするセッション Cookie を挿入します。 Cookie ベースのアフィニティを有効にするには、**BackendHttpSettings** 要素で **CookieBasedAffinity** を **Enabled** に設定します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Azure クラシック デプロイメント モデルの使用と Azure Resource Manager の使用の違いは、設定が必要なアプリケーション ゲートウェイと構成する必要がある項目の作成順序にあります。

Resource Manager を使用すると、アプリケーション ゲートウェイのすべてのコンポーネントは個別に構成され、その後結合されて、アプリケーション ゲートウェイのリソースが作成されます。

ここでは、Application Gateway を作成するために必要な手順を示します。

1. [Resource Manager のリソース グループの作成](#create-a-resource-group-for-resource-manager)
2. [アプリケーション ゲートウェイの仮想ネットワーク、サブネット、およびパブリック IP の作成](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [アプリケーション ゲートウェイの構成オブジェクトの作成](#create-an-application-gateway-configuration-object)
4. [アプリケーション ゲートウェイのリソースの作成](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure リソース マネージャー コマンドレットを使用するように PowerShell モードを切り替えてください。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。

   1. 次のコマンドを入力します。

   ```powershell
   Login-AzureRmAccount
   ```

   2. アカウントのサブスクリプションを確認するには、次のコマンドを入力します。

   ```powershell
   Get-AzureRmSubscription
   ```

   資格情報を使用して認証を行うように求めるメッセージが表示されます。

   3. 使用する Azure サブスクリプションを選択するには、次のコマンドを入力します。

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. リソース グループを作成するには、次のコマンドを実行します。 (既存のリソース グループを使用する場合は、この手順をスキップしてください。)

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この設定は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

前の例では、**appgw-RG** という名前のリソース グループを作成します。作成場所は **West US** (米国西部) です。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

   1. 次のコマンドを入力します。

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   このサンプルでは、アドレス範囲 **10.0.0.0/24** を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

   2. 次のコマンドを入力します。

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   このサンプルでは、サブネット **10.0.0.0/24** とプレフィックス **10.0.0.0/16** を使用して、**米国西部** リージョンのリソース グループ **appgw-rg** に、**appgwvnet** という名前の仮想ネットワークを作成します。

   3. 次のコマンドを入力します。

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   このサンプルでは、次の手順用の変数 **$subnet** にサブネット オブジェクトを割り当てます。

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

フロントエンド構成のパブリック IP アドレスを作成するには、次のコマンドを入力します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

このサンプルでは、**米国西部**リージョンのリソース グループ **appgw-rg** に、パブリック IP リソース **publicIP01** を作成します。

## <a name="create-an-application-gateway-configuration-object"></a>Application Gateway 構成オブジェクトの作成

   1. アプリケーション ゲートウェイ構成オブジェクトを作成するには、次のコマンドを入力します。

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   このサンプルでは、**gatewayIP01** という名前の Application Gateway の IP 構成を作成します。 Application Gateway が起動すると、構成されているサブネットから IP アドレスが取得され、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

   2. 次のコマンドを入力します。

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   このサンプルでは、IP アドレス **134.170.185.46**、**134.170.188.221**、および **134.170.185.50** を使用して、**pool01** という名前のバックエンド IP アドレス プールを構成します。 これらの値は、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 前の例の IP アドレスを Web アプリケーション エンドポイントの IP アドレスに置き換えます。

   3. 次のコマンドを入力します。

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   このサンプルでは、バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、アプリケーション ゲートウェイ設定 **poolsetting01** を構成します。

   4. 次のコマンドを入力します。

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   このサンプルでは、パブリック IP エンドポイントに対して、**frontendport01** という名前のフロントエンド IP ポートを構成します。

   5. 次のコマンドを入力します。

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   このサンプルでは、SSL 接続に使用する証明書を構成します。 証明書は PFX 形式であり、4 ～ 12 文字のパスワードを使用する必要があります。

   6. 次のコマンドを入力します。

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   このサンプルでは、**fipconfig01** という名前のフロントエンド IP 構成を作成し、このフロントエンド IP 構成にパブリック IP アドレスを関連付けます。

   7. 次のコマンドを入力します。

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   このサンプルでは、**listener01** という名前のリスナーを作成し、フロントエンド IP 構成と証明書にフロントエンド ポートを関連付けます。

   8. 次のコマンドを入力します。

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   このサンプルでは、**rule01** というロード バランサーのルーティング ルールを作成し、ロード バランサーの動作を構成します。

   9. 次のコマンドを入力します。

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   このサンプルでは、Application Gateway のインスタンスのサイズを構成します。

   > [!NOTE]
   > **InstanceCount** の既定値は **2**、最大値は 10 です。 **GatewaySize** の既定値は **Medium** です。 Standard_Small、Standard_Medium、Standard_Large のいずれかを選択できます。

   10. 次のコマンドを入力します。

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   この手順では、アプリケーション ゲートウェイで使用する SSL ポリシーを定義します。 詳細については、「[Application Gateway で SSL ポリシーのバージョンと暗号スイートを構成する](application-gateway-configure-ssl-policy-powershell.md)」をご覧ください。

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>New-AzureApplicationGateway を使用した Application Gateway の作成

次のコマンドを入力します。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

このサンプルでは、前の手順の構成項目をすべて使用して、アプリケーション ゲートウェイを作成します。 この例では、Application Gateway は **appgwtest** という名前です。

## <a name="get-the-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得

ゲートウェイを作成した後の次の手順は通信用にフロントエンドを構成することです。 パブリック IP を使用する場合、Application Gateway には、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実にヒットできるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 詳細については、[Azure でのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)に関する記事を参照してください。 

アプリケーション ゲートウェイの DNS 名を取得するには、アプリケーション ゲートウェイに接続されている **PublicIPAddress** 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成します。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。


```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>次のステップ

内部ロード バランサーと共に使用するようにアプリケーション ゲートウェイを構成する場合は、「[内部ロード バランサー (ILB) を使用したアプリケーション ゲートウェイの作成](application-gateway-ilb.md)」を参照してください。

負荷分散のオプション全般の詳細については、次を参照してください。

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Azure の Traffic Manager](https://azure.microsoft.com/documentation/services/traffic-manager/)

