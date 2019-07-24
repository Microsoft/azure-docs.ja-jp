---
title: カスタム ビューから動画を取得する - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Bing Custom Search を使用して、Web のカスタム ビューから動画を取得する方法の概要です。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 202691bc03a7447cce28932406b00cc945a889db
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878291"
---
# <a name="get-videos-from-your-custom-view"></a>カスタム ビューから動画を取得する

Bing Custom Videos Search を使用すると、動画でカスタム検索エクスペリエンスを強化することができます。 Web の結果と同様に、カスタム検索では Web サイトのインスタンスのリストでの動画の検索がサポートされています。 Bing の Custom Videos Search API または Hosted UI 機能を使用して、動画を取得できます。 ホステッド UI 機能は簡単に使用でき、検索エクスペリエンスを短時間で稼働させる場合に推奨されます。 動画を含むように ホステッド UI を構成する方法について詳しくは、「[ホステッド UI エクスペリエンスを構成する](hosted-ui.md)」をご覧ください。

検索結果の表示をより詳細に制御したい場合は、Bing の Custom Videos Search API を使用することができます。 API の呼び出しは、Bing Video Search API の呼び出しと似ているので、API 呼び出しの例については、「[Bing Video Search](../Bing-Video-Search/search-the-web.md)」をご覧ください。 ただしその前に、[Custom Videos Search API リファレンス](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)の内容をよく理解してください。 主な違いは、サポートされているクエリ パラメーター (customConfig クエリ パラメーターを含める必要があります) と、要求送信先のエンドポイントです。

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
