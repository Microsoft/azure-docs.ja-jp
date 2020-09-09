---
title: Azure Monitor for containers のリージョン マッピング
description: この記事では、Azure Monitor for containers、Log Analytics ワークスペース、およびカスタム メトリックの間でサポートされるリージョン マッピングについて説明します。
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: references_regions
ms.openlocfilehash: 3e8ead78c5e0e534e07c1e2ab0e25eb3f5a90c38
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84194985"
---
# <a name="region-mappings-supported-by-azure-monitor-for-containers"></a>Azure Monitor for containers でサポートされるリージョン マッピング

 Azure Monitor for containers を有効にした場合、Log Analytics ワークスペースと AKS クラスターをリンクし、Azure Monitor に送信されたカスタム メトリックを収集するために、特定のリージョンのみサポートされます。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics ワークスペースでサポートされるマッピング

AKS クラスター リソースまたは Log Analytics ワークスペースは他のリージョンに配置できます。次の表にマッピングを示します。

|**AKS クラスター リージョン** | **Log Analytics ワークスペース リージョン** |
|-----------------------|------------------------------------|
|**アフリカ** | |
|SouthAfricaNorth |西ヨーロッパ |
|SouthAfricaWest |西ヨーロッパ |
|**オーストラリア** | |
|AustraliaEast |AustraliaEast |
|AustraliaCentral |AustraliaCentral |
|AustraliaCentral2 |AustraliaCentral |
|AustraliaEast |AustraliaEast |
|**アジア太平洋** | |
|EastAsia |EastAsia |
|SoutheastAsia |SoutheastAsia |
|**ブラジル** | |
|BrazilSouth | SouthCentralUS |
|**カナダ** ||
|CanadaCentral |CanadaCentral |
|CanadaEast |CanadaCentral |
|**ヨーロッパ** | |
|FranceCentral |FranceCentral |
|FranceSouth |FranceCentral |
|NorthEurope |NorthEurope |
|UKSouth |UKSouth |
|UKWest |UKSouth |
|西ヨーロッパ |西ヨーロッパ |
|**インド** | |
|CentralIndia |CentralIndia |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanEast |JapanEast |
|JapanWest |JapanEast |
|**韓国** | |
|KoreaCentral |KoreaCentral |
|KoreaSouth |KoreaCentral |
|**米国** | |
|CentralUS |CentralUS|
|EastUS |EastUS |
|EastUS2 |EastUS2 |
|WestUS |WestUS |
|WestUS2 |WestUS2 |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|
|US Gov バージニア州 |US Gov バージニア州 |

<sup>1</sup> 容量の制約により、新しいリソースを作成するときにリージョンを使用できません。 これには Log Analytics ワークスペースが含まれます。 ただし、リージョン内のリンクされた既存のリソースは引き続き動作します。

## <a name="custom-metrics-supported-regions"></a>カスタム メトリックでサポートされるリージョン

Azure Kubernetes Services (AKS) クラスター ノードおよびポッドからのメトリックの収集は、次の [Azure リージョン](../platform/metrics-custom-overview.md#supported-regions)でのみカスタム メトリックとしての公開がサポートされています。

## <a name="next-steps"></a>次のステップ

AKS クラスターの監視を開始するには、「[Azure Monitor for containers を有効にする方法](container-insights-onboard.md)」を参照し、監視を有効にするための要件と使用できる方法を把握してください。  
