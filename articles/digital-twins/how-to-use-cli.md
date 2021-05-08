---
title: Azure Digital Twins CLI を使用する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins CLI の概要と使用方法について説明します。
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5037450d401153811899b8d769ca92af7ce4068e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103778"
---
# <a name="use-the-azure-digital-twins-cli"></a>Azure Digital Twins CLI を使用する

Azure portal で Azure Digital Twins インスタンスを管理できるだけでなく、Azure Digital Twins には、 **[Azure CLI](/cli/azure/what-is-azure-cli) のコマンド セット** があり、このサービスを使用して、次のような主要なアクションのほとんどを実行できます。
* Azure Digital Twins インスタンスの管理
* モデルの管理
* デジタル ツインの管理
* ツイン リレーションシップの管理
* エンドポイントの構成
* [ルート](concepts-route-events.md)の管理
* Azure ロールベースのアクセス制御 (Azure RBAC) を使用した[セキュリティ](concepts-security.md)の構成

コマンド セットは、**az dt** と呼ばれ、[Azure CLI の Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)の一部として存在します。 コマンドとその用途の完全な一覧は、`az iot` コマンド セット [*az dt* コマンド参照](/cli/azure/dt)の参照ドキュメントで参照できます。

## <a name="uses-deploy-and-validate"></a>使用する (デプロイと検証)

CLI は、インスタンスの一般的な管理だけでなく、デプロイと検証にも役立つツールです。
* コントロール プレーン コマンドを使用すると、新しいインスタンスのデプロイを反復可能にしたり、自動化したりすることができます。
* データ プレーン コマンドを使用すると、インスタンスの値をすばやく確認し、操作が期待どおりに完了したことを確認できます。

## <a name="get-the-command-set"></a>コマンド セットを取得する

Azure Digital Twins コマンドは、[Azure CLI (azure iot) の Azure IoT 拡張機能](https://github.com/Azure/azure-iot-cli-extension)の一部であるため、次の手順に従い、**az dt** コマンドを使用して、最新の `azure-iot` 拡張機能を使用していることを確認してください。

### <a name="cli-version-requirements"></a>CLI バージョンの要件

PowerShell で Azure CLI を使用している場合、拡張機能パッケージは、Azure CLI バージョンが **2.3.1** 以上である必要があります。

Azure CLI のバージョンを確認するには、次の CLI コマンドを使用します。
```azurecli
az --version
```

Azure CLI をインストールまたは新しいバージョンに更新する方法については、「[*Azure CLI のインストール*](/cli/azure/install-azure-cli)」を参照してください。

### <a name="get-the-extension"></a>拡張機能を取得する

拡張機能を必要とするコマンドを初めて使用するときに、それをインストールするように求めるプロンプトが Azure CLI により自動的に表示されます。

あるいは、次のコマンドを使用して、いつでも拡張機能を自分でインストールできます (または、以前のバージョンが既にあることがわかった場合はそれを更新します)。 このコマンドは、[Azure Cloud Shell](../cloud-shell/overview.md) または[ローカルの Azure CLI](/cli/azure/install-azure-cli) で実行できます。

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>次のステップ

CLI とその完全なコマンド セットを、参照ドキュメント:
* [*az dt* コマンド参照](/cli/azure/dt)で確認する