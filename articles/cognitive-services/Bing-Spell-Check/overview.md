---
title: Bing Spell Check API とは
titleSuffix: Azure Cognitive Services
description: Bing Spell Check API について説明します。コンテキストに応じたスペル チェックを行うために、この API では機械学習と統計的機械翻訳が使用されています。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: be864e04143a381a08dedb5693026f05c42c5421
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349301"
---
# <a name="what-is-the-bing-spell-check-api"></a>Bing Spell Check API とは

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関するページを参照してください。

Bing Spell Check API を使用すると、コンテキストに応じた文法とスペルのチェックをテキストに対して実行できます。 ほとんどのスペルチェック機能はディクショナリベースのルール セットに依存しますが、Bing のスペルチェック機能は、機械学習と統計的機械翻訳を活用することでコンテキストに応じた正確な修正を実現しています。 

## <a name="features"></a>特徴

| 機能 | 説明 |
|---------|---------|
|複数のスペル チェック モード     | 複数のスペル チェック モードを使用して、文法やスペルに重点を置いた修正を行うことができます。 |
|スラングやくだけた言葉遣いの認識     | テキスト内で使用されている日常的な表現やくだけた用語を認識します。         |
|似ている単語の区別     | 発音は似ているものの意味が異なる単語間 (例: "see" と "sea") で正しい用法を探し出します。        |
|ブランド、タイトル、普及している用法のサポート     | 新しいブランドやタイトルなど、普及しつつある表現を認識します。 |

## <a name="workflow"></a>ワークフロー

Bing Spell Check API は、HTTP 要求を作成して JSON 応答を解析できる任意のプログラミング言語から簡単に呼び出すことができます。 このサービスには、REST API または Bing Spell Check SDK を使用してアクセスできます。 

1. Bing Search API が利用できる [Cognitive Services API アカウント](../cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料のアカウントを作成できます。 
2. 要求を Bing Web Search API に送信します。
3. JSON 応答を解析します

## <a name="next-steps"></a>次のステップ

まず、Bing Spell Check Search API の[対話型デモ](https://azure.microsoft.com/services/cognitive-services/spell-check/)で、さまざまなテキストをいかにすばやくチェックできるかを見てみましょう。

API を呼び出す準備ができたら、[Cognitive Services API アカウント](../../cognitive-services/cognitive-services-apis-create-account.md)を作成します。 Azure サブスクリプションをお持ちでない場合は、無料で[アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)できます。

[Bing Search API ハブ ページ](../bing-web-search/overview.md)にアクセスして、その他の提供されている API を調べることもできます。