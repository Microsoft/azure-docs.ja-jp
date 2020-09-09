---
title: Azure CLI のサンプル スクリプト - Linux VM の暗号化
description: Azure CLI のサンプル スクリプト - Linux VM の暗号化
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 610bc83925fb543d835df357960c5977860c0189
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87479644"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Azure における Linux 仮想マシンの暗号化

このスクリプトは、安全な Azure Key Vault、暗号化キー、Azure Active Directory サービス プリンシパル、Linux 仮想マシン (VM) を作成します。 その後、Key Vault から取得した暗号化キーとサービス プリンシパルの資格情報を使って、作成した VM を暗号化します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Azure Key Vault、サービス プリンシパル、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az keyvault create](/cli/azure/keyvault) | セキュリティで保護されたデータ (暗号化キーなど) を格納する Azure Key Vault を作成します。 |
| [az keyvault key create](/cli/azure/keyvault/key) | Key Vault に暗号化キーを作成します。 |
| [az ad sp create-for-rbac](/cli/azure/ad/sp) | 暗号化キーへのアクセスを安全に認証して制御する Azure Active Directory サービス プリンシパルを作成します。 |
| [az keyvault set-policy](/cli/azure/keyvault) | サービス プリンシパルに暗号化キーへのアクセス権を付与するために、Key Vault に対するアクセス許可を設定します。 |
| [az vm create](/cli/azure/vm) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm encryption enable](/cli/azure/vm/encryption) | サービス プリンシパルの資格情報と暗号化キーを使って VM に対する暗号化を有効にします。 |
| [az vm encryption show](/cli/azure/vm/encryption) | VM の暗号化処理の状態を表示します。 |
| [az group delete](/cli/azure/vm/extension) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
