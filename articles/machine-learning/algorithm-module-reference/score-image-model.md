---
title: 画像モデルのスコア付けモジュールを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で画像モデルのスコア付けモジュールを使用して、トレーニングされた画像モデルにより予測を生成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b949603b3e6ee51311f9c54f3e1326217f00c82d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039117"
---
# <a name="score-image-model"></a>画像モデルのスコア付け

この記事では Azure Machine Learning デザイナー (プレビュー) 内のモジュールについて説明します。

入力画像データに対してトレーニングされた画像モデルを使用して予測を生成するには、このモジュールを使用します。

## <a name="how-to-configure-score-image-model"></a>画像モデルのスコア付けを構成する方法

1. **画像モデルのスコア付け**モジュールをパイプラインに追加します。

2. トレーニングされた画像モデルと入力画像データを含むデータセットをアタッチします。 

    データは ImageDirectory 型である必要があります。 イメージ ディレクトリを取得する方法の詳細については、「[イメージ ディレクトリへの変換](convert-to-image-directory.md)」を参照してください。 また、一般に、入力データセットのスキーマはモデルのトレーニングに使用されたデータのスキーマと一致している必要があります。

3. パイプラインを送信します。

## <a name="results"></a>結果

[画像モデルのスコア付け](score-image-model.md)を使用して一連のスコアを生成したら、モデルの精度 (パフォーマンス) を評価するために使用される一連のメトリックを生成するために、このモジュールとスコア付けされたデータセットを接続して[モデルを評価](evaluate-model.md)することができます。 

### <a name="publish-scores-as-a-web-service"></a>Web サービスとしてスコアを公開する

スコア付けの一般的な用途は、予測 Web サービスの一部として出力を返すことです。 詳細については、Azure Machine Learning デザイナーでのパイプラインに基づいたリアルタイム エンドポイントのデプロイ方法に関する[このチュートリアル](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-deploy)を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 
