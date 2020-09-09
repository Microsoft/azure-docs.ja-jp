---
title: Bing Custom Search API とは
titleSuffix: Azure Cognitive Services
description: Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズした検索エクスペリエンスを作成できます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 010f2960693a06a50cd15ac3bac2fe35b6c0985f
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434580"
---
# <a name="what-is-the-bing-custom-search-api"></a>Bing Custom Search API とは

Bing Custom Search API を使用すると、関心のあるトピックに合わせてカスタマイズした、広告なしの検索エクスペリエンスを作成できます。 Bing で検索するドメインと Web ページを指定できるほか、特定のコンテンツをピン留め、ブースト、またはレベル下げすることもできます。そのようにして Web の表示をカスタムすれば、適合度の高い検索結果をユーザーがすばやく見つけることができるようになります。 

## <a name="features"></a>特徴

|特徴量  |説明  |
|---------|---------|
|[カスタム リアルタイム検索候補](define-custom-suggestions.md)     | ユーザーが入力するときにドロップダウン リストとして表示される検索候補を提供します。       | 
|[カスタム画像検索エクスペリエンス](get-images-from-instance.md)     | カスタム検索インスタンスで指定されたドメインや Web サイトから画像を検索する機能を実現できます。        |        
|[カスタム動画検索エクスペリエンス](get-videos-from-instance.md)     | カスタム検索インスタンスで指定されたドメインやサイトから動画を検索する機能を実現できます。        |    
|[カスタム検索インスタンスの共有](share-your-custom-search.md)     | 検索インスタンスをチームのメンバーと共有することで、インスタンスの編集やテストを共同で行うことができます。        | 
|[アプリケーションや Web サイトの UI の構成](hosted-ui.md)     | ご自身の Web ページと Web アプリに JavaScript コード スニペットとして簡単に統合できるホステッド UI が用意されています。        | 
## <a name="workflow"></a>ワークフロー

カスタマイズした検索インスタンスは、[Bing Custom Search ポータル](https://customsearch.ai)を使用して作成できます。 Bing に検索させたいドメイン、Web サイト、Web ページと、検索させたくない Web サイトとを指定するカスタムの検索インスタンスをポータルから作成することができます。 ポータルを使用して、検索エクスペリエンスをプレビューしたり、API から提供される検索ランキングを調整したり、必要に応じて、Web サイトやアプリケーションにレンダリングする検索可能なユーザー インターフェイスを構成したりすることができます。

作成した検索インスタンス (と必要に応じてユーザー インターフェイス) は、Bing Custom Search API を呼び出すことで、既存の Web サイトやアプリケーションに統合することができます。

![API で Bing Custom Search に接続できることを示す画像](media/BCS-Overview.png "Bing Custom Search の動作方法")


## <a name="next-steps"></a>次のステップ

すぐに使い始める方法は、「[Create your first Bing Custom Search instance](quick-start.md)」 (最初の Bing Custom Search インスタンスを作成する) を参照してください。

検索インスタンスのカスタマイズについて詳しくは、[カスタム検索インスタンスの定義](define-your-custom-view.md)に関するページをご覧ください。

既存のサービスやアプリケーションにおける検索結果の使用については、[Bing の利用と表示の要件](./use-and-display-requirements.md)に関するページを必ずお読みください。

その他の提供されている API については、[Bing Search API ハブ ページ](../bing-web-search/search-the-web.md)をご覧ください。

各カスタム検索エンドポイントのリファレンス コンテンツをよく理解してください。 リファレンスには、検索結果を要求するときに使用するエンドポイント、ヘッダー、クエリ パラメーターが記載されています。 また、応答オブジェクトの定義も記載されています。

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

