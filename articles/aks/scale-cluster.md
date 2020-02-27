---
title: Azure Kubernetes Service (AKS) クラスターのスケーリング
description: Azure Kubernetes Service (AKS) クラスターでノードの数をスケーリングする方法について説明します。
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2071ba93e826fabc4778426e2b4404b7475d1bd2
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594399"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターでノードの数をスケーリングする

アプリケーションに必要なリソースが変化した場合は、AKS クラスターを手動でスケーリングし、異なる数のノードを実行できます。 スケール ダウンするときには、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に[切断およびドレインされます][kubernetes-drain]。 スケールアップすると、AKS では、ノードが Kubernetes クラスターによって `Ready` とマークされるまで待機してから、それらに基づいてポッドがスケジュールされます。

## <a name="scale-the-cluster-nodes"></a>クラスター ノードのスケーリング

最初に、[az aks show][az-aks-show] コマンドを使用してノード プールの*名前*を取得します。 次の例では、*myResourceGroup* リソース グループ内の *myAKSCluster* という名前のクラスターのノード プール名を取得します。

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

次の出力例は、*name* が *nodepool1* であることを示しています。

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

[az aks scale][az-aks-scale] コマンドを使用してクラスター ノードをスケーリングします。 次の例では、*myAKSCluster* という名前のクラスターを単一のノードにスケーリングします。 前のコマンドの *--nodepool-name* に、*nodepool1* などの独自のノードプール名を指定します。

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

次の出力例は、*agentPoolProfiles* セクションに示されているように、クラスターが正常に 1 つのノードにスケーリングされたことを示しています。

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>次のステップ

この記事では、AKS クラスターを手動でスケールしてノード数を増減しました。 [クラスターの自動スケーラー][cluster-autoscaler]を使用してクラスターを自動的にスケーリングすることもできます。

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
