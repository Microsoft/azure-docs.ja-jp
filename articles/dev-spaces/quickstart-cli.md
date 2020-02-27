---
title: Kubernetes 上でアプリケーションを開発する
services: azure-dev-spaces
ms.date: 02/20/2020
ms.topic: quickstart
description: このクイックスタートでは、Azure Dev Spaces とコマンド ラインを使用し、Azure Kubernetes Service 上でアプリケーションを開発する方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 2d3a498d72264d3084e45202b7daa99806d45ce3
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602615"
---
# <a name="quickstart-develop-an-application-on-kubernetes---azure-dev-spaces"></a>クイック スタート:Kubernetes 上でアプリケーションを開発する - Azure Dev Spaces
このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- コマンド ラインを使用して、コンテナー内でコードを開発して実行します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
- [Azure CLI がインストールされていること](/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="create-an-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターを作成する

[サポートされているリージョン][supported-regions]で AKS クラスターを作成する必要があります。 下記のコマンドを使用すると、*MyResourceGroup* というリソース グループと *MyAKS* という AKS クラスターが作成されます。

```cmd
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus --generate-ssh-keys
```

## <a name="enable-azure-dev-spaces-on-your-aks-cluster"></a>AKS クラスターで Azure Dev Spaces を有効にする

`use-dev-spaces` コマンドを使用して AKS クラスターで Dev Spaces を有効にし、プロンプトに従います。 下記のコマンドを使用すると、*MyResourceGroup* グループ内の *MyAKS* クラスターで Dev Spaces が有効になり、*default* 開発空間が作成されます。

> [!NOTE]
> この `use-dev-spaces` コマンドでは、Azure Dev Spaces CLI がまだインストールされていない場合にはこれもインストールされます。 Azure Dev Spaces CLI を Azure Cloud Shell にインストールすることはできません。

```cmd
$ az aks use-dev-spaces -g MyResourceGroup -n MyAKS

'An Azure Dev Spaces Controller' will be created that targets resource 'MyAKS' in resource group 'MyResourceGroup'. Continue? (y/N): y

Creating and selecting Azure Dev Spaces Controller 'MyAKS' in resource group 'MyResourceGroup' that targets resource 'MyAKS' in resource group 'MyResourceGroup'...2m 24s

Select a dev space or Kubernetes namespace to use as a dev space.
 [1] default
Type a number or a new name: 1

Kubernetes namespace 'default' will be configured as a dev space. This will enable Azure Dev Spaces instrumentation for new workloads in the namespace. Continue? (Y/n): Y

Configuring and selecting dev space 'default'...3s

Managed Kubernetes cluster 'MyAKS' in resource group 'MyResourceGroup' is ready for development in dev space 'default'. Type `azds prep` to prepare a source directory for use with Azure Dev Spaces and `azds up` to run.
```

## <a name="get-sample-application-code"></a>サンプル アプリケーション コードを取得する

この記事では、[Azure Dev Spaces サンプル アプリケーション](https://github.com/Azure/dev-spaces)を使用して、Azure Dev Spaces の使い方のデモを行います。

GitHub からアプリケーションを複製して、*dev-spaces/samples/nodejs/getting-started/webfrontend* ディレクトリに移動します。

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/nodejs/getting-started/webfrontend
```

## <a name="prepare-the-application"></a>アプリケーションを準備する

Azure Dev Spaces でアプリケーションを実行するには、Dockerfile と Helm チャートが必要です。 [Java][java-quickstart]、[.NET core][netcore-quickstart]、[Node.js][nodejs-quickstart] などの一部の言語では、必要なすべての資産を Azure Dev Spaces クライアント ツールで生成できます。 Go、PHP、Python など、他の多くの言語では、有効な Dockerfile を提供できる限り、クライアント ツールで Helm チャートを生成できます。

`azds prep` コマンドを使用して、Kubernetes でアプリケーションを実行するための Docker 資産と Helm チャート資産を生成します。

```cmd
azds prep --enable-ingress
```

Docker 資産と Helm チャート資産を正しく生成するには、*dev-spaces/samples/nodejs/getting-started/webfrontend* ディレクトリから `prep` コマンドを実行する必要があります。

> [!TIP]
> `prep` コマンドを実行すると、[対象のプロジェクトの Dockerfile と Helm チャート](how-dev-spaces-works.md#prepare-your-code)の生成が試行されます。 これらのファイルは、対象のコードをビルドして実行するために Azure Dev Spaces によって使用されますが、プロジェクトのビルドおよび実行方法を変更する場合は、これらのファイルを変更することができます。

## <a name="build-and-run-code-in-kubernetes"></a>Kubernetes でコードをビルドして実行する

`azds up` コマンドを使用して、AKS でコードをビルドして実行します。

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
Step 1/8 : FROM node
Step 2/8 : ENV PORT 80
Step 3/8 : EXPOSE 80
Step 4/8 : WORKDIR /app
Step 5/8 : COPY package.json .
Step 6/8 : RUN npm install
Step 7/8 : COPY . .
Step 8/8 : CMD ["npm", "start"]
Built container image in 6m 17s
Waiting for container...13s
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (http) is available at http://localhost:54256
...
```

`azds up` コマンドの出力に表示されるパブリック URL を開いて、サービスが稼働していることを確認できます。 この例では、パブリック URL は *http://webfrontend.1234567890abcdef1234.eus.azds.io/* です。

> [!NOTE]
> `azds up` の実行中にサービスに移動すると、`azds up` コマンドの出力に HTTP 要求のトレースも表示されます。 それらのトレースを、サービスのトラブルシューティングやデバッグに活かすことができます。 トレースは、`azds up` の実行時に `--disable-http-traces` を使用して無効にできます。

*Ctrl + C* キーを押して `azds up` コマンドを停止すると、サービスは引き続き AKS で稼働し、パブリック URL は使用可能な状態のままになります。

## <a name="update-code"></a>コードの更新

サービスの更新バージョンをデプロイするには、ご自分のプロジェクトにある任意のファイルを更新して、`azds up` コマンドを再実行します。 次に例を示します。

1. `azds up` がまだ実行されている場合、*Ctrl + C* キーを押します。
1. [`server.js` の 13 行目](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13)を以下に更新します。
    
    ```javascript
        res.send('Hello from webfrontend in Azure');
    ```

1. 変更を保存します。
1. `azds up` コマンドを再実行します。

    ```cmd
    $ azds up
    Using dev space 'default' with target 'MyAKS'
    Synchronizing files...1s
    Installing Helm chart...3s
    Waiting for container image build...
    ...    
    ```

1. 稼働中のご自分のサービスに移動して、変更を確認します。
1. *Ctrl + C* キーを押して、`azds up` コマンドを停止します。

## <a name="clean-up-your-azure-resources"></a>Azure リソースをクリーンアップする

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。

> [!div class="nextstepaction"]
> [Azure Dev Spaces でのチーム開発][team-quickstart]

[java-quickstart]: quickstart-java.md
[nodejs-quickstart]: quickstart-nodejs.md
[netcore-quickstart]: quickstart-netcore.md
[team-quickstart]: quickstart-team-development.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service