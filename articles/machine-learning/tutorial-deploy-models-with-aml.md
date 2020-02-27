---
title: イメージの分類チュートリアル:モデルをデプロイする
titleSuffix: Azure Machine Learning
description: このチュートリアルでは、Azure Machine Learning を使用して、Python Jupyter ノートブックの scikit-learn で画像分類モデルをデプロイする方法について説明します。 このチュートリアルは、2 部構成のシリーズの 2 番目です。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: seodec18
ms.openlocfilehash: 071a8dd40d87e5df6fc5c65b789bb63b515dc60a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116508"
---
# <a name="tutorial-deploy-an-image-classification-model-in-azure-container-instances"></a>チュートリアル:Azure Container Instances に画像分類モデルをデプロイする
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

このチュートリアルは、**2 部構成のチュートリアル シリーズのパート 2 です**。 [前のチュートリアル](tutorial-train-models-with-aml.md)では、機械学習モデルをトレーニングしてから、クラウド上のワークスペースにモデルを登録しました。  

これで、[Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) に Web サービスとしてモデルをデプロイする準備が整いました。 Web サービスは、1 つのイメージ (この場合は Docker イメージ) です。 ここにスコアリング ロジックとモデル自体がカプセル化されています。 

チュートリアルのこのパートでは、Azure Machine Learning を使って次の作業を行います。

> [!div class="checklist"]
> * テスト環境を設定する。
> * ワークスペースからモデルを取得する。
> * ローカルでモデルをテストする。
> * Container Instances にモデルをデプロイする。
> * デプロイしたモデルをテストする。

Container Instances は、ワークフローをテストして理解するうえで優れたソリューションです。 スケーラブルな運用環境のデプロイの場合は、Azure Kubernetes Service の使用を検討してください。 詳細については、[デプロイする方法と場所](how-to-deploy-and-where.md)に関するページを参照してください。

>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 1.0.41 を使用してテストされました。

## <a name="prerequisites"></a>前提条件

ノートブックを実行するにはまず、「[チュートリアル (パート 1): 画像分類モデルをトレーニングする](tutorial-train-models-with-aml.md)」を参照してください。   次に、お使いの複製された *tutorials/image-classification-mnist-data* フォルダーの *img-classification-part2-deploy.ipynb* ノートブックを開きます。

このチュートリアルは、独自の[ローカル環境](how-to-configure-environment.md#local)で使用する場合は、[GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) で入手することもできます。  `matplotlib` と `scikit-learn` が環境にインストールされていることを確認してください。 

> [!Important]
> 以降この記事には、ノートブックと同じ内容が記載されています。  
>
> コードを実行しながら読み進めたい方は、ここで Jupyter Notebook に切り替えてください。
> ノートブックで単一のコード セルを実行するには、そのコード セルをクリックして **Shift + Enter** キーを押します。 または、上部のツール バーから **[すべて実行]** を選択して、ノートブック全体を実行します。

## <a name="start"></a>環境をセットアップする

まずテスト環境を設定します。

### <a name="import-packages"></a>パッケージをインポートする

このチュートリアルに必要な Python パッケージをインポートします。

```python
%matplotlib inline
import numpy as np
import matplotlib
import matplotlib.pyplot as plt
 
import azureml
from azureml.core import Workspace, Run

# Display the core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="retrieve-the-model"></a>モデルを取得する

前のチュートリアルでは、ワークスペースにモデルを登録しました。 ここでは、このワークスペースを読み込み、モデルをローカル ディレクトリにダウンロードします。


```python
from azureml.core import Workspace
from azureml.core.model import Model
import os
ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')

model.download(target_dir=os.getcwd(), exist_ok=True)

# verify the downloaded model file
file_path = os.path.join(os.getcwd(), "sklearn_mnist_model.pkl")

os.stat(file_path)
```

## <a name="test-the-model-locally"></a>ローカルでモデルをテストする

デプロイ前に、モデルがローカルで動作していることを確認します。
* テスト データを読み込む。
* テスト データを予測する。
* 混同行列を調べる。

### <a name="load-test-data"></a>テスト データの読み込み

トレーニング チュートリアルで作成した **./data/** ディレクトリからテスト データを読み込みます。

```python
from utils import load_data
import os

data_folder = os.path.join(os.getcwd(), 'data')
# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the neural network converge faster
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)
```

### <a name="predict-test-data"></a>テスト データを予測する

予測を取得するために、テスト データセットをモデルにフィードします。

```python
import pickle
from sklearn.externals import joblib

clf = joblib.load(os.path.join(os.getcwd(), 'sklearn_mnist_model.pkl'))
y_hat = clf.predict(X_test)
```

###  <a name="examine-the-confusion-matrix"></a>混同行列を調べる

混同行列を生成して、テスト セットのうち正しく分類されているサンプル数を確認します。 正しくない予測については、分類が不適切な値に注目します。 

```python
from sklearn.metrics import confusion_matrix

conf_mx = confusion_matrix(y_test, y_hat)
print(conf_mx)
print('Overall accuracy:', np.average(y_hat == y_test))
```

出力には混同行列が表示されます。

    [[ 960    0    1    2    1    5    6    3    1    1]
     [   0 1112    3    1    0    1    5    1   12    0]
     [   9    8  920   20   10    4   10   11   37    3]
     [   4    0   17  921    2   21    4   12   20    9]
     [   1    2    5    3  915    0   10    2    6   38]
     [  10    2    0   41   10  770   17    7   28    7]
     [   9    3    7    2    6   20  907    1    3    0]
     [   2    7   22    5    8    1    1  950    5   27]
     [  10   15    5   21   15   27    7   11  851   12]
     [   7    8    2   13   32   13    0   24   12  898]]
    Overall accuracy: 0.9204
   

`matplotlib` を使用して混同行列をグラフとして表示します。 このグラフの X 軸は実際の値を表し、Y 軸は予測値を表します。 各グリッドの色はエラー率を表します。 色が明るいほど、エラー率が高いことを示します。 たとえば、多くの 5 は 3 と誤って分類されています。 そのため、(5,3) は明るいグリッドで示されています。

```python
# normalize the diagonal cells so that they don't overpower the rest of the cells when visualized
row_sums = conf_mx.sum(axis=1, keepdims=True)
norm_conf_mx = conf_mx / row_sums
np.fill_diagonal(norm_conf_mx, 0)

fig = plt.figure(figsize=(8, 5))
ax = fig.add_subplot(111)
cax = ax.matshow(norm_conf_mx, cmap=plt.cm.bone)
ticks = np.arange(0, 10, 1)
ax.set_xticks(ticks)
ax.set_yticks(ticks)
ax.set_xticklabels(ticks)
ax.set_yticklabels(ticks)
fig.colorbar(cax)
plt.ylabel('true labels', fontsize=14)
plt.xlabel('predicted values', fontsize=14)
plt.savefig('conf.png')
plt.show()
```

![混同行列が表示されたチャート](./media/tutorial-deploy-models-with-aml/confusion.png)

## <a name="deploy-as-a-web-service"></a>Web サービスとしてデプロイする

モデルをテストし、結果に満足したら、Container Instances でホストされる Web サービスとしてモデルをデプロイします。 

Container Instances に適した環境を構築するには、以下のコンポーネントを用意します。
* モデルの使用方法を示すスコアリング スクリプト。
* インストールする必要があるパッケージを示す環境ファイル。
* コンテナー インスタンスを構築する構成ファイル。
* トレーニング済みのモデル。

<a name="make-script"></a>

### <a name="create-scoring-script"></a>スコアリング スクリプトを作成する

**score.py** という名前のスコアリング スクリプトを作成します。 Web サービスの呼び出しでは、モデルの使用方法を示すためにこのスクリプトを使用します。

スコアリング スクリプトには、次の 2 つの関数を含める必要があります。
* `init()` 関数。通常、グローバル オブジェクトにモデルを読み込みます。 この関数は、Docker コンテナーを開始するときに 1 回だけ実行されます。 

* `run(input_data)` 関数。モデルを使用して、入力データに基づく値を予測します。 実行に対する入力と出力は、通常、JSON を使用してシリアル化およびシリアル化解除が実行されますが、その他の形式もサポートされています。

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression

from azureml.core.model import Model

def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    # you can return any data type as long as it is JSON-serializable
    return y_hat.tolist()
```

<a name="make-myenv"></a>

### <a name="create-environment-file"></a>環境ファイルを作成する

次に、**myenv.yml** という環境ファイルを作成します。このファイルには、すべてのスクリプトのパッケージ依存関係が指定されています。 このファイルは、すべての依存関係を Docker イメージに確実にインストールするために使用されます。 このモデルには `scikit-learn` と `azureml-sdk` が必要です。 すべてのカスタム環境ファイルには、pip の依存関係として 1.0.45 以降のバージョンの azureml-defaults を列挙する必要があります。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

```python
from azureml.core.conda_dependencies import CondaDependencies

myenv = CondaDependencies()
myenv.add_conda_package("scikit-learn")
myenv.add_pip_package("azureml-defaults")

with open("myenv.yml", "w") as f:
    f.write(myenv.serialize_to_string())
```
`myenv.yml` ファイルの内容を確認します。

```python
with open("myenv.yml", "r") as f:
    print(f.read())
```

### <a name="create-a-configuration-file"></a>構成ファイルを作成する

デプロイ構成ファイルを作成します。 お使いの Container Instances コンテナーに必要な CPU 数と RAM ギガバイト数を指定します。 実際のモデルにもよりますが、多くのモデルには既定値の 1 コアと 1 ギガバイトの RAM で十分です。 後でもっと必要になった場合は、イメージを再作成し、サービスをデプロイし直す必要があります。

```python
from azureml.core.webservice import AciWebservice

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "MNIST",  
                                                     "method": "sklearn"},
                                               description='Predict MNIST with sklearn')
```

### <a name="deploy-in-container-instances"></a>Container Instances にデプロイする
デプロイ完了までの推定時間は、**約 7 分から 8 分**です。

イメージを構成してデプロイします。 以下のコードでは、次の手順が実行されます。

1. 以下のファイルを使用してイメージをビルドします。
   * スコアリング ファイル (`score.py`)。
   * 環境ファイル (`myenv.yml`)。
   * モデル ファイル。
1. ワークスペースにイメージを登録します。 
1. Container Instances のコンテナーにイメージを送信します。
1. イメージを使用して、Container Instances のコンテナーを起動します。
1. Web サービス HTTP エンドポイントを取得します。

独自の環境ファイルを定義する場合、pip の依存関係として 1.0.45 以降のバージョンの azureml-defaults を列挙する必要があることに注意してください。 このパッケージには、Web サービスとしてモデルをホストするために必要な機能が含まれています。

```python
%%time
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

service = Model.deploy(workspace=ws,
                       name='sklearn-mnist-svc',
                       models=[model], 
                       inference_config=inference_config,
                       deployment_config=aciconfig)

service.wait_for_deployment(show_output=True)
```

スコアリング Web サービスの HTTP エンドポイントを取得します。このエンドポイントで REST クライアントの呼び出しを受け取ります。 このエンドポイントは、Web サービスをテストしたい、またはアプリケーションに統合したい任意のユーザーと共有できます。 

```python
print(service.scoring_uri)
```


## <a name="test-the-deployed-service"></a>デプロイされたサービスをテストする

以前は、ローカル バージョンのモデルを使用してすべてのテスト データにスコアを付けました。 ここでは、テスト データのうちランダムなサンプルの 30 個のイメージを使用して、デプロイされたモデルをテストできます。  

以下のコードでは、次の手順が実行されます。
1. JSON 配列として、Container Instances でホストされている Web サービスにデータを送信します。 

1. SDK の `run` API を使用してサービスを呼び出します。 また、**curl** など、任意の HTTP ツールを使用して生の呼び出しを行うこともできます。

1. 返された予測を出力し、入力イメージと共にプロットします。 赤い文字と白抜きの画像 (黒地に白) は、不適切な分類のサンプルを強調表示するために使用されています。 

モデルの精度は高いので、必要に応じて、不適切な分類のサンプルが表示されるまで次のコードを何度か実行します。

```python
import json

# find 30 random samples from test set
n = 30
sample_indices = np.random.permutation(X_test.shape[0])[0:n]

test_samples = json.dumps({"data": X_test[sample_indices].tolist()})
test_samples = bytes(test_samples, encoding='utf8')

# predict using the deployed model
result = service.run(input_data=test_samples)

# compare actual value vs. the predicted values:
i = 0
plt.figure(figsize=(20, 1))

for s in sample_indices:
    plt.subplot(1, n, i + 1)
    plt.axhline('')
    plt.axvline('')
    
    # use different color for misclassified sample
    font_color = 'red' if y_test[s] != result[i] else 'black'
    clr_map = plt.cm.gray if y_test[s] != result[i] else plt.cm.Greys
    
    plt.text(x=10, y=-10, s=result[i], fontsize=18, color=font_color)
    plt.imshow(X_test[s].reshape(28, 28), cmap=clr_map)
    
    i = i + 1
plt.show()
```

テスト イメージのランダムなサンプルの 1 つの結果を次に示します。

![結果を示すグラフィック](./media/tutorial-deploy-models-with-aml/results.png)

また、生の HTTP 要求を送信して Web サービスをテストすることもできます。

```python
import requests

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

headers = {'Content-Type': 'application/json'}

# for AKS deployment you'd need to the service key in the header as well
# api_key = service.get_key()
# headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ api_key)} 

resp = requests.post(service.scoring_uri, input_data, headers=headers)

print("POST to url", service.scoring_uri)
#print("input data:", input_data)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

他のチュートリアルや探索のためにリソース グループとワークスペースを残すには、次の API 呼び出しを使用して Container Instances のデプロイのみを削除することができます。

```python
service.delete()
```

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]


## <a name="next-steps"></a>次のステップ

+ [Azure Machine Learning のすべてのデプロイ オプション](how-to-deploy-and-where.md)について学習します。
+ [Web サービス用のクライアントを作成](how-to-consume-web-service.md)する方法について学習します。
+  [大量のデータの予測](how-to-use-parallel-run-step.md)を非同期的に行います。
+ [Application Insights を使用して Azure Machine Learning のモデルを監視](how-to-enable-app-insights.md)します。
+ [アルゴリズムの自動選択](tutorial-auto-train-models.md)に関するチュートリアルを試します。 
