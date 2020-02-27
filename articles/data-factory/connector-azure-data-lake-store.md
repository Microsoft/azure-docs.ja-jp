---
title: Azure Data Lake Storage Gen1 との間でデータをコピーする
description: Data Factory を使用して、サポートされるソース データ ストアのデータを Azure Data Lake Store にコピーしたり、Data Lake Store のデータをサポートされるシンク ストアにコピーしたりできます。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 4dae0d10f103710a0e6039127c5c1cacb63c03c4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893103"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Data Lake Storage Gen1 との間でデータをコピーする

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-azure-datalake-connector.md)
> * [現在のバージョン](connector-azure-data-lake-store.md)

この記事では、Azure Data Lake Storage Gen1 をコピー先またはコピー元としてデータをコピーする方法について説明します。 Azure Data Factory については、[入門記事で](introduction.md)をご覧ください。

## <a name="supported-capabilities"></a>サポートされる機能

この Azure Data Lake Storage Gen1 コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md) 
- [マッピング データ フロー](concepts-data-flow-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [アクティビティを削除する](delete-activity.md)

具体的には、このコネクタを使用して次のことができます。

- サービス プリンシパルと Azure リソースのマネージド ID のどちらかの認証方法を使用して、ファイルをコピーする。
- ファイルをそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)でファイルを解析または生成する。
- Azure Data Lake Storage Gen2 にコピーするときに [ACL を保持する](#preserve-acls-to-data-lake-storage-gen2)。

> [!IMPORTANT]
> セルフホステッド統合ランタイムを使ってデータをコピーする場合は、`<ADLS account name>.azuredatalakestore.net` および `login.microsoftonline.com/<tenant>/oauth2/token` に対するポート 443 のアウトバウンド トラフィックを許可するように、会社のファイアウォールを構成します。 後者は、Azure セキュリティ トークン サービスです。統合ランタイムがアクセス トークンを取得するためには、このサービスと通信を行う必要があります。

## <a name="get-started"></a>はじめに

> [!TIP]
> Azure Data Lake Store コネクタの使い方のチュートリアルについては、[Azure Data Lake Store へのデータの読み込み](load-azure-data-lake-store.md)に関する記事を参照してください。

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下のセクションでは、Azure Data Lake Store に固有の Data Factory エンティティを定義するために使用されるプロパティについて説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Data Lake Store のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | `type` プロパティは **AzureDataLakeStore** に設定する必要があります。 | はい |
| dataLakeStoreUri | Azure Data Lake Store アカウントに関する情報です。 この情報の形式は、`https://[accountname].azuredatalakestore.net/webhdfs/v1` または `adl://[accountname].azuredatalakestore.net/` です。 | はい |
| subscriptionId | Data Lake Store アカウントが属している Azure サブスクリプション ID です。 | シンクでは必須 |
| resourceGroupName | Data Lake Store アカウントが属している Azure リソース グループ名です。 | シンクでは必須 |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 データ ストアがプライベート ネットワークにある場合、Azure 統合ランタイムまたはセルフホステッド統合ランタイムを使用できます。 このプロパティが指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="use-service-principal-authentication"></a>サービス プリンシパル認証を使用する

サービス プリンシパル認証を使用するには、次の手順に従います。

1. Azure Active Directory にアプリケーション エンティティを登録し、Data Lake Store へのアクセス権を付与します。 詳細な手順については、「[サービス間認証](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)」を参照してください。 次の値を記録しておきます。リンクされたサービスを定義するときに使います。

    - アプリケーション ID
    - アプリケーション キー
    - テナント ID

2. サービス プリンシパルに適切なアクセス許可を付与します。 Data Lake Storage Gen1 におけるアクセス許可の動作例については、「[Azure Data Lake Store Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)」を参照してください。

    - **ソースとして**: **[データ エクスプローラー]**  >  **[アクセス]** で、すべての上位フォルダー (ルートを含む) について、少なくとも**実行**アクセス許可を与えると共に、コピーするファイルの**読み取り**アクセス許可を与えます。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 アカウント レベルのアクセスの制御 (IAM) に関する要件はありません。
    - **シンクとして**: **[データ エクスプローラー]**  >  **[アクセス]** で、すべての上位フォルダー (ルートを含む) に少なくとも**実行**アクセス許可を与えると共に、シンク フォルダーに**書き込み**アクセス許可を与えます。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 Azure 統合ランタイムを使用してコピーする (ソースとシンクの両方がクラウドに存在する) 場合は、Data Factory で Data Lake Store のリージョンを検出させるために、IAM で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを避けたい場合は、Data Lake Store の場所を使用して明示的に [Azure 統合ランタイムを作成](create-azure-integration-runtime.md#create-azure-ir)してください。 たとえば、Data Lake Store が西ヨーロッパにある場合、場所を "西ヨーロッパ" に設定した Azure 統合ランタイムを作成します。 次の例で示すように、Data Lake Store のリンクされたサービスでそれらを関連付けます。

次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| servicePrincipalId | アプリケーションのクライアント ID を取得します。 | はい |
| servicePrincipalKey | アプリケーションのキーを取得します。 このフィールドを `SecureString` としてマークして Data Factory に安全に格納するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名やテナント ID など) を指定します。 Azure Portal の右上隅をマウスでポイントすることにより取得できます。 | はい |

**例:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure リソースの認証にマネージド ID を使用する

データ ファクトリは、特定のデータ ファクトリを表す、[Azure リソースのマネージド ID](data-factory-service-identity.md) に関連付けることができます。 独自のサービス プリンシパルを使用するのと同様に、Data Lake Store 認証にこのマネージド ID を直接使用できます。 これにより、この指定されたファクトリは、Data Lake Store にアクセスしてデータをコピーできます。

Azure リソースのマネージド ID 認証を使用するには、次のようにします。

1. ファクトリと共に生成された "サービス ID アプリケーション ID" の値をコピーして、[データ ファクトリのマネージド ID 情報を取得](data-factory-service-identity.md#retrieve-managed-identity)します。

2. Data Lake Store へのアクセス権をマネージド ID に付与します。 Data Lake Storage Gen1 におけるアクセス許可の動作例については、「[Azure Data Lake Store Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)」を参照してください。

    - **ソースとして**: **[データ エクスプローラー]**  >  **[アクセス]** で、すべての上位フォルダー (ルートを含む) について、少なくとも**実行**アクセス許可を与えると共に、コピーするファイルの**読み取り**アクセス許可を与えます。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 アカウント レベルのアクセスの制御 (IAM) に関する要件はありません。
    - **シンクとして**: **[データ エクスプローラー]**  >  **[アクセス]** で、すべての上位フォルダー (ルートを含む) に少なくとも**実行**アクセス許可を与えると共に、シンク フォルダーに**書き込み**アクセス許可を与えます。 **[このフォルダーとすべての子]** を選択して再帰的に追加したり、 **[アクセス許可エントリと既定のアクセス許可エントリ]** として追加したりすることができます。 Azure 統合ランタイムを使用してコピーする (ソースとシンクの両方がクラウドに存在する) 場合は、Data Factory で Data Lake Store のリージョンを検出させるために、IAM で少なくとも**閲覧者**ロールを付与します。 この IAM ロールを避けたい場合は、Data Lake Store の場所を使用して明示的に [Azure 統合ランタイムを作成](create-azure-integration-runtime.md#create-azure-ir)してください。 次の例で示すように、Data Lake Store のリンクされたサービスでそれらを関連付けます。

Azure Data Factory では、リンクされたサービスの Data Lake Store の一般的な情報以外にプロパティを指定する必要はありません。

**例:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure Data Lake Store Gen1 では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ   | 説明                                                  | 必須 |
| ---------- | ------------------------------------------------------------ | -------- |
| 型       | データセットの `location` の type プロパティは、**AzureDataLakeStoreLocation** に設定する必要があります。 | はい      |
| folderPath | フォルダーのパス。 ワイルドカードを使用してフォルダーをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |
| fileName   | 特定の folderPath の下のファイル名。 ワイルドカードを使用してファイルをフィルター処理する場合は、この設定をスキップし、アクティビティのソース設定で指定します。 | いいえ       |

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関するページを参照してください。 このセクションでは、Azure Data Lake Store のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-data-lake-store-as-source"></a>ソースとしての Azure Data Lake Store

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure Data Lake Store Gen1 では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| 型                     | `storeSettings` の type プロパティは **AzureDataLakeStoreReadSettings** に設定する必要があります。 | はい                                           |
| recursive                | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 recursive が true に設定されていて、シンクがファイル ベースのストアである場合、空のフォルダーまたはサブフォルダーはシンクでコピーも作成もされません。 使用可能な値: **true** (既定値) および **false**。 | いいえ                                            |
| wildcardFolderPath       | ソース フォルダーをフィルター処理するための、ワイルドカード文字を含むフォルダー パス。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br>「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | いいえ                                            |
| wildcardFileName         | ソース ファイルをフィルター処理するための、特定の folderPath/wildcardFolderPath の下のワイルドカード文字を含むファイル名。 <br>使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 | はい (データセットで `fileName` が指定されていない場合) |
| modifiedDatetimeStart    | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。 | いいえ                                            |
| modifiedDatetimeEnd      | 上記と同じです。                                               | いいえ                                            |
| maxConcurrentConnections | ストレージ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ                                            |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>シンクとしての Azure Data Lake Store

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Azure Data Lake Store Gen1 では、形式ベースのコピー シンクの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| 型                     | `storeSettings` の type プロパティは **AzureDataLakeStoreWriteSettings** に設定する必要があります。 | はい      |
| copyBehavior             | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 それ以外は自動生成されたファイル名になります。 | いいえ       |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>フォルダーとファイル フィルターの例

このセクションでは、ワイルドカード フィルターを使用した結果のフォルダーのパスとファイル名の動作について説明します。

| folderPath | fileName | recursive | ソースのフォルダー構造とフィルターの結果 (**太字**のファイルが取得されます)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (空、既定値を使用) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (空、既定値を使用) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>コピー操作の動作の例

このセクションでは、コピー操作の動作から得られる結果を、`recursive` 値と `copyBehavior` 値の組み合わせごとに説明します。

| recursive | copyBehavior | ソースのフォルダー構造 | ターゲットの結果 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲット Folder1 は、ソースと同じ構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 の自動生成された名前 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。 <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2、File3、File4、File5 の内容は 1 つのファイルにマージされて、自動生成されたファイル名が付けられます。 |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 の自動生成された名前<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | ターゲットの Folder1 は、次の構造で作成されます。<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1、File2 の内容は 1 つのファイルにマージされ、自動生成されたファイル名が付けられます。 File1 の自動生成された名前<br/><br/>Subfolder1 と File3、File4、File5 は取得されません。 |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2 に ACL を保持する

>[!TIP]
>Azure Data Lake Storage Gen1 から Gen2 への一般的なデータ コピーを実行する場合、チュートリアルとベスト プラクティスについては、「[Azure Data Factory を使用して Azure Data Lake Storage Gen1 から Gen2 にデータをコピーする](load-azure-data-lake-storage-gen2-from-gen1.md)」をご覧ください。

Data Lake Storage Gen1 から Data Lake Storage Gen2 にアップグレードするときに、アクセス制御リスト (ACL) をデータ ファイルと共にレプリケートする必要がある場合は、「[Data Lake Storage Gen1 の ACL を保持する](copy-activity-preserve-metadata.md#preserve-acls)」をご覧ください。

## <a name="mapping-data-flow-properties"></a>Mapping Data Flow のプロパティ

マッピング データ フローでデータを変換するときに、JSON、Avro、区切りテキスト、または Parquet 形式で Azure Data Lake Storage Gen1 からファイルを読み取ること、および書き込むことができます。 詳細については、マッピング データ フロー機能の[ソース変換](data-flow-source.md)と[シンク変換](data-flow-sink.md)に関するページを参照してください。

### <a name="source-transformation"></a>ソース変換

ソース変換では、Azure Data Lake Storage Gen1 内のコンテナー、フォルダー、または個々のファイルから読み取ることができます。 **[Source options]\(ソース オプション\)** タブで、ファイルの読み取り方法を管理できます。 

![ソース オプション](media/data-flow/sourceOptions1.png "ソース オプション")

**[Wildcard path]\(ワイルドカード パス\)** : ワイルドカード パターンを使用すると、ADF は、単一のソース変換で一致する各フォルダーとファイルをループ処理するよう指示されます。 これは、単一のフロー内の複数のファイルを処理するのに効果的な方法です。 既存のワイルドカード パターンをポイントしたときに表示される + 記号を使って複数のワイルドカード一致パターンを追加します。

ソース コンテナーから、パターンに一致する一連のファイルを選択します。 データセット内で指定できるのはコンテナーのみです。 そのため、ワイルドカード パスには、ルート フォルダーからのフォルダー パスも含める必要があります。

ワイルドカードの例:

* ```*``` - 任意の文字セットを表します。
* ```**``` - ディレクトリの再帰的な入れ子を表します。
* ```?``` - 1 文字を置き換えます。
* ```[]``` - 角カッコ内の文字のいずれか 1 つと一致します。

* ```/data/sales/**/*.csv``` - /data/sales の下のすべての csv ファイルを取得します。
* ```/data/sales/20??/**``` - 20 世紀のすべてのファイルを取得します。
* ```/data/sales/2004/*/12/[XY]1?.csv``` - 前に 2 桁の数字が付いた X または Y で始まる、2004 年 12 月のすべての csv ファイルを取得します。

**[Partition root path]\(パーティションのルート パス\):** ```key=value``` 形式 (例: year=2019) のファイル ソース内のフォルダーをパーティション分割した場合、そのパーティション フォルダー ツリーの最上位をデータ フロー データ ストリーム内の列名に割り当てることができます。

最初に、ワイルドカードを設定して、パーティション分割されたフォルダーと読み取るリーフ ファイルのすべてのパスを含めます。

![パーティション ソース ファイルの設定](media/data-flow/partfile2.png "パーティション ファイルの設定")

パーティションのルート パス設定を使用して、フォルダー構造の最上位レベルを定義します。 データ プレビューを使用してデータの内容を表示すると、ADF によって、各フォルダー レベルで見つかった解決済みのパーティションが追加されることがわかります。

![パーティションのルート パス](media/data-flow/partfile1.png "パーティション ルート パスのプレビュー")

**[List of files]:** これはファイル セットです。 処理する相対パス ファイルの一覧を含むテキスト ファイルを作成します。 このテキスト ファイルをポイントします。

**[Column to store file name]\(ファイル名を格納する列\):** ソース ファイルの名前をデータの列に格納します。 ファイル名文字列を格納するための新しい列名をここに入力します。

**[After completion]\(完了後\):** データ フローの実行後にソース ファイルに何もしないか、ソース ファイルを削除するか、またはソース ファイルを移動することを選択します。 移動のパスは相対パスです。

後処理でソース ファイルを別の場所に移動するには、まず、ファイル操作の "移動" を選択します。 次に、"移動元" ディレクトリを設定します。 パスにワイルドカードを使用していない場合、"移動元" 設定はソース フォルダーと同じフォルダーになります。

ワイルドカードを含むソース パスがある場合、構文は次のようになります。

```/data/sales/20??/**/*.csv```

"移動元" は次のように指定できます。

```/data/sales```

"移動先" は次のように指定できます。

```/backup/priorSales```

この場合、ソースとして指定された /data/sales の下のすべてのファイルは /backup/priorSales に移動されます。

> [!NOTE]
> ファイル操作は、パイプライン内のデータ フローの実行アクティビティを使用するパイプライン実行 (パイプラインのデバッグまたは実行) からデータ フローを開始する場合にのみ実行されます。 データ フロー デバッグ モードでは、ファイル操作は実行*されません*。

**[Filter by last modified]\(最終更新日時でフィルター処理\):** 最終更新日時の範囲を指定することで、処理するファイルをフィルター処理できます。 日時はすべて UTC 形式です。 

### <a name="sink-properties"></a>シンクのプロパティ

シンク変換では、Azure Data Lake Storage Gen1 内のコンテナーまたはフォルダーに書き込むことができます。 **[設定]** タブで、ファイルの書き込み方法を管理できます。

![シンクのオプション](media/data-flow/file-sink-settings.png "シンク オプション")

**Clear the folder**\(フォルダーのクリア\):データが書き込まれる前に、書き込み先のフォルダーをクリアするかどうかを決定します。

**File name option**\(ファイル名のオプション\):書き込み先のフォルダー内の書き込み先ファイルの命名方法を決定します。 ファイル名のオプションを次に示します。
   * **既定**:Spark は PART の既定値に基づいて、ファイルに名前を付けることができます。
   * **パターン**:パーティションごとに出力ファイルを列挙するパターンを入力します。 たとえば、**loans[n].csv** と入力すると、loans1.csv、loans2.csv のように作成されます。
   * **Per partition**(パーティションあたり):パーティションごとに 1 つのファイル名を入力します。
   * **As data in column**(列内のデータとして):出力ファイルを列の値に設定します。 パスは、書き込み先フォルダーではなく、データセット コンテナーからの相対パスです。
   * **Output to a single file**(1 つのファイルに出力する):パーティション分割された出力ファイルを単一の名前付きファイルに結合します。 パスは、データセット フォルダーからの相対パスです。 このマージ操作は、ノード サイズによっては失敗する可能性があることに注意してください。 このオプションは、大規模なデータセットに対しては推奨されません。

**Quote all**\(すべてを引用符で囲む\):すべての値を引用符で囲むかどうかを決定します

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。

## <a name="getmetadata-activity-properties"></a>GetMetadata アクティビティのプロパティ

プロパティの詳細については、[GetMetadata アクティビティ](control-flow-get-metadata-activity.md)に関するページを参照してください。 

## <a name="delete-activity-properties"></a>Delete アクティビティのプロパティ

プロパティの詳細については、[Delete アクティビティ](delete-activity.md)に関するページを参照してください。

## <a name="legacy-models"></a>レガシ モデル

>[!NOTE]
>次のモデルは、下位互換性のために引き続きそのままサポートされます。 今後は、上のセクションで説明した新しいモデルを使用することをお勧めします。ADF オーサリング UI は、新しいモデルを生成するように切り替えられています。

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | データセットの type プロパティには、**AzureDataLakeStoreFile** を設定する必要があります。 |はい |
| folderPath | Data Lake Store のフォルダーへのパス。 指定しないと、ルートが参照されます。 <br/><br/>ワイルドカード フィルターがサポートされています。 使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。 実際のフォルダー名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。 <br/><br/>例: rootfolder/subfolder/。 「[フォルダーとファイル フィルターの例](#folder-and-file-filter-examples)」の他の例をご覧ください。 |いいえ |
| fileName | 指定された "folderPath" の下にあるファイルの名前またはワイルドカード フィルター。 このプロパティの値を指定しない場合、データセットはフォルダー内のすべてのファイルをポイントします。 <br/><br/>フィルターに使用できるワイルドカードは、`*` (ゼロ文字以上の文字に一致) と `?` (ゼロ文字または 1 文字に一致) です。<br/>- 例 1: `"fileName": "*.csv"`<br/>- 例 2: `"fileName": "???20180427.txt"`<br/>実際のファイル名にワイルドカードまたはこのエスケープ文字が含まれている場合は、`^` を使用してエスケープします。<br/><br/>出力データセットに fileName の指定がなく、アクティビティ シンクに **preserveHierarchy** の指定がない場合、コピー アクティビティは、"*Data.[activity run ID GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]* " というパターンでファイル名が自動的に生成されます (例: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz")。 クエリの代わりにテーブル名を使用して表形式のソースからコピーする場合、名前のパターンは " *[table name].[format].[compression if configured]* " となります。たとえば、"MyTable.csv" などです。 |いいえ |
| modifiedDatetimeStart | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| modifiedDatetimeEnd | 最終変更日時属性に基づくファイル フィルター。 最終変更日時が `modifiedDatetimeStart` から `modifiedDatetimeEnd` の範囲内にあるファイルが選択されます。 時刻は "2018-12-01T05:00:00Z" の形式で UTC タイム ゾーンに適用されます。 <br/><br/> 大量のファイルでファイル フィルターを実行する場合、この設定を有効にすることで、データ移動の全体的なパフォーマンスが影響を受けます。 <br/><br/> 各プロパティには NULL を指定できます。これは、ファイル属性フィルターをデータセットに適用しないことを意味します。 `modifiedDatetimeStart` に datetime 値が設定されており、`modifiedDatetimeEnd` が NULL の場合は、最終変更日時属性が datetime 値以上であるファイルが選択されます。 `modifiedDatetimeEnd` に datetime 値が設定されており、`modifiedDatetimeStart` が NULL の場合は、最終変更日時属性が datetime 値以下であるファイルが選択されます。| いいえ |
| format | ファイルベースのストア間でファイルをそのままコピー (バイナリ コピー) する場合は、入力と出力の両方のデータセット定義で format セクションをスキップします。<br/><br/>特定の形式のファイルを解析または生成する場合、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[Text 形式](supported-file-formats-and-compression-codecs-legacy.md#text-format)、[Json 形式](supported-file-formats-and-compression-codecs-legacy.md#json-format)、[Avro 形式](supported-file-formats-and-compression-codecs-legacy.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs-legacy.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) の各セクションを参照してください。 |いいえ (バイナリ コピー シナリオのみ) |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs-legacy.md#compression-support)に関する記事を参照してください。<br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、および **ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

>[!TIP]
>フォルダーの下のすべてのファイルをコピーするには、**folderPath** のみを指定します。<br>特定の名前が付いた単一のファイルをコピーするには、フォルダー部分で **folderPath**、ファイル名で **fileName** を指定します。<br>フォルダーの下のファイルのサブセットをコピーするには、フォルダー部分で **folderPath**、ワイルドカード フィルターで **fileName** を指定します。 

**例:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>レガシ コピー アクティビティ ソース モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのソースの `type` プロパティは **AzureDataLakeStoreSource** に設定する必要があります。 |はい |
| recursive | データをサブフォルダーから再帰的に読み取るか、指定したフォルダーからのみ読み取るかを指定します。 `recursive` が true に設定されていて、シンクがファイル ベースのストアである場合、シンクでは空のフォルダーまたはサブフォルダーがコピーも作成もされません。 使用可能な値: **true** (既定値) および **false**。 | いいえ |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-copy-activity-sink-model"></a>レガシ コピー アクティビティ シンク モデル

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| 型 | コピー アクティビティのシンクの `type` プロパティは、**AzureDataLakeStoreSink** に設定する必要があります。 |はい |
| copyBehavior | ソースがファイル ベースのデータ ストアのファイルの場合は、コピー動作を定義します。<br/><br/>使用できる値は、以下のとおりです。<br/><b>- PreserveHierarchy (既定値)</b>:ターゲット フォルダー内でファイル階層を保持します。 ソース フォルダーへのソース ファイルの相対パスはターゲット フォルダーへのターゲット ファイルの相対パスと同じになります。<br/><b>- FlattenHierarchy</b>:ソース フォルダーのすべてのファイルをターゲット フォルダーの第一レベルに配置します。 ターゲット ファイルは、自動生成された名前になります。 <br/><b>- MergeFiles</b>:ソース フォルダーのすべてのファイルを 1 つのファイルにマージします。 ファイル名を指定した場合、マージされたファイル名は指定した名前になります。 指定しないと、ファイル名は自動生成されます。 | いいえ |
| maxConcurrentConnections | データ ストアに同時に接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>次のステップ

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
