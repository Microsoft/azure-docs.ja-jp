---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e90ac3aa17d2dfcb4e71ded90337bf88e1f12180
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497347"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Configuration でパブリック ネットワーク アクセスを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d9f5e4c-9947-4579-9539-2a7695fbc187) |公衆ネットワーク アクセスを無効にすれば、パブリック インターネットにリソースが露出されないのでセキュリティが向上します。 プライベート エンドポイントを作成することによってリソースの露出を制限することもできます。 詳細については、[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_PublicNetworkAccess_Audit.json) |
|[App Configuration はカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |カスタマー マネージド キーは、暗号化キーを管理できるようにすることで、データ保護を強化します。 これは、多くの場合、コンプライアンス要件を満たすために必要となります。 |Audit、Deny、Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json) |
|[App Configuration ではプライベート リンクをサポートする SKU を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89c8a434-18f0-402c-8147-630a8dea54e0) |サポートされる SKU を使用すると、Azure Private Link により、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) を参照してください。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_AllowedSku_Audit.json) |
|[App Configuration ではプライベート リンクを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Azure Private Link を使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 プライベート リンク プラットフォームでは、Azure のバックボーン ネットワークを介してコンシューマーとサービスの間の接続が処理されます。 サービス全体ではなく、アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクからも保護されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) を参照してください。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json) |
|[App Configuration でパブリック ネットワーク アクセスを無効に構成する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F73290fa2-dfa7-4bbb-945d-a5e23b75df2c) |App Configuration のパブリック ネットワーク アクセスを無効にして、パブリック インターネット経由ではアクセスできないようにします。 この構成は、データ漏えいリスクへの対策として役立ちます。 プライベート エンドポイントを作成することによってリソースの露出を制限することもできます。 詳細については、[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) を参照してください。 |Modify、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_PublicNetworkAccess_Modify.json) |
|[App Configuration のプライベート エンドポイントの構成](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F614ffa75-862c-456e-ad8b-eaa1b0844b07) |プライベート エンドポイントを使用すると、接続元または接続先にパブリック IP アドレスを使用せずに、仮想ネットワークを Azure サービスに接続できます。 アプリ構成インスタンスにプライベート エンドポイントをマッピングすることで、データ漏えいのリスクが軽減されます。 詳細については、[https://aka.ms/appconfig/private-endpoint](https://aka.ms/appconfig/private-endpoint) を参照してください。 |DeployIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Deploy.json) |
