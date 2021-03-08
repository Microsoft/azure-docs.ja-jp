---
title: ホスト名、ポート、およびキーを取得する - Azure Cache for Redis - Azure CLI
description: この Azure CLI コード サンプルでは、Azure Cache for Redis インスタンスのホスト名、ポート、およびキーを取得する方法を示します。
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: d3e8e359a97c091e025049ac8a978e1beca1d759
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184223"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Azure Cache for Redis のホスト名、ポート、およびキーを取得する

このシナリオでは、Azure Cache for Redis インスタンスへの接続に使用するホスト名、ポートおよびキーを取得する方法について学習します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して Azure Cache for Redis インスタンスのホスト名、キー、およびポートを取得します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az redis の表示](/cli/azure/redis) | Azure Cache for Redis インスタンスの詳細を取得します。 |
| [az redis list-keys](/cli/azure/redis) | Azure Cache for Redis インスタンスのアクセス キーを取得します。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure Cache for Redis の他の CLI スクリプト サンプルは、[Azure Cache for Redis のドキュメント](../cli-samples.md)にあります。