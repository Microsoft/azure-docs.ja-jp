---
title: Azure Kubernetes Service (AKS) での証明書のローテーション
description: Azure Kubernetes Service (AKS) クラスターで証明書をローテーションする方法について説明します。
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/15/2019
ms.author: zarhoads
ms.openlocfilehash: 02bfdbc840065558003b249e1e3ea52f46ec64d6
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77596269"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での証明書のローテーション

Azure Kubernetes Service (AKS) では、そのコンポーネントの多くで認証に証明書が使用されています。 セキュリティやポリシー上の理由から、定期的にこれらの証明書のローテーションが必要になる場合があります。 たとえば、90 日おきにすべての証明書をローテーションするポリシーがある場合などです。

この記事では、AKS クラスターで証明書をローテーションする方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.77 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 証明書、証明機関、サービス アカウント

AKS では、次の証明書、証明機関、およびサービス アカウントが生成されて使用されます。

* AKS API サーバーでは、クラスター CA と呼ばれる証明機関 (CA) が作成されます。
* API サーバーには、API サーバーから kubelets への一方向の通信に使用する証明書に署名するクラスター CA があります。
* また、各 kubelet では、kubelet から API サーバーへの通信のために、クラスター CA によって署名される証明書署名要求 (CSR) も作成されます。
* etcd キー値ストアには、etcd から API サーバーへの通信用に、クラスター CA によって署名された証明書が含まれています。
* etcd キー値ストアでは、AKS クラスター内で etcd レプリカ間のデータ レプリケーションを認証および承認するために証明書に署名する CA が作成されます。
* API アグリゲーターでは、Azure の Open Service Broker などの他の API との通信に証明書を発行するためにクラスター CA が使用されます。 API アグリゲーターでは、これらの証明書を発行するための独自の CA を持つこともできますが、現在はクラスター CA が使用されています。
* 各ノードでは、クラスター CA によって署名されるサービスア カウント (SA) トークンが使用されます。
* `kubectl` クライアントには、AKS クラスターと通信するための証明書があります。

> [!NOTE]
> 2019 年 3 月より前に作成された AKS クラスターには、2 年後に期限切れになる証明書があります。 2019 年 3 月以降に作成されたすべてのクラスター、またはその証明書がローテーションされているすべてのクラスターには、30 年後に期限切れになる証明書があります。 クラスターがいつ作成されたかを確認するには、`kubectl get nodes` を使用して、ノード プールの *Age* を確認します。
> 
> また、クラスターの証明書の有効期限を確認することもできます。 たとえば、次のコマンドは *myAKSCluster* クラスターの証明書の詳細を表示します。
> ```console
> kubectl config view --raw -o jsonpath="{.clusters[?(@.name == 'myAKSCluster')].cluster.certificate-authority-data}" | base64 -d > my-cert.crt
> openssl x509 -in my-cert.crt -text
> ```

## <a name="rotate-your-cluster-certificates"></a>クラスター証明書をローテーションする

> [!WARNING]
> `az aks rotate-certs` を使用して証明書をローテーションすると、AKS クラスターに最大 30 分間のダウンタイムが生じる可能性があります。

[az aks get-credentials][az-aks-get-credentials] を使用して、AKS クラスターにサインインします。 また、このコマンドにより、ご使用のローカル コンピューターに `kubectl` クライアント証明書がダウンロードされて構成されます。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

`az aks rotate-certs` を使用して、ご使用のクラスター上のすべての証明書、CA、および SA をローテーションします。

```console
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> `az aks rotate-certs` が完了するまでに最大で 30 分かかる場合があります。 コマンドが完了する前に失敗した場合は、`az aks show` を使用して、クラスターの状態が *証明書のローテーション中*になっていることを確認します。 クラスターがエラー状態になっている場合は、`az aks rotate-certs` を再実行して、証明書をもう一度ローテーションします。

`kubectl` コマンドを実行して、古い証明書が無効になっていることを確認します。 `kubectl` によって使用される証明書を更新していないため、エラーが表示されます。  次に例を示します。

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

`az aks get-credentials` を実行して、`kubectl` によって使用される証明書を更新します。

```console
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

`kubectl` コマンドを実行して証明書が更新されていることを確認します。今度は成功します。 次に例を示します。

```console
kubectl get no
```

## <a name="next-steps"></a>次のステップ

この記事では、クラスターの証明書、CA、および SA を自動的にローテーションする方法について説明しました。 AKS のセキュリティのベスト プラクティスについては、「[Azure Kubernetes Service (AKS) でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][aks-best-practices-security-upgrades]」を参照してください。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
