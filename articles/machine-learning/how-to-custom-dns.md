---
title: カスタム DNS サーバーを使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning ワークスペースおよびプライベート エンドポイントを使用するようにカスタム DNS サーバーを構成する方法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 2215c47fcd250a9ac1d6621f7e4b434bd33b3832
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2021
ms.locfileid: "98871097"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>カスタム DNS サーバーでワークスペースを使用する方法

プライベート エンドポイントで Azure Machine Learning ワークスペースを使用する場合、[DNS 名前解決を処理する方法はいくつかあります](../private-link/private-endpoint-dns.md)。 既定では、ワークスペースとプライベート エンドポイントの名前解決は、Azure によって自動的に処理されます。 代わりに "_独自のカスタム DNS サーバーを使用する場合_" は、ワークスペース用に DNS エントリを手動で作成したり、条件付きフォワーダーを使用する必要があります。

> [!IMPORTANT]
> この記事では、これらのエントリの完全修飾ドメイン名 (FQDN) と IP アドレスを検索する方法についてのみ説明します。これらの項目の DNS レコードを構成する方法については説明しません。 レコードを追加する方法については、DNS ソフトウェアのドキュメントを参照してください。

## <a name="prerequisites"></a>[前提条件]

- [独自の DNS サーバー](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)を使用する Azure Virtual Network。

- プライベート エンドポイントを持つ Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。

- [トレーニングおよび推論中にネットワーク分離](./how-to-network-security-overview.md)を使用することに精通していること。

- [Azure プライベート エンドポイントの DNS ゾーン構成](../private-link/private-endpoint-dns.md)に関する知識。

- 必要に応じて、[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/install-az-ps)。

## <a name="fqdns-in-use"></a>使用中の FQDN
### <a name="these-fqdns-are-in-use-in-the-following-regions-eastus-southcentralus-and-westus2"></a>これらの FQDN は、eastus、southcentralus、westus2 の各リージョンで使用されています。
次のリストには、ワークスペースで使用される完全修飾ドメイン名 (FQDN) が含まれています。

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* コンピューティング インスタンスを作成する場合は、ワークスペースのプライベート エンドポイントのプライベート IP を持つ `<instance-name>.<region>.instances.azureml.ms` のエントリも追加する必要があります。

    > [!NOTE]
    > コンピューティング インスタンスには、仮想ネットワーク内からのみアクセスできます。
    
### <a name="these-fqdns-are-in-use-in-all-other-regions"></a>これらの FQDN は、他のすべてのリージョンで使用されています
次のリストには、ワークスペースで使用される完全修飾ドメイン名 (FQDN) が含まれています。

* `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.net`
* `<instance-name>.<region>.instances.azureml.ms`

    > [!NOTE]
    > コンピューティング インスタンスには、仮想ネットワーク内からのみアクセスできます。

## <a name="find-the-ip-addresses"></a>IP アドレスを検索する

VNet 内の FQDN の内部 IP アドレスを検索するには、次のいずれかの方法を使用します。

> [!NOTE]
> 完全修飾ドメイン名と IP アドレスは、構成によって異なります。 たとえば、ドメイン名の GUID 値は、ワークスペースに固有です。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com) で、Azure Machine Learning __ワークスペース__ を選択します。
1. __[設定]__ セクションから、 __[プライベート エンドポイント接続]__ を選択します。
1. 表示される __[プライベート エンドポイント]__ 列のリンクを選択します。
1. ワークスペースのプライベート エンドポイントの完全修飾ドメイン名 (FQDN) と IP アドレスのリストがページの下部にあります。

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="ポータルの FQDN のリスト":::

---

どの方法でも返される情報は同じで、リソースの FQDN とプライベート IP アドレスのリストです。

| FQDN | IP アドレス |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> 一部の FQDN は、プライベート エンドポイントによってリストに表示されませんが、eastus、southcentralus、westus2 のワークスペースで必要です。 これらの FQDN は、次の表に示すように、DNS サーバーまたは Azure プライベート DNS ゾーンにも追加する必要があります。
>
> * `<workspace-GUID>.workspace.<region>.cert.api.azureml.ms`
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * コンピューティング インスタンスがある場合は、`<instance-name>.<region>.instances.azureml.ms` を使用します。ここで `<instance-name>` は、コンピューティング インスタンスの名前です。 ワークスペースのプライベート エンドポイントのプライベート IP アドレスを使用してください。 コンピューティング インスタンスには、仮想ネットワーク内からのみアクセスできることに注意してください。
>
> これらの IP アドレスのすべてに対して、前のステップで返された `*.api.azureml.ms` エントリと同じアドレスを使用します。

## <a name="next-steps"></a>次の手順

仮想ネットワークでの Azure Machine Learning の使用の詳細については、[仮想ネットワークの概要](how-to-network-security-overview.md)に関するページを参照してください。

プライベート エンドポイントの DNS 構成への統合については、「[Azure プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-dns.md)」をご覧ください。