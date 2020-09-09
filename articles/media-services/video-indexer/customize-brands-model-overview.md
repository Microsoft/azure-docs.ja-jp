---
title: Video Indexer でのブランド モデルのカスタマイズ - Azure
titleSuffix: Azure Media Services
description: この記事では、Video Indexer におけるブランド モデルについて説明し、そのカスタマイズ方法の概要を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: a1b2738e3be2e2b8a388ef80ca94764cdae99b08
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047354"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Video Indexer でのブランド モデルのカスタマイズ

Video Indexer では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで説明されている場合や、ビデオ内の視覚テキストに表示される場合、Video Indexer ではそのコンテンツ内でブランドとして Microsoft が検出されます。 ブランドは、コンテキストを使用して、他の用語と区別されます。

ブランドの検出は、コンテンツのアーカイブと検出、コンテキストに応じた広告、ソーシャル メディア分析、小売りの競合分析などの多くのさまざまなビジネス シナリオで役立ちます。 Video Indexer のブランド検出機能を使用すると、Bing のブランド データベースを使って音声や視覚テキストでのブランドの言及にインデックスを付けたり、Video Indexer のアカウントごとにカスタム ブランド モデルを作成してカスタマイズしたりできます。 カスタム ブランド モデル機能を使用して、Video Indexer で Bing ブランド データベースのブランドを検出するかどうかの選択、検出される特定のブランドの除外 (実質的にブランドのブラック リストの作成)、Bing のブランド データベースにない可能性があるがモデルの一部にする必要のあるブランドの包含 (実質的にブランドのホワイト リストの作成) を行うことができます。 作成したカスタム ブランド モデルは、モデルを作成したアカウントでのみ使用できます。

## <a name="out-of-the-box-detection-example"></a>そのまま使用できる検出の例

[Microsoft Build 2017 Day 2](https://www.videoindexer.ai/media/ed6ede78ad/) のプレゼンテーションでは、ブランド "Microsoft Windows" が複数回出現します。 トランスクリプト内や視覚テキストでときどき使用されていますが、文字通りに使用されているわけではありません。 Video Indexer では、コンテキストに基づいて、用語が本当にブランドであることを高い精度で検出します。すぐに使える 90,000 件のブランドが網羅されていて、絶えず更新されています。 Video Indexer は、02:25 に音声からブランドを検出し、02:40 に Windows ロゴの一部である視覚テキストから再度検出します。

![ブランドの概要](./media/content-model-customization/brands-overview.png)

コンテキストからあいまいさを解消する方法を熟知している高度な機械学習アルゴリズムに基づくため、建築のコンテキストで "windows" について話しても、"Windows" はブランドとして検出されません。Box、Apple、Fox などでも同様です。 ブランドの検出は、サポートされているすべての言語で機能します。 Microsoft Build 2017 Day 2 の基調講演の動画とインデックスの完全版については、[ここ](https://www.videoindexer.ai/media/ed6ede78ad/)をクリックしてください。

独自のブランドを使用するには、「次の手順」を確認してください。

## <a name="next-steps"></a>次のステップ

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)

[Web サイトを使用してブランド モデルをカスタマイズする](customize-brands-model-with-website.md)
