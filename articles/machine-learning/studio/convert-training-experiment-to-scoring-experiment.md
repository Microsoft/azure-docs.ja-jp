---
title: デプロイのためにモデルを準備する - Azure Machine Learning Studio | Microsoft Docs
description: Machine Learning Studio のトレーニング実験を予測実験に変換することで、トレーニング済みのモデルを Web サービスとしてデプロイするための準備方法。
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: eb943c45-541a-401d-844a-c3337de82da6
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.openlocfilehash: ef54281b91147060e9c00bcc6e45e0db0fb8933a
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273104"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio でのデプロイのためにモデルを準備する方法

Azure Machine Learning Studio が提供するツールは、予測分析モデルを作成し、Azure Web サービスとしてデプロイすることでそのモデルを操作できるようにするために必要です。

これを実行するには、Studio を使用して*トレーニング実験*と呼ばれる実験を作成します。トレーニング実験では、モデルのトレーニング、スコア付け、編集を行います。 満足したら、トレーニング実験をユーザー データがスコア付けされるように構成した*予測実験*に変換することによって、モデルをデプロイする準備を行います。

このプロセスの例については、「[チュートリアル:信用リスク評価のための予測分析ソリューションを Azure Machine Learning で開発する](walkthrough-develop-predictive-solution.md)」を参照してください。

この記事では、トレーニング実験を予測実験に変換する方法と、予測実験をデプロイする方法について詳しく解説します。 これらの詳細を理解することで、デプロイしたモデルをより効果的にするための構成方法を学ぶことができます。



## <a name="overview"></a>概要 

トレーニング実験を予測実験に変換するプロセスには、次の 3 つの手順が含まれます。

1. 機械学習アルゴリズムのモジュールをトレーニング済みのモデルに置き換えます。
2. スコア付けに必要なモジュールのみに実験を絞り込みます。 トレーニング実験にはトレーニングに必要な多数のモジュールが含まれますが、モデルがトレーニングされると、それらのモジュールは不要になります。
3. モデルが Web サービス ユーザーからのデータを受け入れる方法と、どのデータを返すかを定義します。

> [!TIP]
> トレーニング実験では、独自のデータを使用してモデルのトレーニングとスコア付けに取り組みました。 ただしいったんデプロイすると、ユーザーから新しいデータがモデルに送信され、モデルは予測結果を返します。 このため、トレーニング実験を予測実験に変換してデプロイの準備をするときには、モデルが他のユーザーにどのように使用されるかに留意してください。
> 
> 

## <a name="set-up-web-service-button"></a>[Web サービスの設定] ボタン
(実験キャンバスの下部にある **[実行]** をクリックして) 実験を実行したら、(**[予測 Web サービス]** オプションを選択して)**[Web サービスの設定]** ボタンをクリックします。 **[Web サービスの設定]** によって、トレーニング実験を予測実験に変換する次の 3 つの手順が実行されます。

1. トレーニング済みのモデルを、(実験キャンバスの左側にある) モジュール パレットの **[トレーニング済みのモデル]** セクションに保存します。 次に、機械学習アルゴリズムと[モデルのトレーニング][train-model] モジュールを、保存したトレーニング済みのモデルに置き換えます。
2. 実験が分析され、明らかにトレーニングのためにのみ使用されて不要になったモジュールが削除されます。
3. _Web サービスの入力_および_出力_モジュールを、実験の既定の場所に挿入します (これらのモジュールはユーザー データを受け入れて返します)。

たとえば、次の実験では、サンプルの国勢調査のデータを使用した 2 クラスのブースト デシジョン ツリー モデルをトレーニングします。

![トレーニング実験][figure1]

この実験のモジュールは、基本的に 4 つの異なる機能を実行します。

![モジュール関数][figure2]

トレーニング実験を予測実験に変換すると、これらのモジュールの一部は不要になったり、別の役割を果たすようになります。

* **データ**- このサンプル データセット内のデータは、スコア付け中には使用されません。Web サービスのユーザーは、スコア付けするデータを指定します。 ただし、データ型など、このデータセットからのメタデータは、トレーニング済みのモデルで使用されます。 このため、このメタデータを提供できるように、予測実験のデータセットを保持する必要があります。

* **準備** -スコア付けのために送信されるユーザー データによっては、これらのモジュールが受信データの処理に必要な場合と必要でない場合があります。 **[Web サービスの設定]** ボタンではこれらは処理されないため、ユーザーがどのように処理するかを決める必要があります。
  
    たとえばこの例では、サンプル データセットで値が不足している可能性があるため、これに対処するために[見つからないデータのクリーンアップ][clean-missing-data] モジュールが含まれています。 また、サンプル データセットには、モデルのトレーニングには不要な列が含まれています。 このため、これらの余分な列をデータ フローから除外するための[データセット内の列の選択][select-columns]モジュールが含まれています。 Web サービスを通じてスコア付けのために送信されるデータに不足している値がないことがわかっている場合は、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを削除できます。 ただし、[データセット内の列の選択][select-columns]モジュールは、トレーニング済みのモデルが必要とするデータ列の定義に役立つため、このモジュールは残す必要があります。

* **トレーニング** - これらのモジュールは、モデルのトレーニングに使用します。 **[Web サービスの設定]** をクリックすると、これらのモジュールはトレーニング済みのモデルを含む 1 つのモジュールに置き換えられます。 この新しいモジュールは、モジュール パレットの **[Trained Models (トレーニング済みのモデル)]** セクションに保存されます。

* **スコア** - この例では、データ ストリームをテスト データとトレーニング データに分割するために、[データの分割][split] モジュールが使用されます。 予測実験ではもうトレーニングを行わないため、[データの分割][split] を削除できます。 同様に、2 番目の[モデルのスコア付け][score-model]モジュールと[モデルの評価][evaluate-model]モジュールは、テスト データの結果を比較するために使用されるため、これらのモジュールも予測実験では不要です。 ただし、残りの[モデルのスコア付け][score-model]モジュールは、Web サービスを通じてスコア結果を返すために必要です。

**[Web サービスの設定]** をクリックした後で、この例がどのようになるのかを次に示します。

![変換された予測実験][figure3]

**[Web サービスの設定]** によって実行された作業は、実験を Web サービスとしてデプロイするための準備として十分な場合があります。 ただし、実験に合わせて追加の作業を実行する可能性があります。

### <a name="adjust-input-and-output-modules"></a>入力と出力のモジュールを調整する
トレーニング実験では、トレーニング データのセットを使用して、機械学習アルゴリズムが必要とする形式でデータを取得するいくつかの処理を実行しました。 Web サービスを通じて受信することを想定しているデータでこの処理が不要な場合は、**Web サービスの入力モジュール**の出力を実験の別のノードに接続することで、これを回避できます。 この場所のモデルにユーザーのデータが届くようになります。

たとえば、既定では、上の図で示すように、**[Web サービスの設定]** によって、**Web サービスの入力**モジュールがデータ フローの上部に配置されます。 ただし、**Web サービスの入力**をデータ処理モジュールの先に手動で配置することができます。

![Web サービス入力の移動][figure4]

Web サービスを通じて提供される入力データが、これで処理なしでモデルのスコア付けモジュールに直接渡されるようになりました。

同様に、既定では **Web サービスの設定** によって、Web サービスの出力モジュールがデータ フローの下部に置かれます。 この例では、Web サービスはユーザーに、完全な入力データのベクトルとスコア付けの結果を含む[モデルのスコア付け][score-model]モジュールの出力を返します。
ただし、別のものを返したい場合は、**Web サービスの出力**モジュールの前に別のモジュールを追加することができます。 

たとえば、入力データのベクトル全体ではなく、スコア付けの結果のみを返すようにする場合は、[データセット内の列の選択][select-columns]モジュールを追加して、スコア付けの結果以外のすべての列を除外できます。 次に、**Web サービスの出力**モジュールを[データセット内の列の選択][select-columns]モジュールの出力に移動します。 実験は次のようになります。

![Web サービス出力の移動][figure5]

### <a name="add-or-remove-additional-data-processing-modules"></a>データ処理モジュールを追加または削除する
スコア付け中に必要ないことがわかっているモジュールが、他にも実験に存在する場合は、それらを削除できます。 たとえば、**Web サービスの入力**モジュールをデータ処理モジュールの後の地点に移動したため、[見つからないデータのクリーンアップ][clean-missing-data] モジュールを予測実験から削除できます。

これで、予測実験が次のようになります。

![その他のモジュールの取り外し][figure6]


### <a name="add-optional-web-service-parameters"></a>オプションの Web サービス パラメーターを追加する
場合によっては、Web サービスのユーザーがサービスにアクセスしたときに、モジュールの動作を変更できるようにすることもあります。 *Web サービスのパラメーター* を使えば変更できます。

一般的な例として、デプロイされた Web サービスのユーザーが Web サービスにアクセスしたときに別のデータ ソースを指定できるように、[データのインポート][import-data] モジュールを設定する場合があります。 または、別の宛先を指定できるように[データのエクスポート][export-data] モジュールを構成します。

Web サービスのパラメーターを定義して、1 つまたは複数のモジュール パラメーターに関連付け、必須かオプションかを指定することができます。 Web サービスのユーザーは、サービスにアクセスするときにこれらのパラメーターの値を指定することができ、モジュールの動作はそれに応じて変更されます。

Web サービス パラメーターとその使用方法の詳細については、「[Azure Machine Learning Web サービスのパラメーターの使用][webserviceparameters]」を参照してください。

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Web サービスとして予測実験をデプロイする
予測実験の準備を十分に実行したので、Azure Web サービスとしてデプロイできます。 Web サービスを使用してユーザーはデータをモデルに送信でき、モデルは予測を返します。

完全なデプロイ プロセスの詳細については、「[Azure Machine Learning Web サービスをデプロイする][deploy]」を参照してください。

[deploy]: publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]:./media/convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]:./media/convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]:./media/convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]:./media/convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]:./media/convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]:./media/convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
