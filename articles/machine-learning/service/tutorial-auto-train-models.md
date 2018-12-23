---
title: '回帰モデルのチュートリアル: モデルを自動的にトレーニングする'
titleSuffix: Azure Machine Learning service
description: 自動化された機械学習を使用して ML モデルを生成する方法を学習します。  Azure Machine Learning では、データの事前処理、アルゴリズムの選択、ハイパーパラメーターの選択をユーザーに代わって自動的に実行できます。 その後、最終的なモデルを Azure Machine Learning サービスで配置できます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6bbc2d44ab128aec032ead29bf247cd834f932b6
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315205"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>チュートリアル: 自動化された機械学習を使用して回帰モデルを構築する

このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 2 です**。 前回のチュートリアルでは、[回帰モデル用の NYC タクシー データを準備](tutorial-data-prep.md)しました。

Azure Machine Learning サービスを使ってモデルの構築を開始する準備は整っています。 今回のチュートリアルでは、準備したデータを使って、タクシー運賃の価格を予測する回帰モデルを自動生成します。 サービスの自動 ML 機能を使って、機械学習の目標と制約を定義し、自動化された機械学習プロセスを起動して、アルゴリズムの選択とハイパーパラメーターの調整が行えるようにします。 自動 ML テクニックでは、アルゴリズムとハイパーパラメーターの多数の組み合わせをイテレーションして、条件上最適なモデルを探します。

![フロー図](./media/tutorial-auto-train-models/flow2.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Python 環境を設定して SDK パッケージをインポートする
> * Azure Machine Learning サービス ワークスペースを構成する
> * 回帰モデルを自動トレーニングする
> * カスタム パラメーターを使ってローカルでモデルを実行する
> * 結果を検索する
> * 最高のモデルを登録する

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning service](http://aka.ms/AMLFree) を今日からお試しいただけます。

>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 1.0.0 を使用してテストされました

## <a name="prerequisites"></a>前提条件

> * [データ準備のチュートリアルを実行する](tutorial-data-prep.md)。
> * Azure Notebooks、ローカルの Python 環境、または Data Science Virtual Machine など、自動化された機械学習が構成されている環境。 自動化された機械学習を[設定する](samples-notebooks.md)。

## <a name="get-the-notebook"></a>ノートブックを入手する

便利なように、このチュートリアルは[ Jupyter notebook ](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb)として提供されています。 `regression-part2-automated-ml.ipynb`Azure Notebook またはご自身の Jupyter notebook サーバー内のいずれかのノートを実行します。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>パッケージをインポートする
このチュートリアルで必要な Python パッケージをインポートします。


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging
import os
```

## <a name="configure-workspace"></a>ワークスペースの構成

既存のワークスペースからワークスペース オブジェクトを作成します。 `Workspace` は、Azure サブスクリプションとリソース情報を受け取るクラスであり、モデル実行を監視して追跡するためのクラウド リソースを作成するクラスです。 `Workspace.from_config()` は、**aml_config/config.json** ファイルを読み取り、詳細情報を `ws` という名前のオブジェクトに読み込みます。  `ws` は、このチュートリアルの残りのコード全体で使用されています。

ワークスペース オブジェクトを作成したら、実験用に名前を指定し、ワークスペースを使用してローカル ディレクトリを作成および登録します。 すべての実行履歴が、指定された実験と [Azure portal](https://portal.azure.com) に記録されます。


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>データを調査する

前のチュートリアルで作成したデータ フロー オブジェクトを使用します。 データ フローを開いて実行し、結果を確認します。


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Missing Count</th>
      <th>Not Missing Count</th>
      <th>Percent missing</th>
      <th>Error Count</th>
      <th>Empty count</th>
      <th>0.1% Quantile</th>
      <th>1% Quantile</th>
      <th>5% Quantile</th>
      <th>25% Quantile</th>
      <th>50% Quantile</th>
      <th>75% Quantile</th>
      <th>95% Quantile</th>
      <th>99% Quantile</th>
      <th>99.9% Quantile</th>
      <th>平均</th>
      <th>標準偏差</th>
      <th>variance</th>
      <th>傾斜</th>
      <th>尖度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>金曜日</td>
      <td>水曜日</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.90047</td>
      <td>2.69355</td>
      <td>9.72889</td>
      <td>16</td>
      <td>19.3713</td>
      <td>22.6974</td>
      <td>23</td>
      <td>23</td>
      <td>14.2731</td>
      <td>6.59242</td>
      <td>43.46</td>
      <td>-0.693723</td>
      <td>-0.570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>4.99701</td>
      <td>4.95833</td>
      <td>14.1528</td>
      <td>29.3832</td>
      <td>44.6825</td>
      <td>56.4444</td>
      <td>58.9909</td>
      <td>59</td>
      <td>29.427</td>
      <td>17.4333</td>
      <td>303.921</td>
      <td>0.0120999</td>
      <td>-1.20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.28131</td>
      <td>5</td>
      <td>14.7832</td>
      <td>29.9293</td>
      <td>44.725</td>
      <td>56.7573</td>
      <td>59</td>
      <td>59</td>
      <td>29.7443</td>
      <td>17.3595</td>
      <td>301.351</td>
      <td>-0.0252399</td>
      <td>-1.19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>金曜日</td>
      <td>水曜日</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>2.57153</td>
      <td>2</td>
      <td>9.58795</td>
      <td>15.9994</td>
      <td>19.6184</td>
      <td>22.8317</td>
      <td>23</td>
      <td>23</td>
      <td>14.2105</td>
      <td>6.71093</td>
      <td>45.0365</td>
      <td>-0.687292</td>
      <td>-0.61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.44383</td>
      <td>4.84694</td>
      <td>14.1036</td>
      <td>28.8365</td>
      <td>44.3102</td>
      <td>56.6892</td>
      <td>59</td>
      <td>59</td>
      <td>29.2907</td>
      <td>17.4108</td>
      <td>303.136</td>
      <td>0.0222514</td>
      <td>-1.2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0</td>
      <td>5.07801</td>
      <td>5</td>
      <td>14.5751</td>
      <td>29.5972</td>
      <td>45.4649</td>
      <td>56.2729</td>
      <td>59</td>
      <td>59</td>
      <td>29.772</td>
      <td>17.5337</td>
      <td>307.429</td>
      <td>-0.0212575</td>
      <td>-1.226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>Y</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0781</td>
      <td>-73.7459</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0578</td>
      <td>-73.9639</td>
      <td>-73.9656</td>
      <td>-73.9508</td>
      <td>-73.9255</td>
      <td>-73.8529</td>
      <td>-73.8302</td>
      <td>-73.8238</td>
      <td>-73.7697</td>
      <td>-73.9123</td>
      <td>0.0503757</td>
      <td>0.00253771</td>
      <td>0.352172</td>
      <td>-0.923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5755</td>
      <td>40.8799</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632</td>
      <td>40.7117</td>
      <td>40.7115</td>
      <td>40.7213</td>
      <td>40.7565</td>
      <td>40.8058</td>
      <td>40.8478</td>
      <td>40.8676</td>
      <td>40.8778</td>
      <td>40.7649</td>
      <td>0.0494674</td>
      <td>0.00244702</td>
      <td>0.205972</td>
      <td>-0.777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.0857</td>
      <td>-73.7209</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.0775</td>
      <td>-73.9875</td>
      <td>-73.9882</td>
      <td>-73.9638</td>
      <td>-73.935</td>
      <td>-73.8755</td>
      <td>-73.8125</td>
      <td>-73.7759</td>
      <td>-73.7327</td>
      <td>-73.9202</td>
      <td>0.0584627</td>
      <td>0.00341789</td>
      <td>0.623622</td>
      <td>-0.262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.5835</td>
      <td>40.8797</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.5973</td>
      <td>40.6928</td>
      <td>40.6911</td>
      <td>40.7226</td>
      <td>40.7567</td>
      <td>40.7918</td>
      <td>40.8495</td>
      <td>40.868</td>
      <td>40.8787</td>
      <td>40.7583</td>
      <td>0.0517399</td>
      <td>0.00267701</td>
      <td>0.0390404</td>
      <td>-0.203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2.39249</td>
      <td>1.83197</td>
      <td>3.3561</td>
      <td>0.763144</td>
      <td>-1.23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0.01</td>
      <td>32.34</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0108744</td>
      <td>0.743898</td>
      <td>0.738194</td>
      <td>1.243</td>
      <td>2.40168</td>
      <td>4.74478</td>
      <td>10.5136</td>
      <td>14.9011</td>
      <td>21.8035</td>
      <td>3.5447</td>
      <td>3.2943</td>
      <td>10.8524</td>
      <td>1.91556</td>
      <td>4.99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0.1</td>
      <td>88</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>6148.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.33837</td>
      <td>5.00491</td>
      <td>5</td>
      <td>6.93129</td>
      <td>10.524</td>
      <td>17.4811</td>
      <td>33.2343</td>
      <td>50.0093</td>
      <td>63.1753</td>
      <td>13.6843</td>
      <td>9.66571</td>
      <td>93.426</td>
      <td>1.78518</td>
      <td>4.13972</td>
    </tr>
  </tbody>
</table>

モデル作成の機能となるように `dflow_x` に列を追加して、実験用のデータを準備します。 予測値 cost になるように、`dflow_y` を定義します。

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>データをトレーニング セットとテスト セットに分割する

次に、`sklearn` ライブラリの `train_test_split` 関数を使用して、トレーニング セットとテスト セットにデータを分割します。 この関数は、モデル トレーニング用の x (機能) データ セットとテスト用の y (予測する値) データ セットに、データを分割します。 `test_size` パラメーターでは、テストに割り当てるデータの割合を決定します。 `random_state` パラメーターでは、トレーニングとテストの分割が常に決定論的になるように、ランダム ジェネレーターにシードを設定します。

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

これでご自分のモデルを自動的にトレーニングするのに必要なパッケージとデータが揃いました。

## <a name="automatically-train-a-model"></a>自動的にモデルをトレーニングする

自動的にモデルをトレーニングするには、次の手順を実行します。
1. 実験の実行用の設定を定義する
1. モデル調整用の実験を送信する

### <a name="define-settings-for-autogeneration-and-tuning"></a>自動生成と調整用の設定を定義する

自動生成と調整用の実験パラメーターとモデルの設定を定義します。 [設定](how-to-configure-auto-train.md)の完全な一覧を表示します。


|プロパティ| このチュートリアルの値 |説明|
|----|----|---|
|**iteration_timeout_minutes**|10|各イテレーションの分単位での時間制限|
|**iterations**|30|イテレーションの回数。 各イテレーションでは、特定のパイプラインを使用してモデルがデータをトレーニングします。|
|**primary_metric**| spearman_correlation | 最適化したいメトリック。|
|**preprocess**| True | True を指定すると、実験は入力に対して前処理を実行できます。|
|**verbosity**| logging.INFO | ログ記録のレベルを制御します。|
|**n_cross_validationss**|5|クロス検証の分割の数



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>自動回帰モデルをトレーニングする

ローカルで実験の実行を開始します。 定義済みの `automated_ml_config` オブジェクトを実験に渡し、出力を `True` に設定して実験中の進行状況を表示します。


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>結果を検索する

Jupyter ウィジェットを使用するか、実験の履歴を検証して、自動トレーニングの結果を探索します。

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>方法 1: Jupyter ウィジェットを追加して結果を表示する

Jupyter Notebook を使用している場合、この Jupyter Notebook ウィジェットを使ってすべての結果のグラフおよびテーブルを参照します。


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter ウィジェット実行の詳細](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter ウィジェットのプロット](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>方法 2: Python ですべての実行イテレーションを取得して調査する

別の方法として、各実験の履歴を取得して、各イテレーション実行の個々のメトリックを探索できます。

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0.811037</td>
      <td>0.880553</td>
      <td>0.398582</td>
      <td>0.776040</td>
      <td>0.663869</td>
      <td>0.875911</td>
      <td>0.115632</td>
      <td>0.586905</td>
      <td>0.851911</td>
      <td>0.793964</td>
      <td>...</td>
      <td>0.850023</td>
      <td>0.883603</td>
      <td>0.883704</td>
      <td>0.880797</td>
      <td>0.881564</td>
      <td>0.883708</td>
      <td>0.881826</td>
      <td>0.585377</td>
      <td>0.883123</td>
      <td>0.886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2.189444</td>
      <td>1.500412</td>
      <td>5.480531</td>
      <td>2.626316</td>
      <td>2.973026</td>
      <td>1.550199</td>
      <td>6.383868</td>
      <td>4.414241</td>
      <td>1.743328</td>
      <td>2.294601</td>
      <td>...</td>
      <td>1.797402</td>
      <td>1.415815</td>
      <td>1.418167</td>
      <td>1.578617</td>
      <td>1.559427</td>
      <td>1.413042</td>
      <td>1.551698</td>
      <td>4.069196</td>
      <td>1.505795</td>
      <td>1.430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1.438417</td>
      <td>0.850899</td>
      <td>4.579662</td>
      <td>1.765210</td>
      <td>1.594600</td>
      <td>0.869883</td>
      <td>4.266450</td>
      <td>3.627355</td>
      <td>0.954992</td>
      <td>1.361014</td>
      <td>...</td>
      <td>0.973634</td>
      <td>0.774814</td>
      <td>0.797269</td>
      <td>1.147234</td>
      <td>1.116424</td>
      <td>0.783958</td>
      <td>1.098464</td>
      <td>2.709027</td>
      <td>1.003728</td>
      <td>0.851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error</th>
      <td>0.024908</td>
      <td>0.017070</td>
      <td>0.062350</td>
      <td>0.029878</td>
      <td>0.033823</td>
      <td>0.017636</td>
      <td>0.072626</td>
      <td>0.050219</td>
      <td>0.019833</td>
      <td>0.026105</td>
      <td>...</td>
      <td>0.020448</td>
      <td>0.016107</td>
      <td>0.016134</td>
      <td>0.017959</td>
      <td>0.017741</td>
      <td>0.016076</td>
      <td>0.017653</td>
      <td>0.046293</td>
      <td>0.017131</td>
      <td>0.016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0.016364</td>
      <td>0.009680</td>
      <td>0.052101</td>
      <td>0.020082</td>
      <td>0.018141</td>
      <td>0.009896</td>
      <td>0.048538</td>
      <td>0.041267</td>
      <td>0.010865</td>
      <td>0.015484</td>
      <td>...</td>
      <td>0.011077</td>
      <td>0.008815</td>
      <td>0.009070</td>
      <td>0.013052</td>
      <td>0.012701</td>
      <td>0.008919</td>
      <td>0.012497</td>
      <td>0.030819</td>
      <td>0.011419</td>
      <td>0.009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error</th>
      <td>0.047968</td>
      <td>0.037882</td>
      <td>0.085572</td>
      <td>0.052282</td>
      <td>0.065809</td>
      <td>0.038664</td>
      <td>0.109401</td>
      <td>0.071104</td>
      <td>0.042294</td>
      <td>0.049967</td>
      <td>...</td>
      <td>0.042565</td>
      <td>0.037685</td>
      <td>0.037557</td>
      <td>0.037643</td>
      <td>0.037513</td>
      <td>0.037560</td>
      <td>0.037465</td>
      <td>0.072077</td>
      <td>0.037249</td>
      <td>0.036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error</th>
      <td>0.055353</td>
      <td>0.045000</td>
      <td>0.110219</td>
      <td>0.065633</td>
      <td>0.063589</td>
      <td>0.044412</td>
      <td>0.123433</td>
      <td>0.092312</td>
      <td>0.046130</td>
      <td>0.055243</td>
      <td>...</td>
      <td>0.046540</td>
      <td>0.041804</td>
      <td>0.041771</td>
      <td>0.045175</td>
      <td>0.044628</td>
      <td>0.041617</td>
      <td>0.044405</td>
      <td>0.079651</td>
      <td>0.042799</td>
      <td>0.041530</td>
    </tr>
    <tr>
      <th>r2_score</th>
      <td>0.810900</td>
      <td>0.880328</td>
      <td>0.398076</td>
      <td>0.775957</td>
      <td>0.642812</td>
      <td>0.875719</td>
      <td>0.021603</td>
      <td>0.586514</td>
      <td>0.851767</td>
      <td>0.793671</td>
      <td>...</td>
      <td>0.849809</td>
      <td>0.880142</td>
      <td>0.880952</td>
      <td>0.880586</td>
      <td>0.881347</td>
      <td>0.880887</td>
      <td>0.881613</td>
      <td>0.548121</td>
      <td>0.882883</td>
      <td>0.886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4.216362</td>
      <td>3.329810</td>
      <td>7.521765</td>
      <td>4.595604</td>
      <td>5.784601</td>
      <td>3.398540</td>
      <td>9.616354</td>
      <td>6.250011</td>
      <td>3.717661</td>
      <td>4.392072</td>
      <td>...</td>
      <td>3.741447</td>
      <td>3.312533</td>
      <td>3.301242</td>
      <td>3.308795</td>
      <td>3.297389</td>
      <td>3.301485</td>
      <td>3.293182</td>
      <td>6.335581</td>
      <td>3.274209</td>
      <td>3.227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0.243184</td>
      <td>0.197702</td>
      <td>0.484227</td>
      <td>0.288349</td>
      <td>0.279367</td>
      <td>0.195116</td>
      <td>0.542281</td>
      <td>0.405559</td>
      <td>0.202666</td>
      <td>0.242702</td>
      <td>...</td>
      <td>0.204464</td>
      <td>0.183658</td>
      <td>0.183514</td>
      <td>0.198468</td>
      <td>0.196067</td>
      <td>0.182836</td>
      <td>0.195087</td>
      <td>0.349935</td>
      <td>0.188031</td>
      <td>0.182455</td>
    </tr>
    <tr>
      <th>spearman_correlation</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.857965</td>
      <td>0.914703</td>
      <td>0.939846</td>
      <td>0.956159</td>
      <td>0.828187</td>
      <td>0.942069</td>
      <td>0.952581</td>
      <td>0.935477</td>
      <td>...</td>
      <td>0.951287</td>
      <td>0.960335</td>
      <td>0.960195</td>
      <td>0.960279</td>
      <td>0.960288</td>
      <td>0.960323</td>
      <td>0.960161</td>
      <td>0.941254</td>
      <td>0.960293</td>
      <td>0.962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0.944743</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.953618</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>0.956159</td>
      <td>...</td>
      <td>0.960303</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.960335</td>
      <td>0.962158</td>
    </tr>
  </tbody>
</table>
<p>12 行 × 30 列</p>
</div>

## <a name="retrieve-the-best-model"></a>最高のモデルを取得する

イテレーションから最適なパイプラインを選択します。 `automl_classifier` 上の `get_output` メソッドは、最適な実行と、最後の fit の呼び出しで適合したモデルを返します。 `get_output` 上にはオーバーロードがあり、これによって、ログ記録された任意のメトリックや特定のイテレーションに対する最適な実行と適合モデルを取得できます。

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>モデルを登録する

お使いの Azure Machine Learning サービス ワークスペースにモデルを登録します。


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>最高のモデルの正確性をテストする

最高のモデルを使用して、テスト データ セット上で予測を実行します。 関数 `predict` は最高のモデルを使用して、`x_test` データ セットから y (交通費) の値を予測します。 `y_predict` から最初の 10 個の予測コスト値をプリントします。

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

散布図を作成して、実績コスト値と比較した予測コスト値を視覚化します。 以下のコードは、特徴 (`distance`) を X 軸に、交通費 (`cost`) を Y 軸として使用しています。 個々の乗車距離値における予測コストの差異を比較するために、最初の 100 個の予測コスト値と実績コスト値を別個の系列として作成します。 このプロットを観察すると、距離とコストの関係がほぼ線形であり、同じ乗車距離であれば、ほとんどの場合、予測コスト値が実績コスト値にきわめて近いことがわかります。

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![予測散布図](./media/tutorial-auto-train-models/automl-scatter-plot.png)

結果の `root mean squared error` を計算します。 `y_test` データフレームを使用して、予測値と比較するためにリストに変換します。 関数 `mean_squared_error` は 2 つの配列の値を取得して、その 2 つの値間の平均二乗誤差を計算します。 結果の平方根を取ることで、y 変数 (コスト) と同じ単位で誤差が得られ、予測と実績値との間にどの程度の開きがあるかを大まかに把握できます。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

次のコードを実行し、完全な `y_actual` と `y_predict` データ セットを使用して MAPE (平均絶対誤差率) を計算します。 このメトリックは、予測される各値と実際の値の間の絶対誤差を計算し、すべての差分を合計して、その合計を実際の値の合計に対する割合として表します。

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

自動化された機械学習に関するこのチュートリアルでは、次のことを学習しました。

> [!div class="checklist"]
> * 実験用のワークスペースと準備されたデータを構成しました
> * カスタム パラメーターを使って、自動化された回帰モデルをローカルで使用してトレーニングしました
> * トレーニング結果を探索して確認しました
> * 最高のモデルを登録しました

Azure Machine Learning を使って[モデルをデプロイ](tutorial-deploy-models-with-aml.md)してください。
