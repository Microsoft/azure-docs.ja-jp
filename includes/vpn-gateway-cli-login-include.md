---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 2ffd8b42bf43658ba488f4c1c92bb7af5b88339e
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755573"
---
[az login](/cli/azure/) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。 サインインの詳細については、「[Azure CLI を使ってみる](/cli/azure/get-started-with-azure-cli)」を参照してください。

```azurecli
az login
```

複数の Azure サブスクリプションを所有している場合は、アカウントのサブスクリプションが一覧表示されます。

```azurecli
az account list --all
```

使用するサブスクリプションを指定します。

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```
