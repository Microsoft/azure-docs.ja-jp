---
title: Azure Functions ランタイム バージョンをターゲットにする方法
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 Azure でホストされる関数アプリのランタイム バージョンを指定する方法について説明します。
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 5a71338b1b9735d7e7494dc2667bd7addf5d4a53
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151957"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions ランタイム バージョンをターゲットにする方法

関数アプリは、Azure Functions ランタイムの特定のバージョンで実行されます。 メジャー バージョンには、次の 3 つがあります:[1.x、2.x、および 3.x](functions-versions.md)。 既定では、関数アプリはバージョン 2.x のランタイムで作成されます。 この記事では、選択したバージョンで実行されるように Azure の関数アプリを構成する方法について説明します。 特定のバージョン用にローカル開発環境を構成する方法については、「[Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)」を参照してください。

## <a name="automatic-and-manual-version-updates"></a>自動および手動でのバージョンの更新

Azure Functions を使用すると、関数アプリ内で `FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用することで、ランタイムの特定のバージョンをターゲットにすることができます。 関数アプリは、新しいバージョンへの移行を明示的に選択しない限り、指定されたメジャー バージョンに保持されます。

メジャー バージョンのみを指定した場合、関数アプリは、ランタイムの新しいマイナー バージョンが利用可能になった時点で、自動的に新しいマイナー バージョンに更新されます。 新しいマイナー バージョンには、重大な変更は導入できません。 マイナー バージョン (たとえば "2.0.12345") を指定した場合、関数アプリは、明示的にバージョンを変更するまで、その特定のバージョンに固定されます。

> [!NOTE]
> Azure Functions の特定のバージョンに固定された後で、Visual Studio を使用して Azure に発行しようとすると、最新のパ―ジョンへのアップロードまたは発行の取り消しを求めるダイアログ ウィンドウが表示されます。 これを回避するには、`.csproj` ファイルに `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` プロパティを追加します。

新しいバージョンが公開されると、ポータルで確認のメッセージが表示され、そのバージョンに移行することができます。 新しいバージョンに移行した後は、`FUNCTIONS_EXTENSION_VERSION` アプリケーション設定を使用して、いつでも以前のバージョンに戻すことができます。

次の表は、各メジャー バージョンの自動更新を有効にする `FUNCTIONS_EXTENSION_VERSION` 値を示しています。

| メジャー バージョン | `FUNCTIONS_EXTENSION_VERSION` 値 |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

ランタイム バージョンを変更するたびに、関数アプリが再起動されます。

## <a name="view-and-update-the-current-runtime-version"></a>現在のランタイム バージョンの表示と更新

関数アプリによって使用されるランタイム バージョンを変更できます。 破壊的変更の可能性があるため、ランタイム バージョンの変更は、関数アプリで関数を作成する前にのみ実行できます。 

> [!IMPORTANT]
> ランタイム バージョンは `FUNCTIONS_EXTENSION_VERSION` の設定によって決定されますが、この変更はその設定の直接の変更によってではなく、Azure Portal で行う必要があります。 これは、ポータルが変更を検証し、必要に応じてその他の関連する変更を行うためです。

### <a name="from-the-azure-portal"></a>Azure portal から

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure portal では、既に関数が含まれている関数アプリのランタイム バージョンを変更することはできません。

### <a name="view-and-update-the-runtime-version-using-azure-cli"></a>Azure CLI から

Azure CLI から `FUNCTIONS_EXTENSION_VERSION` を表示および設定することもできます。

>[!NOTE]
>その他の設定はランタイム バージョンの影響を受ける可能性があるため、ポータルでバージョンを変更する必要があります。 ランタイム バージョンを変更すると、ポータルで自動的に、Node.js のバージョンやランタイム スタックなどのその他の必要な更新が行われます。  

Azure CLI を使用して、[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドで現在のランタイム バージョンを表示します。

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

このコードでは、`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 

明確にするために切り捨てられていますが、次の出力に `FUNCTIONS_EXTENSION_VERSION` が表示されます。

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドで、関数アプリの `FUNCTIONS_EXTENSION_VERSION` の設定を更新できます。

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```

`<function_app>` をお使いの関数アプリの名前に置き換えます。 また、`<my_resource_group>` をお使いの関数アプリのリソース グループの名前に置き換えます。 また、`<version>` を 1.x ランタイムの有効なバージョンまたはバージョン 2.x の `~2` に置き換えます。

このコマンドは、上記のコード サンプルの **[テスト]** をクリックすることで、[Azure Cloud Shell](../cloud-shell/overview.md) から実行できます。 また、[Azure CLI をローカルに](/cli/azure/install-azure-cli)使用して、[az ログイン](/cli/azure/reference-index#az-login)を実行してサインインした後に、このコマンドを実行することもできます。



## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ローカル開発環境で 2.0 ランタイムをターゲットにする](functions-run-local.md)

> [!div class="nextstepaction"]
> [ランタイムのバージョンについては、リリース ノートを参照してください](https://github.com/Azure/azure-webjobs-sdk-script/releases)
