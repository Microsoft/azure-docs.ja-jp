---
title: Kubernetes on Azure のチュートリアル - クラスターのアップグレード
description: この Azure Kubernetes Service (AKS) のチュートリアルでは、既存の AKS クラスターを最新の使用可能な Kubernetes バージョンにアップグレードする方法を学習します。
services: container-service
ms.topic: tutorial
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: a89e8bb42bec4323d2189ca93dfe73171c4a128c
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2020
ms.locfileid: "84887997"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>チュートリアル:Azure Kubernetes Service (AKS) での Kubernetes のアップグレード

アプリケーションとクラスターのライフサイクルの一環として、使用可能な最新バージョンの Kubernetes にアップグレードし、新しい機能を使用することができます。 Azure Kubernetes Service (AKS) クラスターは、Azure CLI を使用してアップグレードできます。

このチュートリアル (7 部構成の第 7 部) では、Kubernetes クラスターがアップグレードされます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

## <a name="before-you-begin"></a>開始する前に

これまでのチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化しました。 このイメージを Azure Container Registry にアップロードし、AKS クラスターを作成しました。 その後、AKS クラスターにアプリケーションをデプロイしました。 これらの手順を完了しておらず、順番に進めたい場合は、[チュートリアル 1 - コンテナー イメージを作成する][aks-tutorial-prepare-app]に関するページから開始してください。

このチュートリアルでは、Azure CLI バージョン 2.0.53 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="get-available-cluster-versions"></a>使用可能なクラスターのバージョンを取得する

クラスターをアップグレードする前に、[az aks get-upgrades][] コマンドを使用して、アップグレードで利用できる Kubernetes のリリースを確認します。

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

次の例では、現在のバージョンが *1.15.11* であることがわかります。また、 *[Upgrades]* の下に、利用可能なバージョンが示されています。

```json
{
  "agentPoolProfiles": null,
  "controlPlaneProfile": {
    "kubernetesVersion": "1.15.11",
    ...
    "upgrades": [
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.8"
      },
      {
        "isPreview": null,
        "kubernetesVersion": "1.16.9"
      }
    ]
  },
  ...
}
```

## <a name="upgrade-a-cluster"></a>クラスターのアップグレード

実行中のアプリケーションの中断を最小限に抑えるために、AKS ノードは慎重に切断およびドレインされます。 このプロセスでは、以下の手順が行われます。

1. Kubernetes スケジューラが、アップグレードされるノードに追加のポッドがスケジュールされないようにします。
1. ノードで実行中のポッドは、クラスター内の他のノードにスケジュールされます。
1. 最新の Kubernetes コンポーネントを実行するノードが作成されます。
1. 新しいノードの準備が整い、クラスターに参加すると、Kubernetes スケジューラによってそのノードでポッドの実行が開始されます。
1. 古いノードが削除され、クラスター内の次のノードが切断およびドレイン プロセスを開始します。

[az aks upgrade][] コマンドを使用して、AKS クラスターをアップグレードします。

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

> [!NOTE]
> 一度に 1 つのマイナー バージョンのみをアップグレードできます。 たとえば、*1.14.x* から *1.15.x* にアップグレードすることはできますが、*1.14.x* から *1.16.x* に直接アップグレードすることはできません。 *1.14.x* から *1.16.x* にアップグレードするには、まず *1.14.x* から *1.15.x* にアップグレードします。その後、*1.15.x* から *1.16.x* にもう一度アップグレードします。

次の出力例の抜粋には、*1.16.8* にアップグレードした結果が示されています。 *kubernetesVersion* が *1.16.8* としてレポートされていることがわかります。

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.16.8",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>アップグレードを検証する

[az aks show][] コマンドを次のように使用して、アップグレードが成功したことを確認します。

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、AKS クラスターで *KubernetesVersion 1.16.8* が実行されていることを示しています。

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.16.8               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="delete-the-cluster"></a>クラスターを削除する

このチュートリアルはシリーズの最後の部分なので、AKS クラスターを削除することをお勧めします。 Kubernetes ノードは Azure 仮想マシン (VM) で実行され、クラスターを使用しない場合でも引き続き料金が発生するためです。 [az group delete][az-group-delete] コマンドを使用して、リソース グループ、コンテナー サービス、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> クラスターを削除したとき、AKS クラスターで使用される Azure Active Directory サービス プリンシパルは削除されません。 サービス プリンシパルを削除する手順については、[AKS のサービス プリンシパルに関する考慮事項と削除][sp-delete]に関するページを参照してください。 マネージド ID を使用した場合、その ID はプラットフォームによって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、AKS クラスター内の Kubernetes をアップグレードしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * 現在の使用可能な Kubernetes バージョンを識別する
> * Kubernetes ノードをアップグレードする
> * 正常なアップグレードを検証する

AKS の詳細については、このリンクに従ってください。

> [!div class="nextstepaction"]
> [AKS の概要][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-delete]: /cli/azure/group#az-group-delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
