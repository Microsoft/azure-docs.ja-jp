---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "67181654"
---
コンテナーとの直接 SSH セッションを開くには、アプリが実行されている必要があります。

ブラウザーに次の URL を貼り付け、\<app-name> をお使いのアプリの名前に置き換えます。

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

まだ認証されていない場合、接続するには Azure サブスクリプションで認証する必要があります。 認証されると、ブラウザー内シェルが表示され、コンテナー内でコマンドを実行することができます。

![SSH 接続](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
