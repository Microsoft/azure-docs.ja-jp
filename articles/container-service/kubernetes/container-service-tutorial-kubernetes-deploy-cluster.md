---
title: "Azure Container Service チュートリアル - クラスターのデプロイ | Microsoft Docs"
description: "Azure Container Service チュートリアル - クラスターのデプロイ"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/21/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 16070499b7befca26d55259d845d1dbc14110f2a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="deploy-a-kubernetes-cluster-in-azure-container-service"></a>Azure Container Service で Kubernetes クラスターをデプロイする

Kubernetes には、コンテナー化されたアプリケーション用の分散プラットフォームが用意されています。 Azure Container Service を使うと、運用開始準備の整った Kubernetes クラスターのプロビジョニングを簡単かつ迅速に行うことができます。 この 7 部構成の 3 番目のチュートリアルでは、Azure Container Service の Kubernetes クラスターをデプロイします。 手順は次のとおりです。

> [!div class="checklist"]
> * Kubernetes ACS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

この後のチュートリアルでは、Azure Vote アプリケーションをクラスターにデプロイしてから拡張および更新し、Operations Management Suite を Kubernetes クラスターを監視するように構成します。

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、コンテナー イメージを作成して、Azure Container Registry インスタンスにアップロードしました。 これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。

## <a name="create-kubernetes-cluster"></a>Kubernetes クラスターを作成する

[前のチュートリアル](./container-service-tutorial-kubernetes-prepare-acr.md)では、*myResourceGroup* という名前のリソース グループを作成しました。 まだ作成していない場合、ここでこのリソース グループ作成してください。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

[az acs create](/cli/azure/acs#create) コマンドを使用して Azure Container Service に Kubernetes クラスターを作成します。 

次の例では、1 つの Linux マスター ノードと 3 つの Linux エージェント ノードを含む、*myK8sCluster* という名前のクラスターを作成します。

```azurecli-interactive 
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

数分してコマンドが完了すると、この ACS デプロイに関する情報が JSON 形式で表示されます。

## <a name="install-the-kubectl-cli"></a>kubectl CLI をインストールする

クライアント コンピューターから Kubernetes クラスターに接続するには、[kubectl](https://kubernetes.io/docs/user-guide/kubectl/) (Kubernetes コマンドライン クライアント) を使います。 

Azure CloudShell を使用している場合、`kubectl` は既にインストールされています。 ローカルにインストールする場合には、[az acs kubernetes install-cli](/cli/azure/acs/kubernetes#install-cli) コマンドを使います。

Linux または macOS で実行している場合は、sudo で実行することが必要な場合があります。 Windows では、シェルが管理者として実行されていることを確認します。

```azurecli-interactive 
az acs kubernetes install-cli 
```

Windows では、既定のインストールは *c:\program files (x86)\kubectl.exe* です。 Windows パスにこのファイルを追加することが必要な場合があります。 

## <a name="connect-with-kubectl"></a>kubectl を使用して接続する

Kubernetes クラスターに接続するように `kubectl` を構成するには、[az acs kubernetes get-credentials](/cli/azure/acs/kubernetes#get-credentials) コマンドを実行します。

```azurecli-interactive 
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

クラスターへの接続を確認するには、[kubectl get nodes](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) コマンドを実行します。

```azurecli-interactive
kubectl get nodes
```

出力:

```bash
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

チュートリアルが完了し、ACS Kubernetes クラスターはワークロードで使用できるようになりました。 後のチュートリアルでは、マルチコンテナー アプリケーションのクラスターへのデプロイ、スケールアウト、更新、監視を行います。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Container Service の Kubernetes クラスターをデプロイしました。 次の手順を完了しました。

> [!div class="checklist"]
> * Kubernetes ACS クラスターのデプロイ
> * Kubernetes CLI (kubectl) のインストール
> * kubectl の構成

次のチュートリアルに進んで、クラスターでのアプリケーションの実行について学習してください。

> [!div class="nextstepaction"]
> [Kubernetes でアプリケーションをデプロイする](./container-service-tutorial-kubernetes-deploy-application.md)

