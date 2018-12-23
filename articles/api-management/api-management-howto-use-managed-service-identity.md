---
title: Azure API Management で Azure 管理対象サービス ID を使用する | Microsoft Docs
description: API Management で Azure 管理対象サービス ID を使用する方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/18/2017
ms.author: apimpm
ms.openlocfilehash: b7208943a27bcd184100ae426721a2fe8f6e1c72
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970486"
---
# <a name="use-azure-managed-service-identity-in-azure-api-management"></a>Azure API Management で Azure 管理対象サービス ID を使用する

この記事では、API Management サービス インスタンスの管理対象サービス ID を作成する方法と、その他のリソースにアクセスする方法について説明します。 Azure Active Directory (Azure AD) によって生成された管理対象サービス ID によって、API Management インスタンスは、Azure AD で保護された他のリソース (Azure Key Vault など) に簡単かつ安全にアクセスすることができます。 この管理対象サービス ID は Azure によって管理され、ユーザーがシークレットをプロビジョニングしたりローテーションしたりする必要はありません。 Azure 管理対象サービス ID について詳しくは、[Azure リソースの管理対象サービス ID](../active-directory/msi-overview.md) に関するページをご覧ください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="create-a-managed-service-identity-for-an-api-management-instance"></a>API Management インスタンスの管理されたサービス ID を作成する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

ポータルで管理対象サービス ID を設定するには、最初に通常の方法で API 管理インスタンスを作成した後、機能を有効にします。

1. ポータルを使って通常の方法で API 管理インスタンスを作成します。 ポータルでアプリに移動します。
2. **[Managed service identity]\(管理対象のサービス ID\)** を選びます。
3. [Azure Active Directory に登録する] を [オン] に切り替えます。 [保存] をクリックします。

![MSI を有効化する](./media/api-management-msi/enable-msi.png)

### <a name="using-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

ID を持った API Management インスタンスは、リソース定義に次のプロパティを含めることによって作成できます。 

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

これは、API Management インスタンスの ID を作成して管理するよう Azure に命令するものです。 

たとえば、Azure Resource Manager テンプレート全体は次のようになります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0"
    },
    "resources": [
        {
            "apiVersion": "2017-03-01",
            "name": "contoso",
            "type": "Microsoft.ApiManagement/service",
            "location": "[resourceGroup().location]",
            "tags": {},
            "sku": {
                "name": "Developer",
                "capacity": "1"
            },
            "properties": {
                "publisherEmail": "admin@contoso.com",
                "publisherName": "Contoso"
            },
            "identity": { 
                "type": "systemAssigned" 
            }
        }
    ]
}
```
## <a name="use-the-managed-service-identity-to-access-other-resources"></a>管理されたサービス ID を使用してその他のリソースにアクセスする

> [!NOTE]
> 現時点では、管理サービス ID を使用して、API Management のカスタム ドメイン名用に Azure Key Vault から証明書を取得できます。 より多くのシナリオがまもなくサポートされます。
> 
>


### <a name="obtain-a-certificate-from-azure-key-vault"></a>Azure Key Vault から証明書を取得する

#### <a name="prerequisites"></a>前提条件
1. pfx 証明書を含む Key Vault は、API Management サービスと同じ Azure サブスクリプション、同じリソース グループに属している必要があります。 これは Azure Resource Manager テンプレートの要件です。 
2. シークレットのコンテンツ タイプは *application/x-pkcs12* にする必要があります。 証明書をアップロードするには、次のスクリプトを使用します。

```powershell
$pfxFilePath = "PFX_CERTIFICATE_FILE_PATH" # Change this path 
$pwd = "PFX_CERTIFICATE_PASSWORD" # Change this password 
$flag = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable 
$collection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection 
$collection.Import($pfxFilePath, $pwd, $flag) 
$pkcs12ContentType = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12 
$clearBytes = $collection.Export($pkcs12ContentType) 
$fileContentEncoded = [System.Convert]::ToBase64String($clearBytes) 
$secret = ConvertTo-SecureString -String $fileContentEncoded -AsPlainText –Force 
$secretContentType = 'application/x-pkcs12' 
Set-AzureKeyVaultSecret -VaultName KEY_VAULT_NAME -Name KEY_VAULT_SECRET_NAME -SecretValue $Secret -ContentType $secretContentType
```

> [!Important]
> 証明書のオブジェクト バージョンを指定しなかった場合、より新しいバージョンの証明書が Key Vault にアップロードされると、その後 API Management によって自動的に取得されます。 

次の例では、次の手順を含む Azure Resource Manager テンプレートを示します。

1. 管理されたサービス ID を使用して API Management インスタンスを作成します。
2. Azure Key Vault インスタンスのアクセス ポリシーを更新し、そこからシークレットを取得することを API Management インスタンスに許可します。
3. Key Vault インスタンスからの証明書を使用してカスタム ドメイン名を設定して API Management インスタンスを更新します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management service"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management service."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the KeyVault certificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2017-03-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
            
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",        
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    { 
      "apiVersion": "2017-05-10", 
      "name": "apimWithKeyVault", 
      "type": "Microsoft.Resources/deployments",
      "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
      ],
      "properties": { 
        "mode": "incremental", 
        "templateLink": {
          "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
          "contentVersion": "1.0.0.0"
        }, 
        "parameters": {
            "publisherEmail": { "value": "[parameters('publisherEmail')]"},
            "publisherName": { "value": "[parameters('publisherName')]"},
            "sku": { "value": "[parameters('sku')]"},
            "skuCount": { "value": "[parameters('skuCount')]"},
            "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
            "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
        }
      } 
    }]
}
```

## <a name="next-steps"></a>次の手順

Azure 管理対象サービス ID の詳細を確認します。

* [Azure リソースの管理対象サービス ID](../active-directory/msi-overview.md)
* [Azure リソース マネージャーのテンプレート](https://github.com/Azure/azure-quickstart-templates)

