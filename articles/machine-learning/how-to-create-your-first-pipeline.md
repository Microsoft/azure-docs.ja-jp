---
title: ML パイプラインの作成、実行、追跡
titleSuffix: Azure Machine Learning
description: Azure Machine Learning SDK for Python で機械学習パイプラインを作成して実行します。 機械学習 (ML) のフェーズをつなぎ合わせるワークフローを作成して管理するには、ML パイプラインを使用します。 これらのフェーズとしては、データ保護、モデルのトレーニング、モデル デプロイ、推論/スコアリングなどがあります。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sanpil
author: sanpil
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 80f38f8bf323717693df70399ad982c51dda8f13
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169800"
---
# <a name="create-and-run-machine-learning-pipelines-with-azure-machine-learning-sdk"></a>Azure Machine Learning SDK で機械学習パイプラインを作成して管理する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

この記事では、[Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) を使用して、[機械学習パイプライン](concept-ml-pipelines.md)を作成、公開、実行、追跡する方法について説明します。  **ML パイプライン**を使用して、さまざまな ML フェーズをつなぎ合わせるワークフローを作成した後、後でアクセスするか、または他のユーザーと共有するためにそのパイプラインを Azure Machine Learning ワークスペースに発行します。  ML パイプラインは、さまざまなコンピューティングを使用し、ステップを再実行する代わりに再利用し、ML ワークフローを他のユーザーと共有する、バッチ スコアリングのシナリオに最適です。

ML タスクの CI/CD オートメーションには [Azure パイプライン](https://docs.microsoft.com/azure/devops/pipelines/targets/azure-machine-learning?context=azure%2Fmachine-learning%2Fservice%2Fcontext%2Fml-context&view=azure-devops&tabs=yaml)と呼ばれる別の種類のパイプラインを使用できますが、その種類のパイプラインはワークスペース内には格納されません。 [これらの異なるパイプラインを比較してください](concept-ml-pipelines.md#which-azure-pipeline-technology-should-i-use)。

データの準備やモデルのトレーニングなど、ML パイプラインの各フェーズには、1 つまたは複数のステップを含めることができます。

作成した ML パイプラインは、Azure Machine Learning [ワークスペース](how-to-manage-workspace.md)のメンバーであれば見ることができます。 

ML パイプラインでは、そのパイプラインに関連付けられている中間および最終データの計算と格納に、リモート コンピューティング先が使用されます。 それらでは、サポートされている [Azure Storage](https://docs.microsoft.com/azure/storage/) の場所に対してデータを読み取ったり書き込んだりすることができます。

Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) をお試しください。

## <a name="prerequisites"></a>前提条件

* すべてのパイプライン リソースを保持するための [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)を作成します。

* [開発環境を構成](how-to-configure-environment.md)して Azure Machine Learning SDK をインストールするか、SDK が既にインストールされている [Azure Machine Learning コンピューティング インスタンス (プレビュー)](concept-compute-instance.md) を使用します。

まず、ワークスペースをアタッチします。

```Python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()
```


## <a name="set-up-machine-learning-resources"></a>機械学習リソースをセットアップする

ML パイプラインの実行に必要なリソースを作成します。

* パイプラインの手順で必要なデータへのアクセスに使用されるデータストアを設定します。

* データストアに存在するデータまたはデータストアでアクセス可能なデータを指し示すように、`DataReference` オブジェクトを構成します。

* パイプラインの手順が実行される[コンピューティング先](concept-azure-machine-learning-architecture.md#compute-targets)を設定します。

### <a name="set-up-a-datastore"></a>データストアをセットアップする

データストアには、パイプラインでアクセスするデータが格納されます。 各ワークスペースに既定のデータストアがあります。 データストアを追加登録できます。 

ワークスペースを作成すると、[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) と [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) がワークスペースに接続されます。 既定のデータストアが、Azure Blob Storage に接続するために登録されています。 詳細については、[Azure Files、Azure BLOB、Azure ディスクの使い分け](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)に関するページを参照してください。 

```python
# Default datastore 
def_data_store = ws.get_default_datastore()

# Get the blob storage associated with the workspace
def_blob_store = Datastore(ws, "workspaceblobstore")

# Get file storage associated with the workspace
def_file_store = Datastore(ws, "workspacefilestore")

```

パイプラインからアクセスできるように、データ ファイルまたはディレクトリをデータストアにアップロードします。 この例では、データストアとして BLOB ストレージを使用します。

```python
def_blob_store.upload_files(
    ["./data/20news.pkl"],
    target_path="20newsgroups",
    overwrite=True)
```

パイプラインは、1 つまたは複数のステップで構成されます。 ステップは、コンピューティング先で実行される単位です。 ステップでは、データ ソースが使用されて、"中間" データが生成される場合があります。 ステップでは、モデルなどのデータ、モデル ファイルと依存ファイルを含むディレクトリ、一時データが作成されることがあります。 このデータは、パイプラインの後続の他のステップで使用できます。

パイプラインをデータに接続する方法の詳細については、[データへのアクセス方法](how-to-access-data.md)および[データセットの登録方法](how-to-create-register-datasets.md)に関する記事を参照してください。 

### <a name="configure-data-reference"></a>データ参照を構成する

パイプラインでステップへの入力として参照できるデータ ソースを作成しました。 パイプライン内のデータ ソースは [DataReference](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference) オブジェクトによって表されます。 `DataReference` オブジェクトでは、データストアに存在するデータまたはデータストアからアクセス可能なデータが指し示されています。

```python
from azureml.data.data_reference import DataReference

blob_input_data = DataReference(
    datastore=def_blob_store,
    data_reference_name="test_data",
    path_on_datastore="20newsgroups/20news.pkl")
```

中間データ (またはステップの出力) は、[PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) オブジェクトによって表されます。 `output_data1` は、ステップの出力として生成され、後続の 1 つまたは複数のステップの入力として使われます。 `PipelineData` では、ステップの間にデータの依存関係が導入され、パイプライン内に暗黙的な実行順序が作成されます。 このオブジェクトは、後でパイプラインのステップを作成するときに使用されます。

```python
from azureml.pipeline.core import PipelineData

output_data1 = PipelineData(
    "output_data1",
    datastore=def_blob_store,
    output_name="output_data1")
```

### <a name="configure-data-using-datasets"></a>データセットを使用してデータを構成する

ファイルまたはファイルのセットに格納されている表形式データがある場合、[TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) は `DataReference` に代わる効率的な方法です。 `TabularDataset` オブジェクトでは、バージョン管理、差分、概要の統計情報がサポートされます。 `TabularDataset` は (Python ジェネレーターと同様に) 遅延評価され、分割またはフィルター処理によってそれらを効率的にサブセット化できます。 `FileDataset` クラスでは、1 つ以上のファイルを表し、同じように遅延評価されるデータが提供されます。 

`TabularDataset` は、[from_delimited_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none-) などのメソッドを使用して作成します。

```python
from azureml.data import TabularDataset

iris_tabular_dataset = Dataset.Tabular.from_delimited_files([(def_blob_store, 'train-dataset/tabular/iris.csv')])
```

 `FileDataset` は、[from_files](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) を使用して作成します。

 データセットの使用について詳しくは、[データセットの追加と登録](how-to-create-register-datasets.md)に関するページまたは[このサンプル ノートブック](https://aka.ms/train-datasets)をご覧ください。

## <a name="set-up-compute-target"></a>コンピューティング ターゲットを設定する

Azure Machine Learning での __コンピューティング__ (または __コンピューティング先__) という用語は、機械学習パイプラインで計算ステップを実行するマシンまたはクラスターのことです。   コンピューティング先の完全な一覧、およびコンピューティング先を作成してワークスペースにアタッチする方法については、[モデルのトレーニング用のコンピューティング先](how-to-set-up-training-targets.md)に関する記事をご覧ください。  コンピューティング先を作成またはアタッチするプロセスは、モデルをトレーニングするときも、パイプラインのステップを実行するときも同じです。 コンピューティング先を作成してアタッチした後、[パイプラインのステップ](#steps)では `ComputeTarget` オブジェクトを使用します。

> [!IMPORTANT]
> コンピューティング先での管理操作の実行は、リモート ジョブの内部からはサポートされていません。 機械学習パイプラインはリモート ジョブとして送信されるため、パイプラインの内部からはコンピューティング先での管理操作を使用しないでください。

以下では、次のものに対するコンピューティング先の作成とアタッチの例を示します。

* Azure Machine Learning コンピューティング
* Azure Databricks 
* Azure Data Lake Analytics

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning コンピューティング

ステップを実行するための Azure Machine Learning コンピューティングを作成できます。

```python
from azureml.core.compute import ComputeTarget, AmlCompute

compute_name = "aml-compute"
vm_size = "STANDARD_NC6"
if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('Found compute target: ' + compute_name)
else:
    print('Creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,  # STANDARD_NC6 is GPU-enabled
                                                                min_nodes=0,
                                                                max_nodes=4)
    # create the compute target
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # Can poll for a minimum number of nodes and for a specific timeout.
    # If no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current cluster status, use the 'status' property
    print(compute_target.status.serialize())
```

### <a id="databricks"></a>Azure Databricks

Azure Databricks は、Azure クラウド内の Apache Spark ベースの環境です。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Databricks ワークスペースを作成します。 ワークスペース リソースを作成するには、[Azure Databricks での Spark ジョブの実行](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)に関するドキュメントを参照してください。

コンピューティング先として Azure Databricks をアタッチするには、次の情報を指定します。

* __Databricks コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __Databricks ワークスペース名__:Azure Databricks ワークスペースの名前。
* __Databricks アクセス トークン__:Azure Databricks に対する認証に使用するアクセス トークン。 アクセス トークンを生成するには、[認証](https://docs.azuredatabricks.net/dev-tools/api/latest/authentication.html)に関するドキュメントを参照してください。

次のコードでは、Azure Machine Learning SDK を使用してコンピューティング先として Azure Databricks をアタッチする方法を示します (__Databricks ワークスペースは、AML ワークスペースと同じサブスクリプション内に存在する必要があります__)。

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-databricks)を参照してください。

### <a id="adla"></a>Azure Data Lake Analytics

Azure Data Lake Analytics は、Azure クラウド内のビッグ データ分析プラットフォームです。 これは、Azure Machine Learning パイプラインでコンピューティング先として使用できます。

使用する前に、Azure Data Lake Analytics アカウントを作成します。 このリソースを作成するには、「[Azure Data Lake Analytics の使用を開始する](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-get-started-portal)」を参照してください。

コンピューティング ターゲットとして Data Lake Analytics に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

* __コンピューティング名__:このコンピューティング リソースに割り当てる名前。
* __リソース グループ__:Data Lake Analytics アカウントを含むリソース グループ。
* __アカウント名__:Data Lake Analytics アカウント名です。

次のコードは、コンピューティング ターゲットとして Data Lake Analytics に接続する方法を示しています。

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

詳細な例については、GitHub の[サンプル ノートブック](https://aka.ms/pl-adla)を参照してください。

> [!TIP]
> Azure Machine Learning パイプラインは、Data Lake Analytics アカウントの既定のデータ ストアに格納されたデータのみ使用できます。 使用する必要があるデータが既定以外のストアにある場合は、[`DataTransferStep`](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep?view=azure-ml-py) を使用して、トレーニングの前にデータをコピーできます。

## <a id="steps"></a>パイプラインのステップを構築する

コンピューティング先を作成してワークスペースにアタッチした後は、パイプラインのステップを定義できます。 Azure Machine Learning SDK では、多くの組み込みステップを使用できます。 最も基本的なステップは、指定したコンピューティング先で Python スクリプトを実行する [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) です。

```python
from azureml.pipeline.steps import PythonScriptStep

trainStep = PythonScriptStep(
    script_name="train.py",
    arguments=["--input", blob_input_data, "--output", output_data1],
    inputs=[blob_input_data],
    outputs=[output_data1],
    compute_target=compute_target,
    source_directory=project_folder
)
```

共同環境でパイプラインを使用する際は、前の結果 (`allow_reuse`) の再利用が鍵となります。不要な再実行を除去することで機敏性が提供されるからです。 ステップの script_name、inputs、およびパラメーターが同じままのときは、再利用が既定の動作になります。 ステップの出力が再使用されると、ジョブはコンピューティングに送信されず、代わりに、前の実行の結果が、次のステップの実行ですぐに使用可能になります。 `allow_reuse` が false に設定されている場合は、パイプライン実行中、このステップの新規実行が常に生成されます。 

ステップを定義した後は、それらのステップの一部またはすべてを使用してパイプラインをビルドします。

> [!NOTE]
> ステップを定義するとき、またはパイプラインを構築するときに、ファイルまたはデータが Azure Machine Learning にアップロードされることはありません。

```python
# list of steps to run
compareModels = [trainStep, extractStep, compareStep]

from azureml.pipeline.core import Pipeline

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=[compareModels])
```

次の例では、作成済みの Azure Databricks コンピューティング先を使用します。 

```python
from azureml.pipeline.steps import DatabricksStep

dbStep = DatabricksStep(
    name="databricksmodule",
    inputs=[step_1_input],
    outputs=[step_1_output],
    num_workers=1,
    notebook_path=notebook_path,
    notebook_params={'myparam': 'testparam'},
    run_name='demo run name',
    compute_target=databricks_compute,
    allow_reuse=False
)
# List of steps to run
steps = [dbStep]

# Build the pipeline
pipeline1 = Pipeline(workspace=ws, steps=steps)
```

### <a name="use-a-dataset"></a>データセットを使用する 

`TabularDataset` または `FileDataset` をパイプラインで使用するには、[as_named_input(name)](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#as-named-input-name-) を呼び出すことにより、[DatasetConsumptionConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_consumption_config.datasetconsumptionconfig?view=azure-ml-py) オブジェクトに変換する必要があります。 この `DatasetConsumptionConfig` オブジェクトを、`inputs` の 1 つとしてパイプラインのステップに渡します。 

```python
dataset_consuming_step = PythonScriptStep(
    script_name="iris_train.py",
    inputs=[iris_tabular_dataset.as_named_input("iris_data")],
    compute_target=compute_target,
    source_directory=project_folder
)
```

その後、[Run.input_datasets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#input-datasets) ディクショナリを使用して、パイプライン内のデータセットを取得します。

```python
# iris_train.py
from azureml.core import Run, Dataset

run_context = Run.get_context()
iris_dataset = run_context.input_datasets['iris_data']
dataframe = iris_dataset.to_pandas_dataframe()
```

詳細については、「[azure-pipeline-steps package](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)」および「[Pipeline class](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py)」のリファレンスを参照してください。

## <a name="submit-the-pipeline"></a>パイプラインを送信する

パイプラインを送信すると、Azure Machine Learning によって各ステップの依存関係がチェックされ、指定したソース ディレクトリのスナップショットがアップロードされます。 ソース ディレクトリを指定していない場合は、現在のローカル ディレクトリがアップロードされます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。

> [!IMPORTANT]
> スナップショットにファイルが含まれないようにするには、[.gitignore](https://git-scm.com/docs/gitignore) または `.amlignore` ファイルをディレクトリに作成して、そこにそれらのファイルを追加してください。 `.amlignore` ファイルには、[.gitignore](https://git-scm.com/docs/gitignore) ファイルと同じ構文とパターンが使用されます。 両方のファイルが存在する場合、`.amlignore` ファイルが優先されます。
>
> 詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」を参照してください。

```python
from azureml.core import Experiment

# Submit the pipeline to be run
pipeline_run1 = Experiment(ws, 'Compare_Models_Exp').submit(pipeline1)
pipeline_run1.wait_for_completion()
```

初めてパイプラインを実行すると、Azure Machine Learning で次のことが行われます。

* ワークスペースに関連付けられた BLOB ストレージからコンピューティング先に、プロジェクトのスナップショットをダウンロードします。
* パイプラインの各ステップに対応した Docker イメージをビルドします。
* 各ステップの Docker イメージをコンテナー レジストリからコンピューティング先にダウンロードします。
* `DataReference` オブジェクトがステップで指定されている場合は、データ ストアをマウントします。 マウントがサポートされていない場合は、代わりにデータがコンピューティング先にコピーされます。
* ステップの定義で指定されているコンピューティング先で、ステップを実行します。 
* ログ、stdout と stderr、メトリック、ステップによって指定されている出力などの成果物を作成します。 その後、これらの成果物がアップロードされて、ユーザーの既定のデータストアに保持されます。

![パイプラインとして実験を実行する図](./media/how-to-create-your-first-pipeline/run_an_experiment_as_a_pipeline.png)

詳細については、「[Experiment class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py)」リファレンスを参照してください。

### <a name="view-results-of-a-pipeline"></a>パイプラインの結果を表示する

すべてのパイプラインとその実行の詳細の一覧を、Studio で表示します。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. [ワークスペースを表示します](how-to-manage-workspace.md#view)。

1. 左側で **[パイプライン]** を選択し、すべてのパイプラインの実行を表示します。
 ![機械学習パイプラインの一覧](./media/how-to-create-your-first-pipeline/pipelines.png)
 
1. 実行結果を表示する特定のパイプラインを選択します。

## <a name="git-tracking-and-integration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

## <a name="publish-a-pipeline"></a>パイプラインを発行する

後で異なる入力を使用して実行するために、パイプラインを発行することができます。 パラメーターを受け入れるように既に発行されているパイプラインの REST エンドポイントの場合、発行する前にパイプラインをパラメーター化する必要があります。

1. パイプライン パラメーターを作成するには、既定の値で [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py) オブジェクトを使用します。

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. 次のように、パイプラインのいずれかのステップにパラメーターとしてこの `PipelineParameter` オブジェクトを追加します。

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. 呼び出されるときにパラメーターを受け取るこのパイプラインを発行します。

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

### <a name="run-a-published-pipeline"></a>発行されたパイプラインを実行する

発行されたすべてのパイプラインに REST エンドポイントがあります。 このエンドポイントでは、Python ではないクライアントなどの外部システムからパイプラインの実行を呼び出します。 このエンドポイントでは、バッチ スコアリングと再トレーニングのシナリオでの "管理された再現性" が有効になります。

前のパイプラインの実行を呼び出すには、「[AzureCliAuthentication クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.authentication.azurecliauthentication?view=azure-ml-py)」リファレンスで説明されているように、Azure Active Directory 認証ヘッダー トークンが必要です。詳しくは、[Azure Machine Learning の認証](https://aka.ms/pl-restep-auth)に関するノートブックもご覧ください。

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

## <a name="create-a-versioned-pipeline-endpoint"></a>バージョン管理されたパイプライン エンドポイントを作成する
パイプライン エンドポイントは、背後にある発行された複数のパイプラインを使用して作成できます。 これは、発行されたパイプラインのように使用できますが、反復処理を行ったり、ML パイプラインを更新したりすると、固定の REST エンドポイントが発生します。

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PublishedPipeline.get(workspace="ws", name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

### <a name="submit-a-job-to-a-pipeline-endpoint"></a>パイプライン エンドポイントにジョブを送信する
パイプライン エンドポイントの既定のバージョンにジョブを送信できます。
```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```
また、特定のバージョンにジョブを送信することもできます。
```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

同じ操作は、REST API を使用して実行できます。
```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

### <a name="use-published-pipelines-in-the-studio"></a>Studio で発行されたパイプラインを使用する

Studio から発行されたパイプラインを実行することもできます。

1. [Azure Machine Learning Studio](https://ml.azure.com) にサインインします。

1. [ワークスペースを表示します](how-to-manage-workspace.md#view)。

1. 左側で、 **[エンドポイント]** を選択します。

1. 上部で、 **[Pipeline endpoints]\(パイプライン エンドポイント\)** を選択します。
 ![機械学習で発行されたパイプラインの一覧](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. 実行または使用する、またはパイプライン エンドポイントの以前の実行の結果を確認する、特定のパイプラインを選択します。


### <a name="disable-a-published-pipeline"></a>発行されたパイプラインを無効にする

発行済みパイプラインの一覧にパイプラインが表示されないようにするには、Studio または SDK でそれを無効にします。

```
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id="068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

`p.enable()` で再び有効にすることができます。 詳しくは、[PublishedPipeline クラス](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline?view=azure-ml-py)のリファレンスを参照してください。


## <a name="caching--reuse"></a>キャッシュと再利用  

パイプラインの動作を最適化およびカスタマイズするためには、キャッシュと再利用に関連するいくつかのことを実行できます。 たとえば、次のようなことを選択できます。
+ [ステップ定義](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)中に `allow_reuse=False` を設定して、**既定のステップ実行出力の再利用をオフにします**。 共同環境でパイプラインを使用する際は再利用が鍵となります。不要な再実行を除去することで機敏性が提供されるからです。 ただし、再利用しないことも選択できます。
+ `pipeline_run = exp.submit(pipeline, regenerate_outputs=False)` を使用して、**1 回の実行で全ステップの出力の再生成を強制します**。

既定では、ステップの `allow_reuse` が有効になり、ステップの定義に指定されている `source_directory` がハッシュ化されます。 したがって、ある特定のステップのスクリプトが同じ場合 (`script_name`、inputs、およびパラメーター)、` source_directory` では他に何か変更されることはなく、前のステップ実行の出力が再利用されて、ジョブはコンピューティングに送信されず、代わりに、前の実行の結果が次のステップで即時使用可能になります。

```python
step = PythonScriptStep(name="Hello World",
                        script_name="hello_world.py",
                        compute_target=aml_compute,
                        source_directory=source_directory,
                        allow_reuse=False,
                        hash_paths=['hello_world.ipynb'])
```

## <a name="next-steps"></a>次のステップ

- [GitHub 上のこれらの Jupyter notebook](https://aka.ms/aml-pipeline-readme) を使用して、機械学習パイプラインをさらに調べます。
- [azureml-pipelines-core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) パッケージおよび [azureml-pipelines-steps](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py) パッケージの SDK リファレンス ヘルプを参照します。
- パイプラインのデバッグとトラブルシューティングに関するヒントについては、[ハウツー](how-to-debug-pipelines.md)記事を参照してください。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]
