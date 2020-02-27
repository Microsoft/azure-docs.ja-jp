---
title: 'Kubernetes 上でデバッグと反復処理を行う: Visual Studio Code と .NET Core'
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: quickstart
description: このクイックスタートでは、Azure Dev Spaces と Visual Studio Code を使用し、Azure Kubernetes Service 上で .NET Core アプリケーションのデバッグと迅速な反復型開発を行う方法について説明します。
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
manager: gwallace
ms.openlocfilehash: c2d84e823d028f542c5ab852be2ea68a5abafe93
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605292"
---
# <a name="quickstart-debug-and-iterate-on-kubernetes-visual-studio-code-and-net-core---azure-dev-spaces"></a>クイック スタート:Kubernetes 上でデバッグと反復処理を行う: Visual Studio Code と .NET Core - Azure Dev Spaces

このガイドでは、以下の方法について説明します。

- Azure でマネージド Kubernetes クラスターを使用して Azure Dev Spaces をセットアップする。
- Visual Studio Code を使用して、コンテナー内のコードを繰り返し開発する。
- Visual Studio Code で開発空間のコードをデバッグする。

Azure Dev Spaces では、次のものを使用してデバッグと反復処理を行うこともできます。
- [Java と Visual Studio Code](quickstart-java.md)
- [Node.js と Visual Studio Code](quickstart-nodejs.md)
- [.NET Core と Visual Studio](quickstart-netcore-visualstudio.md)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 アカウントがない場合は、[無料アカウントを作成する](https://azure.microsoft.com/free)ことができます。
- [Visual Studio Code がインストールされていること](https://code.visualstudio.com/download)。
- Visual Studio Code 用の [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) 拡張機能と [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 拡張機能がインストールされていること。
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

GitHub からアプリケーションを複製します。

```cmd
git clone https://github.com/Azure/dev-spaces
```

## <a name="prepare-the-sample-application-in-visual-studio-code"></a>Visual Studio Code でサンプル アプリケーションを準備する

Visual Studio Code を開き、 *[ファイル]* 、 *[開く]* の順にクリックし、*dev-spaces/samples/dotnetcore/getting-started/webfrontend* ディレクトリに移動して、 *[開く]* をクリックします。

これで、*webfrontend* プロジェクトが Visual Studio Code で開かれました。 アプリケーションをご利用の開発スペースで実行するには、コマンド パレット内の Azure Dev Spaces 拡張機能を使用して Docker および Helm チャート資産を作成します。

Visual Studio Code でコマンド パレットを開くには、 *[表示]* 、 *[コマンド パレット]* の順にクリックします。 「`Azure Dev Spaces`」の入力を開始して、`Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces` をクリックします。

![Azure Dev Spaces の構成ファイルを準備する](./media/common/command-palette.png)

また Visual Studio Code からパブリック エンドポイントを構成するように求められた場合は、`Yes` を選択してパブリック エンドポイントを有効にします。

![パブリック エンドポイントを選択する](media/common/select-public-endpoint.png)

このコマンドでは、Dockerfile および Helm チャートを生成することで、ご利用のプロジェクトを Azure Dev Spaces で実行するための準備が行われます。 また、デバッグ構成が含まれた *.vscode* ディレクトリが、お客様のプロジェクトのルートに生成されます。

> [!TIP]
> プロジェクトの [Dockerfile と Helm チャート](how-dev-spaces-works.md#prepare-your-code)は、対象のコードをビルドして実行するために Azure Dev Spaces によって使用されますが、プロジェクトのビルドおよび実行方法を変更する場合は、これらのファイルを変更することができます。

## <a name="build-and-run-code-in-kubernetes-from-visual-studio-code"></a>Visual Studio Code で Kubernetes のコードをビルドして実行する

左側の *[デバッグ]* アイコンをクリックし、上部の *[.NET Core Launch (AZDS)]\(.NET Core の起動 (AZDS)\)* をクリックします。

![](media/get-started-netcore/debug-configuration.png)

このコマンドによって、デバッグ モードで Azure Dev Spaces のサービスがビルドされ、稼働します。 下部にある *[ターミナル]* ウィンドウに、Azure Dev Spaces で動作しているサービスのビルド出力と URL が表示されます。 "*デバッグ コンソール*" にログの出力が表示されます。

> [!Note]
> "*コマンド パレット*" に Azure Dev Spaces コマンドが表示されない場合は、[Azure Dev Spaces 用 Visual Studio Code 拡張機能](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds)がインストールされていることを確認してください。 また、Visual Studio Code で *dev-spaces/samples/dotnetcore/getting-started/webfrontend* ディレクトリを開いたことを確認してください。

パブリック URL を開くと、実行中のサービスを確認できます。

*[デバッグ]* 、 *[デバッグの停止]* の順にクリックして、デバッガーを停止します。

## <a name="update-code"></a>コードの更新

サービスの更新バージョンをデプロイするには、ご自分のプロジェクトにある任意のファイルを更新して、 *[.NET Core Launch (AZDS)]\(.NET Core の起動 (AZDS)\)* を再実行します。 次に例を示します。

1. ご利用のアプリケーションがまだ実行されている場合は、 *[デバッグ]* 、 *[デバッグの停止]* の順にクリックしてそれを停止します。
1. [`Controllers/HomeController.cs` の 22 行目](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs#L22)を以下の内容に更新します。
    
    ```csharp
    ViewData["Message"] = "Your application description page in Azure.";
    ```

1. 変更を保存します。
1. *[.NET Core Launch (AZDS)]\(.NET Core の起動 (AZDS)\)* を再実行します。
1. 稼働中の自分のサービスに移動して、 *[詳細]* をクリックします。
1. 変更を確認します。
1. *[デバッグ]* 、 *[デバッグの停止]* の順にクリックして、ご自分のアプリケーションを停止します。

## <a name="setting-and-using-breakpoints-for-debugging"></a>デバッグ用のブレークポイントを設定して使用する

*[.NET Core Launch (AZDS)]\(.NET Core の起動 (AZDS)\)* を使用して、デバッグ モードでサービスを開始します。

*[表示]* 、 *[エクスプローラー]* の順にクリックして、 *[エクスプローラー]* ビューに戻ります。 `Controllers/HomeController.cs` を開き、22 行目のどこかをクリックして、カーソルをそこに置きます。 ブレークポイントを設定するには、*F9* キーを押すか、 *[デバッグ]* 、 *[ブレークポイントの設定/解除]* の順にクリックします。

ブラウザーでサービスを開き、メッセージが表示されないことに注目します。 Visual Studio Code に戻って、20 行目が強調表示されていることを確認します。 設定したブレークポイントによって、20 行目でサービスが一時停止されました。 サービスを再開するには、*F5* キーを押すか、 *[デバッグ]* 、 *[続行]* の順にクリックします。 ブラウザーに戻って、メッセージが表示されたことに注目します。

デバッガーがアタッチされた状態で Kubernetes でサービスを稼働している間、デバッグ情報 (呼び出し履歴、ローカル変数、例外情報など) にフル アクセスできます。

`Controllers/HomeController.cs` の 22 行目にカーソルを置いて *F9* キーを押すことで、ブレークポイントを削除します。

## <a name="update-code-from-visual-studio-code"></a>Visual Studio Code でコードを更新する

デバッグ モードでサービスが稼働している間に、`Controllers/HomeController.cs` の 22 行目を更新します。 次に例を示します。

```csharp
ViewData["Message"] = "Your application description page in Azure while debugging!";
```

ファイルを保存します。 *[デバッグ]* 、 *[デバッグの再起動]* の順にクリックします。または、 *[デバッグ] ツール バー*で、 *[デバッグの再起動]* ボタンをクリックします。

![](media/common/debug-action-refresh.png)

ブラウザーでサービスを開き、更新されたメッセージが表示されることに注目します。

Azure Dev Spaces では、コードが編集されるたびに新しいコンテナー イメージをリビルドして再デプロイするのではなく、既存のコンテナー内でコードの増分再コンパイルを実行して、編集とデバッグのループを高速化します。

## <a name="clean-up-your-azure-resources"></a>Azure リソースをクリーンアップする

```cmd
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces を使用して複数のコンテナーにまたがるより複雑なアプリケーションを開発する方法と、別の空間で別のバージョンまたは分岐を使用して作業することによって共同開発を簡略化する方法について学習します。 

> [!div class="nextstepaction"]
> [複数のコンテナーの操作とチーム開発](multi-service-netcore.md)


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
