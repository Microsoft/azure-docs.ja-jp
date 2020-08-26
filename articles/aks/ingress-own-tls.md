---
title: イングレス用の独自の TLS 証明書の使用
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) クラスターで独自の証明書を使用する NGINX イングレス コントローラーをインストールして構成する方法を説明します。
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 0254b8746c757d98ee98e4815dc53b22d5014765
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272809"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) で HTTPS イングレス コントローラーを作成し、独自の TLS 証明書を使用する

イングレス コントローラーは、リバース プロキシ、構成可能なトラフィック ルーティング、および Kubernetes サービスの TLS 終端を提供するソフトウェアです。 個別の Kubernetes サービスのイングレス ルールとルートを構成するには、Kubernetes イングレス リソースが使われます。 イングレス コントローラーとイングレス ルールを使用すれば、1 つの IP アドレスで Kubernetes クラスター内の複数のサービスにトラフィックをルーティングできます。

この記事では、Azure Kubernetes Service (AKS) クラスターに [NGINX イングレス コントローラー][nginx-ingress]を展開する方法について説明します。 独自の証明書を生成し、イングレス ルートで使用する Kubernetes シークレットを作成します。 最後に、それぞれが 1 つの IP アドレスでアクセスできる、2 つのアプリケーションを AKS クラスターで実行します。

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- Let's Encrypt を使用して、[動的パブリック IP アドレスを指定][aks-ingress-tls]または[静的パブリック IP アドレスを指定][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

## <a name="before-you-begin"></a>開始する前に

この記事では、[Helm 3][helm] を使用し、NGINX イングレス コントローラーをインストールします。 最新リリースの Helm を使用していること、および "*安定した*" Helm リポジトリにアクセスできることを確認します。 アップグレード手順については、[Helm のインストール ドキュメント][helm-install]を参照してください。Helm の構成および使用方法の詳細については、「[Azure Kubernetes Service (AKS) での Helm を使用したアプリケーションのインストール][use-helm]」を参照してください。

この記事ではまた、Azure CLI バージョン 2.0.64 以降を実行していることも必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-an-ingress-controller"></a>イングレス コントローラーを作成する

イングレス コントローラーを作成するには、`Helm` を使用して *nginx-ingress* をインストールします。 追加された冗長性については、NGINX イングレス コントローラーの 2 つのレプリカが `--set controller.replicaCount` パラメーターでデプロイされています。 イングレス コントローラーのレプリカの実行から十分にメリットを享受するには、AKS クラスターに複数のノードが存在していることを確認します。

イングレス コントローラーも Linux ノード上でスケジュールする必要があります。 Windows Server ノードでは、イングレス コントローラーを実行しないでください。 ノード セレクターは、`--set nodeSelector` パラメーターを使用して指定され、Linux ベース ノード上で NGINX イングレス コントローラーを実行するように Kubernetes スケジューラに指示されます。

> [!TIP]
> 次の例では、*ingress-basic* という名前のイングレス リソースの Kubernetes 名前空間が作成されます。 必要に応じて、ご自身の環境の名前空間を指定できます。 AKS クラスターが RBAC 対応でない場合は、Helm コマンドに `--set rbac.create=false` を追加してください。

> [!TIP]
> クラスター内のコンテナーへの要求で[クライアント ソース IP の保持][client-source-ip]を有効にする場合は、Helm インストール コマンドに `--set controller.service.externalTrafficPolicy=Local` を追加します。 クライアント ソース IP が要求ヘッダーの *X-Forwarded-For* の下に格納されます。 クライアント ソース IP の保持が有効になっているイングレス コントローラーを使用する場合、TLS パススルーは機能しません。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

インストールの間に、Azure パブリック IP アドレスがイングレス コントローラーに対して作成されます。 このパブリック IP アドレスは、イングレス コントローラーが存続している間は静的です。 イングレス コントローラーを削除すると、パブリック IP アドレスの割り当てが失われます。 続いてさらに別のイングレス コントローラーを作成すると、新しいパブリック IP アドレスが割り当てられます。 パブリック IP アドレスを使用し続けることを望む場合は、代わりに[静的パブリック IP アドレス][aks-ingress-static-tls]を使用してイングレス コントローラーを作成できます。

パブリック IP アドレスを取得するには、`kubectl get service` コマンドを使います。

```console
kubectl get service -l app=nginx-ingress --namespace ingress-basic
```

IP アドレスがサービスに割り当てられるまでに、少し時間がかかる場合があります。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

このパブリック IP アドレスをメモしておきます。これは、最後の手順でデプロイをテストするために使用します。

まだイングレス ルールは作成されていません。 パブリック IP アドレスを参照すると、NGINX イングレス コントローラーの既定の 404 ページが表示されます。

## <a name="generate-tls-certificates"></a>TLS 証明書を生成する

この記事では、`openssl` を使用して自己署名証明書を生成します。 運用環境で使用する場合は、プロバイダーまたは独自の証明機関 (CA) からの信頼された署名証明書を要求する必要があります。 次の手順では、TLS 証明書と OpenSSL によって生成された秘密キーを使用して、Kubernetes *シークレット*を生成します。

次の例では、365 日間有効な *aks-ingress-tls.crt* という名前の 2048 ビット RSA X509 証明書を作成します。 秘密キー ファイルの名前は *aks-ingress-tls.key* です。 Kubernetes の TLS シークレットには、これらの両方のファイルが必要です。

この記事では *demo.azure.com* サブジェクトの共通名を使用していて、変更する必要はありません。 運用環境で使用する場合は、`-subj` パラメーターに独自の組織の値を指定します。

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>TLS 証明書の Kubernetes シークレットを作成する

Kubernetes でイングレス コントローラーの TLS 証明書と秘密キーを使用できるようにするには、シークレットを作成して使用します。 シークレットは 1 回だけ定義し、前の手順で作成した証明書とキー ファイルを使用します。 その後、イングレス ルートを定義するときにこのシークレットを参照します。

次の例では、*aks-ingress-tls* という名前のシークレットを作成します。

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>デモ アプリケーションを実行する

証明書でイングレス コントローラーとシークレットが構成されました。 ここでは、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、Helm を使って、単純な 'Hello world' アプリケーションのインスタンスを 2 つ実行します。

動作中のイングレス コントローラーを確認するには、AKS クラスターで 2 つのデモ アプリケーションを実行します。 この例では、`kubectl apply` を使って、シンプルな *Hello world* アプリケーションのインスタンスを 2 つデプロイします。

*aks-helloworld.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

*ingress-demo.yaml* ファイルを作成し、次の例のように YAML にコピーします。

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

`kubectl apply` を使用して、次の 2 つのデモ アプリケーションを実行します。

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>イングレス ルートを作成する

両方のアプリケーションが Kubernetes クラスターで実行されるようになりましたが、構成されているサービスの種類は `ClusterIP` です。 そのため、アプリケーションにはインターネットからアクセスできません。 公開するには、Kubernetes イングレス リソースを作成します。 イングレス リソースでは、2 つのアプリケーションのいずれかにトラフィックをルーティングするルールを構成します。

次の例では、アドレス `https://demo.azure.com/` へのトラフィックが `aks-helloworld` という名前のサービスにルーティングされることに注意してください。 アドレス `https://demo.azure.com/hello-world-two` へのトラフィックは、`ingress-demo` サービスにルーティングされます。 この記事では、これらのデモのホスト名を変更する必要はありません。 運用環境で使用する場合は、証明書の要求および生成プロセスの一環として指定した名前を指定します。

> [!TIP]
> 証明書の要求プロセスの間に指定されたホスト名 (CN 名) が、イングレス ルートで定義されているホストと一致しない場合、イングレス コントローラーには "*Kubernetes イングレス コントローラーの偽の証明書*" という警告が表示されます。 証明書とイングレス ルートのホスト名が一致することを確認します。

*ｔls* セクションは、ホスト *demo.azure.com* の *aks-ingress-tls* という名前のシークレットを使用することをイングレス ルートに伝えます。 ここでも、運用環境で使用する場合は、独自のホスト アドレスを指定します。

`hello-world-ingress.yaml` という名前のファイルを作成し、次の例の YAML 内にコピーします。

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

`kubectl apply -f hello-world-ingress.yaml` コマンドを使用してイングレス リソースを作成します。

```console
kubectl apply -f hello-world-ingress.yaml
```

出力例は、イングレス リソースが作成されたことを示しています。

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>イングレスの構成をテストする

偽の *demo.azure.com* ホストで証明書をテストするには、`curl` を使用し、 *--resolve* パラメーターを指定します。 このパラメーターを使用すると、*demo.azure.com* という名前をイングレス コントローラーのパブリック IP アドレスにマッピングできます。 次の例で示すように、独自のイングレス コントローラーのパブリック IP アドレスを指定します。

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

アドレスには追加のパスは指定されなかったため、イングレス コントローラーは既定の */* ルートに設定されます。 次の簡約された出力例に示すように、最初のデモ アプリケーションが返されます。

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

`curl` コマンドの *-v* パラメーターにより、受信した TLS 証明書などの詳細な情報が出力されます。 curl の出力の途中で、独自の TLS 証明書が使用されたことを確認できます。 *-k* パラメーターにより、自己署名証明書を使用している場合でも、引き続きページが読み込まれます。 次の例は、*issuer:CN=demo.azure.com; O=aks-ingress-tls* 証明書が使用されたことを示しています。

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

ここで `https://demo.azure.com/hello-world-two` などの */hello-world-two* パスをアドレスに追加します。 次の簡約された出力例に示すように、カスタム タイトル付きの 2 番目のデモ アプリケーションが返されます。

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事では、Helm を使用して、イングレス コンポーネントおよびサンプル アプリをインストールしました。 Helm グラフをデプロイすると、多数の Kubernetes リソースが作成されます。 これらのリソースには、ポッド、デプロイ、およびサービスが含まれます。 これらのリソースをクリーンアップするには、サンプルの名前空間全体を削除するか、またはリソースを個々に削除します。

### <a name="delete-the-sample-namespace-and-all-resources"></a>サンプルの名前空間とすべてのリソースを削除する

サンプルの名前空間全体を削除するには、`kubectl delete` コマンドを使用して、名前空間の名前を指定します。 名前空間内のすべてのリソースが削除されます。

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>リソースを個々に削除する

作成したリソースを個々に削除するという、きめ細かな方法もあります。 `helm list` コマンドを使用して、Helm リリースを一覧表示します。 

```console
helm list --namespace ingress-basic
```

次の出力例に示すように、*nginx-ingress* という名前のグラフを探します。

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

`helm uninstall` コマンドでリリースをアンインストールします。 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

次の例では、NGINX イングレスのデプロイをアンインストールします。

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

次に、2 つのサンプル アプリケーションを削除します。

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

サンプル アプリにトラフィックを送信していたイングレス ルートを削除します。

```console
kubectl delete -f hello-world-ingress.yaml
```

証明書シークレットを削除します。

```console
kubectl delete secret aks-ingress-tls
```

最後に、名前空間自体を削除します。 `kubectl delete` コマンドを使用して、名前空間の名前を指定します。

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>次のステップ

この記事には、AKS への外部コンポーネントが含まれています。 これらのコンポーネントの詳細については、次のプロジェクト ページを参照してください。

- [Helm CLI][helm-cli]
- [NGINX イングレス コントローラー][nginx-ingress]

次のこともできます。

- [外部のネットワーク接続を使用して基本的なイングレス コントローラーを作成する][aks-ingress-basic]
- [HTTP アプリケーションのルーティング アドオンを有効にする][aks-http-app-routing]
- [内部のプライベート ネットワークと IP アドレスを使用するイングレス コントローラーを作成する][aks-ingress-internal]
- Let's Encrypt を使用して、[動的パブリック IP アドレスを指定][aks-ingress-tls]または[静的パブリック IP アドレスを指定][aks-ingress-static-tls]して TLS 証明書を自動的に作成する、イングレス コントローラーを作成する

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
