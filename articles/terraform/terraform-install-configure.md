---
title: Azure リソースを作成するために Terraform をインストールして構成する
description: Azure リソースを作成するための Terraform のインストールと構成
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tomarchermsft
manager: gwallace
editor: na
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2019
ms.author: tarcher
ms.openlocfilehash: 74728fb05e900c534580f1c8eaf14dd0e48fc42c
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473355"
---
# <a name="install-and-configure-terraform-to-provision-azure-resources"></a>Azure リソースを作成するために Terraform をインストールして構成する
 
Terraform は、[シンプルなテンプレート言語](https://www.terraform.io/docs/configuration/syntax.html)を使ってクラウド インフラストラクチャを簡単に定義、プレビュー、およびデプロイできるツールです。 この記事では、Terraform を使用して Azure にリソースをプロビジョニングするために必要な手順について説明します。

Azure で Terraform を使用する方法の詳細については、[Terraform ハブ](/azure/terraform)に関する記事を参照してください。
> [!NOTE]
> Terraform 固有のサポートについては、コミュニティ チャネルのいずれかを使用して Terraform に直接問い合わせてください。
>
>   • コミュニティ ポータルの [Terraform セクション](https://discuss.hashicorp.com/c/terraform-core)には、質問、ユース ケース、および役立つパターンが含まれています。
>
>   • プロバイダー関連の質問については、コミュニティ ポータルの [Terraform プロバイダー](https://discuss.hashicorp.com/c/terraform-providers) セクションにアクセスしてください。



[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[Cloud Shell](/azure/terraform/terraform-cloud-shell) には、Terraform が既定でインストールされています。 Terraform をローカルにインストールする場合は、次の手順を完了してください。それ以外の場合は、「[Terraform から Azure にアクセスするための設定](#set-up-terraform-access-to-azure)」に進んでください。

## <a name="install-terraform"></a>Terraform のインストール

Terraform をインストールするには、ご利用のオペレーティング システムに適したパッケージを別個のインストール ディレクトリに[ダウンロード](https://www.terraform.io/downloads.html)します。 ダウンロードには実行可能ファイルが 1 つ含まれており、そのファイルのグローバル パスを定義する必要があります。 Linux と Mac 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux)をご覧ください。 Windows 上でパスを設定する方法の詳細については、[こちらの Web ページ](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows)をご覧ください。

`terraform` コマンドでパス構成を確認します。 次の出力例のように、使用可能な Terraform オプションの一覧が表示されます。

```console
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Terraform から Azure にアクセスするための設定

Terraform で Azure にリソースをプロビジョニングできるようにするには、[Azure AD サービス プリンシパル](/cli/azure/create-an-azure-service-principal-azure-cli)を作成します。 サービス プリンシパルは、Terraform スクリプトが Azure サブスクリプションにリソースをプロビジョニングすることを許可します。

複数の Azure サブスクリプションがある場合は、先に [az account list](/cli/azure/account#az-account-list) を使用してアカウントに対するクエリを実行して、サブスクリプション ID とテナント ID の値を取得します。

```azurecli-interactive
az account list --query "[].{name:name, subscriptionId:id, tenantId:tenantId}"
```

選択したサブスクリプションを使用するには、[az account set](/cli/azure/account#az-account-set). を使用して、このセッション用のサブスクリプションを設定します。 使用するサブスクリプションから返された `id` フィールドの値を `SUBSCRIPTION_ID` 環境変数に設定します。

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

これで、Terraform で使用するサービス プリンシパルを作成できます。 次のように、[az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) を使用して、サブスクリプションの*スコープ*を設定します。

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

ご自分の `appId`、`password`、`sp_name`、および `tenant` が返されます。 `appId` と `password` を書き留めておきます。

## <a name="configure-terraform-environment-variables"></a>Terraform 環境変数の構成

Azure AD サービス プリンシパルを使用するように Terraform を構成するには、次の環境変数を設定します。これは、後で [Azure Terraform モジュール](https://registry.terraform.io/modules/Azure)によって使用されます。 Azure パブリック以外の Azure クラウドで作業している場合は、環境を設定することもできます。

- `ARM_SUBSCRIPTION_ID`
- `ARM_CLIENT_ID`
- `ARM_CLIENT_SECRET`
- `ARM_TENANT_ID`
- `ARM_ENVIRONMENT`

これらの変数は、次のサンプル シェル スクリプトを使用して設定できます。

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id

# Not needed for public, required for usgovernment, german, china
export ARM_ENVIRONMENT=public
```

## <a name="run-a-sample-script"></a>サンプル スクリプトの実行

空のディレクトリに `test.tf` というファイルを作成し、次のスクリプトを貼り付けます。

```hcl
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

ファイルを保存し、Terraform のデプロイを初期化します。 この手順によって、Azure リソース グループを作成するために必要な Azure モジュールがダウンロードされます。

```bash
terraform init
```

出力は次の例のようになります。

```console
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

`terraform plan` を使用して、Terraform スクリプトによって完了されるアクションをプレビューできます。 リソース グループを作成する準備が完了したら、次のように、Terraform プランを適用します。

```bash
terraform apply
```

出力は次の例のようになります。

```console
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>次のステップ

この記事では、Terraform をインストールするか、Cloud Shell を使用して Azure の資格情報を構成し、Azure サブスクリプションでのリソースの作成を開始しました。 Azure でより完全な Terraform デプロイを作成するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Terraform がインストールされている Azure VM を作成する](terraform-create-complete-vm.md)
