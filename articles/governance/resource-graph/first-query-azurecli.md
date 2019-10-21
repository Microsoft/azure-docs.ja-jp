---
title: Azure CLI を使用して初めてのクエリを実行する
description: この記事では、Azure CLI の Resource Graph 拡張機能を有効にして、最初のクエリを実行する手順について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: quickstart
ms.service: resource-graph
ms.openlocfilehash: b50a21f9c1a4bae7ced8afd9a49c33cc936983ac
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389727"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>クイック スタート:Azure CLI を使用して最初の Resource Graph クエリを実行します

Azure Resource Graph を使用する最初の手順では、[Azure CLI](/cli/azure/) の拡張機能がインストールされていることを確認します。 このクイック スタートでは、Azure CLI のインストールに拡張機能を追加するプロセスについて説明します。 ローカルにインストールされている Azure CLI または [Azure Cloud Shell](https://shell.azure.com) により拡張機能を使用することができます。

このプロセスの最後では、選択した Azure CLI のインストールに拡張機能を追加して、最初の Resource Graph クエリを実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="add-the-resource-graph-extension"></a>Resource Graph 拡張機能を追加する

Azure CLI を Azure Resource Graph のクエリに対して有効にするには、拡張機能を追加する必要があります。 [Windows 10上の bash](/windows/wsl/install-win10)、 [Cloud Shell](https://shell.azure.com) (スタンドアロンと内部の両方のポータル)、 [Azure CLI Docker 画像](https://hub.docker.com/r/microsoft/azure-cli/)、または、ローカルにインストールされた場合を含め、この拡張機能はAzure CLI を使用できる場合はいつでも動作します。

1. 最新の Azure CLI がインストールされていることを確認します (**2.0.45** 以降)。 インストールされていない場合は、こちらの[手順](/cli/azure/install-azure-cli-windows?view=azure-cli-latest)に従ってください。

1. 選択された Azure CLI 環境では、次のコマンドでインポートします。

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 拡張機能がインストールされていて、必要なバージョン (**1.0.0** 以降) であることを検証します。

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

選択した環境に Azure CLI 拡張機能が追加されたので、簡単な Resource Graph クエリを試してみましょう。 クエリは、各リソースの**名前**および**リソースの種類**を使用して、最初の 5 つの Azure リソースを返します。

1. `graph`拡張機能と`query`コマンドを使用して、最初の Azure Resource Graph クエリを実行します。

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `order by`**名前**プロパティに対するクエリを更新します。

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 これによりクエリの結果をまず制限し、それからそれらを注文します。

1. 最初に**名前**プロパティで並べ替え (`order by`) を行ってから結果を上位 5 件に制限 (`limit`) するようにクエリを更新します。

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

最終的なクエリを複数回実行したとき、環境内で何も変更がないと仮定すると、返される結果は一貫性があり、想定どおりになります。つまり、結果は**名前**プロパティで並べ替えられますが、上位 5 件に制限されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Resource Graph 拡張機能を Azure CLI 環境から削除する場合は、次のコマンドを使用して行うことができます。

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>次の手順

- [クエリ言語](./concepts/query-language.md)に関するさらなる情報を入手します
- [その他のリソース](./concepts/explore-resources.md)
- [Azure PowerShell ](first-query-powershell.md) を使用して最初のクエリを実行します
- [Starter クエリ](./samples/starter.md)のサンプルを参照してください
- [高度なクエリ](./samples/advanced.md)のサンプルを参照してください
- [UserVoice ](https://feedback.azure.com/forums/915958-azure-governance)にフィードバックを提供してください