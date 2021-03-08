---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025504"
---
このサンプルでは、[Twilio](https://www.twilio.com/) サービスを使って、携帯電話に SMS メッセージを送信します。 Azure Functions では [Twilio バインディング](../articles/azure-functions/functions-bindings-twilio.md)により Twilio が既にサポートされており、サンプルではその機能を使います。

最初に必要なものは Twilio アカウントです。 [https://www.twilio.com/try-twilio](https://www.twilio.com/try-twilio ) で無料で作成できます。 アカウントを作成したら、次の 3 つの **アプリ設定** を関数アプリに追加します。

| アプリ設定の名前 | 値の説明 |
| - | - |
| **TwilioAccountSid**  | Twilio アカウントの SID |
| **TwilioAuthToken**   | Twilio アカウントの認証トークン |
| **TwilioPhoneNumber** | Twilio アカウントに関連付けられている電話番号。 これは、SMS メッセージの送信に使われます。 |