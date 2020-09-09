---
title: Azure portal でゾーン Windows VM を作成する
description: Azure Portal を使用して可用性ゾーン内に Windows VM を作成する
documentationcenter: virtual-machines
author: cynthn
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 339c093e5444839f9b106cd08980438a864c0474
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87828916"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Azure Portal を使用して可用性ゾーン内に Windows 仮想マシンを作成する

この記事では、Azure Portal を使用して Azure 可用性ゾーン内に仮想マシンを作成する手順を説明します。 [可用性ゾーン](../../availability-zones/az-overview.md)とは、1 つの Azure リージョン内で物理的に分離されたゾーンのことです。 可用性ゾーンは、データセンター全体に及ぶ珍しい障害や損失からアプリとデータを保護するために使用します。

可用性ゾーンを使用するには、[サポートされている Azure リージョン](../../availability-zones/az-region.md)に仮想マシンを作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン 

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-virtual-machine"></a>仮想マシンの作成

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

2. **[コンピューティング]** 、 **[Windows Server 2016 Datacenter]** の順に選択します。 

3. 仮想マシンの情報を入力します。 ここに入力したユーザー名とパスワードが、仮想マシンへのサインインに使用されます。 パスワードは 12 文字以上で、[定義された複雑さの要件](faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。 米国東部 2 など、可用性ゾーンをサポートする場所を選びます。 完了したら、 **[OK]** をクリックします。

    ![ポータルのブレードで VM に関する基本情報を入力する](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. VM のサイズを選択します。 推奨されるサイズを選ぶか、機能に基づいてフィルター処理します。 使うゾーンでサイズが使用可能であることを確認します。

    ![VM のサイズを選ぶ](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. **[設定]**  >  **[高可用性]** で、 **[可用性ゾーン]** ドロップダウン リストからいずれかの番号付きのゾーンを選択し、残りを既定値のままにして、 **[OK]** をクリックします。

    ![可用性ゾーンを選択する](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. 概要ページで、 **[作成]** をクリックして、仮想マシンの展開を始めます。

7. 対応する VM が、Azure Portal のダッシュボードにピン留めされます。 デプロイが完了すると、VM の概要が自動的に表示されます。

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>マネージド ディスクのゾーンと IP アドレスを確認する

VM が可用性ゾーンに展開されると、その VM のマネージド ディスクが同じ可用性ゾーンに作成されます。 既定では、パブリック IP アドレスもそのゾーンに作成されます。

これらのリソースに対するゾーンの設定は、ポータルで確認できます。  

1. **[リソース グループ]** をクリックし、VM のリソース グループの名前 (*myResourceGroup* など) をクリックします。

2. ディスク リソースの名前をクリックします。 **[概要]** ページには、リソースの場所と可用性ゾーンに関する詳細情報が含まれています。

    ![マネージド ディスクの可用性ゾーン](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. パブリック IP アドレス リソースの名前をクリックします。 **[概要]** ページには、リソースの場所と可用性ゾーンに関する詳細情報が含まれています。

    ![IP アドレスの可用性ゾーン](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>次のステップ

この記事では、可用性ゾーン内に VM を作成する方法を説明しました。 Azure VM の[可用性](../availability.md)の詳細を確認してください。