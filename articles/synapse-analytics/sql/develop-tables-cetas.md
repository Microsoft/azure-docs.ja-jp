---
title: Synapse SQL の CREATE EXTERNAL TABLE AS SELECT (CETAS)
description: Synapse SQL での CREATE EXTERNAL TABLE AS SELECT (CETAS) の使用
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 09/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 23b74c06ebd45092b9efe36a870eeac7f6fb828a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677558"
---
# <a name="cetas-with-synapse-sql"></a>Synapse SQL での CETAS

専用 SQL プールまたはサーバーレス SQL プールで CREATE EXTERNAL TABLE AS SELECT (CETAS) を使用して、次のタスクを実行できます。  

- 外部テーブルを作成する
- Transact-SQL SELECT ステートメントの結果を並行して次にエクスポートする:

  - Hadoop
  - Azure Storage BLOB
  - Azure Data Lake Storage Gen2

## <a name="cetas-in-dedicated-sql-pool"></a>専用 SQL プールでの CETAS

専用 SQL プールの場合、CETAS の使用法と構文については、[CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=azure-sqldw-latest&preserve-view=true) に関する記事をご覧ください。 また、専用 SQL プールを使用した CTAS のガイダンスについては、[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) に関する記事を参照してください。

## <a name="cetas-in-serverless-sql-pool"></a>サーバーレス SQL プールでの CETAS

サーバーレス SQL プールを使用する場合は、CETAS を使用して外部テーブルを作成し、クエリ結果を Azure Storage Blob または Azure Data Lake Storage Gen2 にエクスポートします。

## <a name="syntax"></a>構文

```syntaxsql
CREATE EXTERNAL TABLE [ [database_name  . [ schema_name ] . ] | schema_name . ] table_name
    WITH (
        LOCATION = 'path_to_folder',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name  
)
    AS <select_statement>  
[;]

<select_statement> ::=  
    [ WITH <common_table_expression> [ ,...n ] ]  
    SELECT <select_criteria>
```

## <a name="arguments"></a>引数

*[ [ *database_name* . [ *schema_name* ] . ] | *schema_name* . ] *table_name**

作成するテーブルの 1 つから 3 つの部分で構成される名前。 外部テーブルの場合、サーバーレス SQL プールではテーブルのメタデータのみが格納されます。 サーバーレス SQL プールでは、実際のデータの移動または格納は行われません。

LOCATION = *'path_to_folder'*

外部データ ソースで SELECT ステートメントの結果を書き込む場所を指定します。 ルート フォルダーは、外部データ ソースで指定されたデータの場所です。 LOCATION は、フォルダーを指す必要があり、末尾に / を付ける必要があります。 例: aggregated_data/

DATA_SOURCE = *external_data_source_name*

外部データが格納される場所を含む外部データ ソース オブジェクトの名前を指定します。 外部データ ソースを作成するには、[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](develop-tables-external-tables.md#create-external-data-source) を使用します。

FILE_FORMAT = *external_file_format_name*

外部データ ファイルの形式を含む、外部ファイル形式オブジェクトの名前を指定します。 外部ファイル形式を作成するには、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](develop-tables-external-tables.md#create-external-file-format) を使用します。 現在サポートされている外部ファイル形式は、FORMAT_TYPE=PARQUET と FORMAT_TYPE=DELIMITEDTEXT だけです。 DELIMITEDTEXT 形式に対する GZip 圧縮はサポートされません。

WITH *<common_table_expression>*

共通テーブル式 (CTE) と呼ばれる一時的な名前付き結果セットを指定します。 詳細については、「[WITH common_table_expression (Transact-SQL)](/sql/t-sql/queries/with-common-table-expression-transact-sql?view=azure-sqldw-latest&preserve-view=true)」を参照してください。

SELECT <select_criteria>

SELECT ステートメントの結果を新しいテーブルに追加します。 *select_criteria* は、新しいテーブルにコピーするデータを決定する SELECT ステートメントの本文です。 SELECT ステートメントについては、「[SELECT (Transact-SQL)](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true)」を参照してください。

> [!NOTE]
> SELECT での ORDER BY 句は、CETAS ではサポートされていません。

## <a name="permissions"></a>アクセス許可

CETAS を機能させるには、フォルダーの内容を一覧表示し、LOCATION フォルダーに書き込むためのアクセス許可が必要です。

## <a name="examples"></a>例

これらの例では、CETAS を使用して、年および州別に集計された総人口を、population_ds データソース内の aggregated_data フォルダーに保存します。

このサンプルは、前に作成した資格情報、データ ソース、および外部ファイル形式に依存しています。 [外部テーブル](develop-tables-external-tables.md)に関するドキュメントを参照してください。 同じデータ ソース内の別のフォルダーにクエリ結果を保存するには、LOCATION 引数を変更します。 

結果を別のストレージ アカウントに保存するには、DATA_SOURCE 引数に別のデータ ソースを作成して使用します。

> [!NOTE]
> 次のサンプルでは、パブリックの Azure オープン データ ストレージ アカウントを使用します。 これは読み取り専用です。 これらのクエリを実行するには、書き込みアクセス許可があるデータ ソースを指定する必要があります。

```sql
-- use CETAS to export select statement with OPENROWSET result to  storage
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM
    OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=*/*.parquet',
    FORMAT='PARQUET') AS [r]
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

次のサンプルでは、CETAS のソースとして外部テーブルを使用します。 これは、以前に作成した資格情報、データ ソース、外部ファイル形式、および外部テーブルに依存しています。 [外部テーブル](develop-tables-external-tables.md)に関するドキュメントを参照してください。

```sql
-- use CETAS with select from external table
CREATE EXTERNAL TABLE population_by_year_state
WITH (
    LOCATION = 'aggregated_data/',
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)  
AS
SELECT decennialTime, stateName, SUM(population) AS population
FROM census_external_table
GROUP BY decennialTime, stateName
GO

-- you can query the newly created external table
SELECT * FROM population_by_year_state
```

## <a name="supported-data-types"></a>サポートされるデータ型

CETAS を使用して、次の SQL データ型の結果セットを格納できます。

- binary
- varbinary
- char
- varchar
- nchar
- nvarchar
- smalldate
- date
- DATETIME
- datetime2
- datetimeoffset
- time
- decimal
- numeric
- float
- real
- bigint
- tinyint
- smallint
- INT
- bigint
- bit
- money
- smallmoney
- UNIQUEIDENTIFIER

> [!NOTE]
> 1 MB を超える LOB を CETAS と共に使用することはできません。

## <a name="next-steps"></a>次のステップ

[Apache Spark for Azure Synapse 外部テーブル](develop-storage-files-spark-tables.md)のクエリを試してみます。
