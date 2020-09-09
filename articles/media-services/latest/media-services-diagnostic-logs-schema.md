---
title: Azure Media Services の診断ログのスキーマ - Azure
description: この記事では、Azure Media Services 診断ログのスキーマを示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 8cfbe26458de630aaf411aade4a31cb4e9c72b17
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295429"
---
# <a name="diagnostic-logs-schemas"></a>診断ログのスキーマ

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 Media Services の診断ログを監視し、収集されたメトリックおよびログのアラートと通知を作成できます。 ログを [Azure Storage](https://azure.microsoft.com/services/storage/) に送信し、それらを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信して、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) にエクスポートできます。またはサード パーティのサービスを使用できます。

詳細については、 [Azure Monitor メトリック](../../azure-monitor/platform/data-platform.md)および [Azure Monitor 診断ログ](../../azure-monitor/platform/platform-logs-overview.md)に関する記事をご覧ください。

この記事では、Media Services の診断ログのスキーマについて説明します。

## <a name="top-level-diagnostic-logs-schema"></a>診断ログの上位スキーマ

診断ログの上位スキーマについて詳しくは、「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ](../../azure-monitor/platform/resource-logs-schema.md)」をご覧ください。

## <a name="key-delivery-log-schema"></a>キー配信ログのスキーマ

### <a name="properties"></a>Properties

これらのプロパティは、キー配信ログのスキーマに固有です。

|名前|説明|
|---|---|
|keyId|要求されたキーの ID。|
|keyType|次のいずれかの値を指定できます:"Clear" (暗号化なし)、"FairPlay"、"PlayReady"、または"Widevine"。|
|policyName|ポリシーの Azure Resource Manager の名前。|
|tokenType|トークンの型。|
|statusMessage|状態メッセージ。|

### <a name="examples"></a>例

キー配信の要求スキーマのプロパティ。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ

[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)
