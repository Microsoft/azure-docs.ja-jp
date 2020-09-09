---
title: Windows の Azure CLI サンプル
description: Windows の Azure CLI サンプル
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6c4b707a3cebc1bcdad7c9e14a96d82a8dda2371
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89318796"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Windows 仮想マシン用の Azure CLI サンプル

次の表には、Windows 仮想マシンをデプロイしている Azure CLI を使用して構築された Bash スクリプトへのリンクが含まれています。

| スクリプト | 説明 |
|---|---|
|**仮想マシンの作成**||
| [仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 最小の構成で Windows 仮想マシンを作成します。 |
| [完全に構成された仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | リソース グループ、仮想マシン、およびすべての関連リソースを作成します。|
| [可用性が高い仮想マシンの作成](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。 |
| [VM の作成と構成スクリプトの実行](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure カスタム スクリプトの拡張機能を使用して IIS をインストールします。 |
| [VM の作成と DSC 構成の実行](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Azure Desired State Configuration (DSC) の拡張機能を使用して IIS をインストールします。 |
|**ストレージの管理**||
| [VHD からマネージド ディスクを作成する](../scripts/virtual-machines-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | OS ディスクとして特殊化した VHD から、またはデータ ディスクとしてのデータ VHD からマネージド ディスクを作成します。  |
| [スナップショットからマネージド ディスクを作成する](../scripts/virtual-machines-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | スナップショットからマネージド ディスクを作成します。 |
| [同じまたは別のサブスクリプションにマネージド ディスクをコピーする](../scripts/virtual-machines-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 同じサブスクリプションまたは親マネージド ディスクと同じリージョン内の別のサブスクリプションに、マネージド ディスクをコピーします。 
| [ストレージ アカウントに VHD としてスナップショットをエクスポートする](../scripts/virtual-machines-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 管理スナップショットを VHD として別のリージョンのストレージ アカウントにエクスポートします。 |
| [ストレージ アカウントにマネージド ディスクの VHD をエクスポートする](../scripts/virtual-machines-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 別のリージョンのストレージ アカウントに、マネージド ディスクの基盤となる VHD をエクスポートします。 |
| [同じまたは別のサブスクリプションにスナップショットをコピーする](../scripts/virtual-machines-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 同じサブスクリプションまたは親スナップショットと同じリージョン内の別のサブスクリプションに、スナップショットをコピーします。 |
|**ネットワークの仮想マシン**||
| [仮想マシン間のネットワーク トラフィックのセキュリティ保護](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 2 つの仮想マシン、すべての関連リソース、および内部と外部のネットワーク セキュリティ グループ (NSG) を作成します。 |
|**セキュリティ保護された仮想マシン**||
| [VM とデータ ディスクを暗号化する](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Azure Key Vault、暗号化キー、およびサービス プリンシパルを作成し、VM を暗号化します。 |
|**仮想マシンの監視**||
| [Azure Monitor を使用して VM を監視する](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | 仮想マシンを作成し、Log Analytics エージェントをインストールして、VM を Log Analytics ワークスペースに登録します。  |
| | |
