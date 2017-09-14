---
title: "Azure IoT Hub の cloud-to-device メッセージの理解 | Microsoft Docs"
description: "開発者ガイド - IoT Hub での cloud-to-device メッセージの使用方法。 メッセージのライフサイクル、および構成オプションについて説明します。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 04ac46498c912b0503036f70b7f3d0e28e5a82b8
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017


---
# <a name="send-cloud-to-device-messages-from-iot-hub"></a>cloud-to-device メッセージを IoT Hub から送信する

ソリューション バックエンドから、デバイス アプリに一方向の通知を送信するには、IoT hub からデバイスに cloud-to-device メッセージを送信します。 IoT Hub でサポートされるその他の cloud-to-device オプションの詳細については、「[cloud-to-device 通信に関するガイダンス][lnk-c2d-guidance]」をご覧ください。

C2D メッセージはサービス向けエンドポイント (**/messages/devicebound**) を介して送信できます。 デバイスは、次にそのメッセージをデバイス固有のエンドポイント (**/devices/{deviceId}/messages/devicebound**) を介して受信します。

個々の C2D メッセージの宛先は単一のデバイスであり、**to** プロパティが **/devices/{deviceId}/messages/devicebound** に設定されます。

各デバイスのキューで保持できる C2D メッセージは最大 50 個です。 同じデバイスにそれ以上のメッセージを送信しようとすると、エラーが発生します。

## <a name="the-cloud-to-device-message-lifecycle"></a>cloud-to-device メッセージのライフ サイクル

少なくとも 1 回のメッセージ配信を保証するために、IoT Hub は cloud-to-device デバイス メッセージをデバイスごとのキューに保持します。 IoT Hub がキューからメッセージを削除できるように、デバイスで明示的に *完了* を確認する必要があります。 このような方法で、接続とデバイスの障害に対する復元性が保証されます。

次の図に、IoT Hub の cloud-to-device メッセージのライフサイクルの状態に関するグラフを示します。

![クラウドとデバイス間のメッセージのライフ サイクル][img-lifecycle]

IoT Hub サービスは、デバイスにメッセージを送信するときに、メッセージの状態を **Enqueued** に設定します。 デバイスでメッセージを*受信*する場合、デバイス上の他のスレッドが他のメッセージの受信を開始できるようにするために、IoT Hub によりメッセージが*ロック*されます (状態を**非表示**に設定することによる)。 デバイスのスレッドがメッセージの処理を完了すると、メッセージを *完了* して IoT Hub にその旨を通知します。 IoT Hub は、次に状態を**完了**に設定します。

デバイスは次の選択をすることもあります。

* メッセージの*拒否*。この場合、IoT Hub によってメッセージの状態が**配信不能**に設定されます。 MQTT プロトコル経由で接続するデバイスは、cloud-to-device メッセージを拒否できません。
* メッセージの*破棄*。この場合、IoT Hub によってメッセージがキューに戻され、状態が**エンキュー**に設定されます。

スレッドが IoT Hub に通知することなく、メッセージの処理に失敗することもあります。 その場合、*表示 (またはロック) がタイムアウト*になった後で、メッセージの状態が自動的に**非表示**から**エンキュー**に切り替わります。 このタイムアウトの既定値は 1 分です。

メッセージの状態は**エンキュー**と**非表示**の間で切り替わりますが、この回数が IoT Hub の**最大配信数**のプロパティで指定された上限に達すると、 IoT Hub によってメッセージの状態が**配信不能**に設定されます。 同様に、有効期限 ([有効期限][lnk-ttl]に関するセクションを参照) が切れた後も、IoT Hub によってメッセージの状態が**配信不能**に設定されます。

「[How to send cloud-to-device messages with IoT Hub (cloud-to-device メッセージを IoT Hub で送信する方法)][lnk-c2d-tutorial]」では、cloud-to-device メッセージを、クラウドから送信してデバイスで受信する方法について説明します。

通常、メッセージの損失がアプリケーション ロジックに影響しない場合、デバイスは cloud-to-device メッセージを完了します。 たとえば、デバイスがメッセージの内容をローカルに保持していた場合、または正常に操作が実行されていた場合などです。 また、メッセージには、メッセージの損失がアプリケーションの機能に影響しないことを示す一時的な情報が含まれている場合もあります。 実行時間の長いタスクの場合、タスクの説明をローカル ストレージに保持した後で C2D メッセージを完了できます。 その後、タスクの進行状況のさまざまな段階で、1 つ以上の D2C メッセージによりソリューション バックエンドに通知できます。

## <a name="message-expiration-time-to-live"></a>メッセージの有効期限

すべての C2D メッセージに有効期限があります。 これはサービス (**ExpiryTimeUtc** プロパティ) で設定するか、IoT Hub のプロパティとして指定した既定の *time to live* を使用して IoT Hub で設定できます。 「[C2D の構成オプション][lnk-c2d-configuration]」を参照してください。

メッセージの有効期限を利用し、接続されていないデバイスにメッセージが送信されないようにするには、一般的には、有効期限に短い値を設定します。 この方法を使用すると、デバイスの接続状態を維持するのと同じ結果が得られますが、より効率化されます。 メッセージの受信確認を要求すると、メッセージを受信できるデバイスや、オンラインではないデバイスまたは障害が発生しているデバイスについて IoT Hub から通知を受け取ります。

## <a name="message-feedback"></a>メッセージのフィードバック

C2D メッセージを送信するときに、サービスは、そのメッセージの最終状態に関するメッセージごとのフィードバックの配信を要求できます。

| Ack プロパティ | 動作 |
| ------------ | -------- |
| **positive** | cloud-to-device メッセージの状態が**完了**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。 |
| **negative** | cloud-to-device メッセージの状態が**配信不能**に達した場合に限り、IoT Hub によりフィードバック メッセージが生成されます。 |
| **full**     | IoT Hub は、どちらの場合にも、フィードバック メッセージを生成します。 |

**Ack** が **full** の場合で、フィードバック メッセージを受信しない場合は、フィードバック メッセージの有効期限が切れたことを意味します。 このとき、元のメッセージがどうなったかをサービスが認識することはできません。 実際には、有効期限切れになる前にフィードバックをサービスが確実に処理できることが必要です。 有効期限は最長 2 日間であるため、障害が発生した場合も、サービスを再び稼働させる時間は十分にあります。

[エンドポイント][lnk-endpoints]に関するページで説明されているように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/feedback**) 経由でメッセージとしてフィードバックを配信します。 フィードバックを受信するためのセマンティクスは C2D メッセージの場合と同様であり、[メッセージのライフサイクル][lnk-lifecycle]も同じです。 可能な限り、メッセージのフィードバックは、次の形式で 1 つのメッセージのバッチとして処理します。

| プロパティ     | Description |
| ------------ | ----------- |
| EnqueuedTime | メッセージが作成された日時を示すタイムスタンプ。 |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

本文はシリアル化された JSON レコードの配列で、それぞれ次のプロパティを持っています。

| プロパティ           | Description |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | メッセージの結果が発生した日時を示すタイムスタンプ。 たとえば、デバイスの完了やメッセージの期限切れなどです。 |
| OriginalMessageId  | このフィードバック情報が関連する cloud-to-device メッセージの **MessageId** です。 |
| StatusCode         | 必須の文字列。 IoT Hub によって生成されたフィードバック メッセージで使用されます。 <br/> 'Success' <br/> 'Expired' <br/> 'DeliveryCountExceeded' <br/> 'Rejected' <br/> 'Purged' |
| Description        | **StatusCode**の文字列値。 |
| deviceId           | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの **DeviceId** です。 |
| DeviceGenerationId | フィードバックのこの要素が関連する cloud-to-device メッセージのターゲット デバイスの **DeviceGenerationId** です。 |

メッセージのフィードバックを元のメッセージと関連付けることができるように、サービスは C2D メッセージの **MessageId** を指定する必要があります。

次の例は、フィードバック メッセージの本文を示しています。

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

## <a name="cloud-to-device-configuration-options"></a>C2D の構成オプション

各 IoT hub では、cloud-to-device メッセージング用に次の構成オプションを公開しています。

| プロパティ                  | Description | 範囲と既定値 |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | C2D メッセージの TTL の既定値。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| maxDeliveryCount          | デバイスごとの C2D キューの最大配信数。 | 1 ～ 100。 既定値: 10。 |
| feedback.ttlAsIso8601     | サービス宛てのフィードバックのメッセージの保有期間。 | 最大 2D の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| feedback.maxDeliveryCount |フィードバック キューの最大配信数。 | 1 ～ 100。 既定値: 100。 |

これらの構成オプションを設定する方法の詳細については、[IoT hub の作成][lnk-portal]に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

cloud-to-device メッセージの受信に使用できる SDK については、[Azure IoT SDK][lnk-sdks] に関する記事をご覧ください。

cloud-to-device メッセージへの受信を試すには、[クラウドからデバイスへの送信][lnk-c2d-tutorial] に関するチュートリアルをご覧ください。

[img-lifecycle]: ./media/iot-hub-devguide-messages-c2d/lifecycle.png

[lnk-portal]: iot-hub-create-through-portal.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-ttl]: #message-expiration-time-to-live
[lnk-c2d-configuration]: #cloud-to-device-configuration-options
[lnk-lifecycle]: #message-lifecycle
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

