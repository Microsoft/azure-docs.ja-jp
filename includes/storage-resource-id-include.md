---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/15/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 39b61c78cbd0333183c80234256d6041a109d846
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "68249914"
---
Azure AD リソース ID は、Azure リソースへのアクセスを与える目的で、発行されたトークンを利用できる対象ユーザーを示します。 Azure Storage の場合、リソース ID は 1 つのストレージ アカウントに固有となるか、あらゆるストレージ アカウントに適用されます。 次の表では、リソース ID に指定できる値の詳細についてまとめています。

|Resource ID  |説明  |
|---------|---------|
|`https://<account>.blob.core.windows.net` <br /><br /> `https://<account>.queue.core.windows.net`    | 特定のストレージ アカウントのサービス エンドポイント。 この値を使用し、その特定の Azure Storage アカウントとサービスに対してのみ要求を承認するためのトークンを取得します。 実際のストレージ アカウントの名前に値を置き換えます。      |
|`https://storage.azure.com/`     | あらゆる Azure Storage アカウントへの要求を承認するトークンを取得するために使用します。        |
