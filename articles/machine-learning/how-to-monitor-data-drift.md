---
title: AKS デプロイでのデータ誤差を検出する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Azure Kubernetes Service にデプロイされたモデルのデータの誤差 (プレビュー) を検出します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534239"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) にデプロイされたモデルのデータの誤差 (プレビュー) を検出する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

この記事では、デプロイ済みモデルのトレーニング データセットと推論データの間におけるデータの誤差を監視する方法について説明します。 機械学習のコンテキストでは、トレーニングされた機械学習モデルで、誤差が原因で予測パフォーマンスが低下する場合があります。 Azure Machine Learning では、データの誤差を監視することができ、誤差が検出されると、このサービスから電子メール アラートが送られてきます。

## <a name="what-is-data-drift"></a>データの誤差とは

機械学習においてデータの誤差とは、モデルのパフォーマンスの低下につながるモデルの入力データの変更のことです。 これは、モデルの精度が時間の経過と共に低下する主な理由の 1 つであるため、データの誤差の監視はモデルのパフォーマンスの問題を検出するために役立ちます。 

## <a name="what-can-i-monitor"></a>何を監視できるか

Azure Machine Learning では、AKS にデプロイされたモデルへの入力を監視し、このデータをそのモデルのトレーニング データセットと比較できます。 推論データは一定の間隔で[スナップショットが取得されてプロファイリングされ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)、基準のデータセットに対して計算されて、以下のようなデータの誤差の分析が行われます。 

+ ドリフト係数と呼ばれるデータの誤差の大きさを測定します。
+ フィーチャーごとにデータの誤差の寄与度を測定し、データの誤差の原因となったフィーチャーを示します。
+ 距離メトリックを測定します。 現時点では Wasserstein とエネルギー距離が計算されます。
+ フィーチャーの分布を測定します。 現時点ではカーネル密度の推定とヒストグラムです。
+ データの誤差についてのアラートを電子メールで送信します。

> [!Note]
> このサービスは "プレビュー" であり、構成オプションに制限があります。 詳細情報および更新情報については、[API ドキュメント](https://docs.microsoft.com/python/api/azureml-datadrift/)および[リリース ノート](azure-machine-learning-release-notes.md)を参照してください。 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Azure Machine Learning でデータの誤差が監視される仕組み

Azure Machine Learning を使用すると、データの誤差はデータセットまたはデプロイを通して監視されます。 データの誤差を監視するには、基準データセット (通常は、モデルのトレーニング データセット) を指定します。 基準データセットに対して 2 番目のデータセット (通常はデプロイから収集されたモデル入力データ) がテストされます。 両方のデータセットがプロファイリングされ、データの誤差の監視サービスに入力されます。 2 つのデータセット間の違いを検出するために、機械学習モデルがトレーニングされます。 モデルのパフォーマンスは、2 つのデータセット間の誤差の大きさを測定するドリフト係数に変換されます。 [モデルの解釈可能性](how-to-machine-learning-interpretability.md)を使用して、ドリフト係数に寄与するフィーチャーが計算されます。 データセットのプロファイルから、各フィーチャーに関する統計情報が追跡されます。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 まだ持っていない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

- Azure Machine Learning SDK for Python がインストールされている場合。 [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) の手順を使用して、次のことを実行します。

    - Miniconda 環境を作成する
    - Azure Machine Learning SDK for Python をインストールする

- [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。

- ワークスペース[構成ファイル](how-to-configure-environment.md#workspace)。

- 次のコマンドを使用して、データの誤差 SDK をインストールします。

    ```shell
    pip install azureml-datadrift
    ```

- モデルのトレーニング データから[データセット](how-to-create-register-datasets.md)を作成します。

- モデルを[登録](concept-model-management-and-deployment.md)するときにトレーニング データセットを指定します。 以下の例は、`datasets` パラメーターを使用してトレーニング データセットを指定する方法を示しています。

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [モデル データ収集を有効にして](how-to-enable-data-collection.md)、モデルの AKS のデプロイからデータを収集し、データが `modeldata` BLOB コンテナーに収集されていることを確認します。

## <a name="configure-data-drift"></a>データの誤差の構成
実験でのデータの誤差を構成するには、次の Python の例に示すように、依存関係をインポートします。 

この例は、[`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) オブジェクトの構成を示しています。

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>DataDriftDetector 実行の送信

`DataDriftDetector` オブジェクトが構成されたら、モデルに対して特定の日付に[データの誤差の実行](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-)を送信できます。 実行の一部として、`drift_threshold` パラメーターを設定することにより DataDriftDetector アラートを有効にします。 [datadrift_coefficient](#visualize-drift-metrics) が指定された `drift_threshold` を超えた場合は、電子メールが送信されます。

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>誤差のメトリックの視覚化

<a name="metrics"></a>

DataDriftDetector 実行を送信すると、データの誤差タスクの実行イテレーションごとに保存されている誤差のメトリックを確認できるようになります。


|メトリック|説明|
--|--|
wasserstein_distance|1 次元の数値分布に対して定義される統計的距離。|
energy_distance|1 次元の数値分布に対して定義される統計的距離。|
datadrift_coefficient|Matthew の相関係数と同様に計算されますが、この出力は 0 から 1 までの実数です。 誤差のコンテキストでは、0 は誤差がないことを示し、1 は最大誤差を示します。|
datadrift_contribution|誤差に寄与するフィーチャーの重要度。|

誤差のメトリックを表示する方法は複数あります。

* `RunDetails`[Jupyter ウィジェット](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)を使用する。
* 任意の `datadrift` 実行オブジェクト上で [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) 関数を使用する。
* [Azure Machine Learning Studio](https://ml.azure.com) で、ワークスペースの **[モデル]** セクションから、メトリックを表示します。

次の Python の例は、関連するデータの誤差のメトリックをプロットする方法を示しています。 返されたメトリックを使用して、カスタムの視覚化を作成できます。

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Azure Machine Learning によって検出されたデータの誤差の表示](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>データの誤差のスキャンをスケジュールする 

データの誤差の検出を有効にすると、指定のスケジュールされた頻度で DataDriftDetector が実行されます。 datadrift_coefficient が指定された `drift_threshold` に達した場合は、スケジュールされた実行ごとに電子メールが送信されます。 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

データの誤差の検出機能の構成は、[Azure Machine Learning Studio](https://ml.azure.com) で、 **[モデル]** の下のワークスペースの **[詳細]** タブに表示されます。

[![Azure Machine Learning Studio のデータの誤差](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Azure Machine Learning Studio で結果を表示する

[Azure Machine Learning Studio](https://ml.azure.com) でワークスペースに結果を表示するには、モデル ページに移動します。 モデルの詳細タブに、データの誤差の構成が表示されます。 データの誤差のメトリックを視覚化する **[データの誤差]** タブを利用できるようになりました。 

[![Azure Machine Learning Studio のデータの誤差](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>誤差アラートを受け取る

ドリフト係数アラートしきい値を設定し、電子メール アドレスを指定することによって、ドリフト係数がしきい値を超えたときに [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) の電子メール アラートが自動的に送信されます。 

ユーザーがカスタムのアラートやアクションを設定できるように、データの誤差のすべてのメトリックが、Azure Machine Learning ワークスペースと共に作成された [Application Insights](how-to-enable-app-insights.md) リソース内に格納されます。 電子メール アラートのリンクから Application Insights のクエリに移動できます。

![データの誤差の電子メール アラート](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>誤差の後にモデルを再トレーニングする

データの誤差がデプロイされたモデルのパフォーマンスに悪影響を与える場合は、そのモデルを再トレーニングする必要があります。 それを行うには、次の手順に進みます。

* 収集されたデータを調査し、新しいモデルをトレーニングするためのデータを準備します。
* それをトレーニング/テスト データに分割します。
* 新しいデータを使用して、モデルを再度トレーニングします。
* 新しく生成されたモデルのパフォーマンスを評価します。
* パフォーマンスが運用モデルより優れている場合は、新しいモデルをデプロイします。

## <a name="next-steps"></a>次のステップ

* データの誤差を使用した完全な例については、[Azure ML データの誤差のノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb)に関する記事を参照してください。 この Jupyter Notebook では、[Azure Open Dataset](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) を使用して、天気を予測するモデルをトレーニングし、それを AKS にデプロイし、データの誤差を監視する方法を示します。 

* [データセット モニター](how-to-monitor-datasets.md)を使用して、データの誤差を検出します。

* データの誤差を一般提供に移行するにあたり、ご質問、ご意見、ご提案をお待ちしております。 以下の製品フィードバック ボタンをご利用ください。 
