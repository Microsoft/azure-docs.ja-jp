---
title: チュートリアル - Azure での Windows VM の高可用性
description: このチュートリアルでは、Azure PowerShell を使用して、可用性セット内での高可用性仮想マシンをデプロイする方法について説明します
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: tutorial
ms.date: 11/30/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 7fe1c01542df2fcc38982fe2a30f9e94c712eacb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87065247"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-azure-powershell"></a>チュートリアル:Azure PowerShell を使用して高可用性仮想マシンを作成してデプロイする

このチュートリアルでは、可用性セットを使用して、仮想マシン (VM) の可用性と信頼性を向上させる方法を学習します。 可用性セットを使うことで、Azure にデプロイする VM は、クラスター内で切り離された複数のハードウェア ノードに確実に分散されます。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 可用性セットの作成
> * 可用性セットに VM を作成する
> * 使用可能な VM のサイズを確認する
> * Azure Advisor を確認する


## <a name="availability-set-overview"></a>可用性セットの概要

可用性セットは、デプロイされる VM リソースを相互に分離するための論理的なグループ化機能です。 Azure では、可用性セット内に配置された VM は、複数の物理サーバー、コンピューティング ラック、ストレージ ユニット、およびネットワーク スイッチ間で実行されます。 ハードウェアまたはソフトウェアの障害が発生した場合、影響を受けるのは VM のサブセットだけであり、ソリューション全体は動作状態を維持します。 可用性セットは、信頼性の高いクラウド ソリューションの構築に不可欠です。

4 台のフロントエンド Web サーバーと 2 台のバックエンド VM がある典型的な VM ベースのソリューションを考えてみましょう。 Azure では、VM をデプロイする前に、2 つの可用性セット (Web 層用に 1 つ、バック層用に 1 つ) を定義します。 新しい VM を作成するときに、可用性セットをパラメーターとして指定します。 Azure により、VM は複数の物理的なハードウェア リソースに分離されます。 いずれかのサーバーが実行されている物理ハードウェアで問題が発生した場合でも、サーバーの他のインスタンスは別のハードウェアで実行されているため、稼働し続けます。

Azure に信頼性の高い VM ベースのソリューションをデプロイする場合は、可用性セットを使用します。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-an-availability-set"></a>可用性セットの作成

1 つの場所にあるハードウェアは、複数の更新ドメインと障害ドメインに分割されます。 **更新ドメイン**は、VM と、同時に再起動できる基になる物理ハードウェアのグループです。 同じ**障害ドメイン**内の VM は、共通の電源とネットワーク スイッチだけでなく、共通のストレージも共有します。  

可用性セットは、[New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) を使用して作成します。 この例では、更新ドメインと障害ドメインの数はどちらも *2* であり、可用性セットの名前は *myAvailabilitySet* です。

リソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup `
   -Name myResourceGroupAvailability `
   -Location EastUS
```

`-sku aligned` パラメーターを指定した [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) を使用して、マネージド可用性セットを作成します。

```azurepowershell-interactive
New-AzAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>可用性セット内の VM の作成
VM がハードウェア全体に適切に分散されるようにするには、VM を可用性セット内に作成する必要があります。 可用性セットを作成した後で、既存の VM を追加することはできません。 


[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して VM を作成するときに、`-AvailabilitySetName` パラメーターを使用して可用性セットの名前を指定します。

まず、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential?view=powershell-5.1) を使用して、VM の管理者のユーザー名とパスワードを設定します。

```azurepowershell-interactive
$cred = Get-Credential
```

ここで、[New-AzVM](/powershell/module/az.compute/new-azvm) を使用して、可用性セットに 2 つの VM を作成します。

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

VM 2 台分の作成と構成が終わるまでには、数分かかります。 完了すると、基になるハードウェア全体に 2 つの仮想マシンが分散されています。 

ポータルで **[リソース グループ]**  > **myResourceGroupAvailability** > **myAvailabilitySet** の順に移動して可用性セットを参照すると、2 つの障害ドメインと更新ドメインの間で VM がどのように分散されているかがわかります。

![ポータルの可用性セット](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>使用可能な VM のサイズのチェック 

可用性セット内に VM を作成する場合、ハードウェアで使用可能な VM のサイズを把握しておく必要があります。 可用性セットにデプロイできる仮想マシンの使用可能なすべてのサイズを取得するには、[Get-AzVMSize](/powershell/module/az.compute/get-azvmsize) コマンドを使用します。

```azurepowershell-interactive
Get-AzVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Azure Advisor を確認する 

Azure Advisor を使用して、VM の可用性を向上させる方法についてより多くの情報を取得することもできます。 Azure Advisor では、構成と使用状況のテレメトリが分析された後、Azure リソースの費用対効果、パフォーマンス、可用性、セキュリティを向上させるために役立つソリューションが推奨されます。

[Azure Portal](https://portal.azure.com) にサインインし、 **[すべてのサービス]** を選択して、「**Advisor**」と入力します。 Advisor ダッシュボードに、選択したサブスクリプションの個人向けの推奨事項が表示されます。 詳細については、「[Azure Advisor の使用を開始する](../../advisor/advisor-get-started.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 可用性セットの作成
> * 可用性セットに VM を作成する
> * 使用可能な VM のサイズを確認する
> * Azure Advisor を確認する

次のチュートリアルに進み、仮想マシンのスケール セットについて学習してください。

> [!div class="nextstepaction"]
> [VM スケール セットの作成](tutorial-create-vmss.md)
