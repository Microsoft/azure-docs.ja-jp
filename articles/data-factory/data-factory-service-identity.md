---
title: Data Factory のマネージド ID
description: Azure Data Factory のマネージド ID について説明します。
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: jingwang
ms.openlocfilehash: 7c1de2b6ef59efdaaed64fcf687fed0c834683c0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037598"
---
# <a name="managed-identity-for-data-factory"></a>Data Factory のマネージド ID

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事は、Data Factory のマネージド ID (以前は、マネージド サービス ID/MSI と呼ばれていました) の概要と機能について理解するのに役立ちます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>概要

データ ファクトリの作成時に、ファクトリの作成に加え、マネージド ID を作成できます。 マネージド ID は、Azure Active Directory に登録されているマネージド アプリケーションであり、この特定のデータ ファクトリを表します。

Data Factory のマネージド ID は次の機能に役立ちます。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)。この場合、データ ファクトリのマネージド ID は Azure Key Vault の認証に使用されます。
- [Azure Blob Storage](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure SQL Database](connector-azure-sql-database.md)、および [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md) を含むコネクタ。
- [Web アクティビティ](control-flow-web-activity.md)。

## <a name="generate-managed-identity"></a>マネージド ID の生成

Data Factory のマネージド ID は、次のように生成されます。

- **Azure portal または PowerShell** を使用してデータ ファクトリを作成すると、マネージド ID が常に自動的に作成されます。
- **SDK** を使用してデータ ファクトリを作成すると、作成用のファクトリ オブジェクトで "Identity = new FactoryIdentity()" を指定した場合にのみマネージド ID が作成されます。 [.NET のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-dot-net.md#create-a-data-factory)の例をご覧ください。
- **REST API** を使用してデータ ファクトリを作成すると、要求本文で "Identity" セクションを指定した場合にのみマネージド ID が作成されます。 [REST のクイックスタート - データ ファクトリの作成](quickstart-create-data-factory-rest-api.md#create-a-data-factory)の例をご覧ください。

「[マネージド ID の取得](#retrieve-managed-identity)」の手順に従ってデータ ファクトリにマネージド ID がまだ関連付けられていない場合は、ID イニシエーターを使用してプログラムでデータ ファクトリを更新することで、マネージド ID を明示的に生成できます。

- [PowerShell を使用したマネージド ID の生成](#generate-managed-identity-using-powershell)
- [REST API を使用したマネージド ID の生成](#generate-managed-identity-using-rest-api)
- [Azure Resource Manager テンプレートを使用したマネージド ID の生成](#generate-managed-identity-using-an-azure-resource-manager-template)
- [SDK を使用したマネージド ID の生成](#generate-managed-identity-using-sdk)

>[!NOTE]
>- マネージド ID は変更できません。 マネージド ID が既に存在するデータ ファクトリを更新しても影響はなく、マネージド ID が変更されることはありません。
>- ファクトリ オブジェクトで "identity" パラメーターを指定せずに、または REST 要求本文で "identity" セクションを指定せずに、マネージド ID が既に存在するデータ ファクトリを更新した場合、エラーが発生します。
>- データ ファクトリを削除すると、関連付けられているマネージド ID も削除されます。

### <a name="generate-managed-identity-using-powershell"></a>PowerShell を使用したマネージド ID の生成

**Set-AzDataFactoryV2** コマンドを再度呼び出すと、"Identity" フィールドが新たに生成されます。

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>REST API を使用したマネージド ID の生成

要求本文の "identity" セクションを使用して、次の API を呼び出します。

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**要求本文**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**応答**: マネージド ID が自動的に作成され、これに応じて "identity" セクションが設定されます。

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用したマネージド ID の生成

**テンプレート**: add "identity": { "type":"SystemAssigned" } を追加します。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>SDK を使用したマネージド ID の生成

Identity=new FactoryIdentity() を指定して、データ ファクトリの作成または更新関数を呼び出します。 .NET を使用したサンプル コードは以下のとおりです。

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>マネージド ID の取得

マネージド ID は、Azure portal から取得することも、プログラムによって取得することもできます。 以降のセクションでは、いくつかの例を示します。

>[!TIP]
> マネージド ID が表示されない場合は、ファクトリを更新して[マネージド ID を生成](#generate-managed-identity)してください。

### <a name="retrieve-managed-identity-using-azure-portal"></a>Azure portal を使用したマネージド ID の取得

Azure portal -> ご自分のデータ ファクトリ -> [プロパティ] でマネージド ID 情報を検索できます。

- マネージド ID オブジェクト ID
- マネージド ID のテナント
- マネージド ID アプリケーション ID

マネージ ID 情報は、Azure Blob、Azure Data Lake Storage、Azure Key Vault などのマネージド ID 認証をサポートする、リンクされたサービスを作成するときにも表示されます。

アクセス許可を付与する場合は、オブジェクト ID またはデータ ファクトリ名 (マネージド ID 名) を使用して、この ID を検索します。

### <a name="retrieve-managed-identity-using-powershell"></a>PowerShell を使用したマネージド ID の取得

特定のデータ ファクトリを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。 **PrincipalId** を使用してアクセス権を付与します。

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

アプリケーション ID を取得するには、上のプリンシパル ID をコピーし、プリンシパル ID をパラメーターとして指定して次の Azure Active Directory コマンドを実行します。

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

### <a name="retrieve-managed-identity-using-rest-api"></a>REST API を使用したマネージド ID の取得

特定のデータ ファクトリを取得すると、次のように、マネージド ID のプリンシパル ID とテナント ID が返されます。

要求で次の API を呼び出します。

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataFactory/factories/{factoryName}?api-version=2018-06-01
```

**応答**:次の例のような応答が返されます。 "identity" セクションに値が適宜入力されます。

```json
{
    "name":"<dataFactoryName>",
    "identity":{
        "type":"SystemAssigned",
        "principalId":"554cff9e-XXXX-XXXX-XXXX-90c7d9ff2ead",
        "tenantId":"72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id":"/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>",
    "type":"Microsoft.DataFactory/factories",
    "properties":{
        "provisioningState":"Succeeded",
        "createTime":"2020-02-12T02:22:50.2384387Z",
        "version":"2018-06-01",
        "factoryStatistics":{
            "totalResourceCount":0,
            "maxAllowedResourceCount":0,
            "factorySizeInGbUnits":0,
            "maxAllowedFactorySizeInGbUnits":0
        }
    },
    "eTag":"\"03006b40-XXXX-XXXX-XXXX-5e43617a0000\"",
    "location":"<region>",
    "tags":{

    }
}
```

> [!TIP] 
> ARM テンプレートからマネージド ID を取得するには、ARM JSON に **outputs** セクションを追加します。

```json
{
    "outputs":{
        "managedIdentityObjectId":{
            "type":"string",
            "value":"[reference(resourceId('Microsoft.DataFactory/factories', parameters('<dataFactoryName>')), '2018-06-01', 'Full').identity.principalId]"
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
データ ファクトリのマネージド ID を使用する状況と方法を紹介する次のトピックをご覧ください。

- [Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)
- [Azure リソース認証用のマネージド ID を使用して Azure Data Lake Store との間でデータを双方向にコピーする](connector-azure-data-lake-store.md)

データ ファクトリのマネージド ID の基になっている Azure リソースのマネージド ID の詳細については、[Azure リソースのマネージド ID の概要](/azure/active-directory/managed-identities-azure-resources/overview)に関する記事をご覧ください。 
