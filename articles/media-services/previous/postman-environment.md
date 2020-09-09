---
title: Azure Media Services REST 呼び出し用の Postman 環境のインポート
description: このトピックでは、Azure Media Services REST 呼び出し用の Postman 環境の定義を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 2ccc765a3fb0063d92382edcec260808183f27a2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89259253"
---
# <a name="import-the-postman-environment"></a>Postman 環境のインポート

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

この記事には、Media Services REST API を呼び出す、グループ化された HTTP 要求を含む [Postman コレクション](postman-collection.md)で使用される **Postman** 環境変数の定義が示されています。 環境およびコレクション ファイルは「[Configure Postman for Media Services REST API calls (Media Services REST API のための Postman の構成)](media-rest-apis-with-postman.md)」チュートリアルで使用されます。

> [!NOTE]
> `AzureADSTSEndpoint ` = `https://login.microsoftonline.com/{{TenantId}}/oauth2/token` の値です。 テナント ID を取得するには、ポータル (右上隅) のユーザー名にマウス ポインターを置きます。"ディレクトリ:Microsoft ( {{TENANTID}} )" という形式で表示されます。

```
{
  "id": "2dbce3ce-74c2-2ceb-0461-c4c2323f5b09",
  "name": "AzureMedia",
  "values": [
    {
      "enabled": true,
      "key": "TenantID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AzureADSTSEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "RESTAPIEndpoint",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientID",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "ClientSecret",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "AccessToken",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAssetId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastAccessPolicyId",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "UploadURL",
      "value": "",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "MediaFileName",
      "value": "BigBuckBunny.mp4",
      "type": "text"
    },
    {
      "enabled": true,
      "key": "LastChannelId",
      "value": "",
      "type": "text"
    }
  ],
  "_postman_variable_scope": "environment",
  "_postman_exported_using": "Postman/5.5.0"
}
```
