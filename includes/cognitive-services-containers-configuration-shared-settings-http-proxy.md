---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: ce4cc68826b39b5707549afc799d2d214e8876c6
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88639093"
---
送信要求を行うために HTTP プロキシを構成する必要がある場合は、次の 2 つの引数を使用します。

| Name | データ型 | 説明 |
|--|--|--|
|HTTPS_PROXY|string|使用するプロキシ。例: `https://proxy:8888`<br>`<proxy-url>`|
|HTTPS_PROXY_CREDS|string|プロキシで認証されるために必要な資格情報。例: ユーザー名:パスワード。|
|`<proxy-user>`|string|プロキシのユーザー。|
|`<proxy-password>`|string|プロキシの `<proxy-user>` に関連付けられているパスワード。|
||||


```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
HTTPS_PROXY=<proxy-url> \
HTTPS_PROXY_CREDS=<proxy-user>:<proxy-password> \
```
