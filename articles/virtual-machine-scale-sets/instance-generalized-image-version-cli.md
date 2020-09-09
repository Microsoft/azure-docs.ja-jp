---
title: Azure CLI を使用して一般化されたイメージからスケール セットを作成する
description: 共有イメージ ギャラリー内の一般化されたイメージを使用してスケール セットを作成します。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 3cde06b652befa8fbb655273c19f65bd2f38e850
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87069840"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Azure CLI を使用して一般化されたイメージからスケール セットを作成する

Azure CLI を使用して、[共有イメージ ギャラリー](shared-image-galleries.md)に格納されている一般化されたイメージ バージョンからスケール セットを作成します。 特殊化されたイメージ バージョンを使用してスケール セットを作成する場合は、[特殊化されたイメージからのスケール セット インスタンスの作成](instance-specialized-image-version-cli.md)に関する記事をご覧ください。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.4.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

この例では、必要に応じてリソース名を置き換えてください。 

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) を使用してギャラリー内のイメージ定義を一覧表示すると、定義の名前と ID を確認できます。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

[`az vmss create`](/cli/azure/vmss#az-vmss-create) を使用してスケール セットを作成します。 

`--image` にイメージ定義 ID を使用して、使用可能な最新バージョンのイメージからスケール セット インスタンスを作成します。 また、`--image` にイメージ バージョン ID を指定して、特定のバージョンからスケール セット インスタンスを作成することもできます。 特定のイメージ バージョンを使用すると、その特定のイメージ バージョンが使用できない場合 (リージョンから削除されるなどして)、オートメーションが失敗する可能性があることに注意してください。 特定のイメージ バージョンが必要な場合を除き、新しい VM の作成にはイメージ定義 ID の使用をお勧めします。

この例では、最新バージョンの *myImageDefinition* イメージからインスタンスを作成しています。

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。

## <a name="next-steps"></a>次のステップ
[Azure Image Builder (プレビュー)](../virtual-machines/linux/image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](../virtual-machines/linux/image-builder-gallery-update-image-version.md)に使用することさえできます。 

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

共有イメージ ギャラリーの詳細については、[概要](shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](troubleshooting-shared-images.md)」を参照してください。