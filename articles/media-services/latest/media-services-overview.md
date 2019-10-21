---
title: Azure Media Services v3 の概要 | Microsoft Docs
description: この記事では、Media Services の概要を説明し、詳細についての記事へのリンクを示します。
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 09/17/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: c6359cf2401ff198b0242243dbf6dfdf2e35ce47
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244019"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3 の概要

クラウドベースのプラットフォームである Azure Media Services では、ブロードキャスト品質のビデオ ストリーミングを実現し、アクセス性と配信を強化し、コンテンツを分析するソリューションを構築できます。 アプリケーション開発者、コール センター、政府機関、エンターテイメント会社のいずれであっても、Media Services を利用すると、今日の最も一般的なモバイル デバイスとブラウザーの多くのユーザーに、優れた品質のメディア エクスペリエンスを提供するアプリケーションを作成できます。 

Media Services v3 SDK は [Media Services v3 OpenAPI の仕様 (Swagger)](https://aka.ms/ams-v3-rest-sdk) に基づいています。

> [!NOTE]
> 現時点では、Azure portal を使用して v3 リソースを管理することはできません。 [REST API](https://aka.ms/ams-v3-rest-ref)、[CLI](https://aka.ms/ams-v3-cli-ref)、サポートされている [SDK](media-services-apis-overview.md#sdks) のいずれかを使用します。

## <a name="what-can-i-do-with-media-services"></a>Media Services の機能

Media Services を使うと、クラウドでさまざまなメディア ワークフローを構築できます。Media Services で実現できることの例を次に示します。  

* 広範なブラウザーやデバイスで再生できるように、さまざまな形式のビデオを提供します。 さまざまなクライアント (モバイル デバイス、TV、PC など) にオンデマンドとライブ ストリーミングの両方を提供するには、ビデオ コンテンツとオーディオ コンテンツを適切にエンコードしてパッケージ化する必要があります。 このようなコンテンツを配信およびストリーミングする方法については、[ファイルのエンコードとストリームに関するクイック スタート](stream-files-dotnet-quickstart.md)を参照してください。
* サッカー、野球、大学や高校のスポーツなど、多数のオンライン視聴者にライブ スポーツ イベントをストリーム配信します。 
* タウンホール ミーティング、市議会、立法機関などの公開の会合やイベントをブロードキャストします。
* 録画されたビデオやオーディオ コンテンツを分析します。 たとえば、より高い顧客満足度を実現するため、音声からテキストを抽出して、検索インデックスやダッシュボードを作成できます。 これにより、一般的な苦情、苦情の原因、その他の関連データに関する知見を引き出すことができます。
* 顧客 (たとえば映画スタジオ) が独自の著作権所有作品のアクセスや使用を制限する必要がある場合は、サブスクリプション ビデオ サービスを作成して、DRM で保護されたコンテンツをストリーム配信します。
* 飛行機、列車、自動車で再生するためのオフライン コンテンツを提供します。 顧客は、ネットワークから切断される可能性があるときは、携帯電話やタブレットにコンテンツをダウンロードして再生する必要があります。
* 音声からのテキスト キャプション作成や、多言語への翻訳などのために、Azure Media Services と [Azure Cognitive Services APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai) で教育用 E ラーニング ビデオ プラットフォームを実装します。 
* Azure Media Services を [Azure Cognitive Services APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai) と共に使用して、より広範な視聴者 (たとえば、聴覚障碍を持つ人や、別の言語で読みたい人など) に対応できるよう、ビデオに字幕とキャプションを追加します。
* 瞬間的高負荷 (製品発表イベントの開始時など) を処理しやすくする大規模なスケーリングを Azure CDN が実現できるようにします。 

## <a name="how-can-i-get-started-with-v3"></a>v3 の利用を始める方法 

Media Services v3 を使用して、コンテンツのエンコードとパッケージ化、オンデマンドでのビデオのストリーム配信、ライブ ブロードキャスト、ビデオの分析を行う方法を説明します。 チュートリアルや API リファレンスなどのドキュメントでは、ビデオ ストリーミングやオーディオ ストリーミングをオンデマンドやライブで安全に行い、数百万規模のユーザーに拡張する方法を紹介します。

> [!TIP]
> 開発を開始する前に、以下を確認してください。<br/>* [基本的な概念](concepts-overview.md) (パッケージ化、エンコード、保護などの重要な概念を含む)<br/>* [Media Services v3 API を使用した開発](media-services-apis-overview.md) (API へのアクセスや名前付け規則などに関する情報を含む)

### <a name="quickstarts"></a>クイック スタート  

このクイック スタートでは、新しいお客様が Media Services を簡単に試すことができるよう、基礎について 1 日で説明します。

* [動画ファイルのストリーム配信 - .NET](stream-files-dotnet-quickstart.md)
* [動画ファイルのストリーム配信 - CLI](stream-files-cli-quickstart.md)
* [動画ファイルのストリーム配信 - Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>チュートリアル 

このチュートリアルでは、Media Services でよく行われるタスクについて、シナリオベースで手順を示します。

* [リモート ファイルのエンコードとビデオのストリーム配信 - REST](stream-files-tutorial-with-rest.md)
* [アップロードされたファイルのエンコードとビデオのストリーム配信 - .NET](stream-files-tutorial-with-api.md)
* [ライブ ストリーム配信 - .NET](stream-live-tutorial-with-api.md)
* [ビデオの分析 - .NET](analyze-videos-tutorial-with-api.md)
* [AES-128 動的暗号化 - .NET](protect-with-aes128.md)
    
### <a name="samples"></a>サンプル

[このサンプル ブラウザー](https://docs.microsoft.com/samples/browse/?products=azure-media-services)を使用して、Azure Media Services のコード サンプルを参照します。

### <a name="how-to-guides"></a>ハウツー ガイド

記事には、タスクの実行方法を紹介したコード サンプルが記載されています。 このセクションでは、多くの例を紹介しています。以下に挙げたのは、その一例です。

* [アカウントの作成 - CLI](create-account-cli-how-to.md)
* [API へのアクセス - CLI](access-api-cli-how-to.md)
* [HTTPS をジョブの入力とするエンコード - .NET](job-input-from-http-how-to.md)  
* [イベントの監視 - ポータル](monitor-events-portal-how-to.md)
* [マルチ DRM を使用した動的な暗号化 - .NET](protect-with-drm.md) 
* [カスタム変換を使用してエンコードする方法 - CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>質問する。フィードバックする。最新情報を入手する

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次の手順

[基本的な概念を理解する](concepts-overview.md)

