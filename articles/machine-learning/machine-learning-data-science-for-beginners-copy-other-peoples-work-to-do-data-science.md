---
title: "他のユーザーのデータ サイエンスの例をコピーする - Azure Machine Learning | Microsoft Docs"
description: "データ サイエンスの企業秘密: 自分のために他のユーザーに作業してもらいます。 Cortana Analytics Gallery から Machine Learning の例を取得します。"
keywords: "データ サイエンスの例,Machine Learning の例,クラスタリング アルゴリズム,クラスタリング アルゴリズムの例"
services: machine-learning
documentationcenter: na
author: cjgronlund
manager: jhubbard
editor: cjgronlund
ms.assetid: ec2be823-c325-4ad8-b8b2-3e664f1a44b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: cgronlun
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 440a1ded1f1dc1b8fbe73e3bbcbbd68ec9422bf8
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="copy-other-peoples-work-to-do-data-science"></a>他のユーザーの成果物をコピーしてデータ サイエンスを実行する
## <a name="video-5-data-science-for-beginners-series"></a>ビデオ 5: 初心者向けデータ サイエンス シリーズ
データ サイエンスの企業秘密の 1 つは、自分の代わりに他のユーザーに作業してもらうことです。 自分の Machine Learning の実験に使用するクラスタリング アルゴリズムの例を、Cortana Analytics Gallery で検索します。

このシリーズを最大限に活用するには、このビデオをすべて視聴してください。 [ビデオの一覧に移動する](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/data-science-for-beginners-series-copy-other-peoples-work-to-do-data-science/player]
>
>

## <a name="other-videos-in-this-series"></a>このシリーズの他のビデオ
*"初心者向けデータ サイエンス"* は、データ サイエンスについて簡単に説明した 5 本の短いビデオです。

* ビデオ 1: [データ サイエンスが回答する 5 つの質問](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 分 14 秒)*
* ビデオ 2: [データ サイエンス用のデータの準備はお済みですか?](machine-learning-data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4 分 56 秒)*
* ビデオ 3: [データで回答できる質問をする](machine-learning-data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 分 17 秒)*
* ビデオ 4: [単純なモデルで回答を予測する](machine-learning-data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 分 42 秒)*
* ビデオ 5: 他のユーザーの成果物をコピーしてデータ サイエンスを実行する

## <a name="transcript-copy-other-peoples-work-to-do-data-science"></a>トランスクリプト: 他のユーザーの成果物をコピーしてデータ サイエンスを実行する
"初心者向けデータ サイエンス" シリーズの 5 番目のビデオへようこそ。

このビデオでは、作業の出発点として利用できるサンプルの場所について紹介しています。 このビデオを最大限に活用するためにまず、このシリーズのビデオを最初から視聴するようお勧めします。

データ サイエンスの企業秘密の 1 つは、自分の代わりに他のユーザーに作業してもらうことです。

## <a name="find-examples-in-the-cortana-intelligence-gallery"></a>Cortana Intelligence ギャラリーでサンプルを探す
Microsoft では、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) という、無料で試用できるクラウドベースのサービスを提供しています。 さまざまな機械学習アルゴリズムを使って実験できるワークスペースが用意されており、有益なソリューションが得られたら、それを Web サービスとして立ち上げることができます。

このサービスの一部として、**[Cortana Intelligence ギャラリー](http://aka.ms/CortanaIntelligenceGallery)**というものがあります。 このギャラリーには、さまざまなリソースが存在しますが、その中の 1 つに Azure Machine Learning の実験 (モデル) のコレクションがあります。多くの人々によって構築され、有志で提供されています。 独自ソリューションの構築にあたって、これらの実験に込められている他人の思考や労力を活かさない手はありません。

ギャラリーには、[aka.ms/CortanaIntelligenceGallery](http://aka.ms/CortanaIntelligenceGallery) からアクセスできます。 だれでもアクセスできるので、自由に閲覧してください。

![Cortana Intelligence ギャラリー](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cortana-intelligence-gallery.png)

一番上の **[試験的機能]** をクリックすると、評価の高い最新の実験が多数ギャラリーに表示されます。 その他の実験を検索するには、画面上部の **[Browse All (すべて参照)]** をクリックしてください。そこで検索語句を入力したり、検索フィルターを選択したりすることができます。

## <a name="find-and-use-a-clustering-algorithm-example"></a>クラスタリング アルゴリズム サンプルの検索と使用
たとえばクラスター解析の実例を見てみたいという場合は、「**clustering sweep**」と入力して実験を検索します。

![Search for clustering experiments](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/search-for-clustering-experiments.png)

おもしろそうな実験がギャラリーに投稿されています。

![Clustering experiment](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment.png)

この実験をクリックすると、その投稿者が行った作業とその結果について説明する Web ページが表示されます。

![Clustering experiment description page](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/clustering-experiment-description-page.png)

**[Open in Studio (Studio で開く)]**と書かれたリンクに注目してください。

![Open in Studio button](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/open-in-studio.png)

このリンクをクリックすると、 **Azure Machine Learning Studio**が表示されます。 実験のコピーが作成され、自分のワークスペースに配置されます。 ここには投稿者のデータセットとそのすべての処理、その投稿者が使用したすべてのアルゴリズム、結果の保存方法が含まれています。

![Machine Learning Studio でギャラリー実験を開く - クラスタリング アルゴリズム サンプル](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/cluster-experiment-open-in-studio.png)

これで自分の作業の出発点が得られました。 後は独自のデータに交換し、必要に応じてモデルを調整するだけです。 実験の内容について詳しく把握している人の作業を足掛かりとすることができます。

## <a name="find-experiments-that-demonstrate-machine-learning-techniques"></a>機械学習の手法を示した実験を探す
[Cortana Intelligence ギャラリー](http://aka.ms/CortanaIntelligenceGallery) には、データ サイエンスに初めて取り組む方々のために、その具体的な方法を例示することに特化して投稿された実験も存在します。 たとえばギャラリーには、欠損値の扱い方を紹介した実験として、「[Methods for handling missing values (欠損値の扱い方)](https://gallery.cortanaintelligence.com/Experiment/Methods-for-handling-missing-values-1)」が存在します。 欠損値を置き換える 15 とおりの方法が紹介され、それぞれどのような利点があって、どのようなときに使用すべきかが説明されています。

![ギャラリー実験が Machine Learning Studio で開く - 欠損値のための方法](./media/machine-learning-data-science-for-beginners-copy-other-peoples-work-to-do-data-science/experiment-methods-for-handling-missing-values.png)

[Cortana Intelligence ギャラリー](http://aka.ms/CortanaIntelligenceGallery) です。

Microsoft Azure Machine Learning の "初心者向けデータ サイエンス" の他のビデオもご覧ください。

## <a name="next-steps"></a>次のステップ
* [Azure Machine Learning で初めてのデータ サイエンス実験を実行してみる](machine-learning-create-experiment.md)
* [Microsoft Azure での Machine Learning の概要を学習する](machine-learning-what-is-machine-learning.md)

