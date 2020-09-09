---
title: Azure Dev Spaces ツールのアップグレード方法
services: azure-dev-spaces
ms.date: 07/03/2018
ms.topic: conceptual
ms.custom: devx-track-azurecli
description: Azure Dev Spaces コマンド ライン ツール、Visual Studio Code 拡張機能、および Visual Studio 拡張機能をアップグレードする方法について説明します
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, コンテナー
ms.openlocfilehash: aebe81d58a1bcd44f5766d368dbafa53c81ed2b8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504332"
---
# <a name="how-to-upgrade-azure-dev-spaces-tools"></a>Azure Dev Spaces ツールのアップグレード方法

新しいリリースがあり、既に Azure Dev Spaces を使用している場合、Azure Dev Spaces クライアント ツールのアップグレードが必要になることがあります。

## <a name="update-the-azure-cli"></a>Azure CLI の更新

最新の Azure CLI を更新すると、最新バージョンの Dev Spaces CLI 拡張機能も取得されます。

以前のバージョンをアンインストールする必要はなく、[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) で適切なダウンロードを見つけるだけで構いません。


## <a name="update-the-dev-spaces-cli-extension-and-command-line-tools"></a>Dev Spaces CLI 拡張機能とコマンド ライン ツールの更新

次のコマンドを実行します。

```azurecli
az aks use-dev-spaces -n <your-aks-cluster> -g <your-aks-cluster-resource-group> --update
```

## <a name="update-the-vs-code-extension"></a>VS Code 拡張機能の更新

インストールが完了すると、拡張機能は自動的に更新されます。 新機能を使用するために、拡張機能の再読み込みが必要になる場合があります。 VS Code で **[拡張機能]** ウィンドウを開き、 **[Azure Dev Spaces]** 拡張機能を開いて **[Reload] (再読み込み)** を選択します。

## <a name="update-visual-studio"></a>Visual Studio を更新する

Azure Dev Spaces は、Azure の開発ワークロードの一部であり、Visual Studio のすべての更新プログラムに含まれています。

## <a name="next-steps"></a>次のステップ

新しいクラスターを作成して、新しいツールをテストします。 [Azure Dev Spaces](../index.yml) でクイック スタートおよびチュートリアルをお試しください。
