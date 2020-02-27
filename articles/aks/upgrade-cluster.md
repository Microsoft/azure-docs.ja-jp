---
title: Azure Kubernetes Service (AKS) クラスターのアップグレード
description: Azure Kubernetes Service (AKS) クラスターのアップグレード方法について説明します
services: container-service
ms.topic: article
ms.date: 05/31/2019
ms.openlocfilehash: 4520297e83f96f95b10ecafd5af52a913dc5f450
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77621977"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターのアップグレード

AKS クラスターのライフサイクルの一環として、最新の Kubernetes バージョンへのアップグレードが必要になることはよくあります。 最新の Kubernetes セキュリティ リリースを適用するか、アップグレードして最新の機能を入手することが重要です。 この記事では、AKS クラスター内のマスター コンポーネントまたは 1 つの既定のノード プールをアップグレードする方法について説明します。

複数のノード プールまたは Windows Server ノード (現在 AKS でプレビュー段階) を使用する AKS クラスターについては、[AKS 内のノード プールのアップグレード][nodepool-upgrade]に関するページを参照してください。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

> [!WARNING]
> AKS クラスターのアップグレードで、ノードの切断とドレインがトリガーされます。 使用可能なコンピューティング クォータが少ない場合は、アップグレードが失敗する可能性があります。 詳しくは、「[クォータの増加](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)」をご覧ください。
> 独自のクラスター オートスケーラー デプロイを実行している場合は、アップグレード プロセスを妨げる可能性があるため、アップグレード中はこれを無効にしてください (ゼロ レプリカにスケーリングできます)。 マネージド オートスケーラーは、これを自動的に処理します。 

## <a name="check-for-available-aks-cluster-upgrades"></a>利用できる AKS クラスターのアップグレードを確認する

ご使用のクラスターに利用できる Kubernetes リリースを確認するには、[az aks get-upgrades][az-aks-get-upgrades] コマンドを使用します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myAKSCluster* という名前のクラスターに対して利用できるアップグレードを確認します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS クラスターをアップグレードする際に、Kubernetes マイナー バージョンをスキップすることはできません。 たとえば、*1.12.x* -> *1.13.x* または *1.13.x* -> *1.14.x* の間のアップグレードは許可されていますが、*1.12.x* -> *1.14.x* は許可されていません。
>
> *1.12.x* -> *1.14.x* にアップグレードするには、まず *1.12.x* -> *1.13.x* にアップグレードしてから、*1.13.x* -> *1.14.x* にアップグレードします。

次の出力例は、クラスターをバージョン *1.13.9* と *1.13.10* にアップグレードできることを示しています。

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
アップグレードが利用できない場合は、次のように表示されます。
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="upgrade-an-aks-cluster"></a>AKS クラスターのアップグレード

AKS クラスターに利用できるバージョンの一覧を参照し、[az aks upgrade][az-aks-upgrade] コマンドを使用してアップグレードします。 アップグレード プロセス中に、AKS は指定された Kubernetes バージョンを実行する新しいノードをクラスターに追加した後、実行中のアプリケーションへの中断を最小限に抑えるために古いノードのいずれかを慎重に[切断およびドレイン][kubernetes-drain]します。 新しいノードが実行中のアプリケーション ポッドとして確認されたら、その古いノードが削除されます。 このプロセスは、クラスター内のすべてのノードがアップグレードされるまで繰り返されます。

次の例では、クラスターをバージョン *1.13.10* にアップグレードします。

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.13.10
```

ノード数にもよりますが、クラスターのアップグレードには数分かかります。 

> [!NOTE]
> クラスターのアップグレードについては、完了までの合計許容時間があります。 この時間は `10 minutes * total number of nodes in the cluster` の積を取得することによって計算されます。 たとえば、20 ノードのクラスターでは、アップグレード操作が 200 分で成功する必要があります。それを超えた場合は、AKS によって操作が失敗します。これは、クラスターが回復不能な状態になるのを回避するためです。 アップグレードの失敗から回復するには、タイムアウトに達した後にアップグレード操作を再試行してください。

アップグレードが成功したことを確認するには、[az aks show][az-aks-show] コマンドを使用します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

次の出力例は、クラスターが現在 *1.13.10* で実行されていることを示しています。

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>次のステップ

この記事では、既存の AKS クラスターをアップグレードする方法について説明しました。 AKS クラスターのデプロイと管理の詳細については、一連のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [AKS のチュートリアル][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
