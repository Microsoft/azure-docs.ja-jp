---
title: Azure での Linux 仮想マシンのデプロイに関する問題のトラブルシューティング | Microsoft Docs
description: Azure Resource Manager デプロイ モデルでの Linux 仮想マシンのデプロイに関する問題のトラブルシューティング。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628301"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Azure での Linux 仮想マシンのデプロイに関する問題のトラブルシューティング

Azure での仮想マシン (VM) のデプロイに関する問題のトラブルシューティングを行うため、よくあるエラーと解決策について、[代表的な問題](#top-issues)を確認します。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

## <a name="top-issues"></a>代表的な問題
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>クラスターが要求された VM サイズをサポートできない
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- \<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- VM サイズを小さくして要求を再試行します。
    - 要求した VM のサイズを変更できない場合は、次の手順を実行します。 可用性セットのすべての VM を停止します。
    - **[リソース グループ]** > 対象とするリソース グループ > **[リソース]** > 対象とする可用性セット > **[仮想マシン]** > 対象とする仮想マシン > **[停止]** の順にクリックします。
    - すべての VM が停止したら、目的のサイズで VM を作成します。


## <a name="the-cluster-does-not-have-free-resources"></a>新しい VM を起動してから、停止している各 VM を選択し、[起動] をクリックします。
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- クラスターに空きリソースがない
- \<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
    - 後で要求を再試行してください。
    - 新しい VM を別の可用性セットに含めることができる場合

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>VM を (同じリージョンの) 別の可用性セットに作成します。

新しい VM を同じ仮想ネットワークに追加します。

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Visual Studio Enterprise (BizSpark) 向けの月単位のクレジットを有効にするには、どうすればよいですか?

月単位のクレジットを有効にするには、この[記事](https://azure.microsoft.com/offers/ms-azr-0064p/)を参照してください。 Ubuntu NV VM 用の GPU ドライバーをインストールできないのはなぜですか?

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>現時点では、Linux GPU のサポートは、Ubuntu Server 16.04 LTS を実行する Azure NC VM でのみご利用いただけます。

詳細については、「[Linux を実行している N シリーズ VM の GPU ドライバーをセットアップする](../linux/n-series-driver-setup.md)」を参照してください。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Linux N シリーズ VM のドライバーが見つかりません

Linux ベースの VM 用のドライバーのインストール手順については、[こちら](../sizes-gpu.md#supported-operating-systems-and-drivers)を参照してください。 N シリーズ VM 内で GPU インスタンスが見つかりません

## <a name="are-n-series-vms-available-in-my-region"></a>Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM にグラフィック ドライバーをインストールする必要があります。

ドライバーの設定情報については[こちら](../sizes-gpu.md#supported-operating-systems-and-drivers)を参照してください。

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>自分のリージョンで N シリーズ VM を使用できますか?

[リージョンごとの使用可能な製品の表](https://azure.microsoft.com/regions/services)で、使用できるかどうかを確認できます。価格については、[こちら](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)を参照してください。 VM のサイズ変更時に、必要な VM サイズ ファミリが表示されません。 VM は、実行されている場合、物理サーバーにデプロイされています。

- Azure リージョンの物理サーバーは、一般的な物理ハードウェアのクラスターにグループ化されます。

- 別のハードウェア クラスターに VM を移動することが必要になる VM のサイズ変更は、VM のデプロイに使用したデプロイメント モデルに応じて異なります。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>クラシック デプロイ モデルでデプロイされた VM の場合、VM を他のサイズ ファミリのサイズに変更するには、クラウド サービス デプロイを削除し、再デプロイする必要があります。

Resource Manager デプロイ モデルでデプロイされた VM の場合は、可用性セット内の VM のサイズを変更する前に、可用性セット内のすべての VM を停止する必要があります。 一覧の VM サイズが、可用性セットに対するデプロイでサポートされていません。

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>可用性セットのクラスターでサポートされているサイズを選択してください。

可用性セットを作成するときには、必要だと考えられる最大の VM サイズを選択し、それを可用性セットへの最初のデプロイにすることをお勧めします。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Azure では、どの Linux ディストリビューションおよびバージョンがサポートされていますか?

[Azure での動作保証済みディストリビューション](../linux/endorsed-distros.md)に関するページで、Linux の一覧を確認できます。 可用性セットに既存のクラシック VM を追加できますか? はい。

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>既存のクラシック VM を、新規または既存の可用性セットに追加することができます。
詳細については、「[既存の仮想マシンを可用性セットに追加する](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)」を参照してください。

次のステップ この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。
