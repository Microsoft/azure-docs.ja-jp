---
title: Azure Machine Learning Studio のアルゴリズムを選択する方法 | Microsoft Docs
description: クラスタリング、分類、または回帰の実験で教師あり学習と教師なし学習用の Azure Machine Learning Studio のアルゴリズムを選択する方法。
services: machine-learning
author: ericlicoding
ms.custom: previous-ms.author=pakalra, previous-author=pakalra
ms.author: amlstudiodocs
editor: cgronlun
tags: ''
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.component: studio
ms.topic: article
ms.date: 12/18/2017
ms.openlocfilehash: 9e601464f97a7717a2ccef0e60172357d3169697
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256929"
---
# <a name="how-to-choose-algorithms-for-azure-machine-learning-studio"></a>Azure Machine Learning Studio のアルゴリズムの選択方法

「どのような機械学習アルゴリズムを使用すべきか」という質問への答えは、 常に「場合による」です。 データのサイズ、品質、および性質によって異なります。 得られた回答で何を行うかによって異なります。 アルゴリズムの数値演算が使用しているコンピューターの命令にどのように変換されるかによって異なります。 そして、どれだけ時間があるかによって異なります。 最も経験豊富なデータ科学者であっても、試してみる前にどのアルゴリズムが最適か判断することはできません。

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning Algorithm Cheat Sheet

**Microsoft Azure Machine Learning Algorithm Cheat Sheet** 使用すると、Azure Machine Learning Studio のアルゴリズム ライブラリから、ご自分の予測分析ソリューションに適した機械学習アルゴリズムを選択できます。
この記事では、その使用方法について説明します。

> [!NOTE]
> チート シートをダウンロードし、それを見ながらこの記事を読むには、「 [Microsoft Azure Machine Learning Studio の機械学習アルゴリズム チート シート](algorithm-cheat-sheet.md)」にアクセスしてください。
> 
> 

このチート シートは特定のユーザーを対象にしています。Azure Machine Learning Studio ではじめに使用するアルゴリズムを選択しようとしている、機械学習について学部レベルの知識をお持ちのデータ科学者です。 そのため、一部で一般化や著しい単純化を行うこともありますが、安全な方向に導きます。 同様に、一覧に含まれないアルゴリズムが多くあります。 Azure Machine Learning で使用可能な方法が増えるにしたがって、追加される予定です。

これらの推奨事項は、多くのデータ科学者や機械学習専門家からのフィードバックとヒントをまとめたものです。 すべてに同意したわけではありませんが、大まかな総意に意見を合わせるように調整しました。 意見の相違がある場合、「...によって異なる」という言い回しを使用して記述されています。

### <a name="how-to-use-the-cheat-sheet"></a>チート シートの使用方法

チャートのパス ラベルとアルゴリズム ラベルは、"*&lt;パス ラベル&gt;* の場合は *&lt;アルゴリズム&gt;* を使用する" と読んでください。 たとえば、"*速度重視*の場合は *Two class logistic regression* (2 クラス ロジスティック回帰) を使用する" といった具合です。 場合によっては複数の分岐が適用されます。
どれも完全に一致しない場合もあります。 大まかな推奨事項なので、正確さは気にしないでください。
複数のデータ科学者が、最善のアルゴリズムを探す唯一の方法はすべてを試してみることであると言っています。

[Azure AI Gallery](http://gallery.cortanaintelligence.com/) からは、同じデータに対して複数のアルゴリズムを試し、その結果を比較した実験の例として次を利用できます: 「[Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92)」(多クラス分類子の比較: 文字認識)。

> [!TIP]
> Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](studio-overview-diagram.md)」ご覧ください。
> 
> 

## <a name="flavors-of-machine-learning"></a>機械学習の種類

### <a name="supervised"></a>教師あり

教師あり学習アルゴリズムは、一連の例に基づいて予測を行います。 たとえば、過去の株価を使用して将来の価格を推測できます。 トレーニングに使用される各例には、関心のある値でラベルが付けられます。この場合は株価です。 教師あり学習アルゴリズムでは、これらの値ラベルのパターンを検索します。 関連する可能性があるすべての情報を使用でき (曜日、季節、会社の財務データ、業界の種類、破壊的な地政学的出来事の存在など)、各アルゴリズムは異なる種類のパターンを検索します。 アルゴリズムは、最適なパターンを発見した後、そのパターンを使用してラベル付けされていないテスト データ (明日の価格など) を予測します。

教師あり学習は、人気のある便利な機械学習の種類です。 1 つの例外を除き、Azure Machine Learning のモジュールはすべて教師あり学習アルゴリズムです。 教師あり学習には複数の種類があり、Azure Machine Learning では Classification (分類)、Regression (回帰)、Anomaly detection (異常検出) と表されています。

* **Classification** (分類)。 カテゴリを予測するためにデータが使用されている場合、教師あり学習は分類とも呼ばれます。 これは、写真を「猫」または「犬」に割り当てるような場合です。 選択肢が 2 つだけの場合、これは **2 クラス分類**または**二項分類**と呼ばれます。 全国高校野球選手権大会の優勝校を予測する場合のように、多くのカテゴリがある場合は、**多クラス分類**と呼ばれます。
* **Regression** (回帰)。 株価のような値を予測するときの教師あり学習は回帰と呼ばれます。
* **Anomaly detection** (異常検出)。 目的が単に異常なデータ ポイントを識別することである場合があります。 たとえば、不正行為の検出では、クレジット カードの極めて異常な使用パターンが疑われます。 可能性のあるバリエーションは非常に多く、トレーニングの例は少ないので、不正行為がどのようなものになるかを学習するのは不可能です。 異常検出が使用するアプローチは、単に正常な行為がどのようなものかを (不正でない取引履歴を使用して) 学習し、それと大きく異なるものを識別するというものです。

### <a name="unsupervised"></a>教師なし

教師なし学習では、データ ポイントにラベルが関連付けられていません。 代わりに、教師なし学習アルゴリズムの目的は、いくつかの方法でデータを整理したり、その構造を記述することです。 これは、クラスターにグループ化したり、複雑なデータを簡単に、または整然と表示したりできるようにさまざまな表示方法を検索することを意味します。

### <a name="reinforcement-learning"></a>強化学習

強化学習では、アルゴリズムが各データ ポイントに応答してアクションを選択します。 また、学習アルゴリズムはその決定がどの程度優れていたかを示す報酬信号をその後短時間で受信します。
アルゴリズムはこれを基に戦略を変更し、最大の報酬を実現しようとします。 現時点で、Azure Machine Learning には強化学習アルゴリズム モジュールはありません。 強化学習はロボット工学の一般的な手法です。ある時点での一連のセンサーの読み取りがデータ ポイントになり、アルゴリズムがロボットの次の動作を選択します。 モノのインターネット アプリケーションにも自然に適合します。

## <a name="considerations-when-choosing-an-algorithm"></a>アルゴリズム選択時の考慮事項

### <a name="accuracy"></a>精度

可能な限り最も正確な回答を得ることが常に必要であるとは限りません。
使用目的によっては、近似で十分な場合があります。 その場合は、より大まかな方法を使用することで、処理時間を大幅に削減できることがあります。 近似手法のもう 1 つの利点は、一般に [過剰適合](https://youtu.be/DQWI1kvmwRg)が回避される傾向があることです。

### <a name="training-time"></a>トレーニング時間

モデルのトレーニングに必要な分数または時間数は、アルゴリズムによって大きく異なります。 通常、トレーニング時間は精度と密接に関係しています。 さらに、一部のアルゴリズムは他よりデータ ポイントの数に大きく影響を受けます。
時間が限られている場合、アルゴリズムの選択を左右することがあります。データ セットが大きい場合は特にそうです。

### <a name="linearity"></a>線形性

機械学習アルゴリズムの多くは線形性を使用します。 線形分類アルゴリズムは、クラスを直線 (またはその高次元版) で分離できるものと想定します。 これには、 (Azure Machine Learning で実装されている) ｌogistic regression (ロジスティック回帰) および support vector machine (サポート ベクター マシン) が含まれます。
線形回帰アルゴリズムは、データの傾向が直線に従うことを想定しています。 問題によって、このような想定が適切な場合もありますが、精度が低下することもあります。

![非線形クラス境界][1]

***非線形クラス境界*** *- 線形分類アルゴリズムに従うと精度が低下します*

![非線形傾向のデータ][2]

***非線形傾向のデータ*** *- 線形回帰法を使用すると、必要以上に多くのエラーが生成されます*

危険性はありますが、線形アルゴリズムは最初に使用する方法として非常に一般的です。 アルゴリズムが簡単で、速くトレーニングできる傾向があります。

### <a name="number-of-parameters"></a>パラメーターの数

パラメーターは、アルゴリズムを設定するときに使用します。 エラーの許容範囲や反復回数などのアルゴリズムの動作に影響を与える値、またはアルゴリズムの動作のバリエーションのオプションです。 アルゴリズムのトレーニング時間と精度は、設定の適切さに大きな影響を受けることがあります。 通常、パラメーター数の多いアルゴリズムは、適切な組み合わせを見つけるのに多くの試行錯誤が必要です。

別の方法として、Azure Machine Learning には [パラメーター スイープ](algorithm-parameters-optimize.md) モジュール ブロックがあり、ユーザーが選択した粒度でパラメーターのすべての組み合わせを自動的に試行します。 これは、パラメーター空間を確実に網羅する優れた方法ですが、パラメーターの数が増えるとモデルのトレーニングに必要な時間が指数関数的に増加します。

利点として、通常、パラメーターの数の多さはアルゴリズムがより柔軟であることを示します。 多くの場合、非常に高い精度を得られます。 ただしそれは適切なパラメーター設定の組み合わせを見つけられる場合です。

### <a name="number-of-features"></a>特徴の数

特定の種類のデータでは、特徴の数がデータ ポイントの数と比較して非常に大きくなる可能性があります。 遺伝学やテキスト データの場合によくあります。 特徴の数が多いと一部の学習アルゴリズムは処理が遅くなり、実行不可能なほどトレーニング時間が長くなります。 このケースには、Support Vector Machine が最適です (下記参照)。

### <a name="special-cases"></a>特殊なケース

一部の学習アルゴリズムは、データの構造や目的の結果について特定の想定をします。 ニーズに合うものが見つかれば、より役に立つ結果や正確な予測が得られたり、トレーニングが短時間で済みます。

| **アルゴリズム** | **精度** | **トレーニング時間** | **線形性** | **パラメーター** | **メモ** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Two-class classification** (2 クラス分類) | | | | | |
| [logistic regression](https://msdn.microsoft.com/library/azure/dn905994.aspx) (ロジスティック回帰) | |● |● |5 | |
| [デシジョン フォレスト](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6 | |
| [decision jungle](https://msdn.microsoft.com/library/azure/dn905976.aspx) (デシジョン ジャングル) |● |○ | |6 |低メモリ フットプリント |
| [ブースト デシジョン ツリー](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6 |メモリ フットプリントが大きい |
| [neural network](https://msdn.microsoft.com/library/azure/dn905947.aspx) (ニューラル ネットワーク) |● | | |9 |[追加カスタマイズ可能](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [averaged perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) (平均化パーセプトロン) |○ |○ |● |4 | |
| [support vector machine](https://msdn.microsoft.com/library/azure/dn905835.aspx) (サポート ベクター マシン) | |○ |● |5 |大きい特徴セットに好適 |
| [locally deep support vector machine](https://msdn.microsoft.com/library/azure/dn913070.aspx) (ローカル詳細サポート ベクター マシン) |○ | | |8 |大きい特徴セットに好適 |
| [Bayes’ point machine](https://msdn.microsoft.com/library/azure/dn905930.aspx) (ベイズ ポイント マシン) | |○ |● |3 | |
| **Multi-class classification** (多クラス分類) | | | | | |
| [ロジスティック回帰](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5 | |
| [デシジョン フォレスト](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6 | |
| [decision jungle](https://msdn.microsoft.com/library/azure/dn905963.aspx) (デシジョン ジャングル)  |● |○ | |6 |低メモリ フットプリント |
| [ニューラル ネットワーク](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9 |[追加カスタマイズ可能](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx) (一対全) |- |- |- |- |選択した 2 クラス法のプロパティを参照してください |
| **Regression** (回帰) | | | | | |
| [linear](https://msdn.microsoft.com/library/azure/dn905978.aspx) (線形) | |● |● |4 | |
| [Bayesian linear](https://msdn.microsoft.com/library/azure/dn906022.aspx) (ベイジアン線形) | |○ |● |2 | |
| [decision forest](https://msdn.microsoft.com/library/azure/dn905862.aspx) (デシジョン フォレスト) |● |○ | |6 | |
| [boosted decision tree](https://msdn.microsoft.com/library/azure/dn905801.aspx) (ブースト デシジョン ツリー) |● |○ | |5 |メモリ フットプリントが大きい |
| [fast forest quantile](https://msdn.microsoft.com/library/azure/dn913093.aspx) (高速フォレスト分位) |● |○ | |9 |ポイント予測ではなく分布 |
| [ニューラル ネットワーク](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9 |[追加カスタマイズ可能](https://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) (ポワソン) | | |● |5 |技術的には対数線形。 カウント予測用 |
| [ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx) (序数) | | | |0 |ランク順序予測用 |
| **Anomaly detection** (異常検出) | | | | | |
| [support vector machine](https://msdn.microsoft.com/library/azure/dn913103.aspx) (サポート ベクター マシン) |○ |○ | |2 |大きい特徴セットに特に好適 |
| [PCA-based anomaly detection](https://msdn.microsoft.com/library/azure/dn913102.aspx) (PCA ベースの異常検出) | |○ |● |3 | |
| [K-Means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |クラスタリング アルゴリズム |

**アルゴリズムのプロパティ:**

**●** - 極めて高い精度、高速のトレーニング時間、線形性の使用。

**○** - 高い精度と中程度のトレーニング時間。

## <a name="algorithm-notes"></a>アルゴリズムの注意事項

### <a name="linear-regression"></a>Linear regression (線形回帰)

前に説明したように、 [ｌinear regression](https://msdn.microsoft.com/library/azure/dn905978.aspx) はデータ セットに直線 (または平面、または超平面) を適合させます。 役に立ち簡単で高速ですが、一部の問題については過剰に単純になる可能性があります。
[ｌinear regression のチュートリアル](linear-regression-in-azure.md)についてはこちらをチェックしてください。

![線形傾向のデータ][3]

***線形傾向のデータ***

### <a name="logistic-regression"></a>Logistic regression (ロジスティック回帰)

名前に "regression" が含まれていて紛らわしいのですが、logistic regression は実際には [2 クラス](https://msdn.microsoft.com/library/azure/dn905994.aspx)分類と[多クラス](https://msdn.microsoft.com/library/azure/dn905853.aspx)分類のための強力なツールです。 高速で単純です。 直線ではなく「S」型の曲線を使用するため、データをグループに分割するのに適しています。 ｌogistic regression は線形クラス境界を提供するので、使用するときは、線形近似を受け入れ可能なことを確認します。

![特徴が 1 つだけの 2 クラス データに対する ｌogistic regression][4]

***特徴が 1 つだけの 2 クラス データへの ｌogistic regression*** *- クラス境界はロジスティック曲線が両方のクラスに近いポイントです*

### <a name="trees-forests-and-jungles"></a>ツリー、フォレスト、ジャングル

Decision forest ([regression](https://msdn.microsoft.com/library/azure/dn905862.aspx)、[two-class](https://msdn.microsoft.com/library/azure/dn906008.aspx)、[multiclass](https://msdn.microsoft.com/library/azure/dn906015.aspx))、decision jungle ([two-class](https://msdn.microsoft.com/library/azure/dn905976.aspx)、[multiclass](https://msdn.microsoft.com/library/azure/dn905963.aspx))、および boosted decision tree ([regression](https://msdn.microsoft.com/library/azure/dn905801.aspx)、[two-class](https://msdn.microsoft.com/library/azure/dn906025.aspx)) はすべて、機械学習の基本概念であるデシジョン ツリーに基づきます。 デシジョン ツリーには多くのバリエーションがありますが、行うことはすべて同じで、特徴空間をほとんど同じラベルを持つ領域に分割します。 これらは、分類または回帰のどちらを実行しているかにより、一貫性のあるカテゴリを持つ領域、または定数値の領域です。

![デシジョン ツリーで特徴空間を細分化する][5]

***デシジョン ツリーは特徴空間をほぼ同じ値の領域に細分化します***

特徴空間は任意の小さい領域に分割できるので、領域ごとに 1 つのデータ ポイントを持つようになるまで細かく分割することを簡単に想像できます。 これは極端な過剰適合の例です。 これを回避するには、ツリーが相互に関連しないように数学的に特別に考慮したツリーの大きなセットを作成します。 この「デシジョン フォレスト」の平均が、過剰適合を回避するツリーです。 Decision forest は多くのメモリを使用する場合があります。 Decision jungle は、トレーニング時間が若干長くなるのと引き替えにメモリ消費が少ないバリエーションです。

Boosted decision tree は、分割できる回数、および各領域に許可されるデータ ポイントの最小数を制限することによって、過剰適合を回避します。 このアルゴリズムはツリーのシーケンスを構築し、各ツリーは前のツリーによって残されたエラーを補正するために学習します。 結果は非常に正確ですが、メモリを多く使用する傾向があります。 技術的な詳細については、 [Friedman の原著論文](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf)をご覧ください。

[Fast forest quantile regression](https://msdn.microsoft.com/library/azure/dn913093.aspx) (高速フォレスト分位回帰) は、領域内のデータの標準 (中央) 値だけでなく、分位の形式でその分布も知りたい特殊なケースに対応するデシジョン ツリーの一種です。

### <a name="neural-networks-and-perceptrons"></a>Neural network (ニューラル ネットワーク) と perceptron (パーセプトロン)

Neural network は脳にインスパイアされた学習アルゴリズムで、[多クラス](https://msdn.microsoft.com/library/azure/dn906030.aspx)、[2 クラス](https://msdn.microsoft.com/library/azure/dn905947.aspx)、および[回帰](https://msdn.microsoft.com/library/azure/dn905924.aspx)の問題を対象とします。 無限の多様性がありますが、Azure Machine Learning の neural network はすべてが無閉路有効グラフの形式です。 つまり、入力特徴はレイヤーのシーケンスを前方に通過して (戻ることはありません) 出力に変換されます。 各レイヤーでは、入力はさまざまな組み合わせで加重され、合計されて、次の層に渡されます。 この単純な計算の組み合わせにより、一見マジックのように、高度なクラス境界とデータ傾向を学習できます。 この種の多レイヤー ネットワークでは、多くの技術レポートと SF の基になっている「ディープ ラーニング」が実行されます。

ただし、この高パフォーマンスにはコストがかかります。 Neural network はトレーニングに時間がかかり、多数の特徴を持つ大規模なデータ セットの場合は特にそうです。 また、ほとんどのアルゴリズムよりパラメーターが多く、パラメーター スイープによってトレーニング時間が大幅に延びます。
また、 [独自のネットワーク構造を指定](https://go.microsoft.com/fwlink/?LinkId=402867)しようとがんばると、無限の可能性があります。

![Neural network によって学習された境界][6]
***Neural network によって学習される境界は複雑で不規則になる場合があります***

[two-class averaged perceptron](https://msdn.microsoft.com/library/azure/dn906036.aspx) (2 クラス平均化パーセプトロン) は、急激に増加するトレーニング時間に対する Neural network の回答です。 これは、線形クラス境界を提供するネットワーク構造を使用します。 今日の標準ではほとんど旧式ですが、長い活用の歴史があり、十分に小さいので短時間で学習できます。

### <a name="svms"></a>SVM

Support vector machine (SVM) は、可能な限り広いマージンでクラスを分離する境界を探します。 2 つのクラスを明確に分離できない場合、アルゴリズムは最善の境界を検出します。 Azure Machine Learning の [two-class SVM](https://msdn.microsoft.com/library/azure/dn905835.aspx) (2 クラス SVM) は、直線のみでこれを行います。 (SVM でいうと、線形カーネルを使用します。)この線形近似を行うため、かなり高速に実行できます。 特に有効なのは、テキストやゲノムのような特徴の多いデータの場合です。 このような場合、SVM は、他のほとんどのアルゴリズムより速く、少ない過剰適合でクラスを分離でき、さらに必要なメモリ量も中程度です。

![Support vector machine のクラス境界][7]

***標準的な Support vector machine のクラス境界は、2 つのクラスを分離するマージンを最大化します***

Microsoft Research のもう 1 つの所産である [two-class locally deep SVM](https://msdn.microsoft.com/library/azure/dn913070.aspx) (2 クラス ローカル詳細 SVM) は、SVM の非線形バリエーションであり、線形バージョンの速度とメモリ効率性をほぼ維持しています。 線形アプローチでは十分に正確な回答を得られない場合に最適です。 速さを維持するために、問題が多数の小さな線形 SVM 問題に分解されています。 仕組みについては、 [詳細な説明](http://proceedings.mlr.press/v28/jose13.html) をご覧ください。

非線形 SVM の巧妙な拡張機能を使用して、 [one-class SVM](https://msdn.microsoft.com/library/azure/dn913103.aspx) (1 クラス SVM) はデータ セット全体を正確に示す境界を描画します。 異常検出に有効です。 その境界のはるか外側に位置する新しいデータ ポイントは、注目するのに十分な異常値です。

### <a name="bayesian-methods"></a>ベイズ法

ベイズ法は高品質であり、過剰適合を回避します。 回答の予想される分布について事前にいくつかの想定を行うことによってこれを実現します。 この方法のもう 1 つの副産物は、パラメーターが非常に少ないことです。 Azure Machine Learning には、分類 ([Two-class Bayes' point machine](https://msdn.microsoft.com/library/azure/dn905930.aspx)) と回帰 ([Bayesian linear regression](https://msdn.microsoft.com/library/azure/dn906022.aspx)) の両方に対するベイズ アルゴリズムがあります。
これらのアルゴリズムではデータを直線で分割または適合できるものと想定していることに注意してください。

歴史的には、Bayes' point machine は Microsoft Research で開発されました。 いくつかの非常に美しい理論的な処理が背後で行われています。 興味がある場合は、[JMLR のオリジナル記事](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf)および [Chris Bishop による洞察に富むブログ](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx)をご覧ください。

### <a name="specialized-algorithms"></a>特殊なアルゴリズム
非常に特殊な目標がある場合でも大丈夫です。 Azure Machine Learning コレクション内には、次に特化したアルゴリズムがあります。

- ランク予測 ([ordinal regression](https://msdn.microsoft.com/library/azure/dn906029.aspx))
- カウント予測 ([Poisson regression](https://msdn.microsoft.com/library/azure/dn905988.aspx))
- 異常検出 ([主成分分析](https://msdn.microsoft.com/library/azure/dn913102.aspx)に基づくものと、[サポート ベクター マシン](https://msdn.microsoft.com/library/azure/dn913103.aspx)に基づくもの)
- クラスタリング ([K-means](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![PCA-based anomaly detection (PCA ベースの異常検出)][8]

***PCA-based anomaly detection*** *- 大部分のデータは標準的な分布に分類されます。その分布から大きく逸脱したポイントは異常です*

![K-means を使用してグループ化されたデータ セット][9]

***データ セットは、K-means を使用して 5 つのクラスターにグループ化されます***

集合体の[one-v-all multiclass (一対全多クラス) 分類子](https://msdn.microsoft.com/library/azure/dn905887.aspx)もあります。これは、N クラス分類問題を N-1 2 クラス分類問題に分割します。 精度、トレーニング時間、線形性プロパティは、使用される 2 クラス分類子によって決まります。

![3 クラス分類子を形成するために組み合わされた 2 クラス分類子][10]

***2 クラス分類子のペアの組み合わせで、3 クラス分類子が形成されます***

Azure Machine Learning には、 [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf)のタイトルの下に強力な機械学習フレームワークへのアクセスも含まれます。
VW はここでの分類には当てはまらず、分類と回帰の両方の問題を学習でき、部分的にラベル付けされていないデータからさえも学習できます。 さまざまな学習アルゴリズム、損失関数、および最適化アルゴリズムのいずれかを使用するように構成できます。 効率的で、並列的で、非常に高速なように一から設計されました。 非常に大きい特徴セットを簡単に処理します。
Microsoft Research の John Langford によって開発が進められた VW は、市販車のようなアルゴリズムの中における F1 のような存在です。 VW に適さない問題もありますが、もしご自身の問題に適合するならば学習する価値は十分にあります。 また、複数の言語で [スタンドアロンのオープン ソース コード](https://github.com/JohnLangford/vowpal_wabbit) として使用することもできます。

## <a name="next-steps"></a>次の手順

* アルゴリズムに関する説明と使用例が記載されたインフォグラフィックのダウンロードについては、「[インフォグラフィックのダウンロード: Machine Learning の基礎とアルゴリズムの使用例](basics-infographic-with-algorithm-examples.md)」をご覧ください。

* Machine Learning Studio で使用可能な機械学習アルゴリズムのカテゴリ別の一覧については、Machine Learning Studio アルゴリズムとモジュールのヘルプの[モデルの初期化](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/machine-learning-initialize-model)に関するページを参照してください。

* Machine Learning Studio で使用できるすべてのアルゴリズムとモジュールの一覧 (アルファベット順) については、Machine Learning Studio アルゴリズムとモジュールのヘルプの「[A-Z list of Machine Learning Studio modules (Machine Learning Studio モジュールの一覧 (アルファベット順))](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/a-z-module-list)」を参照してください。

* Machine Learning Studio の機能の概要を示す図をダウンロードして印刷する場合は、「 [Azure Machine Learning Studio 機能の概要図](studio-overview-diagram.md)」ご覧ください。

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
