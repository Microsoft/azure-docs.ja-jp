---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 830bf1223966d7e5df1a7feaa2f4358e5398e211
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100257"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cosmos DB のアカウントにはファイアウォール規則を含める必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F862e97cf-49fc-4a5c-9de4-40d4e2e7c8eb) |承認されていないソースからのトラフィックを防ぐために、Azure Cosmos DB アカウントに対してファイアウォール規則を定義する必要があります。 仮想ネットワーク フィルターを有効にして定義されている IP 規則が少なくとも 1 つあるアカウントは、準拠していると見なされます。 パブリック アクセスを無効にしているアカウントも、準拠していると見なされます。 |Audit、Deny、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_NetworkRulesExist_Audit.json) |
|[Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f905d99-2ab7-462c-a6b0-f709acca6c8f) |カスタマー マネージド キーを使用して、Azure Cosmos DB の保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすには、一般にカスタマー マネージド キーが必要です。 カスタマー マネージド キーを使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 詳細については、[https://aka.ms/cosmosdb-cmk](https://aka.ms/cosmosdb-cmk) をご覧ください。 |audit、deny、disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_CMK_Deny.json) |
|[Azure Cosmos DB が許可されている場所](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |このポリシーでは、Azure Cosmos DB リソースをデプロイするときに組織が指定できる場所を制限できます。 geo コンプライアンス要件を強制するために使用されます。 |[parameters('policyEffect')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Azure Cosmos DB キー ベースのメタデータ書き込みアクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |このポリシーを使用すると、すべての Azure Cosmos DB アカウントでキー ベースのメタデータ書き込みアクセスを無効にすることができます。 |append |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[Azure Cosmos DB のスループットを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |このポリシーを使用すると、Azure Cosmos DB データベースとコンテナーをリソースプロバイダーを介して作成するときに、組織で指定できる最大スループットを制限できます。 自動スケーリング リソースの作成をブロックします。 |audit、deny、disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[Cosmos DB アカウントの Advanced Threat Protection のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |このポリシーを使用して、Cosmos DB アカウント全体で Advanced Threat Protection を有効にすることができます。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
