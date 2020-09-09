---
title: スケジュール設定された通知を送信する方法 | Microsoft Docs
description: このトピックでは、Azure Notification Hubs でスケジュール設定された通知を使用する方法について説明します。
services: notification-hubs
documentationcenter: .net
keywords: プッシュ通知,プッシュ通知,プッシュ通知のスケジュール設定
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998273"
---
# <a name="how-to-send-scheduled-notifications"></a>方法: スケジュール設定された通知の送信

未来のある時点で通知を送信することが必要なシナリオに対して、通知を送信するためのバックエンド コードの簡単なウェイクアップ方法がないような場合、 Standard レベルの通知ハブは、通知を最大 7 日先までスケジュールできる機能をサポートしています。


## <a name="schedule-your-notifications"></a>通知をスケジュール設定する
通知を送信する場合は、次の例に示すように Notification Hubs SDK の [`ScheduledNotification` クラス](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification)を使用します。

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>スケジュールされた通知を取り消す
また、既にスケジュール設定されている通知を、その notificationId を使用して取り消すこともできます。

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

スケジュール設定された、送信可能な通知の数に制限はありません。

## <a name="next-steps"></a>次のステップ

次のチュートリアルを参照してください。

 - [すべての登録済みデバイスにプッシュ通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [特定のデバイスにプッシュ通知を送信する](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [ローカライズしたプッシュ通知を送信する](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [特定のユーザーにプッシュ通知を送信する](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [場所に基づいたプッシュ通知を送信する](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
