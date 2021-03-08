---
title: インクルード ファイル
description: インクルード ファイル
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/20/2018
ms.author: tomfitz
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 53d4aa70b55577cdf2f6a1b898b496eb368157f5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755950"
---
リソース グループに 2 つのタグを追加するには、[az group update](/cli/azure/group) コマンドを使用します。

```azurecli-interactive
az group update -n myResourceGroup --set tags.Environment=Test tags.Dept=IT
```

3 つ目のタグを追加するとします。 新しいタグを指定してコマンドをもう一度実行します。 タグが既存のタグに追加されます。

```azurecli-interactive
az group update -n myResourceGroup --set tags.Project=Documentation
```

リソースは、リソース グループからタグを継承しません。 現在、リソース グループには 3 つのタグがありますが、リソースにはタグがありません。 リソースにある既存のタグを保持したうえで、リソース グループのすべてのタグをリソースに適用するには、次のスクリプトを使用します。

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Get the tags for this resource
  jsonrtag=$(az resource show --id $resid --query tags)
  
  # Reformat from JSON to space-delimited and equals sign
  rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
  
  # Reapply the updated tags to this resource
  az resource tag --tags $t$rt --id $resid
done
```

また、既存のタグを保持せずに、リソース グループのタグをリソースに適用することもできまます。

```azurecli-interactive
# Get the tags for the resource group
jsontag=$(az group show -n myResourceGroup --query tags)

# Reformat from JSON to space-delimited and equals sign
t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')

# Get the resource IDs for all resources in the resource group
r=$(az resource list -g myResourceGroup --query [].id --output tsv)

# Loop through each resource ID
for resid in $r
do
  # Apply tags from resource group to this resource
  az resource tag --tags $t --id $resid
done
```

1 つのタグに複数の値を結合するには、JSON 文字列を使用します。

```azurecli-interactive
az group update -n myResourceGroup --set tags.CostCenter='{"Dept":"IT","Environment":"Test"}'
```

リソース グループのすべてのタグを削除するには、次を使用します。

```azurecli-interactive
az group update -n myResourceGroup --remove tags
```
