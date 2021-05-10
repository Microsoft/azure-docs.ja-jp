---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/07/2020
ms.author: danlep
ms.openlocfilehash: 429377cd50e83195cb1c3a422416fdb35644a28e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773472"
---
## <a name="create-a-docker-enabled-virtual-machine"></a>Docker 対応仮想マシンの作成

テスト目的の場合は、Docker 対応の Ubuntu VM を使用して、Azure コンテナー レジストリにアクセスします。 レジストリに Azure Active Directory 認証を使用するには、VM に [Azure CLI][azure-cli] もインストールします。 既に Azure 仮想マシンがある場合は、この作成手順を省略します。

仮想マシンとコンテナー レジストリに同じリソース グループを使用できます。 このセットアップでは最終的にクリーンアップが簡素化されますが、必須ではありません。 仮想マシンと仮想ネットワークに別々のリソース グループを作成することを選択する場合は、[az group create][az-group-create] を実行します。 次の例では、リソース グループ名とレジストリの場所に対して環境変数を設定していることを前提としています。

```azurecli
az group create --name $RESOURCE_GROUP --location $REGISTRY_LOCATION
```

ここで、[az vm create][az-vm-create] を使用して、既定の Ubuntu Azure 仮想マシンをデプロイします。 次の例では、*myDockerVM* という名前の VM を作成します。

```azurecli
VM_NAME=myDockerVM

az vm create \
  --resource-group $RESOURCE_GROUP \
  --name $VM_NAME \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

VM が作成されるまで､数分間かかります｡ コマンドが完了したら、Azure CLI によって表示された `publicIpAddress` をメモします。 このアドレスは、VM への SSH 接続を作成するために使用します。

### <a name="install-docker-on-the-vm"></a>VM に Docker をインストールする

VM が実行されたら、VM への SSH 接続を作成します。 *publicIpAddress* を VM のパブリック IP アドレスに置き換えます。

```bash
ssh azureuser@publicIpAddress
```

次のコマンドを実行して、Ubuntu VM に Docker をインストールします。

```bash
sudo apt-get update
sudo apt install docker.io -y
```

インストールの後、次のコマンドを実行して、VM 上で Docker が正しく実行されていることを確認します。

```bash
sudo docker run -it hello-world
```

出力:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure CLI のインストール

Ubuntu 仮想マシンに Azure CLI をインストールするには、「[apt での Azure CLI のインストール](/cli/azure/install-azure-cli-apt)」の手順に従います。 次に例を示します。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

SSH 接続を終了します。

[azure-cli]: /cli/azure/install-azure-cli
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-group-create]: /cli/azure/group