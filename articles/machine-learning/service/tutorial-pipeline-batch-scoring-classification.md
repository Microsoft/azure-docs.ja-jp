---
title: チュートリアル:バッチ スコアリング用の ML パイプライン
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の画像分類モデルでバッチ スコアリングを実行するための機械学習パイプラインを作成する 機械学習パイプラインは、速度、移植性、再利用によってワークフローを最適化します。これにより、ユーザーはインフラストラクチャや自動化にではなく、自分の専門である機械学習に専念することができます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/05/2019
ms.openlocfilehash: 3fe25f0f8297a7b743ed5f522e8a35deb165a039
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695612"
---
# <a name="build--use-an-azure-machine-learning-pipeline-for-batch-scoring"></a>バッチ スコアリング用の Azure Machine Learning パイプラインを作成して使用する

このチュートリアルでは、Azure Machine Learning のパイプラインを使用してバッチ スコアリング ジョブを実行します。 この例では、事前トレーニング済みの [Inception-V3](https://arxiv.org/abs/1512.00567) 畳み込みニューラル ネットワーク TensorFlow モデルを使用して、ラベル付けされていない画像を分類します。 パイプラインを作成して発行したら、REST エンドポイントを構成します。それを使用して、任意のプラットフォームで任意の HTTP ライブラリからパイプラインをトリガーできます。

機械学習パイプラインは、速度、移植性、再利用によってワークフローを最適化します。これにより、ユーザーはインフラストラクチャや自動化にではなく、自分の専門である機械学習に専念することができます。 [機械学習パイプラインについての詳細情報をご覧ください](concept-ml-pipelines.md)。

このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> * ワークスペースを構成してサンプル データをダウンロードする
> * データを取り込んで出力するデータ オブジェクトを作成する
> * モデルをダウンロードして準備し、ワークスペースに登録する
> * コンピューティング先をプロビジョニングし、スコアリング スクリプトを作成する
> * パイプラインを作成、実行、発行する
> * パイプラインの REST エンドポイントを有効にする

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning ワークスペースまたはノートブック仮想マシンがまだない場合は、[セットアップのチュートリアルのパート 1](tutorial-1st-experiment-sdk-setup.md) を済ませておいてください。
* セットアップのチュートリアルが完了したら、同じノートブック サーバーを使用して、*tutorials/tutorial-pipeline-batch-scoring-classification.ipynb* ノートブックを開きます。

独自の[ローカル環境](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials)でセットアップのチュートリアルを実行したい場合は、[GitHub](how-to-configure-environment.md#local) 上のチュートリアルを利用できます。 `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-pipeline-steps pandas requests` を実行して必要なパッケージを取得してください。

## <a name="configure-workspace-and-create-a-datastore"></a>ワークスペースを構成してデータストアを作成する

既存の Azure Machine Learning ワークスペースからワークスペース オブジェクトを作成します。

- [ワークスペース](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)は、Azure サブスクリプションとリソースの情報を受け取るクラスです。 また、モデルの実行を監視して追跡する際に使用できるクラウド リソースもワークスペースによって作成されます。 
- `Workspace.from_config()` では `config.json` ファイルを読み取って、`ws` という名前のオブジェクトに認証情報を読み込みます。 `ws` オブジェクトは、このチュートリアルのコード全体で使用されています。

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

### <a name="create-a-datastore-for-sample-images"></a>サンプル画像のデータストアを作成する

`pipelinedata` アカウントのパブリック BLOB コンテナー `sampledata` から、ImageNet のパブリック評価データ サンプルを入手します。 そのデータに `images_datastore` という名前のワークスペースからアクセスできるよう、`register_azure_blob_container()` を呼び出します。 そのうえで、ワークスペースの既定のデータストアを出力データストアとして設定します。 この出力データストアを使用して、パイプラインにおける出力のスコアリングを実行します。

```python
from azureml.core.datastore import Datastore

batchscore_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="images_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata", 
                      overwrite=True)

def_data_store = ws.get_default_datastore()
```

## <a name="create-data-objects"></a>データ オブジェクトを作成する

パイプラインを作成するときは、`DataReference` オブジェクトで、ワークスペースのデータストアからデータを読み取ります。 パイプライン ステップ間では、`PipelineData` オブジェクトによって中間データが転送されます。

> [!Important]
> このチュートリアルのバッチ スコアリングの例で使用するパイプライン ステップは 1 つだけです。 複数のステップを使用したユースケースでは、標準的なフローにこれらのステップが含まれます。
>
> 1. `DataReference` オブジェクトを "*入力*" に使用して生データを取り込み、なんらかの変換を実行した後、`PipelineData` オブジェクトを "*出力*" します。
>
> 2. 前のステップの `PipelineData` "*出力オブジェクト*" を "*入力オブジェクト*" として使用します。 後続のステップに対して処理を繰り返します。

このシナリオでは、入力画像と分類ラベル (y 検定値) の両方について、データストアのディレクトリに対応する `DataReference` オブジェクトを作成します。 バッチ スコアリングの出力データ用の `PipelineData` オブジェクトも作成します。

```python
from azureml.data.data_reference import DataReference
from azureml.pipeline.core import PipelineData

input_images = DataReference(datastore=batchscore_blob, 
                             data_reference_name="input_images",
                             path_on_datastore="batchscoring/images",
                             mode="download"
                            )

label_dir = DataReference(datastore=batchscore_blob, 
                          data_reference_name="input_labels",
                          path_on_datastore="batchscoring/labels",
                          mode="download"                          
                         )

output_dir = PipelineData(name="scores", 
                          datastore=def_data_store, 
                          output_path_on_compute="batchscoring/results")
```

## <a name="download-and-register-the-model"></a>モデルをダウンロードして登録する

パイプラインでのバッチ スコアリングに使用する事前トレーニング済みの TensorFlow モデルをダウンロードします。 まず、モデルの格納先となるローカル ディレクトリを作成します。 その後、モデルをダウンロードして抽出します。

```python
import os
import tarfile
import urllib.request

if not os.path.isdir("models"):
    os.mkdir("models")
    
response = urllib.request.urlretrieve("http://download.tensorflow.org/models/inception_v3_2016_08_28.tar.gz", "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall("models")
```

次に、モデルをワークスペースに登録します。パイプライン処理でモデルを簡単に取得できるようになります。 `register()` 静的関数で重要となるのは `model_name` パラメーターです。SDK 全体でモデルを特定する際に使用します。

```python
from azureml.core.model import Model
 
model = Model.register(model_path="models/inception_v3.ckpt",
                       model_name="inception",
                       tags={"pretrained": "inception"},
                       description="Imagenet trained tensorflow inception",
                       workspace=ws)
```

## <a name="create-and-attach-the-remote-compute-target"></a>リモートのコンピューティング先を作成してアタッチする

機械学習パイプラインは、ローカルで実行することができないので、クラウド リソース ("*リモート コンピューティング先*") で実行します。 リモート コンピューティング先は、実験や機械学習のワークフローを実行する再利用可能な仮想コンピューティング環境です。 

次のコードを実行して GPU 対応のコンピューティング先 [`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) を作成し、自分のワークスペースにアタッチします。 コンピューティング先の詳細については、[概念に関する記事](https://docs.microsoft.com/azure/machine-learning/service/concept-compute-target)を参照してください。


```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.exceptions import ComputeTargetException
compute_name = "gpu-cluster"

# checks to see if compute target already exists in workspace, else create it
try:
    compute_target = ComputeTarget(workspace=ws, name=compute_name)
except ComputeTargetException:
    config = AmlCompute.provisioning_configuration(vm_size="STANDARD_NC6",
                                                   vm_priority="lowpriority", 
                                                   min_nodes=0, 
                                                   max_nodes=1)

    compute_target = ComputeTarget.create(workspace=ws, name=compute_name, provisioning_configuration=config)
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

## <a name="write-a-scoring-script"></a>スコアリング スクリプトを作成する

スコアリングを実行するには、`batch_scoring.py` というバッチ スコアリング スクリプトを作成して現在のディレクトリに書き込みます。 このスクリプトは、入力画像を受け取って分類モデルを適用し、結果ファイルに予測を出力します。

`batch_scoring.py` スクリプトが受け取るパラメーターは次のとおりです。後でこのチュートリアルの中で作成するパイプライン ステップから渡すことになります。

- `--model_name`:使用するモデルの名前。
- `--label_dir`:`labels.txt` ファイルが保持されるディレクトリ。
- `--dataset_path`:入力画像が格納されるディレクトリ。
- `--output_dir`:スクリプトによってデータに対してモデルが実行された後、`results-label.txt` ファイルが出力されるディレクトリ。
- `--batch_size`:モデルの実行に使用されるバッチ サイズ。

パイプライン ステップにパラメーターを渡すために、このパイプラインのインフラストラクチャでは `ArgumentParser` クラスを使用しています。 たとえば、以下のコードでは、最初の引数 `--model_name` に `model_name` というプロパティ識別子が割り当てられています。 `main()` 関数の中では、`Model.get_model_path(args.model_name)` を使用して、このプロパティにアクセスしています。


```python
%%writefile batch_scoring.py

import os
import argparse
import datetime
import time
import tensorflow as tf
from math import ceil
import numpy as np
import shutil
from tensorflow.contrib.slim.python.slim.nets import inception_v3
from azureml.core.model import Model

slim = tf.contrib.slim

parser = argparse.ArgumentParser(description="Start a tensorflow model serving")
parser.add_argument('--model_name', dest="model_name", required=True)
parser.add_argument('--label_dir', dest="label_dir", required=True)
parser.add_argument('--dataset_path', dest="dataset_path", required=True)
parser.add_argument('--output_dir', dest="output_dir", required=True)
parser.add_argument('--batch_size', dest="batch_size", type=int, required=True)

args = parser.parse_args()

image_size = 299
num_channel = 3

# create output directory if it does not exist
os.makedirs(args.output_dir, exist_ok=True)


def get_class_label_dict(label_file):
    label = []
    proto_as_ascii_lines = tf.gfile.GFile(label_file).readlines()
    for l in proto_as_ascii_lines:
        label.append(l.rstrip())
    return label


class DataIterator:
    def __init__(self, data_dir):
        self.file_paths = []
        image_list = os.listdir(data_dir)
        self.file_paths = [data_dir + '/' + file_name.rstrip() for file_name in image_list]
        self.labels = [1 for file_name in self.file_paths]

    @property
    def size(self):
        return len(self.labels)

    def input_pipeline(self, batch_size):
        images_tensor = tf.convert_to_tensor(self.file_paths, dtype=tf.string)
        labels_tensor = tf.convert_to_tensor(self.labels, dtype=tf.int64)
        input_queue = tf.train.slice_input_producer([images_tensor, labels_tensor], shuffle=False)
        labels = input_queue[1]
        images_content = tf.read_file(input_queue[0])

        image_reader = tf.image.decode_jpeg(images_content, channels=num_channel, name="jpeg_reader")
        float_caster = tf.cast(image_reader, tf.float32)
        new_size = tf.constant([image_size, image_size], dtype=tf.int32)
        images = tf.image.resize_images(float_caster, new_size)
        images = tf.divide(tf.subtract(images, [0]), [255])

        image_batch, label_batch = tf.train.batch([images, labels], batch_size=batch_size, capacity=5 * batch_size)
        return image_batch


def main(_):
    label_file_name = os.path.join(args.label_dir, "labels.txt")
    label_dict = get_class_label_dict(label_file_name)
    classes_num = len(label_dict)
    test_feeder = DataIterator(data_dir=args.dataset_path)
    total_size = len(test_feeder.labels)
    count = 0

    # get model from model registry
    model_path = Model.get_model_path(args.model_name)

    with tf.Session() as sess:
        test_images = test_feeder.input_pipeline(batch_size=args.batch_size)
        with slim.arg_scope(inception_v3.inception_v3_arg_scope()):
            input_images = tf.placeholder(tf.float32, [args.batch_size, image_size, image_size, num_channel])
            logits, _ = inception_v3.inception_v3(input_images,
                                                  num_classes=classes_num,
                                                  is_training=False)
            probabilities = tf.argmax(logits, 1)

        sess.run(tf.global_variables_initializer())
        sess.run(tf.local_variables_initializer())
        coord = tf.train.Coordinator()
        threads = tf.train.start_queue_runners(sess=sess, coord=coord)
        saver = tf.train.Saver()
        saver.restore(sess, model_path)
        out_filename = os.path.join(args.output_dir, "result-labels.txt")
        with open(out_filename, "w") as result_file:
            i = 0
            while count < total_size and not coord.should_stop():
                test_images_batch = sess.run(test_images)
                file_names_batch = test_feeder.file_paths[i * args.batch_size:
                                                          min(test_feeder.size, (i + 1) * args.batch_size)]
                results = sess.run(probabilities, feed_dict={input_images: test_images_batch})
                new_add = min(args.batch_size, total_size - count)
                count += new_add
                i += 1
                for j in range(new_add):
                    result_file.write(os.path.basename(file_names_batch[j]) + ": " + label_dict[results[j]] + "\n")
                result_file.flush()
            coord.request_stop()
            coord.join(threads)

        shutil.copy(out_filename, "./outputs/")

if __name__ == "__main__":
    tf.app.run()

```

> [!TIP]
> このチュートリアルのパイプラインに含まれるステップは 1 つだけで、出力はファイルに書き込まれます。 複数のステップから成るパイプラインでは、後続のステップに入力する出力データの書き込み先ディレクトリを定義する場合にも `ArgumentParser` を使用します。 `ArgumentParser` の設計パターンを使用して、複数のパイプライン ステップ間でデータを受け渡しする例については、[ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb)を参照してください。

## <a name="build-and-run-the-pipeline"></a>パイプラインを作成して実行する

パイプラインを実行する前に、Python 環境を定義するオブジェクトを作成し、`batch_scoring.py` スクリプトに必要な依存関係を作成します。 主な依存関係として Tensorflow が必要となるほか、SDK のバックグラウンド処理用に `azureml-defaults` もインストールします。 それらの依存関係を使用して `RunConfiguration` オブジェクトを作成します。 さらに Docker および Docker-GPU のサポートを指定します。

```python
from azureml.core.runconfig import DEFAULT_GPU_IMAGE
from azureml.core.runconfig import CondaDependencies, RunConfiguration

cd = CondaDependencies.create(pip_packages=["tensorflow-gpu==1.13.1", "azureml-defaults"])

amlcompute_run_config = RunConfiguration(conda_dependencies=cd)
amlcompute_run_config.environment.docker.enabled = True
amlcompute_run_config.environment.docker.base_image = DEFAULT_GPU_IMAGE
amlcompute_run_config.environment.spark.precache_packages = False
```

### <a name="parameterize-the-pipeline"></a>パイプラインをパラメーター化する

パイプラインでバッチ サイズを制御するためのカスタム パラメーターを定義します。 パイプラインを発行し、REST エンドポイントを介して公開すると、構成されたパラメーターもすべて公開されます。 HTTP 要求を介してパイプラインを再実行する際に JSON ペイロードでカスタム パラメーターを指定することができます。

この動作を有効にするために `PipelineParameter` オブジェクトを作成し、名前と既定値を定義します。

```python
from azureml.pipeline.core.graph import PipelineParameter
batch_size_param = PipelineParameter(name="param_batch_size", default_value=20)
```

### <a name="create-the-pipeline-step"></a>パイプラインのステップを作成する

パイプライン ステップとは、パイプラインを実行するために必要なあらゆる要素をカプセル化するオブジェクトであり、以下のようなものがあります。

* 環境と依存関係の設定
* パイプラインを実行するためのコンピューティング リソース
* 入力データと出力データ、カスタム パラメーター
* ステップ中に実行するスクリプトまたは SDK ロジックへの参照

親クラスである [`PipelineStep`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep?view=azure-ml-py) は、複数のクラスによって継承されています。 クラスを選択し、特定のフレームワークまたはスタックを使用してステップを作成できます。 この例では [`PythonScriptStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) クラスを使用し、カスタム Python スクリプトを使ってステップのロジックを定義します。 スクリプトの引数がステップへの入力またはステップからの出力である場合、引数を "*2 か所*" で定義する必要があります。1 つは `arguments` 配列、"*もう 1 つ*" は `input` または `output` パラメーターです。 

複数のステップが存在するシナリオでは、`outputs` 配列のオブジェクト参照が後続のパイプライン ステップの "*入力*" として使用できるようになります。

```python
from azureml.pipeline.steps import PythonScriptStep

batch_score_step = PythonScriptStep(
    name="batch_scoring",
    script_name="batch_scoring.py",
    arguments=["--dataset_path", input_images, 
               "--model_name", "inception",
               "--label_dir", label_dir, 
               "--output_dir", output_dir, 
               "--batch_size", batch_size_param],
    compute_target=compute_target,
    inputs=[input_images, label_dir],
    outputs=[output_dir],
    runconfig=amlcompute_run_config
)
```

さまざまなタイプのステップに使用できるクラスを網羅した一覧については、「[ステップ パッケージ](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps?view=azure-ml-py)」を参照してください。

### <a name="run-the-pipeline"></a>パイプラインを実行する

次に、パイプラインを実行します。 まず、自分のワークスペースの参照および作成したパイプライン ステップを使用して `Pipeline` オブジェクトを作成します。 `steps` パラメーターは、ステップの配列です。 この場合、バッチ スコアリング用のステップは 1 つだけです。 複数のステップが含まれたパイプラインを作成する場合は、この配列内にステップを順に配置します。

次に、`Experiment.submit()` 関数を使用して、実行するパイプラインを送信します。 カスタム パラメーター `param_batch_size` も指定します。 パイプラインの作成プロセス中は、`wait_for_completion` 関数からログが出力されます。 ログを使用して、最新の進行状況を確認することができます。

> [!IMPORTANT]
> パイプラインの初回実行には約 "*15 分*" かかります。 依存関係をすべてダウンロードする必要があるほか、Docker イメージの作成と Python 環境のプロビジョニングおよび作成が行われます。 パイプラインを再度実行する際は、それらのリソースが作成されるのではなく再利用されるので、時間が大幅に短縮されます。 ただし、パイプラインの総実行時間は、実際のスクリプトのワークロードと、各パイプライン ステップで実行される処理によって異なります。

```python
from azureml.core import Experiment
from azureml.pipeline.core import Pipeline

pipeline = Pipeline(workspace=ws, steps=[batch_score_step])
pipeline_run = Experiment(ws, 'batch_scoring').submit(pipeline, pipeline_parameters={"param_batch_size": 20})
pipeline_run.wait_for_completion(show_output=True)
```

### <a name="download-and-review-output"></a>出力をダウンロードして確認する

次のコードを実行して、`batch_scoring.py` スクリプトから生成された出力ファイルをダウンロードします。 その後、スコアリングの結果を調べます。

```python
import pandas as pd

step_run = list(pipeline_run.get_children())[0]
step_run.download_file("./outputs/result-labels.txt")

df = pd.read_csv("result-labels.txt", delimiter=":", header=None)
df.columns = ["Filename", "Prediction"]
df.head(10)
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
      <th>ファイル名</th>
      <th>予測</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>ILSVRC2012_val_00000102.JPEG</td>
      <td>ローデシアン リッジバック</td>
    </tr>
    <tr>
      <td>1</td>
      <td>ILSVRC2012_val_00000103.JPEG</td>
      <td>三脚</td>
    </tr>
    <tr>
      <td>2</td>
      <td>ILSVRC2012_val_00000104.JPEG</td>
      <td>タイプライター キーボード</td>
    </tr>
    <tr>
      <td>3</td>
      <td>ILSVRC2012_val_00000105.JPEG</td>
      <td>シルキー テリア</td>
    </tr>
    <tr>
      <td>4</td>
      <td>ILSVRC2012_val_00000106.JPEG</td>
      <td>ウィンザー ノット</td>
    </tr>
    <tr>
      <td>5</td>
      <td>ILSVRC2012_val_00000107.JPEG</td>
      <td>ザトウムシ</td>
    </tr>
    <tr>
      <td>6</td>
      <td>ILSVRC2012_val_00000108.JPEG</td>
      <td>バイオリン</td>
    </tr>
    <tr>
      <td>7</td>
      <td>ILSVRC2012_val_00000109.JPEG</td>
      <td>拡声器</td>
    </tr>
    <tr>
      <td>8</td>
      <td>ILSVRC2012_val_00000110.JPEG</td>
      <td>エプロン</td>
    </tr>
    <tr>
      <td>9</td>
      <td>ILSVRC2012_val_00000111.JPEG</td>
      <td>アメリカン ロブスター</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="publish-and-run-from-a-rest-endpoint"></a>発行して REST エンドポイントから実行する

次のコードを実行してパイプラインをワークスペースに発行します。 Azure portal のワークスペースでは、実行の履歴や時間など、パイプラインのメタデータを確認できます。 ポータルから手動でパイプラインを実行することもできます。

パイプラインを発行すると、任意のプラットフォーム上の任意の HTTP ライブラリから REST エンドポイントを使用してパイプラインを実行できるようになります。

```python
published_pipeline = pipeline_run.publish_pipeline(
    name="Inception_v3_scoring", description="Batch scoring using Inception v3 model", version="1.0")

published_pipeline
```

REST エンドポイントからパイプラインを実行するには、OAuth2 ベアラータイプの認証ヘッダーが必要です。 以下の例では説明のために対話型認証を使用していますが、自動化された認証 (ヘッドレス認証) を必要とする運用環境のシナリオではほとんどの場合、[このノートブックの説明](https://aka.ms/pl-restep-auth)にあるようにサービス プリンシパル認証を使用します。

サービス プリンシパル認証では、*Azure Active Directory* に "*アプリの登録*" を作成する必要があります。 まず、クライアント シークレットを生成したうえで、ご自分の機械学習ワークスペースへの "*ロール アクセス*" をサービス プリンシパルに付与します。 認証フローは、[`ServicePrincipalAuthentication`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication?view=azure-ml-py) クラスを使用して管理します。 

`InteractiveLoginAuthentication` と `ServicePrincipalAuthentication` は、どちらも `AbstractAuthentication` を継承します。 どちらの場合も同じように `get_authentication_header()` 関数を使用してヘッダーをフェッチします。

```python
from azureml.core.authentication import InteractiveLoginAuthentication

interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()
```

REST URL は、発行済みのパイプライン オブジェクトの `endpoint` プロパティから取得します。 Azure portal 内のワークスペースで REST URL を確認することもできます。 

このエンドポイントに対する HTTP POST 要求を作成します。 要求には、認証ヘッダーを指定します。 実験の名前とバッチ サイズ パラメーターが含まれた JSON ペイロード オブジェクトを追加します。 このチュートリアルで前に述べたように、`param_batch_size` は、ステップの構成で `PipelineParameter` オブジェクトとして定義したため、`batch_scoring.py` スクリプトに渡されます。

実行をトリガーするための要求を作成します。 応答ディクショナリの `Id` キーにアクセスして実行 ID の値を取得するコードを追加します。

```python
import requests

rest_endpoint = published_pipeline.endpoint
response = requests.post(rest_endpoint, 
                         headers=auth_header, 
                         json={"ExperimentName": "batch_scoring",
                               "ParameterAssignments": {"param_batch_size": 50}})
run_id = response.json()["Id"]
```

この実行 ID を使用して、新しい実行の状態を監視します。 新しい実行が完了するまでには、10 分から 15 分、さらに時間がかかります。 

新しい実行は、先ほどチュートリアルの中で実行したパイプラインと同様になります。 出力全体の確認は省略してもかまいません。

```python
from azureml.pipeline.core.run import PipelineRun
from azureml.widgets import RunDetails

published_pipeline_run = PipelineRun(ws.experiments["batch_scoring"], run_id)
RunDetails(published_pipeline_run).show()
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure Machine Learning の他のチュートリアルを実行する予定の場合、このセクションは実行しないでください。

### <a name="stop-the-notebook-vm"></a>ノートブック VM を停止する

クラウド ノートブック サーバーを使用していた場合は、コストを抑えるために、使用していない VM を停止します。

1. ワークスペースで、 **[ノートブック VM]** を選択します。
1. VM の一覧で、停止したい VM を選択します。
1. **[停止]** を選択します。
1. サーバーを再び使用する準備が整ったら、 **[開始]** を選択します。

### <a name="delete-everything"></a>すべてを削除する

作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。
1. リソース グループの一覧で、自分が作成したリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。
1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

機械学習パイプラインに関するこのチュートリアルでは、以下のタスクを学習しました。

> [!div class="checklist"]
> * リモートの GPU コンピューティング リソース上で実行するための環境の依存関係を使用してパイプラインを作成する。
> * 事前トレーニング済みの TensorFlow モデルを使用してバッチ予測を実行するスコアリング スクリプトを作成する。
> * パイプラインを発行して REST エンドポイントから実行できるようにする。

Machine Learning SDK を使用してパイプラインを作成するその他の例については、[ノートブック リポジトリ](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/machine-learning-pipelines)を参照してください。
