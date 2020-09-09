---
title: SQL オンデマンド (プレビュー) のストレージ アカウント アクセスを制御する
description: SQL オンデマンド (プレビュー) が Azure Storage にアクセスする方法と、Azure Synapse Analytics で SQL オンデマンドのストレージ アクセスを制御する方法について説明します。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 06/11/2020
ms.author: fipopovi
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: fd4cc4cfa7b7be9085ac404cab7fc7447b6d66a7
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987139"
---
# <a name="control-storage-account-access-for-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) のストレージ アカウント アクセスを制御する

SQL オンデマンドのクエリは、Azure Storage から直接ファイルを読み取ります。 Azure Storage 上のファイルへのアクセス許可は次の 2 つのレベルで制御されます。
- **ストレージ レベル** - ユーザーには、基になるストレージ ファイルにアクセスするためのアクセス許可が必要です。 ストレージの管理者は、ファイルの読み取り/書き込み、またはストレージへのアクセスに使用される SAS キーの生成を Azure AD プリンシパルに許可する必要があります。
- **SQL サービス レベル** - ユーザーには、[外部テーブル](develop-tables-external-tables.md)からデータを読み取るための `SELECT` 権限、または `OPENROWSET` を実行するための `ADMINISTER BULK ADMIN` 権限が必要です。また、ストレージへのアクセスに使用される資格情報を使用する権限も必要です。

この記事では、使用できる資格情報の種類と、SQL および Azure AD のユーザーに対して資格情報の参照がどのように実行されるかについて説明します。

## <a name="supported-storage-authorization-types"></a>サポートされているストレージ承認の種類

SQL オンデマンド リソースにログインしたユーザーは、Azure Storage 内のファイルにアクセスしてクエリを実行する権限を持っている必要があります (ファイルが一般公開されていない場合)。 3 種類の承認 ([ユーザー ID](?tabs=user-identity)、[Shared access signature](?tabs=shared-access-signature)、[マネージド ID](?tabs=managed-identity)) を使用して、非パブリック ストレージにアクセスできます。

> [!NOTE]
> **Azure AD パススルー**は、ワークスペースを作成するときの既定の動作です。

### <a name="user-identity"></a>[ユーザー ID](#tab/user-identity)

**ユーザー ID** ("Azure AD パススルー" とも呼ばれる) は、SQL オンデマンドにログインしている Azure AD ユーザーの ID がデータ アクセスの承認に使用される承認の種類です。 データにアクセスする前に、Azure Storage の管理者が Azure AD ユーザーにアクセス許可を付与する必要があります。 下の表に示されているように、これは SQL ユーザーの種類ではサポートされていません。

> [!IMPORTANT]
> ID を使用してデータにアクセスするには、Storage Blob データの所有者/共同作成者/閲覧者のロールを持っている必要があります。
> ストレージ アカウントの所有者であっても、Storage Blob データのいずれかのロールに自分自身を追加する必要があります。
>
> Azure Data Lake Store Gen2 でのアクセス制御の詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](../../storage/blobs/data-lake-storage-access-control.md)」という記事をご覧ください。
>

### <a name="shared-access-signature"></a>[共有アクセス署名](#tab/shared-access-signature)

**Shared Access Signature (SAS)** を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS を使用すると、お客様はアカウント キーを共有することなく、クライアントにストレージ アカウントのリソースへのアクセス権を付与できます。 SAS を使用すると、有効期間、付与されるアクセス許可、許容される IP アドレス範囲、受け入れ可能なプロトコル (https/http) など、SAS を使用しているクライアントに付与するアクセス権の種類をきめ細かく制御できます。

SAS トークンを取得するには、**Azure portal -> [ストレージ アカウント] -> [Shared Access Signature] -> [アクセス許可の構成] -> [Generate SAS and connection string]\(SAS と接続文字列を生成する\)** に移動します。

> [!IMPORTANT]
> SAS トークンが生成されると、トークンの先頭に疑問符 ("?") が含まれます。 SQL オンデマンドでトークンを使用するには、資格情報を作成するときに疑問符 ("?") を削除する必要があります。 次に例を示します。
>
> SAS トークン: ?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D

SAS トークンを使用したアクセスを有効にするには、データベーススコープまたはサーバースコープの資格情報を作成する必要があります。

### <a name="managed-identity"></a>[Managed Identity](#tab/managed-identity)

**マネージ ID** は MSI とも呼ばれます。 これは、SQL オンデマンドに Azure サービスを提供する Azure Active Directory (Azure AD) の機能です。 また、Azure AD で自動的に管理される ID をデプロイします。 この ID を使用して、Azure Storage でのデータ アクセスの要求を承認できます。

データにアクセスする前に、Azure Storage の管理者が、データにアクセスするためのアクセス許可をマネージド ID に付与する必要があります。 マネージド ID へのアクセス許可の付与は、他の Azure AD ユーザーにアクセス許可を付与するのと同じ方法で行われます。

### <a name="anonymous-access"></a>[匿名アクセス](#tab/public-access)

[匿名アクセスが許可されている](/azure/storage/blobs/storage-manage-access-to-resources) Azure Storage アカウントに配置されている一般公開ファイルにアクセスできます。

---

### <a name="supported-authorization-types-for-databases-users"></a>データベース ユーザーに対してサポートされている認可の種類

次の表では、使用可能な認可の種類を確認できます。

| 承認の種類                    | *SQL ユーザー*    | *Azure AD ユーザー*     |
| ------------------------------------- | ------------- | -----------    |
| [ユーザー ID](?tabs=user-identity#supported-storage-authorization-types)       | サポートされていません | サポートされています      |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)       | サポートされています     | サポートされています      |
| [Managed Identity](?tabs=managed-identity#supported-storage-authorization-types) | サポートされていません | サポートされています      |

### <a name="supported-storages-and-authorization-types"></a>サポートされているストレージと承認の種類

承認と Azure Storage の種類の次の組み合わせを使用できます。

| 承認の種類  | Blob Storage   | ADLS Gen1        | ADLS Gen2     |
| ------------------- | ------------   | --------------   | -----------   |
| [SAS](?tabs=shared-access-signature#supported-storage-authorization-types)    | サポートされています\*      | サポートされていません   | サポートされています\*     |
| [Managed Identity](?tabs=managed-identity#supported-storage-authorization-types) | サポートされています      | サポートされています        | サポートされています     |
| [ユーザー ID](?tabs=user-identity#supported-storage-authorization-types)    | サポートされています\*      | サポートされています\*        | サポートされています\*     |

\* ファイアウォールで保護されていないストレージには、SAS トークンと Azure AD ID を使用してアクセスできます。

> [!IMPORTANT]
> ファイアウォールで保護されているストレージにアクセスする場合に使用できるのは、マネージド ID のみです。 [信頼された Microsoft サービスを許可](../../storage/common/storage-network-security.md#trusted-microsoft-services)する設定を行い、そのリソース インスタンスの[システムによって割り当てられたマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) に明示的に [Azure ロール](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)を割り当てる必要があります。 この場合、インスタンスのアクセス範囲は、マネージド ID に割り当てられた Azure ロールに対応します。
>

## <a name="credentials"></a>資格情報

Azure Storage にあるファイルに対してクエリを実行するには、SQL オンデマンド エンドポイントに、認証情報を含む資格情報が必要です。 2 種類の資格情報が使用されます。
- サーバーレベル資格情報は、`OPENROWSET` 関数を使用して実行されるアドホック クエリに使用されます。 資格情報名はストレージ URL と一致する必要があります。
- データベース スコープ資格情報は、外部テーブルに使用されます。 外部テーブルは、ストレージへのアクセスに使用する必要がある資格情報を使用して `DATA SOURCE` を参照します。

管理者は、ユーザーが資格情報を作成または削除できるようにするために、ユーザーに対して ALTER ANY CREDENTIAL 権限を許可または拒否することができます。

```sql
GRANT ALTER ANY CREDENTIAL TO [user_name];
```

外部ストレージにアクセスするデータベース ユーザーは、資格情報を使用するアクセス許可を持つ必要があります。

### <a name="grant-permissions-to-use-credential"></a>資格情報を使用するためのアクセス許可を付与する

資格情報を使用するには、ユーザーは特定の資格情報に対する `REFERENCES` 権限を持っている必要があります。 specific_user の storage_credential に対する `REFERENCES` 権限を許可するには、次を実行します。

```sql
GRANT REFERENCES ON CREDENTIAL::[storage_credential] TO [specific_user];
```

スムーズな Azure AD パススルーの実行を確保するために、すべてのユーザーには、既定で `UserIdentity` 資格情報を使用する権限が与えられます。

## <a name="server-scoped-credential"></a>サーバースコープ資格情報

サーバースコープ資格情報が使用されるのは、`DATA_SOURCE` が指定されない `OPENROWSET` 関数を SQL ログインが呼び出して、ストレージ アカウント上のファイルを読み取るときです。 サーバースコープ資格情報の名前は、Azure Storage の URL と一致する**必要があります**。 資格情報を追加するには、[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を実行します。 CREDENTIAL NAME 引数の指定が必要になります。 それは、ストレージ内のデータへのパスの一部またはパス全体に一致している必要があります (下記参照)。

> [!NOTE]
> 引数 `FOR CRYPTOGRAPHIC PROVIDER` はサポートされていません。

サーバーレベル資格情報の名前は、`<prefix>://<storage_account_path>/<storage_path>` という形式で、ストレージ アカウントの完全なパス (および必要に応じてコンテナー) と一致する必要があります。 ストレージ アカウント パスについては、次の表で説明します。

| 外部データ ソース       | Prefix | ストレージ アカウント パス                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Storage Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Storage Gen2 | https  | <storage_account>.dfs.core.windows.net              |

サーバースコープ資格情報は、次の認証の種類を使用して Azure Storage にアクセスできるようにします。

### <a name="user-identity"></a>[ユーザー ID](#tab/user-identity)

Azure AD ユーザーは、`Storage Blob Data Owner`、`Storage Blob Data Contributor`、`Storage Blob Data Reader` のいずれかのロールがあれば、Azure Storage 上のあらゆるファイルにアクセスできます。 ストレージにアクセスするために、Azure AD ユーザーの資格情報は必要ありません。 

SQL ユーザーが Azure AD Authentication を使用してストレージにアクセスすることはできません。

### <a name="shared-access-signature"></a>[共有アクセス署名](#tab/shared-access-signature)

次のスクリプトによって作成されるサーバーレベル資格情報は、`OPENROWSET` 関数が SAS トークンを使用して Azure Storage 上の任意のファイルにアクセスするために使用できます。 この資格情報を作成すると、`OPENROWSET` 関数を実行する SQL プリンシパルが有効になり、資格情報名の URL と一致する Azure Storage 上の SAS キーで保護されたファイルを読み取ります。

<*mystorageaccountname*> は、実際のストレージ アカウント名に、<*mystorageaccountcontainername*> は、実際のコンテナー名に置き換えてください。

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='SHARED ACCESS SIGNATURE'
, SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
```

### <a name="managed-identity"></a>[Managed Identity](#tab/managed-identity)

次のスクリプトによって作成されるサーバーレベル資格情報は、`OPENROWSET` 関数がワークスペース マネージド ID トークンを使用して Azure Storage 上の任意のファイルにアクセスするために使用できます。

```sql
CREATE CREDENTIAL [https://<storage_account>.dfs.core.windows.net/<container>]
WITH IDENTITY='Managed Identity'
```

### <a name="public-access"></a>[パブリック アクセス](#tab/public-access)

データベーススコープ資格情報は、一般公開されているファイルへのアクセスを許可する場合には必要がありません。 [データベーススコープ資格情報なしでデータソース](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)を作成して、Azure Storage 上の一般公開されているファイルにアクセスします。

---

## <a name="database-scoped-credential"></a>データベーススコープ資格情報

データベーススコープ資格情報が使用されるのは、任意のプリンシパルが `DATA_SOURCE` を指定した `OPENROWSET` 関数を呼び出すとき、または公開ファイルにアクセスしない[外部テーブル](develop-tables-external-tables.md)のデータを選択するときです。 データベーススコープ資格情報は、ストレージ アカウントの名前と一致する必要はありません。それがストレージの場所を定義するデータ ソースで明示的に使用されるためです。

データベーススコープ資格情報は、次の認証の種類を使用して Azure Storage にアクセスできるようにします。

### <a name="azure-ad-identity"></a>[Azure AD ID](#tab/user-identity)

Azure AD ユーザーは、少なくとも `Storage Blob Data Owner`、`Storage Blob Data Contributor`、`Storage Blob Data Reader` のいずれかのロールがあれば、Azure Storage 上のあらゆるファイルにアクセスできます。 ストレージにアクセスするために、Azure AD ユーザーの資格情報は必要ありません。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
)
```

SQL ユーザーが Azure AD Authentication を使用してストレージにアクセスすることはできません。

### <a name="shared-access-signature"></a>[共有アクセス署名](#tab/shared-access-signature)

次のスクリプトによって作成される資格情報は、資格情報で指定された SAS トークンを使用するストレージ上のファイルにアクセスするために使用されます。 スクリプトでは、この SAS トークンを使用してストレージにアクセスするサンプル外部データ ソースを作成します。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>'
GO
CREATE DATABASE SCOPED CREDENTIAL [SasToken]
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 'sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-04-18T20:42:12Z&st=2019-04-18T12:42:12Z&spr=https&sig=lQHczNvrk1KoYLCpFdSsMANd0ef9BrIPBNJ3VYEIq78%3D';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SasToken
)
```

### <a name="managed-identity"></a>[Managed Identity](#tab/managed-identity)

次のスクリプトによって作成されるデータベーススコープ資格情報は、現在の Azure AD ユーザーをサービスのマネージド ID として借用するために使用できます。 スクリプトでは、ワークスペース ID を使用してストレージにアクセスするサンプル外部データ ソースを作成します。

```sql
-- Optional: Create MASTER KEY if not exists in database:
-- CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<Very Strong Password>
CREATE DATABASE SCOPED CREDENTIAL SynapseIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = SynapseIdentity
)
```

データベーススコープ資格情報は、ストレージ アカウントの名前と一致する必要はありません。それがストレージの場所を定義するデータ ソースで明示的に使用されるためです。

### <a name="public-access"></a>[パブリック アクセス](#tab/public-access)

データベーススコープ資格情報は、一般公開されているファイルへのアクセスを許可する場合には必要がありません。 [データベーススコープ資格情報なしでデータソース](develop-tables-external-tables.md?tabs=sql-ondemand#example-for-create-external-data-source)を作成して、Azure Storage 上の一般公開されているファイルにアクセスします。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.blob.core.windows.net/<container>/<path>'
)
```
---

データベーススコープ資格情報は、外部データ ソースで使用され、そのストレージへのアクセスに使用される認証方法を指定します。

```sql
CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>',
          CREDENTIAL = <name of database scoped credential> 
)
```

## <a name="examples"></a>例

### <a name="access-a-publicly-available-data-source"></a>**一般公開されているデータ ソースにアクセスする**

次のスクリプトを使用して、一般公開されているデータ ソースにアクセスするテーブルを作成します。

```sql
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat]
       WITH ( FORMAT_TYPE = PARQUET)
GO
CREATE EXTERNAL DATA SOURCE publicData
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<public_container>/<path>' )
GO

CREATE EXTERNAL TABLE dbo.userPublicData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [publicData],
       FILE_FORMAT = [SynapseParquetFormat] )
```

データベース ユーザーは、データ ソースを参照する外部テーブルまたは [OPENROWSET](develop-openrowset.md) 関数を使用して、データ ソースからファイルの内容を読み取ることができます。

```sql
SELECT TOP 10 * FROM dbo.userPublicData;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet',
                                DATA_SOURCE = [mysample],
                                FORMAT='PARQUET') as rows;
GO
```

### <a name="access-a-data-source-using-credentials"></a>**資格情報を使用してデータ ソースにアクセスする**

次のスクリプトを変更して、SAS トークン、ユーザーの Azure AD ID、またはワークスペースのマネージド ID を使用して Azure Storage にアクセスする外部テーブルを作成します。

```sql
-- Create master key in databases with some password (one-off per database)
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Y*********0'
GO

-- Create databases scoped credential that use Managed Identity or SAS token. User needs to create only database-scoped credentials that should be used to access data source:

CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity'
GO
CREATE DATABASE SCOPED CREDENTIAL SasCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sv=2019-10-1********ZVsTOL0ltEGhf54N8KhDCRfLRI%3D'

-- Create data source that one of the credentials above, external file format, and external tables that reference this data source and file format:

CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] WITH ( FORMAT_TYPE = PARQUET)
GO

CREATE EXTERNAL DATA SOURCE mysample
WITH (    LOCATION   = 'https://<storage_account>.dfs.core.windows.net/<container>/<path>'
-- Uncomment one of these options depending on authentication method that you want to use to access data source:
--,CREDENTIAL = WorkspaceIdentity 
--,CREDENTIAL = SasCredential 
)

CREATE EXTERNAL TABLE dbo.userData ( [id] int, [first_name] varchar(8000), [last_name] varchar(8000) )
WITH ( LOCATION = 'parquet/user-data/*.parquet',
       DATA_SOURCE = [mysample],
       FILE_FORMAT = [SynapseParquetFormat] );

```

データベース ユーザーは、データ ソースを参照する[外部テーブル](develop-tables-external-tables.md)または [OPENROWSET](develop-openrowset.md) 関数を使用して、データ ソースからファイルの内容を読み取ることができます。

```sql
SELECT TOP 10 * FROM dbo.userdata;
GO
SELECT TOP 10 * FROM OPENROWSET(BULK 'parquet/user-data/*.parquet', DATA_SOURCE = [mysample], FORMAT='PARQUET') as rows;
GO
```

## <a name="next-steps"></a>次のステップ

次の記事では、さまざまなフォルダーの種類やファイルの種類に対してクエリを実行する方法、およびビューの作成と使用について説明します。

- [単一の CSV ファイルに対してクエリを実行する](query-single-csv-file.md)
- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)
- [特定のファイルに対してクエリを実行する](query-specific-files.md)
- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)
- [ビューの作成および使用](create-use-views.md)
- [JSON ファイルに対してクエリを実行する](query-json-files.md)
- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)
