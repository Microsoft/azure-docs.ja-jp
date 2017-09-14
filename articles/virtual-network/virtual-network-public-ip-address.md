---
title: "Azure パブリック IP アドレスの作成、変更、削除 | Microsoft Docs"
description: "パブリック IP アドレスの作成、変更、削除の方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: 41e4fe685f38a6a19a2024a12198cb8a8566182e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---

# <a name="create-change-or-delete-a-public-ip-address"></a>パブリック IP アドレスの作成、変更、削除

パブリック IP アドレスとその作成、変更、削除の方法について説明します。 パブリック IP アドレスは、変更可能な独自の設定を備えたリソースです。 パブリック IP アドレスを他の Azure リソースに割り当てることで次のことが可能となります。
- Azure Virtual Machines (VM)、Azure Virtual Machine Scale Sets、Azure VPN Gateway、Application Gateway、インターネット接続 Azure Load Balancer などの各種リソースに対する受信方向のインターネット接続。 パブリック IP アドレスが割り当てられていない Azure リソースは、インターネットからの受信方向の通信を受信できません。 一部の Azure リソースは何もしなくてもパブリック IP アドレスでアクセスできますが、そうではないリソースにインターネットからアクセスするには、パブリック IP アドレスを割り当てる必要があります。
- 予測できる IP アドレスを使ったインターネットへの送信方向の接続。 たとえば、仮想マシンは、パブリック IP アドレスが割り当てられていなくてもインターネットに対して送信方向の通信を行うことができますが、そのアドレスは Azure によって予測できないパブリック アドレスに変換されたネットワーク アドレスです。 パブリック IP アドレスをリソースに割り当てると、送信方向の接続に使われる IP アドレスがわかります。 アドレスは、予測することはできますが、選ばれている割り当て方法によっては変化する可能性があります。 詳細については、「[パブリック IP アドレスの作成](#create-a-public-ip-address)」を参照してください。 Azure リソースからの送信接続の詳細については、[送信用接続の詳細](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事のセクションに記載された手順を始める前に、次のタスクを完了してください。

- [Azure の制限](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)に関する記事を確認して、パブリック IP アドレスの制限について学習します。
- Azure アカウントを使って、Azure [Portal](https://portal.azure.com)、Azure コマンド ライン インターフェイス (CLI)、または Azure PowerShell にログインします。 まだ Azure アカウントを持っていない場合は、[無料試用版アカウント](https://azure.microsoft.com/free)にサインアップしてください。
- PowerShell コマンドを使用してこの記事のタスクを実行する場合は、[Azure PowerShell をインストールして構成します](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認してください。 PowerShell コマンドのヘルプとサンプルを表示するには、「`get-help <command> -full`」と入力します。
- Azure コマンド ライン インターフェイス (CLI) のコマンドを使ってこの記事のタスクを実行する場合は、[Azure CLI をインストールして構成します](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)。 最新バージョンの Azure CLI がインストールされていることを確認してください。 CLI コマンドのヘルプを表示するには、「`az <command> --help`」と入力します。 CLI とその前提条件をインストールする代わりに、Azure Cloud Shell を使うことができます。 Azure Cloud Shell は、Azure Portal 内で直接実行できる無料の Bash シェルです。 Azure CLI が事前にインストールされており、アカウントで使用できるように構成されています。 Cloud Shell を使うには、[ポータル](https://portal.azure.com)の上部にある [Cloud Shell**> _**] ボタンをクリックします。

パブリック IP アドレスには、わずかですが料金が発生します。 料金については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページを参照してください。 

## <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードの **[+ 追加]** をクリックします。
4. 表示された **[パブリック IP アドレスの作成]** ブレードで以下の設定の値を入力するか選択し、**[作成]** をクリックします。

    |Setting|必須|詳細|
    |---|---|---|
    |名前|あり|選択したリソース グループ内で一意となる名前を使用してください。|
    |IP バージョン|あり| IPv4 または IPv6 を選びます。 パブリック IPv4 アドレスは複数の Azure リソースに割り当てることができますが、IPv6 のパブリック IP アドレスはインターネットに接続するロード バランサーにのみ割り当てることができます。 ロード バランサーは、複数の Azure 仮想マシンに IPv6 トラフィックを負荷分散できます。 詳細については、[仮想マシンへの IPv6 トラフィックの負荷分散](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
    |IP アドレスの割り当て|あり|**[動的]:** 動的アドレスの割り当ては、VM の NIC にパブリック IP アドレスが関連付けられた後、VM の初回起動後に行われます。 NIC の接続先 VM が停止 (割り当て解除) された場合、動的アドレスは変化する場合があります。 VM が再起動された場合や、停止されても割り当て解除されなければ、アドレスは変化しません。 **[静的]:** 静的アドレスは、パブリック IP アドレスの作成時に割り当てられます。 VM が停止 (割り当て解除) 状態になっても静的アドレスは変化しません。 アドレスが解放されるのは、NIC が削除されたときだけです。 NIC の作成後に、割り当て方法を変更することができます。 **[IP バージョン]** で [IPv6] を選んだ場合、使うことができる割り当て方法は **[動的]** だけです。|
    |アイドル タイムアウト (分)|いいえ|クライアントからキープアライブ メッセージを送信しなくても TCP 接続または HTTP 接続が開いたまま維持される時間 (分)。 **[IP バージョン]** で [IPv6] を選んだ場合、この値を変更することはできません。 |
    |DNS 名ラベル|いいえ|名前の作成先となる Azure の場所 (すべてのサブスクリプション、すべての顧客) で一意となるようにしてください。 指定した名前のリソースに接続できるよう、その名前と IP アドレスが Azure のパブリック DNS サービスによって自動的に登録されます。 指定した名前に *location.cloudapp.azure.com* (location は選択した場所) が自動的に付加されて完全修飾 DNS 名が作成されます。 両方のアドレス バージョンの作成を選んだ場合は、IPv4 アドレスと IPv6 アドレスの両方に同じ DNS 名が割り当てられます。 Azure DNS サービスには IPv4 A と IPV6 AAAA の両方の名前レコードが含まれており、DNS 名が参照されると両方のレコードで応答します。 どちらのアドレス (IPv4 または IPv6) と通信するかは、クライアントが選択します。|
    |IPv6 アドレスを作成します/IPv4 アドレスを作成します|いいえ| IPv6 または IPv4 のどちらが表示されるかは、**[IP バージョン]** の選択に依存します。 たとえば、**[IP バージョン]** で **[IPv4]** を選んだ場合、ここには **IPv6** が表示されます。
    |名前 (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合にのみ表示されます)|はい (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合)。|この名前は、この表の最初の **[名前]** で入力した名前と異なっている必要があります。 IPv4 アドレスと IPv6 アドレス両方の作成を選ぶと、2 つのパブリック IP アドレス リソースが作成されて、それぞれに IP アドレスのバージョンが割り当てられます。|
    |IP アドレスの割り当て (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合にのみ表示されます)|はい (**[IPv6 アドレスを作成します]/[IPv4 アドレスを作成します]** チェック ボックスをオンにした場合)。|チェック ボックスに **[IPv4 アドレスを作成します]** と表示されている場合は、割り当て方法を選ぶことができます。 チェック ボックスに **[IPv6 アドレスを作成します]** と表示されている場合は、**[動的]** が必須なので、割り当て方法を選ぶことはできません。|
    |[サブスクリプション]|あり|パブリック IP アドレスを関連付けるリソースと同じ[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内に存在する必要があります。|
    |リソース グループ|あり|所属する[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)は、パブリック IP アドレスを関連付けるリソースと同じであっても異なっていてもかまいません。|
    |場所|あり|パブリック IP アドレスを関連付けるリソースと同じ[場所](https://azure.microsoft.com/regions) (リージョンとも呼ばれる) に存在する必要があります。|

**コマンド**

ポータルでは 2 つのパブリック IP アドレス リソース (IPv4 と IPv6) を作成できますが、次の CLI および PowerShell のコマンドでは、どちらか一方の IP バージョンのアドレスで 1 つのリソースが作成されます。 2 つのパブリック IP アドレス リソース (IP バージョンごとに 1 つ) を作成したい場合は、コマンドを 2 回実行し、異なる名前とバージョンでパブリック IP アドレス リソースを指定する必要があります。 

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip create](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress)|

## <a name="view-change-settings-for-or-delete-a-public-ip-address"></a>パブリック IP アドレスを表示、設定変更、削除する

1. ご利用のサブスクリプションのネットワーク作成協力者ロール (またはそれ以上) のアクセス許可が割り当てられているアカウントで [Azure Portal](https://portal.azure.com) にログインします。 アカウントへのロールとアクセス許可の割り当てについて詳しくは、「[Azure のロールベースのアクセス制御のための組み込みロール](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)」の記事を参照してください。
2. Azure Portal 上部に *[リソースの検索]* というテキストが表示されたボックスがあります。そこに「*パブリック IP アドレス*」と入力します。 検索結果に **[パブリック IP アドレス]** が表示されたら、それをクリックします。
3. 表示された **[パブリック IP アドレス]** ブレードで、表示、設定変更、削除の対象となるパブリック IP アドレスの名前をクリックします。
4. 表示されたパブリック IP アドレスのブレードで、実行する操作 (パブリック IP アドレスの表示、削除、変更) に応じて次のいずれかを実行します。
    - **表示**: ブレードの **[概要]** セクションに、関連付けられているネットワーク インターフェイス (アドレスがネットワーク インターフェイスに関連付けられている場合) など、パブリック IP アドレスの主要な設定が表示されます。 ポータルでは、アドレスのバージョン (IPv4 または IPv6) は表示されません。 バージョン情報を表示するには、PowerShell または CLI のコマンドを使って、パブリック IP アドレスを表示します。 IP アドレスのバージョンが IPv6 の場合、割り当てられたアドレスはポータル、PowerShell、または CLI では表示されません。 
    - **削除**: パブリック IP アドレスを削除するには、ブレードの **[概要]** セクションで **[削除]** をクリックします。 現在 IP 構成に関連付けられているアドレスは削除できません。 アドレスが構成に関連付けられている場合は、**[関連付け解除]** をクリックすると、IP 構成からアドレスの関連付けが解除されます。
    - **変更**: **[構成]** をクリックします。 この記事の「[パブリック IP アドレスの作成](#create-a-public-ip-address)」セクションにある手順 4. の情報を参考にして設定を変更します。 IPv4 アドレスの割り当て方法を静的から動的に変更する場合はまず、IP 構成からパブリック IPv4 アドレスの関連付けを解除する必要があります。 そのうえで、動的な割り当て方法に変更し、**[関連付け]** をクリックして、同じ IP 構成または異なる IP 構成に IP アドレスを関連付けるか、関連付けを解除したままの状態にすることができます。 パブリック IP アドレスの関連付けを解除するには、**[概要]** セクションの **[関連付け解除]** をクリックします。

>[!WARNING]
>割り当て方法を静的から動的に変更すると、パブリック IP アドレスに割り当てられた IP アドレスが失われます。 DNS 名ラベル (定義した場合) に対する静的アドレスまたは動的アドレスのマッピングは Azure パブリック DNS サーバーによって管理されますが、動的 IP アドレスは、VM が停止 (割り当て解除) 状態になった後、起動されたときに変化する可能性があります。 アドレスが変化しないようにするには、静的 IP アドレスを割り当ててください。

**コマンド**

|ツール|コマンド|
|---|---|
|CLI|[az network public-ip-list](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#list) (パブリック IP アドレスの一覧表示)、[az network public-ip-show](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#show) (設定の表示)、[az network public-ip update](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#update) (更新)、[az network public-ip delete](/cli/azure/network/public-ip?toc=%2fazure%2fvirtual-network%2ftoc.json#delete) (削除)|
|PowerShell|[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (パブリック IP アドレス オブジェクトの取得とその設定の表示)、[Set-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/set-azurermpublicipaddress?toc=%2fazure%2fvirtual-network%2ftoc.json) (設定の更新)、[Remove-AzureRmPublicIpAddress](/powershell/module/azurerm.network/remove-azurermpublicipaddress) (削除)|

## <a name="next-steps"></a>次のステップ
次の Azure リソースの作成時にパブリック IP アドレスを割り当てます。

- [Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンまたは [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシン
- [インターネットに接続する Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure VPN Gateway を使用したサイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

