---
title: クイック スタート:Speech SDK C++ (macOS) プラットフォームの設定 - Speech サービス
titleSuffix: Azure Cognitive Services
description: Speech サービス SDK を使用して、macOS 上の C++ のプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3ad8eb9564c4d8343a0763cc2f6f5061ee602b72
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188371"
---
このガイドでは、macOS 10.13 以降に C++ 用の [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>システム要件

macOS 10.13 以降

## <a name="install-speech-sdk"></a>Speech SDK をインストールする

1. Speech SDK のファイル抽出先にする必要があるディレクトリを選択し、そのディレクトリを指すように `SPEECHSDK_ROOT` 環境変数を設定します。 この変数によって、後のコマンドでこのディレクトリを参照することが容易になります。 たとえば、ホーム ディレクトリで `speechsdk` ディレクトリを使用する場合、次のようなコマンドを使用します。

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. このディレクトリがまだ存在しない場合は作成します。

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Speech SDK フレームワークを含む `.zip` アーカイブをダウンロードして抽出します。

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. 展開されたパッケージの最上位ディレクトリの内容を検証します。

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   ディレクトリの一覧には、サード パーティの通知やライセンスのファイルのほか、`MicrosoftCognitiveServicesSpeech.framework` ディレクトリが含まれているはずです。

これで、下記の「[次の手順](#next-steps)」に進むことができます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [windows](../quickstart-list.md)]
