---
title: Azure Red Hat OpenShift 開発環境の設定
description: Microsoft Azure Red Hat OpenShift を使用して作業するための前提条件を示します。
keywords: red hat openshift セットアップ設定
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: 64b71c135126ebb540d5ccf72f829f83c74ed0b3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495320"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Azure Red Hat OpenShift 開発環境の設定

Microsoft Azure Red Hat OpenShift アプリケーションをビルドして実行するには、次のことを行う必要があります。

* Azure CLI のバージョン 2.0.65 (またはそれ以降) をインストールします (または、Azure Cloud Shell を使用します)。
* `AROGA` 機能および関連付けられているリソース プロバイダーに登録します。
* Azure Active Directory (Azure AD) テナントを作成します。
* Azure AD アプリケーション オブジェクトを作成します。
* Azure AD ユーザーを作成します。

次の手順ではこれらの前提条件のすべてを説明します。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール

Azure Red Hat OpenShift には、Azure CLI のバージョン 2.0.65 またはそれ以降が必要です。 Azure CLI を既にインストール済みである場合は、以下を実行してバージョンを確認できます。

```azurecli
az --version
```

出力の最初の行に CLI バージョン (たとえば `azure-cli (2.0.65)`) が表示されます。

新しいインストールまたはアップグレードが必要な場合、[Azure CLI をインストールする手順はこちらにあります](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

別の方法として、[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) を使用することもできます。 Azure Cloud Shell の使用時、[Azure Red Hat OpenShift クラスターの作成と管理](tutorial-create-cluster.md)のチュートリアル シリーズに従って進める予定の場合は、必ず **Bash** 環境を選択してください。

## <a name="register-providers-and-features"></a>プロバイダーと機能の登録

初めての Azure Red Hat OpenShift クラスターをデプロイする前に、`Microsoft.ContainerService AROGA` 機能、`Microsoft.Solutions`、`Microsoft.Compute`、`Microsoft.Storage`、`Microsoft.KeyVault`、および `Microsoft.Network` プロバイダーを手動でサブスクリプションに登録する必要があります。

これらのプロバイダーと機能を手動で登録するには、CLI をインストール済みの場合は Bash シェルから、または Azure portal 上で Azure Cloud Shell (Bash) セッションから、次の手順を使用します。

1. 複数の Azure サブスクリプションがある場合は、適切なサブスクリプション ID を指定します。

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Microsoft.ContainerService AROGA 機能を登録します。

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Microsoft.Storage プロバイダーを登録します。

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Microsoft.Compute プロバイダーを登録します。

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Microsoft.Solutions プロバイダーを登録します。

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Microsoft.Network プロバイダーを登録します。

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Microsoft.KeyVault プロバイダーを登録します。

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Microsoft.ContainerService リソース プロバイダーの登録を更新します。

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active Directory (Azure AD) テナントの作成

Azure Red Hat OpenShift サービスには、ご自身の組織およびその組織と Microsoft との関係を表す、関連する Azure Active Directory (Azure AD) テナントが必要です。 Azure AD テナントを使用すると、アプリを登録、ビルド、管理するだけでなく、その他の Azure サービスを使用できます。

ご自分の Azure Red Hat OpenShift クラスターのテナントとして使用する Azure AD がない場合、またはテスト用のテナントを作成する必要がある場合は、「[Create an Azure AD tenant for your Azure Red Hat OpenShift cluster](howto-create-tenant.md)」(Azure Red Hat OpenShift クラスター用の Azure AD テナントの作成) の手順に従ってください。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD ユーザー、セキュリティ グループ、アプリケーション オブジェクトを作成する

Azure Red Hat OpenShift には、ストレージの構成など、クラスター上でタスクを実行するためのアクセス許可が必要です。 これらのアクセス許可は、[サービス プリンシパル](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)を介して表されます。 また、Azure Red Hat OpenShift クラスター上で実行されているアプリをテストするための新しい Active Directory ユーザーを作成できます。

「[Azure AD アプリ オブジェクトとユーザーの作成](howto-aad-app-configuration.md)」の手順に従って、サービス プリンシパルを作成し、アプリのクライアント シークレットと認証コールバック URL を生成し、クラスターにアクセスするための新しい Azure AD セキュリティ グループとユーザーを作成します。

## <a name="next-steps"></a>次のステップ

以上で Azure Red Hat OpenShift を使用する準備ができました。

次のチュートリアルを試してください。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
