---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: 05794a046fdcb15a91145a75717a6a454d15a8da
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72511493"
---
Azure Disk Encryption は、[Azure CLI](/cli/azure) と [Azure PowerShell](/powershell/azure/new-azureps-module-az) を使用して、有効にして管理することができます。 そのためには、ツールをローカルにインストールし、Azure サブスクリプションに接続する必要があります。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) は、Azure リソースを管理するためのコマンドライン ツールです。 CLI は、データのクエリを柔軟に実行し、長時間実行される操作を非ブロッキング プロセスとしてサポートし、スクリプトが簡単になるように設計されています。 「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」の手順に従って、これをローカルにインストールすることができます。

[Azure CLI を使用して Azure アカウントにログインする](/cli/azure/authenticate-azure-cli)には、[az login](/cli/azure/reference-index?view=azure-cli-latest#az-login) コマンドを使用します。

```azurecli
az login
```

サインインするテナントを選択するには、以下を使用します。
    
```azurecli
az login --tenant <tenant>
```

複数のサブスクリプションがあり、特定のサブスクリプションを指定する場合は、[az account list](/cli/azure/account#az-account-list) を使用してサブスクリプションの一覧を表示し、[az account set](/cli/azure/account#az-account-set) を使用して指定します。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

詳しくは、[Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli)に関する記事をご覧ください。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az モジュール](/powershell/azure/new-azureps-module-az)には、Azure リソースの管理に [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 これは、[Azure Cloud Shell](/azure/cloud-shell/overview) を使用してブラウザーで使用することも、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」の手順に従ってご使用のローカル マシンにインストールすることもできます。 

既にローカルにインストールされている場合、Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用します。 [Azure PowerShell リリース](https://github.com/Azure/azure-powershell/releases)の最新バージョンをダウンロードします。

[Azure PowerShell を使用して Azure アカウントにサインインする](/powershell/azure/authenticate-azureps?view=azps-2.5.0)には、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) コマンドレットを使用します。

```powershell
Connect-AzAccount
```

複数のサブスクリプションがあり、1 つを指定する場合は、[Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) コマンドレットを使用してそれらを一覧表示し、次に [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) コマンドレットを使用します。

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) コマンドレットを実行すると、正しいサブスクリプションが選択されていることが確認されます。

Azure Disk Encryption コマンドレットがインストールされていることを確認するには、[Get-command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6) コマンドレットを使用します。
     
```powershell
Get-command *diskencryption*
```
詳細については、「[Azure PowerShell の使用に関するページ](/powershell/azure/get-started-azureps)」を参照してください。 