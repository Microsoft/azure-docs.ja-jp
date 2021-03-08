---
title: クイック スタート:カスタム音声アシスタントを作成する - Speech Service
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/20/2020
ms.author: travisw
ms.openlocfilehash: bb88ef7f048b2454f05ef33286e59277070c3ea0
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187568"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、既に作成および構成したボットに接続するカスタム音声アシスタント アプリケーションを作成する方法を説明します。 ボットを作成する必要がある場合は、より総合的な手順を[関連するチュートリアル](~/articles/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk.md)で参照してください。

いくつかの前提条件を満たすと、お使いのカスタム音声アシスタントは数手順のみで接続できるようになります。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `BotFrameworkConfig` オブジェクトを作成します。
> * 上記の `BotFrameworkConfig` オブジェクトを使用して `DialogServiceConnector` オブジェクトを作成します。
> * `DialogServiceConnector` オブジェクトを使用して、1 つの発話のリッスン プロセスを開始します。
> * 返された `ActivityReceivedEventArgs` を検査します。
