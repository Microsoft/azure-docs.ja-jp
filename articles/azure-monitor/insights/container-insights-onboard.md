---
title: Azure Monitor for containers を有効にする | Microsoft Docs
description: この記事では、コンテナーのパフォーマンス、および明らかになっているパフォーマンスに関する問題を把握できるように、Azure Monitor for containers を有効にして構成する方法について説明します。
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 1cb393faded714593ce635669f585d5979ee69b8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320308"
---
# <a name="enable-azure-monitor-for-containers"></a>Azure Monitor for containers を有効にする

この記事では、Kubernetes 環境にデプロイされ、以下の上でホストされているワークロードのパフォーマンスを監視することを目的として、Azure Monitor for containers を設定するために使用できるオプションの概要について説明します。

- [Azure Kubernetes Service (AKS)](../../aks/index.yml)  
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) バージョン 3.x、4.x  
- [Red Hat OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) バージョン 4.x  
- [Arc 対応 Kubernetes クラスター](../../azure-arc/kubernetes/overview.md)

また、以下でホストされている自己管理型 Kubernetes クラスターにデプロイされているワークロードのパフォーマンスを監視することもできます。
- Azure ([AKS エンジン](https://github.com/Azure/aks-engine)を使用)
- [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) またはオンプレミス (AKS エンジンを使用)。

次のいずれかのサポートされている方法を使用して、Kubernetes の新しいデプロイまたは 1 つ以上の既存のデプロイに対して、Azure Monitor for containers を有効にできます。

- Azure ポータル
- Azure PowerShell
- Azure CLI
- [Terraform と AKS](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

始める前に、次の要件を満たしていることを確認します。

- Log Analytics ワークスペースがあります。

   Azure Monitor for containers では、[リージョンごとに使用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)の一覧で示されているリージョンの Log Analytics ワークスペースがサポートされます。

   新しい AKS クラスターに対する監視を有効にするときにワークスペースを作成すること、またはオンボード エクスペリエンスを使用して AKS クラスター サブスクリプションの既定のリソース グループに既定のワークスペースを作成することができます。 
   
   自分でワークスペースを作成する場合は、次を使用して作成できます。 
   - [Azure Resource Manager](../platform/template-workspace-configuration.md)
   - [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)
   - [Azure ポータル](../learn/quick-create-workspace.md) 
   
   既定のワークスペースに使用する、サポートされているマッピング ペアの一覧については、[Azure Monitor for containers のリージョンのマッピング](container-insights-region-mapping.md)に関するページを参照してください。

- コンテナーの監視を有効にする *Log Analytics 共同作成者ロール*のメンバーです。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../platform/manage-access.md)」を参照してください。

- AKS クラスター リソースに対する ["*所有者*" グループ](../../role-based-access-control/built-in-roles.md#owner)のメンバーです。

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- 監視データを表示するには、Log Analytics ワークスペースでの [*Log Analytics 閲覧者*](../platform/manage-access.md#manage-access-using-azure-permissions)ロールが、Azure Monitor for containers で構成されている必要があります。

- 既定では、Prometheus のメトリックは収集されません。 メトリックを収集するための[エージェントを構成する](container-insights-prometheus-integration.md)前に、[Prometheus のドキュメント](https://prometheus.io/)を確認して、スクレイピングできるデータおよびサポートされているメソッドを理解しておくことが重要です。

## <a name="supported-configurations"></a>サポートされている構成

Azure Monitor for containers では、以下の構成が公式にサポートされています。

- 環境:Azure Red Hat OpenShift、オンプレミスの Kubernetes、Azure および Azure Stack 上の AKS エンジン。 詳細については、[Azure Stack 上の AKS エンジン](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)に関するページを参照してください。
- Kubernetes のバージョンとサポート ポリシーは、[Azure Kubernetes Service (AKS) でサポートされている](../../aks/supported-kubernetes-versions.md)ものと同じです。 

## <a name="network-firewall-requirements"></a>ネットワーク ファイアウォールの要件

次の表は、コンテナー化されたエージェントが Azure Monitor for containers と通信するために必要なプロキシおよびファイアウォールの構成情報をまとめたものです。 エージェントからのすべてのネットワーク トラフィックは、Azure Monitor に送信されます。

|エージェントのリソース|Port |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

次の表は、Azure China 21Vianet のプロキシとファイアウォールの構成情報をまとめたものです。

|エージェントのリソース|Port |説明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | データ インジェスト |
| `*.oms.opinsights.azure.cn` | 443 | OMS のオンボード |
| `dc.services.visualstudio.com` | 443 | Azure パブリック クラウド Application Insights を使用するエージェント テレメトリの場合 |

次の表は、Azure US Government のプロキシとファイアウォールの構成情報をまとめたものです。

|エージェントのリソース|Port |説明 | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | データ インジェスト |
| `*.oms.opinsights.azure.us` | 443 | OMS のオンボード |
| `dc.services.visualstudio.com` | 443 | Azure パブリック クラウド Application Insights を使用するエージェント テレメトリの場合 |

## <a name="components"></a>コンポーネント

パフォーマンスを監視する能力は、Azure Monitor for containers用に特別に開発された、Linux 用のコンテナー化 Log Analytics エージェントに依存します。 この特殊なエージェントは、クラスター内のすべてのノードからパフォーマンスとイベント データを収集します。エージェントは自動的にデプロイされ、デプロイ時に指定した Log Analytics ワークスペースに登録されます。 

このエージェントのバージョンは microsoft/oms:ciprod04202018 以降であり、*mmddyyyy* という形式の日付で表されます。

>[!NOTE]
>一般提供されるようになった Windows Server による AKS のサポートでの、Windows Server ノードが含まれる AKS クラスターでは、ログを収集して Log Analytics に転送するため、個々の Windows Server ノードに、デーモンセット ポッドとしてプレビュー エージェントがインストールされています。 パフォーマンス メトリックの場合は、標準のデプロイの一部としてクラスターに自動的にデプロイされる Linux ノードにより、クラスターのすべての Windows ノードに代わってデータが収集されて、Azure Monitor に転送されます。

エージェントの新しいバージョンがリリースされた場合でも、Azure Kubernetes Service (AKS) でホストされているマネージド Kubernetes クラスター上のエージェントが自動的にアップグレードされることはありません。 リリースされているバージョンを追跡するには、[エージェントのリリースのお知らせ](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)を参照してください。

> [!NOTE]
> AKS クラスターを既にデプロイしてある場合は、この記事で後ほど示すように、Azure CLI または提供されている Azure Resource Manager テンプレートを使用して、監視を有効にします。 `kubectl` を使用して、エージェントのアップグレード、削除、再デプロイ、デプロイを行うことはできません。
>
> テンプレートはクラスターと同じリソース グループ内に展開する必要があります。

Azure Monitor for containers を有効にするには、次の表で説明されている方法のいずれかを使用します。

| デプロイの状態 | Method | 説明 |
|------------------|--------|-------------|
| 新しい Kubernetes クラスター | [Azure CLI を使用して AKS クラスターを作成する](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Azure CLI を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| | [Terraform を使用して AKS クラスターを作成する](container-insights-enable-new-cluster.md#enable-using-terraform)| オープンソースのツールである Terraform を使用して作成する新しい AKS クラスターの監視を有効にできます。 |
| | [Azure Resource Manager テンプレートを使用して OpenShift クラスターを作成する](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | 事前構成済みの Azure Resource Manager テンプレートを使用して作成した新しい OpenShift クラスターの監視を有効にできます。 |
| | [Azure CLI を使用して OpenShift クラスターを作成する](/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Azure CLI を使用して新しい OpenShift クラスターをデプロイするときに、監視を有効にすることができます。 |
| 既存の Kubernetes クラスター | [Azure CLI を使用して AKS クラスターの監視を有効にする](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Azure CLI を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| |[Terraform を使用して AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-using-terraform) | オープンソースのツールである Terraform を使用して既にデプロイされている AKS クラスターの監視を有効にできます。 |
| | [Azure Monitor から AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Azure Monitor のマルチクラスター ページから、既にデプロイされている 1 つまたは複数の AKS クラスターの監視を有効にできます。 |
| | [AKS クラスターから有効にする](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Azure portal の AKS クラスターから直接監視を有効にできます。 |
| | [Azure Resource Manager テンプレートを使用して AKS クラスターを有効にする](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| 事前構成済みの Azure Resource Manager テンプレートを使用して AKS クラスターの監視を有効にできます。 |
| | [ハイブリッド Kubernetes クラスターの場合に有効にする](container-insights-hybrid-setup.md) | Azure Stack でホストされている AKS エンジン、またはオンプレミスでホストされている Kubernetes クラスターの監視を有効にすることができます。 |
| | [Arc 対応の Kubernetes クラスターに対して有効にする](container-insights-enable-arc-enabled-clusters.md) | Azure の外部でホストされ、Azure Arc で有効にされている Kubernetes クラスターの監視を有効にすることができます。 |
| | [Azure Resource Manager テンプレートを使用して OpenShift クラスターを有効にする](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | 事前構成済みの Azure Resource Manager テンプレートを使用して既存の OpenShift クラスターの監視を有効にすることができます。 |
| | [Azure Monitor から OpenShift クラスターを有効にする](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Azure Monitor のマルチクラスター ページから、既にデプロイされている 1 つまたは複数の OpenShift クラスターの監視を有効にすることができます。 |

## <a name="next-steps"></a>次のステップ

監視を有効にしたので、Azure Kubernetes Service (AKS)、Azure Stack、または他の環境でホストされている Kubernetes クラスターのパフォーマンスの分析を開始できます。 コンテナー用 Azure Monitor を使用する方法については、[Kubernetes クラスターのパフォーマンスの表示](container-insights-analyze.md)に関するページをご覧ください。

