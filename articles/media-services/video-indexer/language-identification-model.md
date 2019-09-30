---
title: Video Indexer を使用して音声言語を自動的に識別する - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer の言語識別モデルを使用して動画の音声言語を自動的に識別する方法について説明します。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/12/2019
ms.author: ellbe
ms.openlocfilehash: 843b92d3fe34d592b39cd86ece447fef2ff9af67
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931107"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>言語識別モデルを使用して音声言語を自動的に識別する

Video Indexer は、自動言語識別 (LID) をサポートしています。これは、オーディオから音声言語コンテンツが自動的に識別され、識別された主要言語で書き起こすメディア ファイルが送信されるプロセスです。 現在、LID は、英語、スペイン語、フランス語、ドイツ語、イタリア語、中国語 (簡体字)、日本語、ロシア語、ポルトガル語 (ブラジル) をサポートしています。 

## <a name="choosing-auto-language-identification-on-indexing"></a>インデックス作成時の自動言語識別の選択

API を使用して動画のインデックスを作成するとき、または[インデックスを再作成](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?)するときは、`sourceLanguage` パラメーターで `auto detect` オプションを選択します。

ポータルを使用している場合は、[Video Indexer](https://www.videoindexer.ai/) ホーム ページの **[アカウントのビデオ]** に移動し、インデックスを再作成するビデオの名前の上にカーソルを合わせます。 右下にあるインデックスの再作成ボタンをクリックします。 **[ビデオのインデックスの再作成]** ダイアログで、 **[ビデオのソース言語]** ドロップダウン ボックスから *[自動検出]* を選択します。

![自動検出](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>モデルの出力

その言語に対する信頼度が `> 0.6` の場合、Video Indexer では最も可能性の高い言語に従ってビデオが書き起こされます。 言語を確実に識別できない場合、音声言語は英語と想定されます。 

モデルの主要言語は、分析情報の JSON で `sourceLanguage` 属性 (root/videos/insights 以下) として使用できます。 対応する信頼度スコアも `sourceLanguageConfidence` 属性以下にあります。

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>ガイドラインと制限

* 英語、スペイン語、フランス語、ドイツ語、イタリア語、中国語 (簡体字)、日本語、ロシア語、ポルトガル語 (ブラジル) などの言語がサポートされています。
* オーディオに上のサポートされている一覧以外の言語が含まれている場合は、予期しない結果になります。
* Video Indexer で十分に高い信頼度 (`>0.6`) で言語が識別されない場合、フォールバック言語は英語です。
* 現在、言語が混在したオーディオを含むファイルはサポートされていません。 オーディオに複数の言語が混在している場合は、予期しない結果になります。 
* オーディオの品質が低い場合、モデルの結果に影響する可能性があります。
* このモデルには、オーディオに少なくとも 1 分間の音声が必要です。
* このモデルは、(音声コマンド、歌声などではなく) 自然な会話音声を認識するように設計されています。

## <a name="next-steps"></a>次の手順

* [概要](video-indexer-overview.md)
* [複数言語のコンテンツを自動的に識別および文字起こしする](multi-language-identification-transcription.md)
