---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86218439"
---
以下の手順では、次の構成を使用しました。

- コンピューター:Ubuntu Server 18.04
- 依存関係: strongSwan


次のコマンドを使用して、必要な strongSwan 構成をインストールします。

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

次のコマンドを使用して、Azure コマンド ライン インターフェイスをインストールします。

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI のインストール方法の詳細](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
