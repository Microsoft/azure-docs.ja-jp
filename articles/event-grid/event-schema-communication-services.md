---
title: Event Grid ソースとしての Azure Communication Services
description: この記事では、Event Grid イベント ソースとして Azure Communication Services を使用する方法について説明します。
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: mikben
ms.openlocfilehash: 862d5edeecf0be1de8026d21342ede2ef1851e8c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363595"
---
# <a name="azure-communication-services-as-an-event-grid-source"></a>Event Grid ソースとしての Azure Communication Services

> [!IMPORTANT]
> Azure Communication Services は現在パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、Azure Communication Services イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Event Grid は、[イベント サブスクリプション](./concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。 

Azure Communication Services から出力されるイベントの種類は次のとおりです。

| イベントの種類                                                  | 説明                                                                                    |
| ----------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| Microsoft.Communication.SMSReceived                         | Communication Service に関連付けられた電話番号によって SMS が受信されたときに発行されます。 |
| Microsoft.Communication.SMSDeliveryReportReceived           | Communication Service によって送信された SMS の配信レポートを受信したときに発行されます。     |
| Microsoft.Communication.ChatMessageReceived*                | 自分がメンバーになっているチャット スレッド内のユーザーのメッセージを受信したときに発行されます。        |
| Microsoft.Communication.ChatMessageEdited*                   | ユーザーがメンバーになっているチャット スレッド内でメッセージが編集されたときに発行されます。                |
| Microsoft.Communication.ChatMessageDeleted*                  | ユーザーがメンバーになっているチャット スレッド内でメッセージが削除されたときに発行されます。               |
| Microsoft.Communication.ChatThreadCreatedWithUser           | チャット スレッドの作成時にユーザーがメンバーとして追加されたときに発行されます。           |
| Microsoft.Communication.ChatThreadWithUserDeleted           | ユーザーがメンバーになっているチャット スレッドが削除されたときに発行されます。                           |
| Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser  | ユーザーがメンバーになっているチャット スレッドのプロパティが更新されたときに発行されます。              |
| Microsoft.Communication.ChatMemberAddedToThreadWithUser     | ユーザーがメンバーとしてチャット スレッドに追加されたときに発行されます。                                   |
| Microsoft.Communication.ChatMemberRemovedFromThreadWithUser | ユーザーがチャット スレッドから削除されたときに発行されます。                                         |


* これらのイベントがトリガーされるように、"メッセージの送信" API 呼び出しで "送信者名" を指定していることをご確認ください。

## <a name="event-subjects"></a>イベントの件名

すべての Communication Services イベントの `subject` フィールドは、イベントの対象となるユーザー、電話番号、またはエンティティを識別します。 単純な [Event Grid のフィルター処理](./event-filtering.md)を可能にするために、共通プレフィックスが使用されます。

| 件名のプレフィックス                              | Communication Service エンティティ |
| ------------------------------------------- | ---------------------------- |
| `phonenumber/`                              | PSTN の電話番号            |
| `user/`                                     | Communication Services ユーザー  |
| `thread/`                                   | チャット スレッド。                 |

次の例は、Communication Services リソースが所有する市外局番が 555 のすべての電話番号に送信されるすべての SMS メッセージと配信レポートに対するフィルターを示しています。

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Communication.SMSReceived",
    "Microsoft.Communication.SMSDeliveryReportReceived"
  ],
  "subjectBeginsWith": "phonenumber/1555",
}
```

## <a name="sample-event-responses"></a>イベント応答のサンプル

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。 このセクションには、各イベントでそのデータがどのように見えるかの例が含まれています。

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived イベント

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "eventType": "Microsoft.Communication.SMSDeliveryReportReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:22:20Z"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived イベント

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "topic": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "eventType": "Microsoft.Communication.SMSReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:27:47Z"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived イベント

```json
[{
  "id": "c13afb5f-d975-4296-a8ef-348c8fc496ee",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/sender/{id-of-message-sender}/recipient/{id-of-message-recipient}",
  "data": {
    "messageBody": "Welcome to Azure Communication Services",
    "messageId": "1600389507167",
    "senderId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e0d-e5aa-0e04-343a0d00037c",
    "senderDisplayName": "John",
    "composeTime": "2020-09-18T00:38:27.167Z",
    "type": "Text",
    "version": 1600389507167,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e1a-3090-6a0b-343a0d000409",
    "transactionId": "WGW1YmwRzkupk0UI0QA9ZA.1.1.1.1.1797783722.1.9",
    "threadId": "19:46df844a4c064bfaa2b3b30e385d1018@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageReceived",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:38:28.0946757Z"
}
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited イベント

```json
[{
  "id": "18247662-e94a-40cc-8d2f-f7357365309e",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "editTime": "2020-09-18T00:48:47.361Z",
    "messageBody": "Let's Chat about new communication services.",
    "messageId": "1600390097873",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:17.873Z",
    "type": "Text",
    "version": 1600390127361,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "bbopOa1JZEW5NDDFLgH1ZQ.2.1.2.1.1822032097.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageEdited",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:48:48.037823Z"
}]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted イベント
```json
[{
  "id": "08034616-cf11-4fc2-b402-88963b93d083",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deleteTime": "2020-09-18T00:48:47.361Z",
    "messageId": "1600390099195",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:19.195Z",
    "type": "Text",
    "version": 1600390152154,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "mAxUjeTsG06NpObXkFcjVQ.1.1.2.1.1823015063.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMessageDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:12.6698791Z"
}]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser イベント 

```json
[{
  "id": "06c7c381-bb0a-4fff-aedd-919df1d52137",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2/createdBy/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "createdBy": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_06014f-6001fc107f",
    "properties": {
      "topic": "Chat about new commuication services",
    },
    "members": [
      {
        "displayName": "Bob",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0"
      },
      {
        "displayName": "John",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f1"
      }
    ],
    "createTime": "2020-09-17T22:06:09.988Z",
    "version": 1600380369988,
    "recipientId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "9ZxrGXVXCkOTygd5iwsvAQ.1.1.1.1.1440874720.1.1",
    "threadId": "19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadCreatedWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-17T22:06:10.3235137Z"
}]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted イベント

```json
[{
  "id": "7f4fa31b-e95e-428b-a6e8-53e2553620ad",
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/deletedBy/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deletedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "deleteTime": "2020-09-18T00:49:26.3694459Z",
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071625,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "MoZlSM2j7kSD2b5X8bjH7Q.1.1.2.1.1823539230.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadWithUserDeleted",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:49:26.4269056Z"
}]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser イベント 

```json
[{
  "id": "47a66834-57d7-4f77-9c7d-676d45524982",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:a33a128babf04431b7fe8cbca82f4238@thread.v2/editedBy/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8/recipient/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
  "data": {
    "editedBy": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8",
    "editTime": "2020-09-18T00:40:38.4914428Z",
    "properties": {
      "topic": "Communication in Azure"
    },
    "createTime": "2020-09-18T00:39:02.541Z",
    "version": 1600389638481,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
    "transactionId": "+ah9tVwqNkCT6nUGCKIvAg.1.1.1.1.1802895561.1.1",
    "threadId": "19:a33a128babf04431b7fe8cbca82f4238@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:40:38.5804349Z"
}]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser イベント

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberAdded/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:13.2342692Z"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser イベント

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberRemoved/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "eventType": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-09-18T00:47:51.2244511Z"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

### <a name="microsoftcommunicationsmsdeliveryreportreceived-event"></a>Microsoft.Communication.SMSDeliveryReportReceived イベント

```json
[{
  "id": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Outgoing_202009180022138813a09b-0cbf-4304-9b03-1546683bb910",
    "From": "15555555555",
    "To": "+15555555555",
    "DeliveryStatus": "Delivered",
    "DeliveryStatusDetails": "No error.",
    "ReceivedTimestamp": "2020-09-18T00:22:20.2855749Z",
    "DeliveryAttempts": [
      {
        "Timestamp": "2020-09-18T00:22:14.9315918Z",
        "SegmentsSucceeded": 1,
        "SegmentsFailed": 0
      }
    ]
  },
  "type": "Microsoft.Communication.SMSDeliveryReportReceived",
  "time": "2020-09-18T00:22:20Z",
  "specversion": "1.0"
}]
```
### <a name="microsoftcommunicationsmsreceived-event"></a>Microsoft.Communication.SMSReceived イベント

```json
[{
  "id": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
  "source": "/subscriptions/50ad1522-5c2c-4d9a-a6c8-67c11ecb75b8/resourcegroups/acse2e/providers/microsoft.communication/communicationservices/{communication-services-resource-name}",
  "subject": "/phonenumber/15555555555",
  "data": {
    "MessageId": "Incoming_20200918002745d29ebbea-3341-4466-9690-0a03af35228e",
    "From": "15555555555",
    "To": "15555555555",
    "Message": "Great to connect with ACS events ",
    "ReceivedTimestamp": "2020-09-18T00:27:45.32Z"
  },
  "type": "Microsoft.Communication.SMSReceived",
  "time": "2020-09-18T00:27:47Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmessagereceived-event"></a>Microsoft.Communication.ChatMessageReceived イベント

```json
[{
  "id": "c13afb5f-d975-4296-a8ef-348c8fc496ee",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/{thread-id}/sender/{id-of-message-sender}/recipient/{id-of-message-recipient}",
  "data": {
    "messageBody": "Welcome to Azure Communication Services",
    "messageId": "1600389507167",
    "senderId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e0d-e5aa-0e04-343a0d00037c",
    "senderDisplayName": "John",
    "composeTime": "2020-09-18T00:38:27.167Z",
    "type": "Text",
    "version": 1600389507167,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e1a-3090-6a0b-343a0d000409",
    "transactionId": "WGW1YmwRzkupk0UI0QA9ZA.1.1.1.1.1797783722.1.9",
    "threadId": "19:46df844a4c064bfaa2b3b30e385d1018@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageReceived",
  "time": "2020-09-18T00:38:28.0946757Z",
  "specversion": "1.0"
}
]
```

### <a name="microsoftcommunicationchatmessageedited-event"></a>Microsoft.Communication.ChatMessageEdited イベント

```json
[{
  "id": "18247662-e94a-40cc-8d2f-f7357365309e",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "editTime": "2020-09-18T00:48:47.361Z",
    "messageBody": "Let's Chat about new communication services.",
    "messageId": "1600390097873",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:17.873Z",
    "type": "Text",
    "version": 1600390127361,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "bbopOa1JZEW5NDDFLgH1ZQ.2.1.2.1.1822032097.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageEdited",
  "time": "2020-09-18T00:48:48.037823Z",
  "specversion": "1"
}]
```

### <a name="microsoftcommunicationchatmessagedeleted-event"></a>Microsoft.Communication.ChatMessageDeleted イベント
```json
[{
  "id": "08034616-cf11-4fc2-b402-88963b93d083",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/sender/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deleteTime": "2020-09-18T00:48:47.361Z",
    "messageId": "1600390099195",
    "senderId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "senderDisplayName": "Bob(Admin)",
    "composeTime": "2020-09-18T00:48:19.195Z",
    "type": "Text",
    "version": 1600390152154,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "mAxUjeTsG06NpObXkFcjVQ.1.1.2.1.1823015063.1.5",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMessageDeleted",
  "time": "2020-09-18T00:49:12.6698791Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadcreatedwithuser-event"></a>Microsoft.Communication.ChatThreadCreatedWithUser イベント 

```json
[{
  "id": "06c7c381-bb0a-4fff-aedd-919df1d52137",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2/createdBy/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "createdBy": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_06014f-6001fc107f",
    "properties": {
      "topic": "Chat about new communication services"
    },
    "members": [
      {
        "displayName": "Bob",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0"
      },
      {
        "displayName": "John",
        "memberId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f1"
      }
    ],
    "createTime": "2020-09-17T22:06:09.988Z",
    "version": 1600380369988,
    "recipientId": "8:acs:73551687-f8c8-48a7-bf06-d8263f15b02a_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "9ZxrGXVXCkOTygd5iwsvAQ.1.1.1.1.1440874720.1.1",
    "threadId": "19:7bdf5504a23f41a79d1bd472dd40044a@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadCreatedWithUser",
  "time": "2020-09-17T22:06:10.3235137Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadwithuserdeleted-event"></a>Microsoft.Communication.ChatThreadWithUserDeleted イベント

```json
[{
  "id": "7f4fa31b-e95e-428b-a6e8-53e2553620ad",
  "source":"/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/deletedBy/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "deletedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe",
    "deleteTime": "2020-09-18T00:49:26.3694459Z",
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071625,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "MoZlSM2j7kSD2b5X8bjH7Q.1.1.2.1.1823539230.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadWithUserDeleted",
  "time": "2020-09-18T00:49:26.4269056Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatthreadpropertiesupdatedperuser-event"></a>Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser イベント 

```json
[{
  "id": "47a66834-57d7-4f77-9c7d-676d45524982",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:a33a128babf04431b7fe8cbca82f4238@thread.v2/editedBy/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8/recipient/8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
  "data": {
    "editedBy": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-2b7f-ac00-343a0d0005a8",
    "editTime": "2020-09-18T00:40:38.4914428Z",
    "properties": {
      "topic": "Communication in Azure"
    },
    "createTime": "2020-09-18T00:39:02.541Z",
    "version": 1600389638481,
    "recipientId": "8:acs:fac4607d-d2d0-40e5-84df-6f32ebd1251a_00000005-3e88-15fa-ac00-343a0d0005a7",
    "transactionId": "+ah9tVwqNkCT6nUGCKIvAg.1.1.1.1.1802895561.1.1",
    "threadId": "19:a33a128babf04431b7fe8cbca82f4238@thread.v2"
  },
  "type": "Microsoft.Communication.ChatThreadPropertiesUpdatedPerUser",
  "time": "2020-09-18T00:40:38.5804349Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmemberaddedtothreadwithuser-event"></a>Microsoft.Communication.ChatMemberAddedToThreadWithUser イベント

```json
[{
  "id": "4abd2b49-d1a9-4fcc-9cd7-170fa5d96443",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberAdded/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:13.1867087Z",
    "addedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberAdded": {
      "displayName": "John Smith",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390033176,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "pVIjw/pHEEKUOUJ2DAAl5A.1.1.1.1.1818361951.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMemberAddedToThreadWithUser",
  "time": "2020-09-18T00:47:13.2342692Z",
  "specversion": "1.0"
}]
```

### <a name="microsoftcommunicationchatmemberremovedfromthreadwithuser-event"></a>Microsoft.Communication.ChatMemberRemovedFromThreadWithUser イベント

```json
[{
  "id": "b3701976-1ea2-4d66-be68-4ec4fc1b4b96",
  "source": "/subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/Microsoft.Communication/communicationServices/{communication-services-resource-name}",
  "subject": "thread/19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2/memberRemoved/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe/recipient/8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
  "data": {
    "time": "2020-09-18T00:47:51.1461742Z",
    "removedBy": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f1",
    "memberRemoved": {
      "displayName": "John",
      "memberId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003fe"
    },
    "createTime": "2020-09-18T00:46:41.559Z",
    "version": 1600390071131,
    "recipientId": "8:acs:5354158b-17b7-489c-9380-95d8821ff76b_00000005-3e5f-1bc6-f40f-343a0d0003f0",
    "transactionId": "G9Y+UbjVmEuxAG3O4bEyvw.1.1.1.1.1819803816.1.1",
    "threadId": "19:6d20c2f921cd402ead7d1b31b0d030cd@thread.v2"
  },
  "type": "Microsoft.Communication.ChatMemberRemovedFromThreadWithUser",
  "time": "2020-09-18T00:47:51.2244511Z",
  "specversion": "1.0"
}]
```

---

## <a name="tutorials"></a>チュートリアル

| タイトル | 説明 |
|---------|---------|
| [クイック スタート:SMS イベントの処理](../communication-services/quickstarts/telephony-sms/handle-sms-events.md) | Event Grid を使用して SMS イベントをサブスクライブする方法について説明します。   |

## <a name="next-steps"></a>次のステップ

* Azure Event Grid の概要については、[Event Grid の紹介](./overview.md)に関する記事を参照してください。
* Azure Event Grid の概念の概要については、[Event Grid の概念](./concepts.md)に関する記事を参照してください。
* Azure Event Grid のシステム トピックの概要については、「[Azure Event Grid でのシステム トピック](./system-topics.md)」を参照してください。