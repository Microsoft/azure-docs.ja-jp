---
title: Azure Load Balancer の分散モードの構成
titleSuffix: Azure Load Balancer
description: この記事では、ソース IP アフィニティをサポートするための Azure Load Balancer に対する分散モードの構成について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: d6995073ba7d283a30f7580456cd61573752f7cc
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051391"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Azure Load Balancer の分散モードを構成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>ハッシュベースの分散モード

Azure Load Balancer の既定の分散モードは、5 タプル ハッシュです。 

タプルは、次のもので構成されます。
* **ソース IP**
* **ソース ポート**
* **宛先 IP**
* **宛先ポート**
* **プロトコルの種類**

ハッシュは、使用可能なサーバーにトラフィックをマップするために使用されます。 アルゴリズムでは、トランスポート セッション内でのみ持続性が提供されます。 同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータセンターの IP アドレスに送信されます。 クライアントが同じソース IP アドレスから新しいセッションを開始すると、接続元ポートが変更され、そのトラフィックは別のデータセンター エンドポイントに送信されます。

![5 タプルのハッシュベースの分散モード](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>ソース IP アフィニティ モード

ロード バランサーは、ソース IP アフィニティ分散モードを使用して構成することもできます。 この分散モードは、セッション アフィニティまたはクライアント IP アフィニティとも呼ばれます。 このモードは 2 タプル (ソース IP と接続先 IP) または 3 タプル (ソース IP、接続先 IP、プロトコルの種類) のハッシュを使用して、使用可能なサーバーにトラフィックをマップします。 ソース IP アフィニティを使用すると、同じクライアント コンピューターから開始された接続は、同じデータセンター エンドポイントに送られます。

次の図は 2 タプルの構成を示しています。 2 タプルの場合に、どのように接続がロード バランサーを通過して仮想マシン 1 (VM1) に向かうかに注目してください。 その後、VM1 は VM2 と VM3 でバックアップされます。

![2 タプルのセッション アフィニティ分散モード](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

ソース IP アフィニティ モードは、Azure Load Balancer とリモート デスクトップ ゲートウェイ(RD ゲートウェイ) 間の非互換性を解決します。 このモードを使用すれば、1 つのクラウド サービス内に RD ゲートウェイ ファームを構築できます。

もう 1 つの使用シナリオは、メディア アップロードです。 データのアップロードを UDP 経由で行い、コントロール プレーンは TCP 経由で実現します。

* クライアントで、負荷分散されたパブリック アドレスに対する TCP セッションが開始され、特定の DIP に送られます。 チャネルは、接続の正常性を監視するためにアクティブのままになります。
* 同じクライアント コンピューターからの新しい UDP セッションが、負荷分散された同じパブリック エンドポイントに対して開始されます。 接続先は前回の TCP 接続と同じ DIP エンドポイントになります。 コントロール チャネルは TCP 経由で維持しつつ、メディア アップロードを高スループットで実行できます。

> [!NOTE]
> 負荷分散されたセットが仮想マシンの削除や追加で変更されると、クライアント要求の分散が再計算されます。 最終的に同じサーバーに接続する既存のクライアントからの新しい接続に依存することはできません。 また、ソース IP アフィニティ分散モードを使用すると、トラフィックが均等に分散されない可能性があります。 プロキシの背後で実行しているクライアントは、1 つの固有のクライアント アプリケーションと見なすことができます。

## <a name="configure-source-ip-affinity-settings"></a>ソース IP アフィニティ設定を構成する

### <a name="azure-portal"></a>Azure portal

分散モードの構成を変更するには、ポータルで負荷分散規則を変更します。

1. Azure portal にサインインし、 **[リソース グループ]** をクリックして、変更するロード バランサーが含まれているリソース グループを見つけます。
2. ロード バランサーの概要画面で、 **[設定]** の **[負荷分散規則]** をクリックします。
3. [負荷分散規則] 画面で、分散モードを変更する負荷分散規則をクリックします。
4. 規則の **[セッション永続化]** ドロップ ダウン ボックスを変更することで、分散モードが変更されます。  次のオプションを使用できます。
    
    * **[None (hash-based)]\(なし (ハッシュベース)\)** - 同じクライアントからの連続した要求が、任意の仮想マシンによって処理できることを指定します。
    * **[Client IP (source IP affinity 2-tuple)]\(クライアント IP (ソース IP アフィニティ 2 タプル)\)** - 同じクライアント IP アドレスからの連続する要求が、同じ仮想マシンによって処理されることを指定します。
    * **[Client IP and protocol (source IP affinity 3-tuple)]\(クライアント IP とプロトコル (ソース IP アフィニティ 3 タプル)\)** - 同じクライアント IP アドレスとプロトコルの組み合わせからの連続する要求が、同じ仮想マシンによって処理されることを指定します。

5. 分散モードを選択し、 **[保存]** をクリックします。

### <a name="azure-powershell"></a>Azure PowerShell

Resource Manager を使用してデプロイされた仮想マシンの場合は、PowerShell を使用して、既存の負荷分散規則でのロード バランサーの分散設定を変更します。 次のコマンドで、分散モードが更新されます。 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

従来の仮想マシンの場合は、Azure PowerShell を使用して分散設定を変更します。 次のようにして、Azure エンドポイントを仮想マシンに追加してロード バランサー分散モードを構成します。

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

`LoadBalancerDistribution` 要素の値を、必要な負荷分散の量に合わせて設定します。 2 タプル (ソース IP と接続先 IP) の負荷分散の場合は sourceIP を指定します。 3 タプル (ソース IP、接続先 IP、プロトコルの種類) の負荷分散の場合は sourceIPProtocol を指定します。 既定の 5 タプルの負荷分散の動作の場合は none を指定します。

次の設定を使用して、エンドポイント ロード バランサー分散モード構成を取得します。

```azurepowershell
PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
LBSetName : MyLoadBalancedSet
LocalPort : 80
Name : HTTP
Port : 80
Protocol : tcp
Vip : 65.52.xxx.xxx
ProbePath :
ProbePort : 80
ProbeProtocol : tcp
ProbeIntervalInSeconds : 15
ProbeTimeoutInSeconds : 31
EnableDirectServerReturn : False
Acl : {}
InternalLoadBalancerName :
IdleTimeoutInMinutes : 15
LoadBalancerDistribution : sourceIP
```

`LoadBalancerDistribution` 要素が存在しない場合、Azure Load Balancer は既定の 5 タプルのアルゴリズムを使用します。

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>負荷分散エンドポイント セットで分散モードを構成する

エンドポイントが負荷分散エンドポイント セットの一部である場合、分散モードは負荷分散エンドポイント セットで構成される必要があります。

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Cloud Services エンドポイントで分散モードを構成する

クラウド サービスを更新するには、Azure SDK for .NET 2.5 をご使用ください。 Cloud Services のエンドポイントの設定は、.csdef ファイルで行われます。 ロード バランサー分散モードを更新して Cloud Services をデプロイするには、デプロイのアップグレードが必要です。

エンドポイント設定の .csdef の変更例は次の通りです。

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API の例

次の例では、デプロイで指定された負荷分散セットのロード バランサー分散モードを再構成する方法を示します。 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>デプロイ済みの負荷分散セットの分散モードを変更する

Azure クラシック デプロイ モデルを使用して、既存のデプロイ構成を変更します。 `x-ms-version` ヘッダーを追加し、値をバージョン 2014-09-01 以降に設定します。

#### <a name="request"></a>Request

```http
POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
Content-Type: application/xml
```

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
            <Port> port-assigned-to-probe </Port>
            <Protocol> probe-protocol </Protocol>
            <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
            <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

前述のとおり、`LoadBalancerDistribution` 要素を sourceIP (2 タプルのアフィニティ)、sourceIPProtocol (3 タプルのアフィニティ)、none (アフィニティなし、5 タプルのアフィニティ) に設定します。

#### <a name="response"></a>Response

```http
HTTP/1.1 202 Accepted
Cache-Control: no-cache
Content-Length: 0
Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
x-ms-servedbyregion: ussouth2
x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
Date: Thu, 16 Oct 2014 22:49:21 GMT
```

## <a name="next-steps"></a>次のステップ

* [Azure 内部ロード バランサーの概要](load-balancer-internal-overview.md)
* [インターネットに接続するロード バランサーの構成の開始](quickstart-load-balancer-standard-public-powershell.md)
* [ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
