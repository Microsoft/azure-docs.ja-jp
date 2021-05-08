---
title: 接続ストレージを使用して関数アプリを作成する - Azure CLI
description: Azure CLI スクリプトのサンプル - Azure Storage に接続する Azure 関数の作成
ms.topic: sample
ms.date: 04/20/2017
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 539c3a7dd95045b2e569dbb339be0e5a0c845902
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786288"
---
# <a name="create-a-function-app-with-a-named-storage-account-connection"></a>名前付きストレージ アカウント接続を使用して関数アプリを作成する 

この Azure Functions サンプル スクリプトは、関数アプリを作成し、関数を Azure Storage アカウントに接続します。 作成された、接続を含むアプリ設定は、[ストレージのトリガーまたはバインド](../functions-bindings-storage-blob.md)と共に使用することができます。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このサンプルでは、Azure Function App を作成し、ストレージ接続文字列をアプリ設定に追加します。

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-storage/create-function-app-connect-to-storage-account.sh "Integrate Function App into Azure Storage Account")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 任意の場所にリソース グループを作成します。 |
| [az storage account create](/cli/azure/storage/account#az_storage_account_create) | ストレージ アカウントを作成します。 |
| [az functionapp create](/cli/azure/functionapp#az_functionapp_create) | サーバーレスの[従量課金プラン](../consumption-plan.md)で関数アプリを作成します。 |
| [az storage account show-connection-string](/cli/azure/storage/account#az_storage_account_show_connection_string) | アカウント用の接続文字列を取得します。 |
| [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) | 関数アプリのアプリ設定として接続文字列を設定します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の Azure Functions CLI のサンプル スクリプトは、[Azure Functions のドキュメント](../functions-cli-samples.md)で確認できます。
