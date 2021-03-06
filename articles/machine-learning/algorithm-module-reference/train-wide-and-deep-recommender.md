---
title: ワイドかつディープなレコメンダーのトレーニング モジュールを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーでワイドかつディープなレコメンダーのトレーニング モジュールを使用して、推奨モデルをトレーニングする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/12/2020
ms.openlocfilehash: d7dd7105ddb0d6503faefb996b84c0e53a62ce49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104655378"
---
# <a name="train-wide--deep-recommender"></a>ワイドかつディープなレコメンダーのトレーニング
この記事では、Azure Machine Learning デザイナーで **ワイドかつディープなレコメンダーのトレーニング** モジュールを使用して、推奨モデルをトレーニングする方法について説明します。 このモジュールは、Google によって提案されているワイド & ディープ ラーニング (Wide & Deep Learning) に基づいています。

**ワイドかつディープなレコメンダーのトレーニング** モジュールは、ユーザー項目の評価 3 要素のデータセットと、オプションで一部のユーザーと項目の特徴を読み取ります。 これにより、トレーニング済みのワイドかつディープなレコメンダーが返されます。  その後、トレーニング済みのモデルを使用して、[ワイドかつディープなレコメンダーのスコア付け](score-wide-and-deep-recommender.md)モジュールを使用して評価予測や提案を生成することができます。  

## <a name="more-about-recommendation-models-and-the-wide--deep-recommender"></a>推奨モデルとワイドかつディープなレコメンダーの詳細  

レコメンデーション システムの主な目的は、システムの *ユーザー* に対して 1 つ以上の *項目* を推奨することです。 項目の例として、映画、レストラン、本、曲などがあります。 ユーザーは、個人、個人のグループ、または項目の好みがあるその他のエンティティにすることができます。  

レコメンダー システムには、主に 2 つのアプローチがあります。 

+ 1 つ目は、**コンテンツベースの** アプローチです。これは、ユーザーと項目の両方の特徴を利用します。 ユーザーは、年齢や性別などのプロパティによって記述できます。また、項目は作成者や製造元などのプロパティによって記述される場合があります。 コンテンツベースのレコメンデーション システムの一般的な例は、出会い系のサイトです。 
+ 2 番目の方法は **協調フィルタリング** です。このフィルターでは、ユーザーと項目の識別子のみを使用し、ユーザーが項目に対して指定した (疎な) 評価マトリックスから、これらのエンティティについての暗黙的な情報を取得します。 ユーザーについての情報は、そのユーザーが評価した項目や、同じ項目を評価した他のユーザーから学ぶことができます。  

ワイドかつディープなレコメンダーは、協調フィルタリングとコンテンツベースのアプローチを使用して、これらのアプローチを組み合わせています。 したがって、**ハイブリッド レコメンダー** と考えることができます。 

このしくみは次のとおりです。ユーザーがシステムの比較的初心者である場合は、ユーザーに関する特徴の情報を使用することによって予測が向上します。このようにしてよく知られた "コールド スタート" の問題に対処します。 ただし、特定のユーザーから十分な数の評価を収集した後は、これらのユーザーに対して、その特徴だけではなく特定の評価に基づいて完全に個人的な予測を行うことができます。 そのため、コンテンツベースの推奨事項から協調フィルタリングに基づく推奨事項へのスムーズな移行があります。 ユーザーまたは項目の特徴を使用できない場合でも、ワイドかつディープなレコメンダーは協調フィルタリング モードで動作します。  

ワイドかつディープなレコメンダーおよびその基礎となっている確率論的アルゴリズムの詳細については、関連の調査報告書があります。[レコメンダー システム対応のワイドかつディープ ラーニング](https://arxiv.org/pdf/1606.07792.pdf)。  

## <a name="how-to-configure-train-wide--deep-recommender"></a>ワイドかつディープなレコメンダーのトレーニングを構成する方法  

+ [トレーニング データの準備](#prepare-data)
+ [モデルのトレーニング](#train-the-model)

### <a name="prepare-data"></a>データを準備する

モジュールを使用する前に、データを推奨モデルで想定される形式にする必要があります。 **ユーザー、項目、評価を表す 3 つの要素** のトレーニング データセットが必要ですが、ユーザーの特徴と項目の特徴 (入手可能な場合) を別々のデータセットに含めることもできます。

#### <a name="required-dataset-of-user-item-ratings"></a>ユーザー、項目、評価の必須データセット

トレーニングに使用する入力データに、適切な形式のデータが含まれている必要があります。 

+ 最初の列には、ユーザーの識別子が含まれている必要があります。
+ 2 番目の列には、項目の識別子が含まれている必要があります。
+ 3 番目の列には、ユーザーと項目のペアの評価が含まれています。 評価の値は数値型にする必要があります。 

たとえば、典型的なユーザー、項目、評価のセットは次のようになります。

|UserId|MovieId|Rating|
|------------|-------------|------------|
|1|68646|10|
|223|31381|10|

#### <a name="user-features-dataset-optional"></a>ユーザーの特徴のデータセット (オプション)

**ユーザーの特徴** のデータセットには、ユーザーの識別子を含める必要があります。また、ユーザー、項目、評価のデータセットの最初の列に指定されていたものと同じ識別子を使用します。 その他の列には、ユーザーを表す特徴がいくつ含まれていてもかまいません。  

たとえば、典型的なユーザーの特徴のセットは次のようになります。 

|UserId|Age|性別|興味|場所|
|------------|--------------|-----------------------|---------------|------------|
|1|25|male| ドラマ    |ヨーロッパ|
|223|40|female|ロマンス|アジア|

#### <a name="item-features-dataset-optional"></a>項目の特徴のデータセット (オプション)

項目の特徴のデータセットでは、最初の列に項目の識別子が含まれている必要があります。 その他の列には、項目を説明する特徴がいくつ含まれていてもかまいません。  

たとえば、典型的な項目の特徴のセットは次のようになります。  

|MovieId|タイトル|原語|ジャンル|年|
|-------------|-------------|-------------------|-----------|---------------|
|68646|ゴッドファーザー|英語|ドラマ|1972|
|31381|風と共に去りぬ|英語|履歴|1939|

### <a name="train-the-model"></a>モデルをトレーニングする

1.  **ワイドかつディープなレコメンダーのトレーニング** モジュールをデザイナー内の実験に追加し、これをトレーニング データセットに接続します。  
  
2. ユーザーの特徴または項目の特徴のいずれかの個別のデータセットがある場合は、これらを **ワイドかつディープなレコメンダーのトレーニング** モジュールに接続します。  
  
    - **ユーザーの特徴のデータセット**:ユーザーを記述するデータセットを 2 番目の入力に接続します。
    - **項目の特徴のデータセット**:項目を記述するデータセットを 3 番目の入力に接続します。  
    
3.  **[Epochs]\(エポック\)** : アルゴリズムがトレーニング データ全体を処理する回数を示します。 

    この数が多いほど、トレーニングが十分になります。ただし、トレーニングにかかる時間が増え、オーバーフィットの原因になる可能性があります。

4. **[バッチ サイズ]** : 1 回のトレーニング ステップで使用されるトレーニング例の数を入力します。 

     このハイパーパラメーターはトレーニング速度に影響を与える場合があります。 バッチ サイズを大きくすると時間が短縮されますが、収束時間が長くなる可能性があります。 また、バッチが大きすぎて GPU/CPU に適合しない場合、メモリ エラーが発生することがあります。

5.  **[Wide part optimizer]\(ワイド パート オプティマイザー\)** : モデルのワイド部分にグラデーションを適用するための 1 つのオプティマイザーを選択します。

6.  **[Wide optimizer learning rate]\(ワイド オプティマイザー学習速度\)** : ワイド パート オプティマイザーの学習速度を定義する 0.0 から 2.0 までの数値を入力します。

    このハイパーパラメーターは、各トレーニング ステップでのステップ サイズを決定しながら、損失関数の最適解に近づきます。 学習速度が大きすぎると、学習で極小値を飛び越える可能性がありますが、学習速度が小さすぎると収束の問題が発生する可能性があります。

7.  **[Crossed feature dimension]\(クロス積変換された特徴のディメンション\)** : 目的のユーザー ID および項目 ID の特徴を入力することによって、ディメンションを入力します。 

    ワイドかつディープなレコメンダーは、既定でユーザー ID および項目 ID の特徴に対してクロス積変換を実行します。 クロス積変換された結果は、ディメンションを確認するために、この数に従ってハッシュされます。

8.  **[Deep part optimizer]\(ディープ パート オプティマイザー\)** : モデルのディープ部分にグラデーションを適用するための 1 つのオプティマイザーを選択します。

9.  **[Deep optimizer learning rate]\(ディープ オプティマイザー学習速度\)** : ディープ パート オプティマイザーの学習速度を定義する 0.0 から 2.0 までの数値を入力します。

10.  **[User embedding dimension]\(ユーザーの埋め込みディメンション\)** : ユーザー ID 埋め込みのディメンションを指定する整数を入力します。

     ワイドかつディープなレコメンダーは、ワイド パートとディープ パートの両方のための共有されたユーザー ID 埋め込みと項目 ID 埋め込みを作成します。

11.  **[Item embedding dimension]\(項目の埋め込みディメンション\)** : 項目 ID 埋め込みのディメンションを指定する整数を入力します。

12.  **[Categorical features embedding dimension]\(カテゴリ別の特徴の埋め込みディメンション\)** : カテゴリ別の特徴の埋め込みのディメンションを指定する整数を入力します。

     ワイドかつディープなレコメンダーのディープ コンポーネントでは、カテゴリ別の特徴の埋め込みベクトルが習得されます。 また、これらの埋め込みベクトルは同じディメンションを共有します。

13.  **[Hidden units]\(非表示単位\)** : ディープ コンポーネントの非表示ノードの数を入力します。 各レイヤーのノード数は、コンマで区切られます。 たとえば、"1000,500,100" と入力すると、ディープ コンポーネントに 3 つのレイヤーがあることを指定し、最初のレイヤーから最後のレイヤーにかけて、それぞれ 1000 ノード、500 ノード、および 100 ノードがあります。

14.  **[Activation function]\(アクティブ化関数\)** : 各レイヤーに適用されているアクティブ化関数を 1 つ選択します。既定値は ReLU です。

15.  **ドロップアウト**: トレーニング中に各レイヤーで出力が削除される確率を決定するために、0.0 から 1.0 の範囲の数値を入力します。

     ドロップアウトはニューラル ネットワークのオーバーフィットを防ぐための正則化メソッドです。 この値に対する一般的な決定の 1 つは、さまざまなネットワークやタスクにとってほぼ最適と思われる 0.5 で開始することです。

16.  **[Batch Normalization]\(バッチ正規化\)** : ディープ コンポーネントの各非表示レイヤーの後にバッチ正規化を使用するには、このオプションを選択します。

     バッチ正規化は、ネットワーク トレーニング中の内部的な共変量シフトの問題を軽減するための手法です。 これは一般に、ネットワークの速度、パフォーマンス、および安定性を向上させるのに役立ちます。 

17.  パイプラインを実行します。

## <a name="results"></a>結果

パイプラインの実行が完了した後、モデルをスコアリングに使用するには、[ワイドかつディープなレコメンダーのトレーニング](train-wide-and-deep-recommender.md)を [ワイドかつディープなレコメンダーのスコア付け](score-wide-and-deep-recommender.md)に接続し、新しい入力例の値を予測します。

##  <a name="technical-notes"></a>テクニカル ノート

ワイド & ディープでは、ワイド線形モデルとディープ ニューラル ネットワークを結合してトレーニングすることで、記憶と汎化の長所を組み合わせることができます。 ワイド コンポーネントは、特徴の相互作用を記憶するために、一連の未加工の特徴と特徴の変換を受け入れます。 また、ディープ コンポーネントは、少ない特徴エンジニアリングで、低次元の高密度な特徴の埋め込みにより、予測されない特徴の組み合わせに一般化します。 

ワイドかつディープなレコメンダーの実装では、モジュールは既定のモデル構造を使用します。 ワイド コンポーネントでは、ユーザーの埋め込み、項目の埋め込み、およびユーザー ID と項目 ID のクロス積変換を入力として取得します。 モデルのディープ パートでは、カテゴリ別の特徴の埋め込みベクトルが習得されます。 これらのベクトルは、その他の数値特徴ベクトルと共に、詳細なフィードフォワード ニューラル ネットワークに取り込まれます。 ワイド パートとディープ パートは、最終的な出力対数オッズを予測として合計することによって組み合わされ、最終的には結合トレーニングのための 1 つの共通損失関数になります。


## <a name="next-steps"></a>次のステップ

Azure Machine Learning の[利用可能な一連のモジュール](module-reference.md)を参照してください。 
