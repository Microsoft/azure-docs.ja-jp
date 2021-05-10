---
title: Azure CLI スクリプト サンプル - Web トラフィックの制限 | Microsoft Docs
description: Azure CLI スクリプトのサンプル - OWASP 規則を使用してトラフィックを制限する Web アプリケーション ファイアウォールと仮想マシン スケール セットがあるアプリケーション ゲートウェイを作成します。
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 6e20a4324f46925b8f83d7519c481a1d5bfc06a9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789305"
---
# <a name="restrict-web-traffic-using-the-azure-cli"></a>Azure CLI を使用して Web トラフィックを制限する

このスクリプトでは、バックエンド サーバーに仮想マシン スケール セットを使用する Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成します。 Web アプリケーション ファイアウォールは、OWASP 規則に基づいて Web トラフィックを制限します。 スクリプトを実行したら、パブリック IP アドレスを使用してアプリケーション ゲートウェイをテストできます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/application-gateway/create-vmss/create-vmss-waf.sh "Create application gateway")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroupAG --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | 仮想ネットワークを作成します。 |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | 仮想ネットワーク内にサブネットを作成します。 |
| [az network public-ip create](/cli/azure/network/public-ip) | アプリケーション ゲートウェイのパブリック IP アドレスを作成します。 |
| [az network application-gateway create](/cli/azure/network/application-gateway) | アプリケーション ゲートウェイを作成します。 |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | 仮想マシン スケール セットを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | ストレージ アカウントを作成します。 |
| [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) | ストレージ アカウントを作成します。 |
| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) | アプリケーション ゲートウェイのパブリック IP アドレスを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure/overview)のページをご覧ください。

追加のアプリケーション ゲートウェイの CLI スクリプトのサンプルは、[Azure Application Gateway のドキュメント](../cli-samples.md)にあります。
