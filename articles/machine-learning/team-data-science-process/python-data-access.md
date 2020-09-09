---
title: Python クライアント ライブラリを使用したデータセットへのアクセス - Team Data Science Process
description: Python クライアント ライブラリをインストールして使用し、ローカル Python 環境から Azure Machine Learning データを安全に管理します。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: dcbaaeb2868a421a41227e1b5d9fdd84a96248eb
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852363"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Azure Machine Learning Python クライアント ライブラリを使って Python のデータ セットにアクセスする
Microsoft Azure Machine Learning Python クライアント ライブラリのプレビューは、ローカルの Python 環境から Azure Machine Learning データセットへの安全なアクセスを確立し、ワークスペースにおけるデータセットを作成して管理できるようにします。

このトピックでは次の方法について説明します。

* Machine Learning Python クライアント ライブラリをインストールする
* ローカルの Python 環境から Azure Machine Learning データセットへのアクセス認証を取得するなど、データ セットにアクセスしてアップロードする
* 実験から中間データセットにアクセスする
* Python クライアント ライブラリを使用してデータ セットを列挙、メタデータにアクセス、データ セットのコンテンツを読み込み、新しいデータ セットを作成して既存のデータ セットを更新する

## <a name="prerequisites"></a><a name="prerequisites"></a>前提条件
Python クライアント ライブラリは、次の環境でテストされています。

* Windows、Mac、Linux
* Python 2.7、3.3、3.4

次のパッケージに依存します。

* requests
* python-dateutil
* pandas

[Anaconda](https://www.anaconda.com/)、[Canopy](https://store.enthought.com/downloads/) などの Python ディストリビューションを使用し、Python、IPython、また上述の 3 つのパッケージをインストールすることを推奨します。 IPython は厳密には必要ありませんが、データをインタラクティブに操作して視覚化する優れた環境になります。

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Azure Machine Learning Python クライアント ライブラリをインストールする方法
このトピックで説明されているタスクを完了するには、Azure Machine Learning Python クライアント ライブラリをインストールします。 このライブラリは、[Python Package Index](https://pypi.python.org/pypi/azureml) から入手できます。 ご利用の Python 環境にインストールするには、ローカルの Python 環境から次のコマンドを実行します。

```console
pip install azureml
```

代わりに、[GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python) からソースをダウンロードしてインストールすることもできます。

```console
python setup.py install
```

コンピューターに git をインストールしていれば、git リポジトリから直接 pip を使ってインストールできます。

```console
pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git
```

## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>を使ってデータ セットにアクセスする
Python クライアント ライブラリを使うと、実行している実験から既存のデータ セットへプログラムでアクセスできるようになります。

Azure Machine Learning Studio (クラシック) Web インターフェイスから、ローカル コンピューターでデータ セットを pandas DataFrame オブジェクトとしてダウンロードして逆シリアル化するために必要なすべての情報を含むコード スニペットを生成できます。

### <a name="security-for-data-access"></a><a name="security"></a>データ アクセスのためのセキュリティ
Python クライアント ライブラリで使用するために Azure Machine Learning Studio (クラシック) で提供されるコード スニペットには、ユーザーのワークスペース ID や認証トークンが含まれます。 これらを使用すればワークスペースにフル アクセスできますので、パスワードなどで保護する必要があります。

セキュリティ上の理由から、ワークスペースの **[所有者]** として設定された役割を持つユーザーのみコード スニペットの機能を使うことができます。 役割は Azure Machine Learning Studio (クラシック) で **[設定]** の下の **[ユーザー]** ページに表示されます。

![Security][security]

役割が **[所有者]** に設定されていない場合は、所有者として再度招待をリクエストするか、ワークスペースの所有者にコード スニペットを提供するよう依頼する必要があります。

認証トークンを取得するには、次のいずれかの方法を選択します。

* 所有者からトークンをリクエストします。 所有者は Azure Machine Learning Studio (クラシック) のワークスペースの [設定] ページから自分の認証トークンを確認できます。 左側のウィンドウから **[設定]** を選択し、 **[認証トークン]** をクリックしてプライマリとセカンダリのトークンを確認します。 プライマリとセカンダリの認証トークンのどちらもコード スニペットで使用できますが、所有者はセカンダリの認証トークンのみ共有することをお勧めします。

   ![認証トークン](./media/python-data-access/ml-python-access-settings-tokens.png)

* 所有者のロールに昇格するように依頼します。ワークスペースの現在の所有者がまずワークスペースからあなたを削除してから、所有者として再び招待する必要があります。

開発者がワークスペース ID と認証トークンを取得したら、ロールに関係なく、コード スニペットを使用してワークスペースにアクセスできるようになります。

認証トークンは、**[設定]** ページの **[認証トークン]** で管理されます。 認証トークンを再度生成することは可能ですが、以前のトークンへのアクセスは無効になります。

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>ローカル Python アプリケーションからデータ セットにアクセスする
1. Machine Learning Studio (クラシック) で、左側のナビゲーション バーの **[データセット]** をクリックします。
2. アクセスするデータ セットを選択します。 **[マイ データ セット]** リストか **[サンプル]** リストからどのデータ セットでも選択できます。
3. 下のツールバーから、 **[データ アクセス コードの生成]** をクリックします。 データが Python クライアント ライブラリと互換性のない形式の場合、このボタンは無効になります。
   
    ![データセット][datasets]
4. 表示されるウィンドウからコード スニペットを選択し、クリップボードにコピーします。
   
    ![[データ アクセス コードの生成] ボタン][dataset-access-code]
5. ローカル Python アプリケーションのノートブックにコードを貼り付けます。
   
    ![ノートブックにコードを貼り付ける][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Machine Learning 実験から中間データセットにアクセスする
Machine Learning Studio (クラシック) で実験が実行されると、モジュールの出力ノードから中間データセットにアクセスできます。 中間データセットは、モデル ツールが実行されているときに中間手順で作成され使用されるデータです。

中間データセットは、データの形式が Python クライアント ライブラリと互換性がある限りアクセスできます。

次の形式がサポートされます (これらの形式の定数は `azureml.DataTypeIds` クラスです)。

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

モジュールの出力ノードの上にカーソルを置くと、形式を確認できます。 ヒントにノード名とともに表示されます。

[[分割]][split] モジュールなど一部のモジュールでは、Python クライアント ライブラリでサポートされない `Dataset` 名の形式で出力されます。

![データセット形式][dataset-format]

[[CSV に変換]][convert-to-csv] などの変換モジュールを使用して、サポートされる形式で出力を取得する必要があります。

![GenericCSV 形式][csv-format]

次の手順は、実験を作成、実行して中間データ セットにアクセスする例を示します。

1. 新しい実験を作成する
2. **[米国国勢調査局提供の、成人収入に関する二項分類データセット]** モジュールを挿入します。
3. [[分割]][split] モジュールを挿入して、その入力値をデータセット モジュール出力に接続します。
4. [[CSV に変換]][convert-to-csv] モジュールを挿入して、その入力値を [[分割]][split] モジュール出力の 1 つに接続します。
5. 実験を保存し、実行して、ジョブが完了するまで待ちます。
6. [[CSV に変換]][convert-to-csv] モジュールで出力ノードをクリックします。
7. コンテキスト メニューが表示されたら、 **[データ アクセス コードの生成]** を選択します。
   
    ![コンテキスト メニュー][experiment]
8. 表示されたウィンドウからコード スニペットを選択し、クリップボードにコピーします。
   
    ![コンテキスト メニューからアクセス コードを生成する][intermediate-dataset-access-code]
9. ノートブックにコードを貼り付けます。
   
    ![ノートブックにコードを貼り付ける][ipython-intermediate-dataset]
10. Matplotlib を使用してデータを視覚化できます。 これは、age 列のヒストグラムで表示されます。
    
    ![ヒストグラム][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Machine Learning Python クライアント ライブラリを使用してデータ セットのアクセス、読み込み、作成、管理をする
### <a name="workspace"></a>ワークスペース
ワークスペースは、Python クライアント ライブラリのエントリ ポイントです。 `Workspace` クラスに自分のワークスペース ID と認証トークンを指定し、インスタンスを作成します。

```python
ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
               authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')
```

### <a name="enumerate-datasets"></a>データセットを列挙する
特定のワークスペースですべてのデータセットを列挙するには:

```python
for ds in ws.datasets:
    print(ds.name)
```

ユーザーが作成したデータセットのみを列挙するには:

```python
for ds in ws.user_datasets:
    print(ds.name)
```

サンプルのデータセットのみを列挙するには:

```python
for ds in ws.example_datasets:
    print(ds.name)
```

名前でデータセットにアクセスするには (大文字小文字は区別されます):

```python
ds = ws.datasets['my dataset name']
```

または、インデックスでアクセスするには:

```python
ds = ws.datasets[0]
```

### <a name="metadata"></a>メタデータ
データセットには、コンテンツに加え、メタデータがあります (中間データセットはこのルールの例外であり、メタデータはありません)。

作成時にユーザーが割り当てるメタデータの値の一部には次のものがあります。

* `print(ds.name)`
* `print(ds.description)`
* `print(ds.family_id)`
* `print(ds.data_type_id)`

Azure ML によって割り当てられるその他の値には次のものがあります。

* `print(ds.id)`
* `print(ds.created_date)`
* `print(ds.size)`

使用できるメタデータの詳細については、 `SourceDataset` クラスをご覧ください。

### <a name="read-contents"></a>コンテンツを読み込む
Machine Learning Studio (クラシック) で提供されるコード スニペットにより、データセットが自動的にダウンロードされ、pandas DataFrame オブジェクトに逆シリアル化されます。 これは次の `to_dataframe` メソッドで実行されます。

```python
frame = ds.to_dataframe()
```

生データをダウンロードして自分で逆シリアル化を実行する場合は、上記のようにします。 現時点では、Python クライアント ライブラリで逆シリアル化できない「ARFF」などの形式に対する唯一の方法になります。

テキストとして内容を読み込むには:

```python
text_data = ds.read_as_text()
```

バイナリとして内容を読み込むには:

```python
binary_data = ds.read_as_binary()
```

内容にストリームを開くこともできます。

```python
with ds.open() as file:
    binary_data_chunk = file.read(1000)
```

### <a name="create-a-new-dataset"></a>新しいデータセットを作成する
Python クライアント ライブラリでは、Python プログラムからデータセットをアップロードできます。 これらのデータセットは、その後、ご利用のワークスペースで使用できます。

pandas DataFrame にデータがある場合は、次のコードを使用します。

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_dataframe(
    dataframe=frame,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

ご利用のデータが既にシリアル化されている場合、次を使用できます。

```python
from azureml import DataTypeIds

dataset = ws.datasets.add_from_raw_data(
    raw_data=raw_data,
    data_type_id=DataTypeIds.GenericCSV,
    name='my new dataset',
    description='my description'
)
```

Python クライアント ライブラリは、pandas DataFrame を次の形式にシリアル化できます (`azureml.DataTypeIds` クラスの定数)。

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>既存のデータセットを更新する
既存のデータセットに一致する名前で新しいデータセットを更新しようとすると、競合エラーが発生します。

既存のデータセットを更新するには、まず既存のデータセットへの参照を取得する必要があります。

```python
dataset = ws.datasets['existing dataset']

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

その後、 `update_from_dataframe` を使用してをシリアル化し、Azure のデータセットの内容を置き換えます。

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(frame2)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

データを別の形式にシリアル化する場合は、省略可能な `data_type_id` パラメーターの値を指定します。

```python
from azureml import DataTypeIds

dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    data_type_id=DataTypeIds.GenericTSV,
)

print(dataset.data_type_id) # 'GenericTSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to jan 2015'
```

`description` パラメーターの値を指定して、新しい説明をオプションで設定できます。

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    description='data up to feb 2015',
)

print(dataset.data_type_id) # 'GenericCSV'
print(dataset.name)         # 'existing dataset'
print(dataset.description)  # 'data up to feb 2015'
```

`name` パラメーターの値を指定して、新しい名前をオプションで設定できます。 その後は、新しい名前のみでデータセットを取得することになります。 次のコードを使用すると、データ、名前、説明を更新できます。

```python
dataset = ws.datasets['existing dataset']

dataset.update_from_dataframe(
    dataframe=frame2,
    name='existing dataset v2',
    description='data up to feb 2015',
)

print(dataset.data_type_id)                    # 'GenericCSV'
print(dataset.name)                            # 'existing dataset v2'
print(dataset.description)                     # 'data up to feb 2015'

print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
print(ws.datasets['existing dataset'].name)    # IndexError
```

`data_type_id`、`name`、`description` のパラメーターはすべて省略可能で、前の値に対する既定値になります。 `dataframe` パラメーターは常に必要です。

ご利用のデータが既にシリアル化されている場合、`update_from_dataframe` の代わりに `update_from_raw_data` を使用します。 `dataframe` の代わりに `raw_data` を渡すだけで、同様に動作します。

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

