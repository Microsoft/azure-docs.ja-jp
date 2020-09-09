---
title: モデル トレーニング用のコンピューティング ターゲットを使用する
titleSuffix: Azure Machine Learning
description: 機械学習モデル トレーニング用のトレーニング環境 (コンピューティング ターゲット) を構成します。 トレーニング環境を簡単に切り替えることができます。 ローカルでトレーニングを開始します。 スケール アウトする必要がある場合は、クラウド ベースのコンピューティング先に切り替えます。
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e83faee7d72026dafc50b21d0a0773e663e5a03a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933115"
---
# <a name="set-up-and-use-compute-targets-for-model-training"></a>モデル トレーニング用のコンピューティング先を設定して使用する 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning では、さまざまなリソースまたは環境でご利用のモデルをトレーニングでき、それらを総称して[__コンピューティング先__](concept-azure-machine-learning-architecture.md#compute-targets)と呼びます。 コンピューティング先は、ローカル マシンでも、Azure Machine Learning コンピューティング、Azure HDInsight、リモート仮想マシンなどのクラウド リソースでもかまいません。  [モデルをデプロイする場所と方法](how-to-deploy-and-where.md)に関するページで説明されているように、モデルのデプロイ用のコンピューティング先を作成することもできます。

Azure Machine Learning SDK、Azure Machine Learning Studio、Azure CLI、または Azure Machine Learning VS Code 拡張機能を使用してコンピューティング ターゲットを作成および管理できます。 別のサービス (たとえば、HDInsight クラスター) によって作成されたコンピューティング先がある場合、それらを Azure Machine Learning ワークスペースに接続して使用できます。
 
この記事では、モデル トレーニング用にさまざまなコンピューティング先を使用する方法について説明します。  すべてのコンピューティング先の手順が、同じワークフローに従います。
1. まだない場合は、コンピューティング先を__作成__します。
2. コンピューティング先をワークスペースに __アタッチ__ します。
3. スクリプトに必要な Python 環境とパッケージ依存関係が含まれるように、コンピューティング先を __構成__ します。


>[!NOTE]
> この記事のコードは、Azure Machine Learning SDK バージョン 1.0.74 を使用してテストされています。

## <a name="compute-targets-for-training"></a>モデル トレーニング用のコンピューティング先

Azure Machine Learning では、異なるコンピューティング先に対してさまざまなサポートが提供されています。 典型的なモデル開発ライフサイクルは、少量のデータを用いた開発と実験から始まります。 この段階では、ローカル環境を使用することをお勧めします。 たとえば、ローカル コンピューターやクラウドベースの VM などです。 より大規模なデータ セットにトレーニングをスケールアップする、または分散トレーニングを実行する段階で、Azure Machine Learning コンピューティングを使用して、実行を送信するたびに自動スケーリングするシングルノードまたはマルチノード クラスターを作成することをお勧めします。 独自のコンピューティング リソースを接続することもできますが、以下で説明するように、シナリオによってサポートが異なる場合があります:

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]


> [!NOTE]
> Azure Machine Learning コンピューティング クラスターは、永続的なリソースとして作成するか、または実行を要求するときに動的に作成できます。 実行ベースの作成では、トレーニングの実行の完了後にコンピューティング ターゲットが削除されるため、この方法で作成されたコンピューティング ターゲットは再利用できません。

## <a name="whats-a-run-configuration"></a>実行構成とは

トレーニングのときは、ローカル コンピューターで開始し、後で別のコンピューティング先でそのトレーニング スクリプトを実行するのが一般的です。 Azure Machine Learning では、スクリプトを変更しなくても、さまざまなコンピューティング先でスクリプトを実行できます。

必要なのは、**実行構成**内で各コンピューティング先の環境を定義することだけです。  その後、異なるコンピューティング先でトレーニング実験を実行するときは、そのコンピューティングの実行構成を指定します。 環境を指定し、構成を実行するためにバインドする方法の詳細については、「[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)」を参照してください。

詳しくは、この記事の最後にある[実験の送信](#submit)に関する説明をご覧ください。

## <a name="whats-an-estimator"></a>Estimator とは

一般的なフレームワークを使用したモデルのトレーニングを容易にするために、Azure Machine Learning の Python SDK には、代替の高度な抽象化である Estimator クラスが用意されています。  このクラスを使用すると、実行構成を簡単に作成できます。 汎用の [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) を作成し、それを使用して、自分で選択した任意の学習フレームワーク (scikit-learn など) を使用するトレーニング スクリプトを送信できます。 環境オブジェクトや RunConfiguration オブジェクトなどの埋め込みオブジェクトがお客様用に自動的に構成されるため、トレーニングには見積もりツールを使用することをお勧めします。 これらのオブジェクトの作成方法をさらに詳細に制御し、試験実行用にインストールするパッケージを指定する場合は、[次の手順](#amlcompute)に従って、Azure Machine Learning コンピューティング上で RunConfiguration オブジェクトを使用してトレーニング試験を送信します。

Azure Machine Learning には、[PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py)、[TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)、[Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py)、および [Ray RLlib](how-to-use-reinforcement-learning.md) のために固有の推定器が用意されています。

詳細については、「[Estimator を使用して ML モデルをトレーニングする](how-to-train-ml-models.md)」を参照してください。

## <a name="whats-an-ml-pipeline"></a>ML パイプラインとは

ML パイプラインを使用すると、シンプルさ、速度、移植性、再利用によってワークフローを最適化できます。 Azure Machine Learning を使用してパイプラインを構築すると、インフラストラクチャとオートメーションではなく、自分の専門知識と機械学習に焦点を絞ることができます。

ML パイプラインは、パイプライン内の個別のコンピューティング単位である複数の**ステップ**から構成されます。 各ステップは個別に実行でき、分離されたコンピューティング リソースを使用できます。 このアプローチにより、複数のデータ サイエンティストが、コンピューティング リソースを過剰に使用することなく、同じパイプラインで同時に作業を行うことができます。また、各ステップに対して異なるコンピューティングの種類/サイズを簡単に使用できるようになります。

> [!TIP]
> ML パイプラインでは、モデルのトレーニング時に実行構成または Estimator を使用できます。

ML パイプラインではモデルをトレーニングできますが、トレーニング前にデータを準備し、トレーニング後にモデルをデプロイすることもできます。 パイプラインの主なユースケースの 1 つは、バッチ スコアリングです。 詳細については、「[パイプライン: 機械学習ワークフローの最適化](concept-ml-pipelines.md)」を参照してください。

## <a name="set-up-in-python"></a>Python での設定

以下のセクションでは、次のコンピューティング先を構成する方法を示します。

* [ローカル コンピューター](#local)
* [Azure Machine Learning コンピューティング クラスター](#amlcompute)
* [Azure Machine Learning コンピューティング インスタンス](#instance)
* [リモート仮想マシン](#vm)
* [Azure HDInsight](#hdinsight)


### <a name="local-computer"></a><a id="local"></a>ローカル コンピューター

1. **作成してアタッチする**:トレーニング環境としてローカル コンピューターを使用する場合は、コンピューティング先を作成またはアタッチする必要はありません。  

1. **構成する**:ローカル コンピューターをコンピューティング先として使用すると、トレーニング コードは自分の[開発環境](how-to-configure-environment.md)で実行されます。  その環境に必要な Python パッケージが既にある場合は、ユーザー管理環境を使用します。

 [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=run_local)]

コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

### <a name="azure-machine-learning-compute-cluster"></a><a id="amlcompute"></a>Azure Machine Learning コンピューティング クラスター

Azure Machine Learning コンピューティング クラスターは、シングルノードまたはマルチノードのコンピューティングを簡単に作成できるマネージド コンピューティング インフラストラクチャです。 コンピューティングは、リソースとしてワークスペース リージョン内に作成され、ワークスペース内の他のユーザーと共有できます。 コンピューティングはジョブが送信されると自動的にスケールアップされ、Azure 仮想ネットワークに配置できます。 コンピューティングはコンテナー化環境で実行され、モデルの依存関係が [Docker コンテナー](https://www.docker.com/why-docker)にパッケージ化されます。

Azure Machine Learning コンピューティングを使用して、クラウド内の CPU または GPU コンピューティング ノードのクラスター全体にトレーニング プロセスを分散させることができます。 GPU を含む VM サイズの詳細については、「[GPU 最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)」を参照してください。 

Azure Machine Learning コンピューティングには、割り当て可能なコア数などの既定の制限があります。 詳細については、「[Azure リソースのクォータの管理と要求](how-to-manage-quotas.md)」を参照してください。


> [!TIP]
> 必要なコア数に十分に対応するクォータを備えている限り、クラスターは一般に、最大で 100 ノードまでスケールアップすることができます。 既定では、たとえば、MPI ジョブをサポートするために、クラスターは、そのノード間でノード間通信を有効にした状態でセットアップされます。 ただし、[サポート チケットを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)し、ご利用のサブスクリプション、ワークスペース、またはノード間通信を無効にする特定のクラスターをリストに登録することを要求するだけで、ご利用のクラスターを数千のノードにスケーリングすることができます。 

Azure Machine Learning コンピューティングは、複数回の実行で再利用できます。 コンピューティングは、ワークスペース内の他のユーザーと共有することができ、複数回の実行の間で保持されます。この場合、送信された実行の回数およびクラスター上で設定された max_nodes に基づいてノードは自動的にスケールアップまたはスケールダウンされます。 min_nodes 設定では、使用可能な最小ノード数が制御されます。

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

1. **作成してアタッチする**:Python で永続的な Azure Machine Learning コンピューティング リソースを作成するには、**vm_size** および **max_nodes** プロパティを指定します。 その後、Azure Machine Learning では他のプロパティに対してスマート既定値が使用されます。 コンピューティングは、使用されていないときは、0 ノードまで自動的にスケールダウンされます。   必要に応じて、ジョブ実行専用の VM が作成されます。
    
    * **vm_size**:Azure Machine Learning コンピューティングによって作成されるノードの VM ファミリ。
    * **max_nodes**:Azure Machine Learning コンピューティングでジョブを実行中に自動スケールアップする最大ノード数。
    
   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

   Azure Machine Learning コンピューティングを作成するときに、いくつかの詳細プロパティも設定できます。 これらのプロパティを使用すると、永続的なクラスターを固定サイズで、またはサブスクリプションの既存の Azure 仮想ネットワーク内に作成できます。  詳しくは、「[AmlCompute クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py
    )」をご覧ください。

    または、[Azure Machine Learning Studio](#portal-create) で、永続的な Azure Machine Learning コンピューティング リソースを作成してアタッチすることもできます。

   
1. **構成する**:永続的なコンピューティング先の実行構成を作成します。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=run_amlcompute)]

コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

 ### <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a>コンピューティング クラスターのコストを削減する

[優先順位の低い VM](concept-plan-manage-cost.md#low-pri-vm) を使用して、一部または全部のワークロードを実行することもできます。 これらの VM では、可用性が保証されず、使用中に割り込まれる可能性があります。 割り込まれたジョブは、再開されるのではなく、最初から開始し直されます。 

優先順位の低い VM を指定するには、次のいずれかの方法を使用します。
    
* VM を作成するときに、スタジオで **[低優先度]** を選択します。
    
* Python SDK では、プロビジョニングの構成で `vm_priority` 属性を設定します。  
    
    ```python
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                vm_priority='lowpriority',
                                                                max_nodes=4)
    ```
    
* CLI を使用して、`vm-priority` を設定します。
    
    ```azurecli-interactive
    az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
    ```

 ### <a name="set-up-managed-identity"></a><a id="managed-identity"></a> マネージド ID を設定する

 また、Azure Machine Learning コンピューティング クラスターによって、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) がサポートされ、コードに資格情報を含めることなく、Azure リソースへのアクセスが認証されます。 マネージド ID には、次の 2 種類があります。

* **システム割り当てマネージド ID** は、Azure Machine Learning コンピューティング クラスターで直接有効になります。 システム割り当て ID のライフ サイクルは、コンピューティング クラスターに直接関連付けられます。 コンピューティング クラスターが削除された場合、Azure は Azure AD の資格情報および ID を自動的にクリーンアップします。
* **ユーザー割り当てマネージド ID** は、Azure マネージド ID サービスを介して提供されるスタンドアロンの Azure リソースです。 ユーザー割り当てマネージド ID を複数のリソースに割り当てることができ、任意の有効期間を設定できます。

次のいずれかの方法を使用して、コンピューティング クラスターのマネージド ID を指定します。
    
* スタジオで、コンピューティング クラスターを作成するとき、またはコンピューティング クラスターの詳細を編集するときは、 **[マネージド ID を割り当てる]** を切り替えて、システム割り当て ID まはたユーザー割り当て ID を指定します。
    
* Python SDK では、プロビジョニングの構成で `identity_type` 属性を設定します。  
    
    ```python
    # configure cluster with a system-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="SystemAssigned",
                                                            )

    # configure cluster with a user-assigned managed identity
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=5,
                                                            identity_type="UserAssigned",
                                                            identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])

    cpu_cluster_name = "cpu-cluster"
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
    ```

* Python SDK では、プロビジョニングの構成で `identity_type` と `identity_id` (ユーザー割り当てマネージド ID を作成している場合) を設定します。  
    
    ```python
    # add a system-assigned managed identity
    cpu_cluster.add_identity(identity_type="SystemAssigned")

    # add a user-assigned managed identity
    cpu_cluster.add_identity(identity_type="UserAssigned", 
                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    ```
    
* CLI を使用して、クラスターの作成時に `assign-identity` 属性を設定します。
    
    ```azurecli
    # create a cluster with a user-assigned managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # create a cluster with a system-managed identity
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'

* Using the CLI, execute the following commands to assign a managed identity on an existing cluster:
    
    ```azurecli
    # add a user-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'

    # add a system-assigned managed identity
    az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'

> [!NOTE]
> Azure Machine Learning compute clusters support only **one system-assigned identity** or **multiple user-assigned identities**, not both concurrently.
> 
> Additionally, you can assign only one managed identity from the studio.

#### Managed identity usage

AML defines the **default managed identity** as the system-assigned managed identity or the first user-assigned managed identity.

During a run there are two applications of an identity:
1. The system uses an identity to setup the user's storage mounts, container registry, and datastores.
    * In this case, the system will use the default managed identity.

1. The user applies an identity to access resources from within the code for a submitted run
    
    * In this case, the user must provide the *client_id* corresponding to the managed identity they want to use to retrieve a credential. 
    * Alternatively, AML exposes the user-assigned identity's client ID through the *DEFAULT_IDENTITY_CLIENT_ID* environment variable.
    
    For example, to retrieve a token for a datastore with the default managed identity:
    
    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')



### <a id="instance"></a>Azure Machine Learning compute instance

[Azure Machine Learning compute instance](concept-compute-instance.md) is a managed-compute infrastructure that allows you to easily create a single VM. The compute is created within your workspace region, but unlike a compute cluster, an instance cannot be shared with other users in your workspace. Also the instance does not automatically scale down.  You must stop the resource to prevent ongoing charges.

A compute instance can run multiple jobs in parallel and has a job queue. 

Compute instances can run jobs securely in a [virtual network environment](how-to-enable-virtual-network.md#compute-instance), without requiring enterprises to open up SSH ports. The job executes in a containerized environment and packages your model dependencies in a Docker container. 

1. **Create and attach**: 
    
    ```python
    import datetime
    import time
    
    from azureml.core.compute import ComputeTarget, ComputeInstance
    from azureml.core.compute_target import ComputeTargetException
    
    # Choose a name for your instance
    # Compute instance name should be unique across the azure region
    compute_name = "ci{}".format(ws._workspace_id)[:10]
    
    # Verify that instance does not exist already
    try:
        instance = ComputeInstance(workspace=ws, name=compute_name)
        print('Found existing instance, use it.')
    except ComputeTargetException:
        compute_config = ComputeInstance.provisioning_configuration(
            vm_size='STANDARD_D3_V2',
            ssh_public_access=False,
            # vnet_resourcegroup_name='<my-resource-group>',
            # vnet_name='<my-vnet-name>',
            # subnet_name='default',
            # admin_user_ssh_public_key='<my-sshkey>'
        )
        instance = ComputeInstance.create(ws, compute_name, compute_config)
        instance.wait_for_completion(show_output=True)
    ```

1. **構成する**:実行構成を作成します。
    
    ```python
    
    from azureml.core import ScriptRunConfig
    from azureml.core.runconfig import DEFAULT_CPU_IMAGE
    
    src = ScriptRunConfig(source_directory='', script='train.py')
    
    # Set compute target to the one created in previous step
    src.run_config.target = instance
    
    # Set environment
    src.run_config.environment = myenv
     
    run = experiment.submit(config=src)
    ```

これでコンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。


### <a name="remote-virtual-machines"></a><a id="vm"></a>リモート仮想マシン

Azure Machine Learning では、独自のコンピューティング リソースを用意してワークスペースに接続することもサポートされています。 任意のリモート VM もそのようなリソースの一種ですが、Azure Machine Learning からアクセスできることが条件です。 リソースは、Azure VM でも、組織内またはオンプレミスのリモート サーバーでもかまいません。 具体的には、IP アドレスと資格情報 (ユーザー名とパスワードまたは SSH キー) があれば、任意のアクセス可能な VM をリモート実行に使用できます。

システムで構築済みの conda 環境、既存の Python 環境、または Docker コンテナーを使用できます。 Docker コンテナーで実行するには、Docker エンジンを VM で実行する必要があります。 この機能は、ローカル コンピューターよりも柔軟性がある、クラウドベースの開発/実験環境が必要な場合に特に役立ちます。

このシナリオ向けに選択する Azure VM としては、Azure Data Science 仮想マシン (DSVM) を使用します。 この VM は、Azure での事前構成済みのデータ サイエンスおよび AI 開発環境です。 その VM では、完全なライフサイクルの機械学習開発用に精選されたツールとフレームワークが提供されます。 Azure Machine Learning での DSVM の使用方法について詳しくは、[開発環境の構成](https://docs.microsoft.com/azure/machine-learning/how-to-configure-environment#dsvm)に関する記事をご覧ください。

1. **作成**:モデルのトレーニングに使用する DSVM を事前に作成します。 このリソースの作成については、「[Linux (Ubuntu) データ サイエンス仮想マシンのプロビジョニング](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)」をご覧ください。

    > [!WARNING]
    > Azure Machine Learning では、**Ubuntu** を実行する仮想マシンのみがサポートされます。 VM を作成するとき、または既存の VM を選択するときは、Ubuntu を使用する VM を選択する必要があります。
    > 
    > さらに Azure Machine Learning では、仮想マシンに__パブリック IP アドレス__が必要です。

1. **アタッチする**:コンピューティング ターゲットとして既存の仮想マシンを接続するには、仮想マシンのリソース ID、ユーザー名、およびパスワードを入力する必要があります。 VM のリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、VM 名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](#portal-reuse)、DSVM をワークスペースにアタッチすることもできます。

1. **構成する**:DSVM コンピューティング先用の実行構成を作成します。 Docker と conda は、DSVM でトレーニング環境を作成および構成するために使用されます。

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/dsvm.py?name=run_dsvm)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。

### <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Azure HDInsight は、ビッグ データ分析のための一般的なプラットフォームです。 そのプラットフォームでは、モデルのトレーニングに使用できる Apache Spark が提供されます。

1. **作成**:モデルのトレーニングに使用する HDInsight クラスターを、事前に作成します。 HDInsight クラスターで Spark を作成するには、[HDInsight での Spark クラスターの作成](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql)に関する記事をご覧ください。 

    > [!WARNING]
    > Azure Machine Learning では、HDInsight クラスターに__パブリック IP アドレス__が必要です。

    クラスターを作成するとき、SSH ユーザー名とパスワードを指定する必要があります。 コンピューティング先として HDInsight を使用するときに必要になるので、これらの値をメモしておいてください。
    
    クラスターが作成された後、ホスト名 \<clustername>-ssh.azurehdinsight.net でそれに接続します。\<clustername> はクラスターに指定した名前です。 

1. **アタッチする**:コンピューティング先として HDInsight クラスターをアタッチするには、HDInsight クラスターのリソース ID、ユーザー名、およびパスワードを指定する必要があります。 HDInsight クラスターのリソース ID は、次の文字列形式を使用して、サブスクリプション ID、リソース グループ名、HDInsight クラスター名から作成できます: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   または、[Azure Machine Learning Studio を使用して](#portal-reuse)、HDInsight クラスターをワークスペースにアタッチすることもできます。

1. **構成する**:HDI コンピューティング先用の実行構成を作成します。 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


コンピューティングをアタッチし、実行を構成したので、次のステップでは[トレーニング実行を送信](#submit)します。


### <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch は、大規模な並列コンピューティングやハイパフォーマンス コンピューティング (HPC) のアプリケーションをクラウドで効率的に実行するために使用されます。 AzureBatchStep を Azure Machine Learning Pipeline で使用して、マシンの Azure Batch プールにジョブを送信できます。

コンピューティング ターゲットとして Azure Batch に接続するには、Azure Machine Learning SDK を使用し、次の情報を提供する必要があります。

-    **Azure Batch のコンピューティング名**:ワークスペース内のコンピューティングに使用されるフレンドリ名
-    **Azure Batch アカウント名**:Azure Batch アカウントの名前
-    **リソース グループ**:Azure Batch アカウントを含むリソース グループ。

次のコードは、コンピューティング ターゲットとして Azure Batch に接続する方法を示しています。

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

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

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

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

## <a name="set-up-in-azure-machine-learning-studio"></a>Azure Machine Learning Studio で設定する

Azure Machine Learning Studio で、ワークスペースに関連付けられているコンピューティング ターゲットにアクセスできます。  Studio を使用すると、次のことが可能になります。

* ワークスペースにアタッチされている[コンピューティング先を表示する](#portal-view)
* ワークスペースに[コンピューティング先を作成する](#portal-create)
* ワークスペースの外部に作成された[コンピューティング ターゲットにアタッチする](#portal-reuse)


コンピューティング先を作成してワークスペースにアタッチした後は、`ComputeTarget` オブジェクトを使用して実行構成でそれを使用します。 

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

### <a name="view-compute-targets"></a><a id="portal-view"></a>コンピューティング先を表示する


ワークスペースのコンピューティング先を表示するには、次の手順を使用します。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
 
1. __[アプリケーション]__ で __[Compute]__ を選択します。

    [![[計算] タブを表示する](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace.png)](./media/how-to-set-up-training-targets/azure-machine-learning-service-workspace-expanded.png)

### <a name="create-a-compute-target"></a><a id="portal-create"></a>コンピューティング先を作成する

コンピューティング先の一覧を表示するには、前の手順に従います。 その後、次の手順を使用してコンピューティング先を作成します。 

1. プラス記号 (+) を選択して、コンピューティング先を追加します。

    ![コンピューティング先を追加する](./media/how-to-set-up-training-targets/add-compute-target.png) 

1. コンピューティング ターゲットの名前を入力します。 

1. __[トレーニング]__ に使用するコンピューティングの種類として **[Machine Learning コンピューティング]** を選択します。 

    >[!NOTE]
    >Azure Machine Learning コンピューティングは、Azure Machine Learning Studio で作成できる唯一のマネージド コンピューティング リソースです。  他のすべてのコンピューティング リソースは、作成した後でアタッチできます。

1. フォームに入力します。 必須のプロパティの値を指定します。特に、コンピューティングの起動に使用する **[VM ファミリ]** と **[最大ノード数]** を指定します。  

1. __［作成］__ を選択します


1. 一覧からコンピューティング先を選択することによって、作成操作の状態を表示します。

    ![作成操作の状態を表示するコンピューティング先を選択する](./media/how-to-set-up-training-targets/View_list.png)

1. コンピューティング先の詳細が表示されます。 

    ![コンピューティング先の詳細を表示する](./media/how-to-set-up-training-targets/compute-target-details.png) 

### <a name="attach-compute-targets"></a><a id="portal-reuse"></a>コンピューティング ターゲットにアタッチする

Azure Machine Learning ワークスペースの外部に作成されたコンピューティング先を使用するには、それらをアタッチする必要があります。 コンピューティング ターゲットをアタッチすることで、ワークスペースで利用できるようにします。

コンピューティング先の一覧を表示するには、前に説明した手順に従います。 その後、次の手順を使用して、コンピューティング ターゲットをアタッチします。 

1. プラス記号 (+) を選択して、コンピューティング先を追加します。 
1. コンピューティング ターゲットの名前を入力します。 
1. __トレーニング__ に接続するコンピューティングの種類を選択します。

    > [!IMPORTANT]
    > Azure Machine Learning Studio から、すべてのコンピューティングの種類をアタッチできるわけではありません。 現在トレーニング用に接続できるコンピューティングの種類は次のとおりです。
    >
    > * リモート VM
    > * Azure Databricks (機械学習パイプラインで使用)
    > * Azure Data Lake Analytics (機械学習パイプラインで使用)
    > * Azure HDInsight

1. フォームに入力し、必要なプロパティの値を指定します。

    > [!NOTE]
    > パスワードよりも安全な SSH キーを使用することをお勧めします。 パスワードはブルート フォース攻撃に対して脆弱です。 SSH キーは暗号署名に依存します。 Azure Virtual Machines で使用するための SSH キーを作成する方法の詳細については、次のドキュメントを参照してください。
    >
    > * [Linux または macOS で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)
    > * [Windows で SSH キーを作成して使用する](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)

1. __[接続]__ を選択します。 
1. 一覧からコンピューティング先を選択することによって、接続操作の状態を表示します。

## <a name="set-up-with-cli"></a>CLI を使用した設定

Azure Machine Learning 用の [CLI 拡張機能](reference-azure-machine-learning-cli.md)を使用して、ワークスペースに関連付けられたコンピューティング先にアクセスすることができます。  CLI を使用して次のことができます。

* マネージド コンピューティング先を作成する
* マネージド コンピューティング先を更新する
* アンマネージド コンピューティング先をアタッチする

詳しくは、「[リソース管理](reference-azure-machine-learning-cli.md#resource-management)」をご覧ください。

## <a name="set-up-with-vs-code"></a>VS Code を使用した設定

Azure Machine Learning 用の [VS Code 拡張機能](how-to-manage-resources-vscode.md#compute-clusters)を使用して、ワークスペースに関連付けられたコンピューティング先にアクセスし、これを作成および管理することができます。

## <a name="submit-training-run-using-azure-machine-learning-sdk"></a><a id="submit"></a>Azure Machine Learning SDK を使用してトレーニングの実行を送信する

実行構成を作成した後は、それを使用して実験を実行します。  トレーニングの実行を送信するためのコード パターンは、すべての種類のコンピューティング先について同じです。

1. 実行する実験を作成する
1. 実行を送信します。
1. 実行が完了するのを待ちます。

> [!IMPORTANT]
> トレーニングの実行を送信すると、トレーニング スクリプトが含まれるディレクトリのスナップショットが作成されて、コンピューティング先に送信されます。 スナップショットは、ご利用のワークスペースにも実験の一部として保存されます。 ファイルを変更して実行を再度送信すると、変更したファイルのみがアップロードされます。
>
> [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
> 
> 詳細については、「[スナップショット](concept-azure-machine-learning-architecture.md#snapshots)」を参照してください。

### <a name="create-an-experiment"></a>実験の作成

最初に、自分のワークスペース内に実験を作成します。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=experiment)]

### <a name="submit-the-experiment"></a>実験を送信する

`ScriptRunConfig` オブジェクトを使用して実験を送信します。  このオブジェクトには以下のものが含まれます。

* **source_directory**:トレーニング スクリプトが格納されているソース ディレクトリ
* **script**:トレーニング スクリプトを示します
* **run_config**:トレーニングが行われる場所が定義されている実行構成。

たとえば、[ローカル ターゲット](#local)の構成を使用するには、次のようになります。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/local.py?name=local_submit)]

[amlcompute ターゲット](#amlcompute)などの別の実行構成を使用して、別のコンピューティング先で実行するように、同じ実験を切り替えます。

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=amlcompute_submit)]

> [!TIP]
> この例では、トレーニング用にコンピューティング ターゲットの 1 つのノードのみを使用するように既定で設定されています。 複数のノードを使用するには、実行構成の `node_count` を必要な数のノードに設定します。 たとえば、次のコードでは、トレーニングに使用するノードの数を 4 に設定しています。
>
> ```python
> src.run_config.node_count = 4
> ```

または、次のことができます。

* [Estimator での ML モデルのトレーニング](how-to-train-ml-models.md)に関する記事で示されているように、`Estimator` オブジェクトを使用して実験を送信します。
* [ハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)用の HyperDrive 実行を送信します。
* [VS Code 拡張機能](tutorial-train-deploy-image-classification-model-vscode.md#train-the-model)を介して実験を送信します。

詳細については、[ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) および [RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) のドキュメントを参照してください。

## <a name="create-run-configuration-and-submit-run-using-azure-machine-learning-cli"></a>Azure Machine Learning CLI を使用して実行構成の作成および実行の送信を行う

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) と [Machine Learning CLI 拡張機能](reference-azure-machine-learning-cli.md)を使用して、実行構成を作成し、さまざまなコンピューティング先に実行を送信できます。 次の例は、既存の Azure Machine Learning ワークスペースがあること、および `az login` CLI コマンドを使用して Azure にログインしていることを前提としています。 

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

### <a name="create-run-configuration"></a>実行構成の作成

実行構成を作成する最も簡単な方法は、機械学習 Python スクリプトが格納されているフォルダーにナビゲートし、CLI コマンドを使用することです。

```azurecli
az ml folder attach
```

このコマンドにより、さまざまなコンピューティング先のためのテンプレート実行構成ファイルを含むサブフォルダー `.azureml` が作成されます。 これらのファイルをコピーして編集し、構成をカスタマイズできます。たとえば、Python パッケージを追加したり、Docker 設定を変更したりできます。  

### <a name="structure-of-run-configuration-file"></a>実行構成ファイルの構造

実行構成ファイルは YAML 形式で、次のセクションで構成されています。
 * 実行するスクリプトとその引数
 * コンピューティング先の名前 ("local" またはワークスペースの下にあるコンピューティングの名前)。
 * 実行のためのパラメーター: フレームワーク、分散実行用コミュニケーター、最大期間、コンピューティング ノード数。
 * 環境セクション。 このセクションのフィールドの詳細については、「[トレーニングとデプロイのための環境の作成と管理](how-to-use-environments.md)」を参照してください。
   * 実行用にインストールする Python パッケージを指定するには、[conda 環境ファイル](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#create-env-file-manually)を作成し、__condaDependenciesFile__ フィールドを設定します。
 * 実行履歴の詳細。ログ ファイル フォルダーを指定し、出力コレクションと実行履歴のスナップショットを有効または無効にします。
 * 選択されたフレームワークに固有の構成の詳細。
 * データ参照とデータ ストアの詳細。
 * 新しいクラスターを作成するための Machine Learning コンピューティングに固有の構成の詳細。

完全な runconfig のスキーマについては、[JSON ファイル](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)の例を参照してください。

### <a name="create-an-experiment"></a>実験の作成

最初に、自分の実行のための実験を作成します。

```azurecli
az ml experiment create -n <experiment>
```

### <a name="script-run"></a>スクリプトの実行

スクリプトの実行を送信するには、コマンドを実行します。

```azurecli
az ml run submit-script -e <experiment> -c <runconfig> my_train.py
```

### <a name="hyperdrive-run"></a>HyperDrive の実行

Azure CLI で HyperDrive を使用すると、パラメーターの調整実行を行うことができます。 まず、次の形式で HyperDrive 構成ファイルを作成します。 ハイパーパラメーターのチューニング パラメーターの詳細については、[モデルのハイパーパラメーターのチューニング](how-to-tune-hyperparameters.md)に関する記事を参照してください。

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

実行構成ファイルと共にこのファイルを追加します。 その後、以下を使用して HyperDrive 実行を送信します。
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

runconfig の *arguments* セクションと HyperDrive 構成の *parameter space* に注意してください。これらには、トレーニング スクリプトに渡されるコマンドライン引数が含まれています。 runconfig の値は繰り返しごとに変わりませんが、HyperDrive 構成の範囲は反復処理されます。 両方のファイルで同じ引数を指定しないでください。

これらの ```az ml``` CLI コマンドの詳細については、[リファレンス ドキュメント](reference-azure-machine-learning-cli.md)を参照してください。

<a id="gitintegration"></a>

## <a name="git-tracking-and-integration"></a>Git の追跡と統合

ソース ディレクトリがローカル Git リポジトリであるトレーニング実行を開始すると、リポジトリに関する情報が実行履歴に格納されます。 詳細については、「[Azure Machine Learning との Git 統合](concept-train-model-git-integration.md)」を参照してください。

## <a name="notebook-examples"></a>ノートブックの例

さまざまなコンピューティング先を使用したトレーニングの例については、以下のノートブックをご覧ください。
* [how-to-use-azureml/training](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>次のステップ

* [チュートリアル:モデルのトレーニング](tutorial-train-models-with-aml.md)に関する記事では、マネージド コンピューティング先を使用してモデルをトレーニングします。
* より優れたモデルを構築するために、[ハイパーパラメーター](how-to-tune-hyperparameters.md)を効率的に調整する方法を学習します。
* モデルのトレーニングが済んだら、[モデルをデプロイする方法と場所](how-to-deploy-and-where.md)を確認します。
* [RunConfiguration クラス](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py)の SDK リファレンスを確認します。
* [Azure Machine Learning と Azure Virtual Network を使用する](how-to-enable-virtual-network.md)
