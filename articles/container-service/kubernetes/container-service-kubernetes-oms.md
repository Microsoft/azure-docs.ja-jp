---
title: (非推奨) Azure Kubernetes クラスターの監視 - Operations Management
description: Log Analytics を使用した Azure Container Service での Kubernetes クラスターの監視
services: container-service
author: bburns
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/09/2016
ms.author: bburns
ms.custom: mvc
ms.openlocfilehash: 8010d7cbf960c3e2f6528687be97a47d31270696
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997214"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-log-analytics"></a>(非推奨) Log Analytics による Azure Container Service クラスターの監視

> [!TIP]
> Azure Kubernetes Service の使用に関するこの記事の更新版については、[コンテナーに対する Azure Monitor](../../azure-monitor/insights/container-insights-overview.md) に関する記事を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。

また、`az` Azure CLI と `kubectl` ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。
または、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使うこともできます。これには、`az` Azure CLI と `kubectl` ツールが既にインストールされています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。
```console
$ az acs kubernetes install-cli
```

kubernetes キーが kubectl ツールにインストールされているかどうかをテストするには、次のコマンドを実行します。
```console
$ kubectl get nodes
```

上のコマンドでエラーが発生する場合は、kubernetes クラスター キーを kubectl ツールにインストールする必要があります。 これを行うには、次のコマンドを使います。
```console
RESOURCE_GROUP=my-resource-group
CLUSTER_NAME=my-acs-name
az acs kubernetes get-credentials --resource-group=$RESOURCE_GROUP --name=$CLUSTER_NAME
```

## <a name="monitoring-containers-with-log-analytics"></a>Log Analytics でコンテナーを監視する

Log Analytics は、Microsoft のクラウドベースの IT 管理ソリューションです。OMS を使用して、オンプレミスとクラウドのインフラストラクチャを管理し、保護することができます。 コンテナー ソリューションは、コンテナー インベントリ、パフォーマンス、およびログを 1 つの場所で表示するのに役立つ、Log Analytics のソリューションです。 一元的な場所でログを表示して監査やコンテナーのトラブルシューティングを行い、ホスト上のノイズと消費の多いコンテナーを検索することができます。

![](media/container-service-monitoring-oms/image1.png)

コンテナー ソリューションの詳細については、[Log Analytics のコンテナー ソリューション](../../azure-monitor/insights/containers.md)に関するページを参照してください。

## <a name="installing-log-analytics-on-kubernetes"></a>Kubernetes に Log Analytics をインストールする

### <a name="obtain-your-workspace-id-and-key"></a>ワークスペース ID とキーを取得する
Log Analytics エージェントがサービスと通信するには、エージェントがワークスペース ID とワークスペース キーで構成されている必要があります。 ワークスペース ID とキーを取得するには、<https://mms.microsoft.com> でアカウントを作成する必要があります。
手順に従ってアカウントを作成してください。 アカウントの作成が完了した後、**[Log Analytics]** ブレードをクリックすると、ID とキーおよびワークスペースの名前を取得できます。 続いて **[詳細設定]**、**[接続先ソース]**、**[Linux サーバー]** で、次に示す必要な情報を確認します。

 ![](media/container-service-monitoring-oms/image5.png)

### <a name="install-the-log-analytics-agent-using-a-daemonset"></a>DaemonSet を使用して Log Analytics エージェントをインストールする
DaemonSet は Kubernetes によって使用され、クラスターのホストごとに 1 つのコンテナーの 1 つのインスタンスを実行します。
この DaemonSet は、監視エージェントの実行に最適です。

[DaemonSet の YAML ファイル](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)を次に示します。 これを `oms-daemonset.yaml` という名前のファイルに保存し、`WSID` と `KEY` のプレースホルダーの値を、このファイル内のワークスペース ID とキーに置き換えます。

DaemonSet 構成にワークスペース ID とキーを追加したら、`kubectl` コマンド ライン ツールを使用してクラスターに Log Analytics エージェントをインストールできます。

```console
$ kubectl create -f oms-daemonset.yaml
```

### <a name="installing-the-log-analytics-agent-using-a-kubernetes-secret"></a>Kubernetes シークレットを使用した Log Analytics エージェントのインストール
Log Analytics ワークスペースの ID とキーを保護するには、DaemonSet YAML ファイルの一部とし Kubernetes シークレットを使うことができます。

 - スクリプト、シークレット テンプレート ファイル、DaemonSet YAML ファイルを ([リポジトリ](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes)から) コピーし、それらが同じディレクトリにあることを確認します。
      - シークレット生成スクリプト: secret-gen.sh
      - シークレット テンプレート: secret-template.yaml
   - DaemonSet YAML ファイル: omsagent-ds-secrets.yaml
 - スクリプトを実行します。 スクリプトでは、Log Analytics ワークスペースの ID と主キーの指定を求められます。 それを挿入すると、スクリプトによってシークレット YAML ファイルが作成されるので、それを実行します。
   ```
   #> sudo bash ./secret-gen.sh
   ```

   - 次のコマンドを実行して、シークレット ポッドを作成します。``` kubectl create -f omsagentsecret.yaml ```

   - 確認するには、次のコマンドを実行します。

   ```
   root@ubuntu16-13db:~# kubectl get secrets
   NAME                  TYPE                                  DATA      AGE
   default-token-gvl91   kubernetes.io/service-account-token   3         50d
   omsagent-secret       Opaque                                2         1d
   root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
   Name:           omsagent-secret
   Namespace:      default
   Labels:         <none>
   Annotations:    <none>

   Type:   Opaque

   Data
   ====
   WSID:   36 bytes
   KEY:    88 bytes
   ```

  - ``` kubectl create -f omsagent-ds-secrets.yaml ``` を実行して、omsagent daemon-set を作成します。

### <a name="conclusion"></a>まとめ
これで完了です。 しばらくすると、Log Analytics ダッシュボードへのデータ フローを確認できます。
