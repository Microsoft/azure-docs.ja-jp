---
title: Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する | Microsoft Docs
description: この記事では、Azure Red Hat OpenShift バージョン 4 以降でホストされている Azure Monitor を使用して Kubernetes クラスターの監視を構成する方法を説明します。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91b5644c13ac560910703454c4052e223f958014
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499173"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Azure Monitor for containers を使用して Azure Red Hat OpenShift v4.x を構成する

Azure Monitor for containers は、Azure Kubernetes Service (AKS) と AKS エンジン クラスター用の監視エクスペリエンスを提供するものです。 この記事では [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) バージョン 4.x でホストされている Kubernetes クラスターの監視を有効にすることで、同様の監視エクスペリエンスを実現する方法について説明します。

>[!NOTE]
>現時点では、Azure Red Hat OpenShift のサポートはパブリック プレビューの機能です。
>

この記事に記載されているサポートされている方法を使用して、1 つ以上の既存の Azure Red Hat OpenShift v4.x のデプロイに対して Azure Monitor for containers を有効にできます。

既存のクラスターの場合は、[Azure CLI でこの Bash スクリプト](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create)を実行します。

## <a name="supported-and-unsupported-features"></a>サポートされている機能とサポートされていない機能

Azure Monitor for containers では、次の機能を除き、「[Azure Monitor for containers の概要](container-insights-overview.md)」で説明されているように、Azure Red Hat OpenShift v4.x の監視がサポートされています。

- ライブ データ (プレビュー)
- クラスター ノードとポッドから[メトリックが収集され](container-insights-update-metrics.md)、Azure Monitor メトリック データベースに格納される

## <a name="prerequisites"></a>前提条件

- Azure CLI バージョン 2.0.72 以降  

- [Helm 3](https://helm.sh/docs/intro/install/) CLI ツール

- [Bash バージョン 4](https://www.gnu.org/software/bash/)

- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) コマンドライン ツール

- [Log Analytics ワークスペース。](../platform/design-logs-deployment.md)

    Azure Monitor for containers では、Azure の[リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)に関するページに一覧表示されているリージョンの Log Analytics ワークスペースがサポートされます。 ワークスペースは、[Azure Resource Manager](../platform/template-workspace-configuration.md)、[PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../learn/quick-create-workspace.md) のいずれかを使用して作成できます。

- Azure Monitor for containers の機能を有効にしてアクセスするには、少なくとも、Azure サブスクリプションの Azure "*共同作成者*" ロールと、Azure Monitor for containers で構成された Log Analytics ワークスペースの "[*Log Analytics 共同作成者*](../platform/manage-access.md#manage-access-using-azure-permissions)" ロールを持っている必要があります。

- 監視データを表示するには、Azure Monitor for containers で構成された Log Analytics ワークスペースの "[*Log Analytics 閲覧者*](../platform/manage-access.md#manage-access-using-azure-permissions)" ロールを持っている必要があります。

## <a name="enable-monitoring-for-an-existing-cluster"></a>既存のクラスターの監視を有効にする

提供されている Bash スクリプトを使用して Azure にデプロイされている Azure Red Hat OpenShift バージョン 4 以降のクラスターの監視を有効にするには、次の手順を行います。

1. 次のコマンドを実行して Azure にサインインします。

    ```azurecli
    az login
    ```

1. 次のコマンドを実行して、監視アドインを使用してクラスターを構成するスクリプトをローカル フォルダーにダウンロードし、保存します。

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. クラスターの *kubeContext* を特定するには、次のコマンドを実行します。

    ```
    adminUserName=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminUsername' -o tsv)
    adminPassword=$(az aro list-credentials -g $clusterResourceGroup -n $clusterName --query 'kubeadminPassword' -o tsv)
    apiServer=$(az aro show -g $clusterResourceGroup -n $clusterName --query apiserverProfile.url -o tsv)
    oc login $apiServer -u $adminUserName -p $adminPassword
    # openshift project name for azure monitor for containers
    openshiftProjectName="azure-monitor-for-containers"
    oc new-project $openshiftProjectName
    # get the kube config context
    kubeContext=$(oc config current-context)
    ```

1. 後で使用するために値をコピーします。

### <a name="integrate-with-an-existing-workspace"></a>既存のワークスペースと統合する

このセクションでは、前の手順でダウンロードした Bash スクリプトを使用してクラスターの監視を有効にします。 既存の Log Analytics ワークスペースと統合するには、まず、`logAnalyticsWorkspaceResourceId` パラメーターに必要な Log Analytics ワークスペースの完全なリソース ID を特定してから、コマンドを実行して、指定したワークスペースに対して監視アドインを有効にします。

指定するワークスペースがない場合は、「[既定のワークスペースと統合する](#integrate-with-the-default-workspace)」セクションに進み、スクリプトを使用して新しいワークスペースの作成を自動実行します。

1. 次のコマンドを実行して、アクセス権のあるすべてのサブスクリプションを一覧表示します。

    ```azurecli
    az account list --all -o table
    ```

    出力は次のようになります。

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. **SubscriptionId** の値をコピーします。

1. 次のコマンドを実行して、Log Analytics ワークスペースをホストするサブスクリプションに切り替えます。

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. 次のコマンドを実行して、サブスクリプション内のワークスペースのリストを既定の JSON 形式で表示します。

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. 出力でワークスペース名を探し、その Log Analytics ワークスペースについてフィールド **ID** にある完全なリソース ID をコピーします。

1. 監視を有効にするには、次のコマンドを実行します。 `azureAroV4ClusterResourceId`、`logAnalyticsWorkspaceResourceId`、`kubeContext` の各パラメーターの値を置き換えます。

    ```bash
    export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
    export logAnalyticsWorkspaceResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>”
    export kubeContext="<kubeContext name of your ARO v4 cluster>"  
    ```

    例:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext --workspace-id $logAnalyticsWorkspaceResourceId`

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="integrate-with-the-default-workspace"></a>既定のワークスペースと統合する

このセクションでは、ダウンロードした Bash スクリプトを使用して、Azure Red Hat OpenShift v4. x クラスターの監視を有効にします。

この例では、ワークスペースを事前に作成したり、既存のワークスペースを指定したりする必要はありません。 このコマンドでは、リージョンにワークスペースがまだ存在しない場合、クラスター サブスクリプションの既定のリソース グループに既定のワークスペースが作成されるので、プロセスが簡単になります。

作成される既定のワークスペースは、*DefaultWorkspace-\<GUID>-\<Region>* の形式になります。  

`azureAroV4ClusterResourceId` パラメーターと `kubeContext` パラメーターの値を置き換えます。

```bash
export azureAroV4ClusterResourceId=“/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>”
export kubeContext="<kubeContext name of your ARO v4 cluster>"
```

次に例を示します。

`bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --kube-context $kubeContext`

監視を有効にした後、クラスターの正常性メトリックが表示されるまで、約 15 分かかる場合があります。

### <a name="enable-monitoring-from-the-azure-portal"></a>Azure portal から監視を有効にする

Azure Monitor for containers のマルチクラスター ビューでは、 **[監視対象外クラスター]** タブでは、監視が有効になっていない Azure Red Hat OpenShift クラスターが強調表示されます。クラスターの横にある **[有効にする]** オプションを選択しても、ポータルからの監視のオンボードは開始されません。 この記事で既に説明した手順に従って手動で監視を有効にするように、この記事にリダイレクトされます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウまたはホーム ページで、 **[Azure Monitor]** を選択します。

1. **[分析情報]** セクションで **[コンテナー]** を選択します。

1. **[モニター - コンテナー]** ページで、 **[Unmonitored clusters]\(監視対象外のクラスター\)** を選択します。

1. 監視されていないクラスターのリストで、クラスターを選択し、 **[有効にする]** を選択します。

    リストで結果を確認するには、 **[クラスターの種類]** 列で **ARO** 値を探します。 **[有効にする]** を選択すると、この記事にリダイレクトされます。

## <a name="next-steps"></a>次のステップ

- RedHat OpenShift バージョン 4.x クラスターの正常性とリソース使用率、およびそれらで実行されているワークロードを収集するための監視を有効にしたので、Azure Monitor for containers の[使用方法](container-insights-analyze.md)について学習します。

- 既定では、コンテナー化されたエージェントによって、kube-system を除くすべての名前空間で実行されているすべてのコンテナーの *stdout* および *stderr* コンテナー ログが収集されます。 特定の名前空間に固有のコンテナー ログ収集を構成するには、[Container Insights エージェントの構成](container-insights-agent-config.md)に関するページを確認して、*ConfigMap* 構成ファイルに必要なデータ収集設定を構成します。

- クラスターから Prometheus メトリックをスクレイピングして分析するには、[Prometheus メトリックのスクレイピングの構成](container-insights-prometheus-integration.md)に関するページをご確認ください。

- Azure Monitor for containers を使用してクラスターの監視を停止する方法については、「[お使いの Azure Red Hat OpenShift クラスターの監視を停止する方法](./container-insights-optout-openshift-v3.md)」を参照してください。
