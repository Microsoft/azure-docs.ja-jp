---
title: "Web UI を使用した Azure Kubernetes クラスターの管理 | Microsoft Docs"
description: "Azure Container Service の Kubernetes Web UI を使用する"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: bburns
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: fbdd3c2450c5385b782d72eb90008a093f6a7298
ms.contentlocale: ja-jp
ms.lasthandoff: 07/25/2017

---

# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>Azure Container Service で Kubernetes Web UI を使用する

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure Container Service を使用して Kubernetes クラスターを作成](container-service-kubernetes-walkthrough.md)したことを想定します。


また、Azure CLI 2.0 と `kubectl` ツールをインストールしていることも想定します。

`az` ツールがインストールされていることを確認するには、次を実行します。

```console
$ az --version
```

`az` ツールをインストールしていないない場合、[ここ](https://github.com/azure/azure-cli#installation)に手順が記載されています。

`kubectl` ツールがインストールされていることを確認するには、次を実行します。

```console
$ kubectl version
```

`kubectl` をインストールしていない場合、次を実行できます。

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>概要

### <a name="connect-to-the-web-ui"></a>Web UI に接続する
Kubernetes Web UI を起動するには、次を実行できます。

```console
$ az acs kubernetes browse -g [Resource Group] -n [Container service instance name]
```

これにより、お使いのローカル コンピューターと Kubernetes Web UI を接続する Secure プロキシと対話するように構成された Web ブラウザーが開きます。

### <a name="create-and-expose-a-service"></a>サービスを作成および公開する
1. Kubernetes Web UI で、右上のウィンドウの **[作成]** ボタンをクリックします。

    ![Kubernetes [作成] UI](./media/container-service-kubernetes-ui/create.png)

    ダイアログ ボックスが開き、アプリケーションの作成を開始できます。

2. `hello-nginx` と名付けます。 [Docker からの `nginx` コンテナー](https://hub.docker.com/_/nginx/)を使用して、この Web サービスの 3 つのレプリカをデプロイします。

    ![Kubernetes ポッドの [作成] ダイアログ](./media/container-service-kubernetes-ui/nginx.png)

3. **[サービス]** で **[外部]** を選び、ポート 80 を入力します。

    この設定は、トラフィックを 3 つのレプリカに負荷分散します。

    ![Kubernetes サービスの [作成] ダイアログ](./media/container-service-kubernetes-ui/service.png)

4. **[デプロイ]** をクリックして、これらのコンテナーとサービスをデプロイします。

    ![Kubernetes デプロイ](./media/container-service-kubernetes-ui/deploy.png)

### <a name="view-your-containers"></a>コンテナーを表示する
**[デプロイ]** をクリックした後、UI は、デプロイされているサービスのビューを表示します。

![Kubernetes の状態](./media/container-service-kubernetes-ui/status.png)

**[Pods (ポッド)]** の下の UI の左側にある円の中で、各 Kubernetes オブジェクトの状態を確認できます。 円が部分的である場合、オブジェクトはデプロイ中であることを示します。 オブジェクトが完全にデプロイされると、緑色のチェック マークが表示されます。

![デプロイされた Kubernetes](./media/container-service-kubernetes-ui/deployed.png)

すべてが正常に実行している場合、Pod のいずれかをクリックすると、実行中の Web サービスに関する詳細が表示されます。

![Kubernetes ポッド](./media/container-service-kubernetes-ui/pods.png)

**[Pods (ポッド)]** ビューでは、Pod でコンテナーについての情報を確認できます。また、これらのコンテナーで使われる CPU およびメモリ リソースも表示できます。

![Kubernetes リソース](./media/container-service-kubernetes-ui/resources.png)

リソースが表示されない場合は、監視データが反映されるまで数分間待つことが必要になる場合があります。

コンテナーのログを表示するには、**[ログの表示]** をクリックします。

![Kubernetes のログ](./media/container-service-kubernetes-ui/logs.png)

### <a name="viewing-your-service"></a>サービスを表示する
コンテナーを実行するほかにも、Kubernetes UI は外部 `Service` を作成しました。これにより、クラスターにあるコンテナーにトラフィックを運ぶロード バランサーがプロビジョニングされます。

左側のナビゲーション ウィンドウで、**[サービス]** をクリックしてすべてのサービスを表示します (1 つだけあるはずです)。

![Kubernetes サービス](./media/container-service-kubernetes-ui/service-deployed.png)

そのビューでは、サービスに割り当てられている外部エンドポイント (IP アドレス) が表示されます。
その IP アドレスをクリックすると、ロード バランサーの背後で実行している Nginx コンテナーが表示されます。

![nginx ビュー](./media/container-service-kubernetes-ui/nginx-page.png)

### <a name="resizing-your-service"></a>サービスのサイズを変更する
UI でオブジェクトを表示するだけではなく、Kubernetes API オブジェクトを編集および更新することもできます。

まず、左側のナビゲーション ウィンドウで **[デプロイメント]** をクリックして、サービスのデプロイを表示します。

そのビューが表示されたら、レプリカ セットをクリックし、上部のナビゲーション バーで **[編集]** をクリックします。

![Kubernetes 編集](./media/container-service-kubernetes-ui/edit.png)

`spec.replicas` フィールドを `2` に編集し、**[更新]** をクリックします。

これで、Pod のいずれかを削除することにより、レプリカの数を 2 つに下げます。

 


