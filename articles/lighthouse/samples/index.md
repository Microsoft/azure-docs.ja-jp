---
title: Azure Lighthouse のサンプルとテンプレート
description: これらのサンプルと Azure Resource Manager テンプレートでは、Azure の委任されたリソース管理に顧客をオンボードする方法を示し、Azure Lighthouse のシナリオをサポートしています。
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 07/11/2019
ms.author: jenhayes
ms.openlocfilehash: d888fb0d50bf0336f8fe830c567586e66065871f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286138"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse サンプル

次の表は、Azure Lighthouse 用の主要な Azure Resource Manager テンプレートのリンク一覧です。 [Azure Lighthouse サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)にはこれらのファイル以外にさらに多くのファイルが用意されています。

| **テンプレート** | **説明** |
|---------|---------|
| [create-multiple-rgs](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/create-multiple-rgs) | 1 つの Azure Resource Manager テンプレートを使用し、複数のリソース グループを作成します。 |
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | 2 つの異なるリソース グループにストレージ アカウントをデプロイします。 |
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Azure の委任されたリソース管理に顧客のサブスクリプションをオンボードします。 |
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure 管理サービスを作成し、それらをリンクし、追加のソリューションをデプロイします。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | 対象となる Azure サブスクリプション内で Azure Security Center を有効にし、構成します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | マーケットプレースに公開されたマネージド サービス オファーに基づき、Azure の委任されたリソース管理に顧客のサブスクリプションをオンボードします。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Azure の委任されたリソース管理に顧客のリソース グループを 1 つまたは複数オンボードします。 |

## <a name="next-steps"></a>次の手順

- [Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)について学習してください。
- Azure Resource Manager テンプレートを使用し、[Azure の委任されたリソース管理に顧客をオンボードする](../how-to/onboard-customer.md)方法について学習してください。
