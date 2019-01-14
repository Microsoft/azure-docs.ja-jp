---
title: '書き込み: Data Prep Python SDK'
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Data Prep SDK を使用してデータを書き込む方法について説明します。 データ フローの任意の時点でデータを書き込むことができます。また、サポートされている任意の場所 (ローカル ファイル システム、Azure Blob Storage、Azure Data Lake Storage) のファイルに書き込むことができます。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: a6abdaff986df3c457a0118f6e143fe4ff0daf49
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384345"
---
# <a name="write-data-using-the-azure-machine-learning-data-prep-sdk"></a>Azure Machine Learning Data Prep SDK を使用してデータを書き込む

この記事では、Azure Machine Learning Data Prep SDK を使用してデータを読み込むさまざまな方法について説明します。 出力データは、データ フローの任意の時点で書き込みを行うことができ、書き込みは、結果のデータ フローへのステップとして追加され、データ フローがあるたびに実行されます。 データは複数のパーティション ファイルに書き込まれ、並列書き込みが可能です。

パイプライン内に存在する書き込みのステップ数に制限はないため、書き込みステップを簡単に追加して、トラブルシューティングやその他のパイプライン用に中間結果を取得することができます。

書き込みステップを実行するたびに、データ フローの全データのプルが実行されます。 たとえば、3 つの書き込みステップがあるデータ フローの場合、データ セット内の各レコードの読み取りと処理が 3 回行われます。

## <a name="supported-data-types-and-location"></a>サポートされているデータ型と場所

次のファイル形式がサポートされています
-   区切りファイル (CSV や TSV など)
-   Parquet ファイル

[Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk) を使用して、次の場所にデータを書き込みます。
+ ローカル ファイル システム
+ Azure Blob Storage
+ Azure Data Lake Storage

## <a name="spark-considerations"></a>Spark に関する考慮事項

Spark でデータ フローを実行する場合は、空のフォルダーに書き込む必要があります。 既存フォルダーへの書き込みを実行しようとすると失敗します。 ターゲット フォルダーが空であることを確認するか、実行ごとに別のターゲットの場所を使用してください。そうしないと、書き込みは失敗します。

## <a name="monitoring-write-operations"></a>書き込み操作の監視

作業を容易にするために、書き込みが完了すると、SUCCESS という名前のセンチネル ファイルが生成されます。 このファイルにより、パイプライン全体が完了するのを待つことなく、中間書き込みが完了したときを特定できます。

## <a name="example-write-code"></a>書き込みコードの例

この例では、まずデータ フローにデータを読み込みます。 このデータをさまざまな形式で再利用します。

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(10)
```

出力例:
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   なし|       NO|     NO  |   ENRS    |NaN    |   NaN |   (NaN)|    
|   1|      10003.0 |   99999.0 |   なし|       NO|     NO  |   ENSO|       NaN|        NaN |(NaN)|   
|   2|  10010.0|    99999.0|    なし|   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    なし|   NO| NO| |   (NaN)|    NaN|    (NaN)|
|4| 10014.0|    99999.0|    なし|   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    なし|   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   なし|   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    なし|   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    なし|   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    なし|   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="delimited-file-example"></a>区切りファイルの例

次のコードは、区切り記号入りファイルにデータを書き込むため`write_to_csv`関数を使用します。

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

出力例:
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   ERROR |       NO|     NO  |   ENRS    |ERROR    |   ERROR |   ERROR|    
|   1|      10003.0 |   99999.0 |   ERROR |       NO|     NO  |   ENSO|       ERROR|        ERROR |ERROR|   
|   2|  10010.0|    99999.0|    ERROR |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    ERROR |   NO| NO| |   ERROR|    ERROR|    ERROR|
|4| 10014.0|    99999.0|    ERROR |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    ERROR |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   ERROR |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    ERROR |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    ERROR |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    ERROR |   NO| SV|     |77000.0|   15500.0|    120.0|

上記の出力では、正しく解析されなかった数値があるため、数値列にエラーがいくつか表示されます。 CSV に書き込まれると、既定でこれらの null 値は "ERROR" という文字列に置き換えられます。

書き込みの呼び出しの一部としてパラメーターを追加し、null 値を表すために使用する文字列を指定することができます。

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(10)
```

上記のコードでは、次の出力が生成されます。
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   BadData |       NO|     NO  |   ENRS    |BadData    |   BadData |   BadData|    
|   1|      10003.0 |   99999.0 |   BadData |       NO|     NO  |   ENSO|       BadData|        BadData |BadData|   
|   2|  10010.0|    99999.0|    BadData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    BadData |   NO| NO| |   BadData|    BadData|    BadData|
|4| 10014.0|    99999.0|    BadData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    BadData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   BadData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    BadData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    BadData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    BadData |   NO| SV|     |77000.0|   15500.0|    120.0|

### <a name="parquet-file-example"></a>Parquet ファイルの例

`write_to_csv` と同様、`write_to_parquet` 関数では、データ フローの実行時に実行される書き込みの Parquet ステップを含む新しいデータ フローが返されます。

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

データ フローを実行して書き込み操作を実行します。

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(10)
```

上記のコードでは、次の出力が生成されます。
|   |  Column1 |    Column2 | Column3 | Column4  |Column5   | Column6 | Column7 | Column8 | Column9 |
| -------- |  -------- | -------- | -------- |  -------- |  -------- |  -------- |  -------- |  -------- |  -------- |
| 0 |   10000.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENRS    |MiscreantData    |   MiscreantData |   MiscreantData|    
|   1|      10003.0 |   99999.0 |   MiscreantData |       NO|     NO  |   ENSO|       MiscreantData|        MiscreantData |MiscreantData|   
|   2|  10010.0|    99999.0|    MiscreantData |   NO| JN| ENJA|   70933.0|    -8667.0 |90.0|
|3| 10013.0|    99999.0|    MiscreantData |   NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014.0|    99999.0|    MiscreantData |   NO| NO| ENSO|   59783.0|    5350.0| 500.0|
|5| 10015.0|    99999.0|    MiscreantData |   NO| NO| ENBL|   61383.0|    5867.0| 3270.0|
|6| 10016.0 |99999.0|   MiscreantData |   NO| NO|     |64850.0|   11233.0|    140.0|
|7| 10017.0|    99999.0|    MiscreantData |   NO| NO| ENFR|   59933.0|    2417.0| 480.0|
|8| 10020.0|    99999.0|    MiscreantData |   NO| SV|     |80050.0|   16250.0|    80.0|
|9| 10030.0|    99999.0|    MiscreantData |   NO| SV|     |77000.0|   15500.0|    120.0|
