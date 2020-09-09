---
title: 論理的な組織化のためにリソース、リソース グループ、サブスクリプションにタグを付ける
description: タグを適用して、課金や管理のために Azure リソースを整理する方法を示します。
ms.topic: conceptual
ms.date: 07/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: daedb5dcd660ec2637557fe5af75db2939318495
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499995"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>タグを使用して Azure リソースと整理階層を整理する

Azure リソース、リソース グループ、サブスクリプションにタグを適用して、論理的な分類に整理できます。 各タグは、名前と値のペアで構成されます。 たとえば、運用環境のすべてのリソースには名前 "環境" と値 "運用" を適用できます。

タグ付け戦略の実装方法に関する推奨事項については、「[リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)」を参照してください。

> [!IMPORTANT]
> 操作のタグの名前は大文字と小文字が区別されません。 大文字と小文字の区別に関係なく、タグ名を持つタグが更新または取得されます。 ただし、リソース プロバイダーでは、タグ名に指定した大文字と小文字がそのまま保持される可能性があります。 コスト レポートにはその大文字と小文字で表示されます。
> 
> タグの値は大文字と小文字が区別されます。

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>必要なアクセス

リソースにタグを適用するには、**Microsoft.Resources/tags** リソースの種類に対する書き込みアクセス権が必要です。 [タグ共同作成者](../../role-based-access-control/built-in-roles.md#tag-contributor)ロールを使用すると、エンティティ自体へのアクセス権がなくても、エンティティにタグを適用できます。 現時点では、タグの共同作成者ロールでは、ポータルからリソースまたはリソース グループにタグを適用することはできません。 ポータルを使用したサブスクリプションへのタグの適用は可能です。 PowerShell と REST API によるすべてのタグ操作がサポートされます。  

[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロールでも、任意のエンティティにタグを適用するために必要なアクセス権が付与されます。 1 つのリソースの種類だけにタグを適用するには、そのリソースの共同作成者ロールを使用します。 たとえば、仮想マシンにタグを適用するには、[仮想マシン共同作成者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)を使用します。

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>タグを適用する

Azure PowerShell には、タグを適用するために、[New-AzTag](/powershell/module/az.resources/new-aztag) と [Update-AzTag](/powershell/module/az.resources/update-aztag) の 2 つのコマンドが用意されています。 Az.Resources モジュール 1.12.0 以降が必要です。 `Get-Module Az.Resources` を使用して、お使いのバージョンを確認できます。 そのモジュールをインストールすることも、[Azure PowerShell をインストール](/powershell/azure/install-az-ps) (3.6.1 以降) することもできます。

**New-AzTag** では、リソース、リソース グループ、またはサブスクリプションのすべてのタグが置き換えられます。 コマンドを呼び出すときに、タグを付けるエンティティのリソース ID を渡します。

次の例では、一連のタグがストレージ アカウントに適用されます。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

コマンドが完了すると、リソースに 2 つのタグが付いていることがわかります。

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

異なるタグを指定してコマンドをもう一度実行すると、前のタグが削除されることに注意してください。

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

既にタグがあるリソースにタグを追加するには、**Update-AzTag** を使用します。 **-Operation** パラメーターを **Merge** に設定します。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

2 つの新しいタグが既存の 2 つのタグに追加されていることに注意してください。

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

各タグ名に対して設定できる値は 1 つだけです。 タグに新しい値を指定すると、マージ操作を使用した場合でも、古い値が置き換えられます。 次の例では、Status タグを Normal から Green に変更します。

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

**-Operation** パラメーターを **Replace** に設定すると、既存のタグが新しいタグのセットに置き換えられます。

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

リソースには新しいタグだけが残ります。

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

同じコマンドを、リソース グループまたはサブスクリプションに対しても使用できます。 タグを付けるリソース グループまたはサブスクリプションの識別子を渡します。

リソース グループに新しいタグのセットを追加する場合の使用方法は、次のとおりです。

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

リソース グループのタグを更新する場合の使用方法は、次のとおりです。

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

サブスクリプションに新しいタグのセットを追加する場合の使用方法は、次のとおりです。

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

サブスクリプションのタグを更新する場合の使用方法は、次のとおりです。

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

1 つのリソース グループに同じ名前のリソースが複数存在する場合があります。 その場合は、次のコマンドを使用して各リソースを設定できます。

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>タグの一覧を表示する

リソース、リソース グループ、またはサブスクリプションのタグを取得するには、[Get-AzTag](/powershell/module/az.resources/get-aztag) コマンドを使用して、エンティティのリソース ID を渡します。

リソースのタグを表示するには、次のように使用します。

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

リソース グループのタグを表示するには、次のように使用します。

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

サブスクリプションのタグを表示するには、次のように使用します。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>タグで一覧を取得する

特定のタグ名と値を持つリソースを取得するには、次のように使用します。

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

特定のタグ名と任意のタグ値を持つリソースを取得するには、次のように使用します。

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

特定のタグ名と値を持つリソース グループを取得するには、次のように使用します。

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>タグを削除する

特定のタグを削除するには、**Update-AzTag** を使用し、 **-Operation** を **Delete** に設定します。 削除するタグを渡します。

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

指定したタグが削除されます。

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

すべてのタグを削除するには、[Remove-AzTag](/powershell/module/az.resources/remove-aztag) コマンドを使用します。

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>タグを適用する

リソース グループまたはリソースにタグを追加する場合は、既存のタグを上書きするか、既存のタグに新しいタグを追加することができます。

リソースのタグを上書きするには、次のコマンドを使用します。

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

リソースの既存のタグにタグを追加するには、次のコマンドを使用します。

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

リソース グループの既存のタグを上書きするには、次のコマンドを使用します。

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

リソース グループの既存のタグにタグを追加するには、次のコマンドを使用します。

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

現在、Azure CLI には、サブスクリプションにタグを適用するコマンドはありません。 ただし、CLI を使用して、サブスクリプションにタグを適用する ARM テンプレートをデプロイできます。 「[リソース グループまたはサブスクリプションにタグを適用する](#apply-tags-to-resource-groups-or-subscriptions)」を参照してください。

### <a name="list-tags"></a>タグの一覧を表示する

リソースの既存のタグを表示するには、次のように使用します。

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

リソース グループの既存のタグを表示するには、次のようにします。

```azurecli-interactive
az group show -n examplegroup --query tags
```

このスクリプトは次の形式を返します。

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>タグで一覧を取得する

特定のタグと値を持つすべてのリソースを取得するには、`az resource list` を使用します。

```azurecli-interactive
az resource list --tag Dept=Finance
```

特定のタグが付いたリソース グループを取得するには、`az group list` を使用します。

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>スペースを処理する

タグの名前または値にスペースが含まれている場合は、いくつかの追加手順を実行する必要があります。 次の例では、タグにスペースが含まれている場合に、リソース グループのすべてのタグをそのリソースに適用します。

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>テンプレート

Resource Manager テンプレートによるデプロイの間に、リソース、リソース グループ、サブスクリプションにタグを付けることができます。

### <a name="apply-values"></a>値を適用する

次の例では、3 つのタグが適用されたストレージ アカウントがデプロイされます。 2 つのタグ (`Dept` と `Environment`) にはリテラル値が設定されます。 1 つのタグ (`LastDeployed`) には、現在の日付が既定値のパラメーターが設定されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>オブジェクトを適用する

複数のタグを格納するオブジェクト パラメーターを定義し、そのオブジェクトをタグ要素に適用できます。 この方法では、オブジェクトは異なるプロパティを持つことができるため、前の例より柔軟性が高くなります。 オブジェクト内の各プロパティがリソースの個々のタグになります。 次の例は、タグ要素に適用される `tagValues` という名前のパラメーターを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>JSON 文字列を適用する

1 つのタグに複数の値を格納するには、値を表す JSON 文字列を適用します。 JSON 文字列全体は、1 つのタグとして格納されます。256 文字以下にする必要があります。 次の例は、JSON 文字列で複数の値を格納する `CostCenter` という名前の 1 つのタグを示しています。  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>リソース グループからタグを適用する

リソース グループからリソースにタグを適用するには、[resourceGroup()](../templates/template-functions-resource.md#resourcegroup) 関数を使います。 タグの値を取得するときは、`tags.tag-name` 構文ではなく `tags[tag-name]` 構文を使います。これは、ドット表記では一部の文字が正しく解析されないためです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>リソース グループまたはサブスクリプションにタグを適用する

リソースの種類 **Microsoft.Resources/tags** をデプロイすることにより、リソース グループまたはサブスクリプションにタグを追加できます。 タグは、デプロイのターゲット リソース グループまたはサブスクリプションに適用されます。 テンプレートをデプロイするたびに、以前に適用したタグがすべて置き換えられます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

リソース グループにタグを適用するには、PowerShell または Azure CLI を使用します。 タグを付けるリソース グループにデプロイします。

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

サブスクリプションにタグを適用するには、PowerShell または Azure CLI を使用します。 タグを付けるサブスクリプションにデプロイします。

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

サブスクリプション デプロイの詳細については、「[サブスクリプション レベルでリソース グループとリソースを作成する](../templates/deploy-to-subscription.md)」を参照してください。

次のテンプレートでは、オブジェクトからリソース グループまたはサブスクリプションにタグが追加されます。

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>ポータル

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure REST API でタグを操作するには、次のように使用します。

* [Tags - スコープを指定して作成または更新する](/rest/api/resources/tags/createorupdateatscope) (PUT 操作)
* [Tags - スコープを指定して更新する](/rest/api/resources/tags/updateatscope) (PATCH 操作)
* [Tags - スコープを指定して取得する](/rest/api/resources/tags/getatscope) (GET 操作)
* [Tags - スコープを指定して削除する](/rest/api/resources/tags/deleteatscope) (DELETE 操作)

## <a name="inherit-tags"></a>タグを継承する

リソース グループまたはサブスクリプションに適用されたタグは、リソースによって継承されません。 サブスクリプションまたはリソース グループのタグをリソースに適用するには、[タグに関する Azure Policy](tag-policies.md) についての記事を参照してください。

## <a name="tags-and-billing"></a>タグと課金

タグを使用して課金データをグループ化できます。 たとえば、異なる組織向けに複数の VM を実行している場合は、タグを使用して、コスト センターごとに使用状況をグループ化します。 また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、[Azure Resource Usage API や Rate Card API](../../cost-management-billing/manage/usage-rate-card-overview.md)、またはコンマ区切り値 (CSV) ファイルから取得できます。 使用状況ファイルは [Azure アカウント センター](https://account.azure.com/Subscriptions)または Azure portal からダウンロードします。 詳細については、「[Azure の請求書と毎日の使用状況データをダウンロードまたは表示する](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)」を参照してください。 Azure アカウント センターから使用状況ファイルをダウンロードする場合は、 **[バージョン 2]** を選択します。 課金のタグがサポートされているサービスの場合、タグは **[Tags]** 列に表示されます。

REST API の操作については、「 [Azure Billing REST API Reference (Azure Billing REST API リファレンス)](/rest/api/billing/)」を参照してください。

## <a name="limitations"></a>制限事項

タグには次の制限事項が適用されます。

* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
* 各リソース、リソース グループ、サブスクリプションには、最大で 50 個のタグ名と値のペアを付けることができます。 許可される最大数よりも多くのタグを適用する必要がある場合は、タグ値に JSON 文字列を使用します。 JSON 文字列には、1 つのタグ名に適用される値を多数含めることができます。 リソース グループまたはサブスクリプションには、それぞれ 50 個のタグ名と値のペアが付けられたリソースを多数含めることができます。
* タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。 ストレージ アカウントについては、タグ名は 128 文字まで、タグ値は 256 文字までに制限されます。
* Cloud Services など、クラシック リソースにタグを適用することはできません。
* タグ名には、これらの文字を含めることはできません: `<`、`>`、`%`、`&`、`\`、`?`、`/`

   > [!NOTE]
   > また、Azure DNS ゾーンと Traffic Manager サービスでは現在、タグ内でスペースを使用することはできません。
   >
   > Azure Front Door では、タグ名に `#` を使用できません。
   >
   > Azure Automation と Azure CDN では、リソースで 15 個のタグのみがサポートされています。

## <a name="next-steps"></a>次のステップ

* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
* タグ付け戦略の実装方法に関する推奨事項については、「[リソースの名前付けとタグ付けの意思決定ガイド](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)」を参照してください。
