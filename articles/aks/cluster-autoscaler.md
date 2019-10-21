---
title: Azure Kubernetes Service (AKS) でのクラスター オートスケーラーの使用
description: Azure Kubernetes Service (AKS) クラスターでのアプリケーションの需要を満たすように、クラスター オートスケーラーを使用してクラスターを自動的にスケーリングする方法について説明します。
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 9d7a404b767d3975cefd55e1db8487fbb45042e2
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72174184"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でのアプリケーションの需要を満たすようにクラスターを自動的にスケーリングする

Azure Kubernetes Service (AKS) のアプリケーションの需要に対応するには、ワークロードを実行するノードの数を調整する必要が生じる場合があります。 クラスター オートスケーラー コンポーネントは、リソース制約のためにスケジュールできないクラスター内のポッドを監視できます。 問題が検出されると、アプリケーションの需要を満たすためにノード プール内のノードの数が増やされます。 また、実行ポッドの不足について定期的にノードがチェックされ、必要に応じてノードの数が減らされます。 AKS クラスター内のノードの数を自動的に増減するこの機能を使用すると、効率的で、コスト効率の高いクラスターを実行できます。

この記事では、AKS クラスターでクラスター オートスケーラーを有効にして管理する方法について説明します。 クラスター オートスケーラーはプレビューで AKS クラスターでのみテストする必要があります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスのオプトインです。 プレビューは、"現状有姿のまま" および "利用可能な限度" で提供され、サービス レベル契約および限定保証から除外されるものとします。 AKS プレビューは、カスタマー サポートによってベスト エフォートで部分的にカバーされます。 そのため、これらの機能は、運用環境での使用を意図していません。 詳細については、次のサポートに関する記事を参照してください。
>
> * [AKS のサポート ポリシー][aks-support-policies]
> * [Azure サポートに関する FAQ][aks-faq]

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.65 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 拡張機能をインストールする

クラスター オートスケーラーを使用するには、*aks-preview* CLI 拡張機能のバージョン 0.4.12 以降が必要です。 [az extension add][az-extension-add] コマンドを使用して *aks-preview* Azure CLI 拡張機能をインストールし、[az extension update][az-extension-update] コマンドを使用して使用可能な更新プログラムがあるかどうかを確認します。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="limitations"></a>制限事項

クラスター オートスケーラーを使用する AKS クラスターを作成および管理する場合には、次の制限が適用されます。

* HTTP アプリケーションのルーティング アドオンは使用できません。

## <a name="about-the-cluster-autoscaler"></a>クラスター オートスケーラーについて

平日と夜間、または週末など、アプリケーションの変則的な需要に対応するために、多くの場合、クラスターには自動的にスケーリングする方法が必要になります。 AKS クラスターは、次の 2 つの方法のいずれかでスケーリングできます。

* **クラスター オートスケーラー**は、リソース制約のためにノードでスケジュールできないポッドを監視します。 その後、クラスターによってノードの数が自動的に増やされます。
* **ポッドの水平オートスケーラー**は、Kubernetes クラスターのメトリック サーバーを使用して、ポッドのリソースの需要をモニターします。 アプリケーションで必要なリソースが増えると、その需要を満たすためにポッドの数が自動的に増やされます。

![クラスター オートスケーラーとポッドの水平オートスケーラーは、必要なアプリケーション需要に対応するために、多くの場合、連携して機能します。](media/autoscaler/cluster-autoscaler.png)

また、ポッドの水平オートスケーラーとクラスター オートスケーラーでは、必要に応じてポッドとノードの数を減らすこともできます。 クラスター オートスケーラーは、一定期間未使用の容量があった場合、ノードの数を減らします。 クラスター オートスケーラーによって削除されるノード上のポッドは、クラスター内の他の場所で安全にスケジュールされます。 クラスター オートスケーラーは、以下の状況のように、ポッドが移動できない場合はスケールダウンできないことがあります。

* ポッドが直接作成されており、デプロイやレプリカ セットなどのコントローラー オブジェクトによってサポートされていない。
* Pod Disruption Budget (PDB) の制限が非常に厳しく、ポッドの数が特定のしきい値を下回ることが許可されていない。
* ポッドが、別のノードでスケジュールされた場合に適用できないノード セレクターまたはアンチ アフィニティーを使用している。

クラスター オートスケーラーでスケールダウンを行えない場合がある状況について詳しくは、「[What types of pods can prevent the cluster autoscaler from removing a node?][autoscaler-scaledown]」 (クラスター オートスケーラーがノードから削除されるのを防止できるのは、どの種類のポッドですか?) を参照してください。

クラスター オートスケーラーは、スケール イベント間の時間間隔やリソースしきい値などに対して開始パラメーターを使用します。 これらのパラメーターは、Azure プラットフォームによって定義されており、現在公開されていないため、調整することはできません。 クラスター オートスケーラーが使用するパラメーターについて詳しくは、「[What are the cluster autoscaler parameters?][autoscaler-parameters]」 (クラスター オートスケーラー パラメーターとは何ですか?) を参照してください。

クラスター オートスケーラーとポッドの水平オートスケーラーは連携して機能し、多くの場合、両方のオートスケーラーが一緒に 1 つのクラスターにデプロイされます。 この 2 つを組み合わせて使用する場合、ポッドの水平オートスケーラーの主な役割は、アプリケーションの需要を満たすために必要な数のポッドを実行することになります。 クラスター オートスケーラーの主な役割は、スケジュールされたポッドをサポートするために必要な数のノードを実行することになります。

> [!NOTE]
> クラスター オートスケーラーを使用する場合、手動スケーリングは無効になります。 必要なノード数は、クラスター オートスケーラーによって決定されるようにします。 クラスターを手動でスケーリングする場合は、[クラスター オートスケーラーを無効化](#disable-the-cluster-autoscaler)します。

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS クラスターの作成とクラスター オートスケーラーの有効化

AKS クラスターを作成する必要がある場合は、[az aks create][az-aks-create] コマンドを使用します。 クラスターのノード プール上でクラスター オートスケーラーを有効にして構成するには、 *--enable-cluster-autoscaler* パラメーターを使用し、ノードの *--min-count* と *--max-count* を指定します。

> [!IMPORTANT]
> クラスター オートスケーラーは、Kubernetes のコンポーネントです。 AKS クラスターは、ノードに仮想マシン スケール セットを使用しますが、Azure portal で、または Azure CLI を使用して、スケール セットの自動スケーリングの設定を手動で有効にしたり編集したりしないでください。 必要なスケール設定の管理は、Kubernetes クラスター オートスケーラーが行います。 詳細については、[ノード リソース グループ内の AKS リソースを変更可能かどうか](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)に関するセクションを参照してください。

次の例では、仮想マシンのスケール セットによって戻された 1 つのノード プールを使用して、AKS クラスターを作成します。 また、クラスターのノードプール上でクラスター オートスケーラーを有効にし、最小値を *1* ノード、最大値を *3* ノードに設定します。

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> `az aks create` を実行するときに *--kubernetes-version* を指定した場合、そのバージョンは、前述の「[開始する前に](#before-you-begin)」セクションで概説されている必須の最小バージョン番号以上である必要があります。

このクラスターを作成して、クラスター オートスケーラーの設定を構成するには数分かかります。

## <a name="change-the-cluster-autoscaler-settings"></a>クラスター オートスケーラーの設定の変更

> [!IMPORTANT]
> サブスクリプションで複数の "*エージェント プール*" 機能が有効になっている場合は、[複数のエージェント プールを使用する自動スケーリングに関するセクション](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)に進んでください。 複数のエージェント プールが有効になっているクラスターでは、`az aks` ではなく `az aks nodepool` コマンド セットを使用して、ノード プール固有のプロパティを変更する必要があります。 次の手順では、複数のノード プールが有効になっていないことを前提としています。 それが有効になっているかどうかを確認するには、`az feature  list -o table` を実行し、`Microsoft.ContainerService/multiagentpoolpreview` を探します。

AKS クラスターを作成するか既存のノード プールを更新する前述のステップで、クラスター オートスケーラーの最小ノード数は *1* に設定され、最大ノード数は *3* に設定されました。 アプリケーション需要の変化に応じて、クラスター オートスケーラーのノード数の調整が必要になる場合があります。

ノード数を変更するには、[az aks update][az-aks-update] コマンドを使用します。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

上の例では、*myAKSCluster* の単一のノード プールにあるクラスター オートスケーラーを更新して、最小値を *1* ノード、最大値を *5* ノードにします。

> [!NOTE]
> プレビュー中は、現在ノード プールに設定されている数より多い最小ノード数を設定することはできません。 例えば、現在、最小数が *1* に設定されている場合、最小数を *3* に更新することはできません。

アプリケーションとサービスのパフォーマンスをモニターして、必要なパフォーマンスに一致するようにクラスター オートスケーラーのノード数を調整してください。

## <a name="disable-the-cluster-autoscaler"></a>クラスター オートスケーラーの無効化

クラスター オートスケーラーを今後使用しない場合は、[az aks update][az-aks-update] コマンドで *--disable-cluster-autoscaler* パラメーターを指定することで無効にできます。 クラスター オートスケーラーが無効になってもノードは削除されません。

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

クラスター オートスケーラーを無効にした後、[az aks scale][az-aks-scale] コマンドを使用して、クラスターを手動でスケーリングできます。 ポッドの水平オートスケーラーを使用している場合、その機能はクラスター オートスケーラーを無効にしても引き続き実行されますが、ノード リソースがすべて使用中になると、ポッドをスケジュールできなくなる可能性があります。

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>無効なクラスター オートスケーラーを再度有効にする

既存のクラスター上でクラスター オートスケーラーを再度有効にする場合は、[az aks update][az-aks-update] コマンドで *--enable-cluster-autoscaler* パラメーターを指定することで有効にできます。

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>複数のノード プールを有効にしてクラスター オートスケーラーを使用する

クラスター オートスケーラーは、[複数のノード プールのプレビュー機能](use-multiple-node-pools.md)を有効にして一緒に使用できます。 そのドキュメントに従って、既存のクラスターで複数のノード プールを有効にし、ノード プールを追加する方法を確認してください。 両方の機能を一緒に使用すると、クラスター内の個々のノード プールでクラスター オートスケーラーを有効にし、それぞれに一意の自動スケーリング ルールを渡すことができます。

次のコマンドは、このドキュメントで前述した[初期手順](#create-an-aks-cluster-and-enable-the-cluster-autoscaler)に従っており、既存のノード プールの最大数を *3* から *5* に更新することを前提としています。 既存のノード プールの設定を更新するには、[az aks nodepool update][az-aks-nodepool-update] コマンドを使用します。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

クラスター オートスケーラーは、[az aks nodepool update][az-aks-nodepool-update] で `--disable-cluster-autoscaler` パラメーターを渡すことで無効にすることができます。

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name multipoolcluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

## <a name="next-steps"></a>次の手順

この記事では、AKS ノードの数を自動的にスケーリングする方法について説明します。 また、ポッドの水平オートスケーラーを使用して、アプリケーションを実行するポッドの数を自動的に調整することもできます。 ポッドの水平オートスケーラーの使用手順については、「[AKS でのアプリケーションのスケーリング][aks-scale-apps]」を参照してください。

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
