---
title: Azure Dev Spaces が有効な Kubernetes クラスターを作成する - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: 何もインストールせずにブラウザーから直接 Azure Dev Spaces に対応する Kubernetes クラスターをすばやく作成する方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: f6da9055e11b5c514d71122c3650bbc326fb8de8
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232289"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Azure Cloud Shell を使用して Azure Dev Spaces が有効な Kubernetes クラスターを作成する

このページの **[使ってみる]** ボタンを使用すると、[Azure Cloud Shell](/azure/cloud-shell) を使用して Azure Kubernetes Service クラスターを作成できます。 サインインしていない場合は、プロンプトに従って Azure アカウントを使用してサインインした後、表示された Azure Cloud Shell プロンプトでコマンドを入力します。

## <a name="create-the-cluster"></a>クラスターを作成する

まず、[Azure Dev Spaces をサポートするリージョン][supported-regions]にリソース グループを作成します。

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

以下のコマンドを使用して Kubernetes クラスターを作成します。

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

クラスターの作成には数分かかります。  完了すると、JSON 形式で出力が表示されます。 `provisioningState` を見つけ、それが `Succeeded` であることを確認します。

## <a name="next-steps"></a>次のステップ

各種のチュートリアルへのリンクについては、「[Azure Dev Spaces](../index.yml)」を参照してください。

> [!IMPORTANT]
> Azure Dev Spaces の多くのクイックスタートとチュートリアルでは、Azure Dev Spaces CLI を使用して操作が実行されます。 Azure Dev Spaces CLI を Azure Cloud Shell にインストールすることはできません。


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
