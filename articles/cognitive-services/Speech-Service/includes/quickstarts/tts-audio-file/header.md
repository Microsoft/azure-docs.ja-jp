---
author: IEvangelist
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: cd8da5eb9313685361ca56b56c024c2dfb37276e
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961500"
---
このクイックスタートでは、[Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) を使用して、テキストをオーディオ ファイルの合成音声に変換します。 テキスト読み上げサービスには、合成音声に関するオプションが数多く用意されています ([テキスト読み上げの言語サポート](../../../language-support.md#text-to-speech)に関するセクションを参照)。 いくつかの前提条件を満たすと、次の 5 つの手順だけで音声をファイルに合成します。
> [!div class="checklist"]
> * サブスクリプション キーとリージョンから `SpeechConfig` オブジェクトを作成します。
> * .WAV ファイル名を指定するオーディオ構成オブジェクトを作成します。
> * 上記の構成オブジェクトを使用して `SpeechSynthesizer` オブジェクトを作成します。
> * `SpeechSynthesizer` オブジェクトを使用して、テキストを合成された音声に変換し、指定されたオーディオ ファイルに保存します。
> * 返された `SpeechSynthesizer` でエラーがないか確認します。
