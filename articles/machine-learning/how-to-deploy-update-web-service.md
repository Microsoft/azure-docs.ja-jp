---
title: デプロイされた Web サービスを更新する
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0c2811b6bed3d02a9689f3b9e49a4c3888dff6c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935570"
---
# <a name="update-a-deployed-web-service"></a>デプロイされた Web サービスを更新する

この記事では、Azure Machine Learning と共にデプロイされた Web サービスをデプロイする方法を示します。

## <a name="prerequisites"></a>前提条件

このチュートリアルは、既に Web サービスを Azure Machine Learning と共にデプロイしてあることを前提としています。 Web サービスをデプロイする方法を学習する必要がある場合は、[これらの手順に従ってください](how-to-deploy-and-where.md)。

## <a name="update-web-service"></a>Web サービスを更新する

Web サービスを更新するには、`update` メソッドを使用します。 推論の構成で指定できる新しいモデル、新しいエントリ スクリプト、または新しい依存関係を使用するように Web サービスを更新することができます。 詳細については、[Webservice.update](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-) のドキュメントを参照してください。

[AKS Service の Update メソッド](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-)を参照してください。

[ACI Service の Update メソッド](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-)を参照してください。

> [!IMPORTANT]
> 新しいバージョンのモデルを作成するときは、それを使用したい各サービスを手動で更新する必要があります。
>
> Azure Machine Learning デザイナーから発行された Web サービスを、SDK を使用して更新することはできません。

**SDK を使用する**

次のコードは、SDK を使用して Web サービスのモデル、環境、エントリ スクリプトを更新する方法を示しています。

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**CLI を使用する**

ML CLI を使用して Web サービスを更新することもできます。 次の例では、新しいモデルを登録し、この新しいモデルを使用するように Web サービスを更新する方法を示します。

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> この例では、JSON ドキュメントを使用して、登録コマンドから更新コマンドにモデル情報を渡します。
>
> 新しいエントリ スクリプトまたは環境を使用するようにサービスを更新するには、[推論構成ファイル](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema)を作成し、それを `ic` パラメーターで指定します。

詳細については、[az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) のドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

* [失敗したデプロイをトラブルシューティングする](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service にデプロイする](how-to-deploy-azure-kubernetes-service.md)
* [Web サービスを使用するクライアント アプリケーションを作成する](how-to-consume-web-service.md)
* [カスタム Docker イメージを使用してモデルをデプロイする方法](how-to-deploy-custom-docker-image.md)
* [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)
* [Application Insights を使用して Azure Machine Learning のモデルを監視する](how-to-enable-app-insights.md)
* [実稼働環境でモデルのデータを収集する](how-to-enable-data-collection.md)
* [モデル デプロイのイベント アラートおよびトリガーを作成する](how-to-use-event-grid.md)
