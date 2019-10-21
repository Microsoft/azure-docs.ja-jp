---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4dd43c5bcc5a0e9a734db4ca9a4b3d7137f85250
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203131"
---
## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Functions は、関数に関する状態その他の情報を維持するために Azure Storage の汎用のアカウントを使用します。 [az storage account create](/cli/azure/storage/account#az-storage-account-create) コマンドを使用して作成したリソース グループ内に汎用のストレージ アカウントを作成します。

次のコマンドでは、`<storage_name>` プレースホルダーをグローバルに一意なストレージ アカウント名で置き換えます。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
