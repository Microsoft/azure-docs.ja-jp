---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b27a4d58280f48c491efb6fb1668481092791a10
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99215148"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |カスタマー マネージド キーを使用して、レジストリのコンテンツ保存時の暗号化を管理します。 既定では、データはサービス マネージド キーを使用して保存時に暗号化されますが、規制コンプライアンス標準を満たすためには一般に、カスタマー マネージド キー (CMK) が必要です。 CMK を使用すると、自分が作成して所有する Azure Key Vault キーを使用してデータを暗号化できます。 ローテーションや管理など、キーのライフサイクルを完全に制御し、責任を負うことになります。 CMK 暗号化の詳細については、[https://aka.ms/acr/CMK](https://aka.ms/acr/CMK) を参照してください。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[コンテナー レジストリでは無制限のネットワーク アクセスを許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |既定では、Azure コンテナー レジストリは、任意のネットワーク上のホストからのインターネット経由の接続を受け入れます。 潜在的な脅威からレジストリを保護するには、特定のパブリック IP アドレスまたはアドレス範囲のみからのアクセスを許可します。 レジストリに IP またはファイアウォール規則、あるいは構成済みの仮想ネットワークがない場合は、異常なリソースとして表示されます。 Container Registry ネットワーク規則の詳細については、[https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) と [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[コンテナー レジストリではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure バックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。サービス全体ではなく、コンテナー レジストリにプライベート エンドポイントをマッピングすることで、データ漏洩のリスクからも保護されます。 詳細については、[https://aka.ms/acr/private-link](https://aka.ms/acr/private-link) を参照してください。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Azure Container Registry イメージの脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f0f936f-2f01-4bf5-b6be-d423792fa562) |コンテナー イメージの脆弱性評価では、プッシュされた各コンテナー イメージのセキュリティ脆弱性についてご利用のレジストリがスキャンされ、各イメージの詳細な結果が公開されます (Qualys を使用)。 脆弱性を解決することで、お使いのコンテナーのセキュリティ体制が大幅に向上し、それらを攻撃から保護できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerRegistryVulnerabilityAssessment_Audit.json) |
