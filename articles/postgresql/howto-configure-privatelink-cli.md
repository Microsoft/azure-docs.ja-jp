---
title: Azure Database for PostgreSQL 単一サーバー (プレビュー) 用のプライベート リンクの CLI のセットアップ方法
description: Azure CLI から Azure Database for PostgreSQL 単一サーバー用のプライベート リンクを構成する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d982771d5c7ebc864991026e399e9648d333cc8f
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425529"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-cli"></a>CLI を使用して Azure Database for PostgreSQL 単一サーバー (プレビュー) 用のプライベート リンクを作成および管理する

プライベート エンドポイントは、Azure におけるプライベート リンクの基本的な構成要素です。 これによって、仮想マシン (VM) などの Azure リソースが Private Link リソースと非公開で通信できるようになります。 この記事では、Azure CLI を使用して Azure Virtual Network 内に VM を作成し、Azure プライベート エンドポイントを含む Azure Database for PostgreSQL 単一サーバーを作成する方法について説明します。

> [!NOTE]
> この機能は、Azure Database for PostgreSQL 単一サーバーが汎用およびメモリ最適化の価格レベルをサポートしているすべての Azure リージョンで使用できます。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を実行するには、以下が必要です。

- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

代わりに Azure CLI をローカルにインストールして使用する場合は、このクイック スタートには Azure CLI バージョン 2.0.28 以降を使用する必要があります。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードについては、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」をご覧ください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソースを作成するには、その前に、仮想ネットワークをホストするリソース グループを作成する必要があります。 [az group create](/cli/azure/group) を使用して、リソース グループを作成します。 この例では、*westeurope* の場所に *myResourceGroup* という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します
[az network vnet create](/cli/azure/network/vnet) を使用して仮想ネットワークを作成します。 この例では、*mySubnet* という名前のサブネットを使って、*myVirtualNetwork* という名前の既定の仮想ネットワークを作成します。

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>サブネットのプライベート エンドポイント ポリシーを無効にする 
Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントのネットワーク ポリシーを無効にするようにサブネットを作成または更新する必要があります。 [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update) を使用して  *mySubnet*  という名前のサブネット構成を更新します。

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM の作成 
az vm create を使用して VM を作成します。 メッセージが表示されたら、VM のサインイン資格情報として使用するパスワードを入力します。 この例では、*myVm* という名前の VM を作成します。 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 VM のパブリック IP アドレスを書き留めます。 このアドレスは、次の手順でインターネットから VM に接続するために使用します。

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 単一サーバーを作成する 
az postgres server create コマンドを使用して Azure Database for PostgreSQL を作成します。 PostgreSQL サーバーの名前は Azure 全体で一意である必要があるため、角かっこ内のプレースホルダー値を独自の一意の値に置き換えることを忘れないでください。 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

PostgreSQL サーバー ID は  ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` と同様であることに注意してください。この PostgreSQL サーバー ID は次の手順で使用します。 

## <a name="create-the-private-endpoint"></a>プライベート エンドポイントを作成する 
Virtual Network 内に PostgreSQL サーバーのプライベート エンドポイントを作成します。 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する 
PostgreSQL サーバー ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network との関連付けリンクを作成します。 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>インターネットから VM に接続する

次のように、インターネットから VM *myVm* に接続します。

1. ポータルの検索バーに、「*myVm*」と入力します。

1. **[接続]** を選択します。 **[接続]** ボタンを選択すると、 **[Connect to virtual machine]\(仮想マシンに接続する\)** が開きます。

1. **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. *downloaded.rdp* ファイルを開きます。

    1. メッセージが表示されたら、 **[Connect]** を選択します。

    1. VM の作成時に指定したユーザー名とパスワードを入力します。

        > [!NOTE]
        > 場合によっては、 **[その他]**  >  **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。

1. **[OK]** を選択します。

1. サインイン処理中に証明書の警告が表示される場合があります。 証明書の警告を受信する場合は、 **[はい]** または **[続行]** を選択します。

1. VM デスクトップが表示されたら最小化してローカル デスクトップに戻ります。  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>VM から PostgreSQL サーバーにプライベートにアクセスする

1.  *myVM* のリモート デスクトップで、PowerShell を開きます。

2. 「 `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`」と入力します。 

    次のようなメッセージが返されます。
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. 使用可能な任意のクライアントを使用して、PostgreSQL サーバーのプライベートリンク接続をテストします。 次の例では、[Azure Data スタジオ](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) を使用して操作を行いました。

4. **新しい接続** で、この情報を入力または選択します。

    | 設定 | Value |
    | ------- | ----- |
    | サーバーの種類| **PostgreSQL** を選択します。|
    | サーバー名| *mydemopostgresserver.privatelink.postgres.database.azure.com* の選択 |
    | ユーザー名 | PostgreSQL サーバーの作成時に指定したusername@servernameユーザ名を入力します。 |
    |Password |PostgreSQL サーバーの作成時に指定したパスワードを入力します。 |
    |SSL|**必須**を選択します。|
    ||

5. [接続] を選択します。

6. 左側のメニューでデータベースを参照します。

7. (省略可能) 情報を作成するか、postgreSQL に対して情報のクエリを実行します。

8. myVM へのリモート デスクトップ接続を閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする 
不要になったら、az group delete を使用して、リソース グループとそのすべてのリソースを削除できます。 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>次のステップ
- [Azure プライベート エンドポイントの概要](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)について学習します。
