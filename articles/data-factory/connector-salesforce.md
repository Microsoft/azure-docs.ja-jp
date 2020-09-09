---
title: Salesforce との間でデータをコピーする
description: データ ファクトリ パイプラインでコピー アクティビティを使用して、Salesforce からサポートされているシンク データ ストアに、またはサポートされているソース データ ストアから Salesforce にデータをコピーする方法について説明します。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/13/2020
ms.openlocfilehash: 292d80f7fad796b2ee4f80478c55099148d7f855
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086695"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Azure Data Factory を使用して Salesforce をコピー元またはコピー先としてデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-salesforce-connector.md)
> * [現在のバージョン](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Salesforce から、または Salesforce にデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Salesforce コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Salesforce から、サポートされている任意のシンク データ ストアにデータをコピーできます。 サポートされている任意のソース データ ストアから Salesforce にデータをコピーすることもできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表を参照してください。

具体的には、この Salesforce コネクタは以下をサポートします。

- Salesforce Developer、Professional、Enterprise、または Unlimited エディション。
- Salesforce 運用環境、サンドボックス、およびカスタム ドメインをコピー先またはコピー元とするデータのコピー。

Salesforce コネクタは、Salesforce REST/Bulk API 上に構築されます。 既定では、コネクタによって、Salesforce からデータをコピーするには [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) が、Salesforce にデータをコピーするには [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) が使用されます。 また、リンクされたサービスで [`apiVersion` プロパティ](#linked-service-properties) を使用して、データの読み取りまたは書き込みに使用する API バージョンを明示的に設定することもできます。

## <a name="prerequisites"></a>前提条件

Salesforce で API アクセス許可を有効にする必要があります。 詳細については、「[How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)」(権限セットで Salesforce の API アクセスを有効にする方法) を参照してください。

## <a name="salesforce-request-limits"></a>Salesforce の要求の制限

Salesforce では、API 要求数の合計と、API の同時要求数に上限が設けられています。 以下の点に注意してください。

- 同時要求数が上限を超えると調整が発生し、ランダムにエラーが表示されます。
- 要求数の合計が上限を超えると、Salesforce アカウントが 24 時間ブロックされます。

また、どちらのシナリオでも、"REQUEST_LIMIT_EXCEEDED" エラー メッセージが表示されることがあります。 詳細については、[Salesforce Developer の制限](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm)に関する資料の「API Request Limits」(API 要求の制限) をご覧ください。

## <a name="get-started"></a>はじめに

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Salesforce コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Salesforce のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type |type プロパティを **Salesforce** に設定する必要があります。 |はい |
| environmentUrl | Salesforce インスタンスの URL を指定します。 <br> 既定値は `"https://login.salesforce.com"` です。 <br> - サンドボックスからデータをコピーするには、`"https://test.salesforce.com"` を指定します。 <br> - カスタム ドメインからデータをコピーするには、`"https://[domain].my.salesforce.com"`のように指定します。 |いいえ |
| username |ユーザー アカウントのユーザー名を指定します。 |はい |
| password |ユーザー アカウントのパスワードを指定します。<br/><br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| securityToken |ユーザー アカウントのセキュリティ トークンを指定します。 <br/><br/>セキュリティ トークンの概要については、「[Security and the API (セキュリティと API)](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)」をご覧ください。 セキュリティ トークンをスキップできるのは、Salesforce で[信頼済み IP アドレスの一覧](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm)に Integration Runtime の IP を追加した場合のみです。 Azure IR を使用する場合は、「[Azure Integration Runtime の IP アドレス](azure-integration-runtime-ip-addresses.md)」を参照してください。<br/><br/>セキュリティ トークンの取得およびリセット方法については、[セキュリティ トークンの取得](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)に関する記事を参照してください。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |いいえ |
| apiVersion | `48.0` など、使用する Salesforce の REST または Bulk API バージョンを指定します。 既定では、コネクタによって、Salesforce からデータをコピーするには [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) が、Salesforce にデータをコピーするには [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) が使用されます。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 | ソースの場合は「いいえ」、シンクの場合は「はい」 (ソースにリンクされたサービスに統合ランタイムがない場合) |

>[!IMPORTANT]
>Salesforce にデータをコピーする場合は、既定の Azure Integration Runtime を使用してコピーを実行することはできません。 言い換えると、ソースのリンクされたサービスに指定された統合ランタイムがない場合は、Salesforce インスタンスに近い場所に明示的に [Azure Integration Runtime を作成](create-azure-integration-runtime.md#create-azure-ir)します。 次の例のように、Salesforce のリンクされたサービスを関連付けます。

**例:Data Factory に資格情報を格納する**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例:Key Vault に資格情報を格納する**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Salesforce データセット でサポートされるプロパティの一覧を示します。

Salesforce をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **SalesforceObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは **SalesforceObject** に設定する必要があります。  | はい |
| objectApiName | データの取得元の Salesforce オブジェクト名。 | ソースの場合はいいえ、シンクの場合ははい |

> [!IMPORTANT]
> カスタム オブジェクトには、**API 名**の "__c" の部分が必要となります。

![Data Factory の Salesforce 接続 API 名](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**例:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>旧バージョンとの互換性の維持:Salesforce からデータをコピーする際に、以前の "RelationalTable" 型のデータセットを引き続き使用できますが、新しい "SalesforceObject" 型に切り替えることを推奨するメッセージが表示されます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは **RelationalTable** に設定する必要があります。 | はい |
| tableName | Salesforce のテーブル名。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Salesforce ソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="salesforce-as-a-source-type"></a>ソース タイプとしての Salesforce

Salesforce からデータをコピーするには、コピー アクティビティのソースの種類を **SalesforceSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **SalesforceSource** に設定する必要があります。 | はい |
| query |カスタム クエリを使用してデータを読み取ります。 [Salesforce オブジェクト クエリ言語 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) クエリまたは SQL-92 クエリを使用できます。 その他のヒントについては、「[クエリのヒント](#query-tips)」セクションをご覧ください。 クエリが指定されていない場合は、データセット内の "objectApiName"で指定された Salesforce オブジェクトのすべてのデータが取得されます。 | いいえ (データセットの "objectApiName" が指定されている場合) |
| readBehavior | 既存のレコード、または削除されたものを含むすべてのレコードの、どちらのクエリを行うかを示します。 指定しない場合の既定の動作は前者です。 <br>使用可能な値: **query** (既定値)、**queryAll**.  | いいえ |

> [!IMPORTANT]
> カスタム オブジェクトには、**API 名**の "__c" の部分が必要となります。

![Data Factory の Salesforce 接続 API 名一覧](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**例:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>旧バージョンとの互換性の維持:Salesforce からデータをコピーする際に、以前の "RelationalSource" 型のコピーを引き続き使用できますが、新しい "SalesforceSource" 型に切り替えることを推奨するメッセージが表示されます。

### <a name="salesforce-as-a-sink-type"></a>シンクの種類としての Salesforce

Salesforce にデータをコピーするには、コピー アクティビティのシンクの種類を **SalesforceSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのシンクの type プロパティは **SalesforceSink** に設定する必要があります。 | はい |
| writeBehavior | 操作の書き込み動作。<br/>使用可能な値: **Insert** および **Upsert**。 | いいえ (既定値は Insert) |
| externalIdFieldName | Upsert 操作の外部 ID フィールドの名前。 指定するフィールドは、Salesforce オブジェクトに "External ID Field" として定義されている必要があります。 対応する入力データに NULL 値を持つことはできません。 | "Upsert" の場合ははい |
| writeBatchSize | 各バッチで Salesforce に書き込まれたデータの行数。 | いいえ (既定値は 5,000) |
| ignoreNullValues | 書き込み操作時に入力データからの NULL 値を無視するかどうかを示します。<br/>使用可能な値: **true** および **false**。<br>- **True**:upsert または更新操作を行うときに、対象オブジェクト内のデータが変更されないようにします。 挿入操作を実行するときに、定義済みの既定値を挿入します。<br/>- **False**:upsert または更新操作を行うときに、対象オブジェクト内のデータを NULL に更新します。 挿入操作を実行するときに、NULL 値を挿入します。 | いいえ (既定値は false) |

**例:コピー アクティビティでの Salesforce シンク**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>クエリのヒント

### <a name="retrieve-data-from-a-salesforce-report"></a>Salesforce レポートからデータを取得する

クエリを `{call "<report name>"}` と指定することで、Salesforce レポートからデータを取得できます。 たとえば `"query": "{call \"TestReport\"}"` です。

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Salesforce のごみ箱から削除済みレコードを取得する

論理的に削除されたレコードを Salesforce のごみ箱から検索するには、`readBehavior` を `queryAll` と指定できます。 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL クエリと SQL クエリの構文の違い

Salesforce からデータをコピーするときは、SOQL クエリまたは SQL クエリのいずれかを使用できます。 これら 2 つのクエリでは構文と機能のサポートが異なるので、混在させないでください。 Salesforce でネイティブにサポートされている SOQL クエリを使用することをお勧めします。 次の表に、主な違いを示します。

| 構文 | SOQL モード | SQL モード |
|:--- |:--- |:--- |
| 列の選択 | コピーするフィールドをクエリで列挙する必要があります (例: `SELECT field1, filed2 FROM objectname`) | 列の選択に加えて、`SELECT *` がサポートされています。 |
| 引用符 | フィールド/オブジェクト名を引用符で囲むことはできません。 | フィールド/オブジェクト名を引用符で囲むことができます (例: `SELECT "id" FROM "Account"`) |
| 日時の形式 |  [こちら](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)の詳細と次のセクションのサンプルをご覧ください。 | [こちら](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017)の詳細と次のセクションのサンプルをご覧ください。 |
| ブール値 | `False` および `True` と表されます (例: `SELECT … WHERE IsDeleted=True`)。 | 0 または 1 と表されます (例: `SELECT … WHERE IsDeleted=1`)。 |
| 列の名前変更 | サポートされていません。 | サポートされています (例: `SELECT a AS b FROM …`)。 |
| リレーションシップ | サポートされています (例: `Account_vod__r.nvs_Country__c`)。 | サポートされていません。 |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>DateTime 列で where 句を使用してデータを取得する

SOQL クエリまたは SQL クエリを指定する場合は、DateTime 形式の違いに注意してください。 次に例を示します。

* **SOQL サンプル**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL の例**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>MALFORMED_QUERY:Truncated のエラー

"MALFORMED_QUERY:Truncated" のエラーが発生した場合、通常は、データ内に JunctionIdList 型の列があり、Salesforce において行数が多いデータなどのサポートに対して制限があることが原因です。 移行するには、JunctionIdList 列の除外か、またはコピーする行数の制限を試みます (パーティション分割して複数のコピー アクティビティの実行にすることが可能です)。

## <a name="data-type-mapping-for-salesforce"></a>Salesforce のデータ型マッピング

Salesforce からデータをコピーするとき、次の Salesforce のデータ型から Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Salesforce のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| Auto Number |String |
| Checkbox |Boolean |
| Currency |Decimal |
| Date |DateTime |
| 日付/時刻 |DateTime |
| Email |String |
| id |String |
| Lookup Relationship |String |
| Multi-Select Picklist |String |
| Number |Decimal |
| Percent |Decimal |
| Phone |String |
| Picklist |String |
| Text |String |
| Text Area |String |
| Text Area (Long) |String |
| Text Area (Rich) |String |
| Text (Encrypted) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
