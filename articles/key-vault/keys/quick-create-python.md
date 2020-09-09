---
title: クイックスタート - Python 用の Azure Key Vault クライアント ライブラリ - キーの管理
description: Python クライアント ライブラリを使用して Azure Key Vault からキーを作成、取得、削除する方法について学習します。
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 18ba00b39d8ffd703eb31b95d373e5b89e51c59b
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376827"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>クイック スタート:Python 用 Azure Key Vault キー クライアント ライブラリ

Python 用 Azure Key Vault クライアント ライブラリを使ってみます。 以下の手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。

Azure Key Vault は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットをセキュリティで保護するために役立ちます。 Python 用 Key Vault クライアント ライブラリは、次の目的で使用します。

- キーとパスワードのセキュリティと制御を強化する。
- 暗号化キーの作成とインポートを数分で実行する。
- クラウド スケールおよびグローバルな冗長性により待ち時間を短縮する。
- TLS または SSL 証明書のタスクを簡略化および自動化する。
- FIPS 140-2 レベル 2 への準拠が検証済みの HSM を使用する。

[API のリファレンスのドキュメント](/python/api/overview/azure/keyvault-keys-readme?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [パッケージ (Python Package Index)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Python 2.7、3.5.3 以降
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) または [Azure PowerShell](/powershell/azure/)。

このクイックスタートは、Linux ターミナル ウィンドウで [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) を実行していることを前提としています。

## <a name="setting-up"></a>設定

### <a name="install-the-package"></a>パッケージをインストールする

コンソール ウィンドウから、Python 用 Azure Key Vault キー ライブラリをインストールします。

```console
pip install azure-keyvault-keys
```

このクイックスタートでは、azure.identity パッケージもインストールする必要があります。

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>リソース グループとキー コンテナーを作成する

このクイックスタートでは、あらかじめ作成しておいた Azure キー コンテナーを使用します。 キー コンテナーは、[Azure CLI のクイックスタート](quick-create-cli.md)、[Azure PowerShell のクイックスタート](quick-create-powershell.md)、または [Azure portal のクイックスタート](quick-create-portal.md)の手順に従って作成できます。 または、以下の Azure CLI コマンドを実行することもできます。

> [!Important]
> 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>サービス プリンシパルの作成

クラウドベースのアプリケーションの認証を行うための最も簡単な方法は、マネージド ID を使用することです。詳細については、「[Azure Key Vault に対する認証](../general/authentication.md)」を参照してください。 

ただし簡潔にするために、このクイックスタートではデスクトップ アプリケーションを作成します。デスクトップ アプリケーションでは、サービス プリンシパルとアクセス制御ポリシーを使用する必要があります。 サービス プリンシパルは、"http://&lt;my-unique-service-principal-name&gt;" の形式で一意の名前にする必要があります。

Azure CLI の [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用してサービス プリンシパルを作成します。

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

この操作では、一連のキーと値のペアが返されます。 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

clientId および clientSecret を書き留めておきます。これらは、下記の手順「[環境変数の設定](#set-environmental-variables)」で使用します。

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>サービス プリンシパルにキー コンテナーへのアクセス権を付与する

[az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) コマンドに clientId を渡すことでご利用のサービス プリンシパルにアクセス許可を付与するアクセス ポリシーをご利用のキー コンテナー用に作成します。 キーに対する get、list、create の各アクセス許可をサービス プリンシパルに付与します。

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>環境変数の設定

アプリケーションの DefaultAzureCredential メソッドは、3 つの環境変数 (`AZURE_CLIENT_ID`、`AZURE_CLIENT_SECRET`、`AZURE_TENANT_ID`) に依存しています。 これらの変数を、`export VARNAME=VALUE` 形式を使用して「[サービス プリンシパルの作成](#create-a-service-principal)」の手順に記載されている clientId、clientSecret、tenantId の値に設定します。 (この方法では、現在のシェルの変数とシェルから作成されたプロセスのみが設定されます。これらの変数を環境に永続的に追加するには、`/etc/environment ` ファイルを編集します。) 

また、キー コンテナー名を `KEY_VAULT_NAME` という環境変数として保存する必要もあります。

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>オブジェクト モデル

Python 用 Azure Key Vault クライアント ライブラリを使用すると、キーおよび関連するアセット (証明書、シークレットなど) を管理できます。 以下のコード サンプルでは、クライアントの作成、キーの作成、キーの取得、キーの削除を行う方法を示します。

## <a name="code-examples"></a>コード例

### <a name="add-directives"></a>ディレクティブの追加

コードの先頭に次のディレクティブを追加します。

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>クライアントの認証と作成

キー コンテナーに対する認証とキー コンテナー クライアントの作成は、上記の「[環境変数の設定](#set-environmental-variables)」の手順にある環境変数によって異なります。 キー コンテナーの名前は、"https://<your-key-vault-name>.vault.azure.net" という形式で、キー コンテナーの URI に展開されます。

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>キーの保存

アプリケーションが認証されたら、キー コンテナーにキーを設定できます。 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

キーが設定されたことは、[az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) コマンドを使用して確認できます。

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>キーの取得

以前作成したキーを取得できます。

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

キーは `retrieved_key` として保存されます。

### <a name="delete-a-key"></a>キーの削除

最後に、キー コンテナーからキーを削除してみましょう。

```python
client.delete_key(keyName)
```

[az keyvault key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) コマンドを使用して、キーがなくなったことを確認できます。

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure CLI または Azure PowerShell を使用して、キー コンテナーとそれに対応するリソース グループを削除できます。

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>サンプル コード

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成し、キーを格納して、そのキーを取得しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)
- [Azure Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)
