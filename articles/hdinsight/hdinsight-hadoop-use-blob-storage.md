---
title: HDFS と互換性のある Azure Storage のデータのクエリ - Azure HDInsight
description: Azure Storage と Azure Data Lake Storage からのデータに対してクエリを実行し、分析結果を格納する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 55cddf5317938dea353517cde7260a1aa531d1df
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061260"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Storage を使用する

HDInsight クラスターでデータを分析するには、そのデータを [Azure Storage](../storage/common/storage-introduction.md)、[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md) のいずれか、またはその組み合わせに格納できます。 いずれのストレージ オプションでも、計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

Apache Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス中に、Azure Storage 内の BLOB コンテナーを既定のファイル システムとして指定できます。または、HDInsight 3.6 では、Azure Storage または Azure Data Lake Storage Gen 1/Azure Data Lake Store Gen 2 のどちらかを既定のファイル システムとして選択できます (いくつかの例外があります)。 Data Lake Storage Gen 1 を既定のストレージとリンクされたストレージの両方として使用することのサポート可能性については、「[ HDInsight クラスターの利用可能性](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)」を参照してください。

この記事では、HDInsight クラスターでの Azure Storage の動作について説明します。 HDInsight クラスターでの Data Lake Storage Gen 1 の動作については、「[Azure HDInsight クラスターで Azure Data Lake Storage を使用する](hdinsight-hadoop-use-data-lake-store.md)」を参照してください。 HDInsight クラスターの作成について詳しくは、[HDInsight での Apache Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

> [!IMPORTANT]  
> ストレージ アカウントの種類 **BlobStorage** は、HDInsight クラスターのセカンダリ ストレージとしてのみ使用できます。

| Storage アカウントの種類 | サポートされているサービス | サポートされているパフォーマンス レベル | サポートされているアクセス層 |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (汎用 v2)  | BLOB     | Standard                    | ホット、クール、アーカイブ\*   |
| Storage (汎用 V1)   | BLOB     | Standard                    | 該当なし                    |
| BlobStorage                    | BLOB     | Standard                    | ホット、クール、アーカイブ\*   |

ビジネス データを格納するために既定の BLOB コンテナーを使用することはお勧めできません。 ストレージ コストを削減するために、既定の BLOB コンテナーの使用後、コンテナーを毎回削除することをお勧めします。 既定のコンテナーには、アプリケーション ログとシステム ログが格納されます。 コンテナーを削除する前に、ログを取り出してください。

1 つの BLOB コンテナーを複数のクラスターの既定のファイル システムとして共有することはサポートされていません。

> [!NOTE]  
> アーカイブ アクセス層はオフライン層であり、取得の際に数時間の待ち時間があるので、HDInsight での使用は推奨されません。 詳しくは、「[アーカイブ アクセス層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)」をご覧ください。

## <a name="access-files-from-the-cluster"></a>クラスターからファイルにアクセスする

複数の方法で、HDInsight クラスターから Data Lake Storage のファイルにアクセスできます。 この URI スキームは、暗号化なしのアクセス (*wasb*: プレフィックス) と SSL で暗号化されたアクセス (*wasbs*) に対応しています。 同じ Azure リージョン内のデータにアクセスする場合でも、できる限り *wasbs* を使用することをお勧めします。

* **完全修飾名の使用**。 この方法により、アクセスするファイルへの完全パスを指定します。

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **短縮されたパスの使用**。 この方法により、クラスター ルートへのパスを次に置き換えます。

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **相対パスの使用**。 この方法により、アクセスするファイルへの相対パスのみを指定します。

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>データ アクセスの例

例は、クラスターのヘッド ノードへの [ssh 接続](./hdinsight-hadoop-linux-use-ssh-unix.md)に基づいています。 この例では、3 つの URI スキームがすべて使用されています。 `CONTAINERNAME` と `STORAGEACCOUNT` を関連する値に置き換えます。

#### <a name="a-few-hdfs-commands"></a>いくつかの hdfs コマンド

1. ローカル ストレージで単純なファイルを作成します。

    ```bash
    touch testFile.txt
    ```

1. クラスター ストレージにディレクトリを作成します。

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. ローカル ストレージからクラスター ストレージにデータをコピーします。

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. クラスター ストレージのディレクトリの内容を一覧表示します。

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> HDInsight の外部から BLOB を操作する場合、ほとんどのユーティリティで WASB 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

#### <a name="creating-a-hive-table"></a>Hive テーブルの作成

3 つのファイルの場所は説明目的で提示されています。 実際の実行では、`LOCATION` エントリを 1 つだけ使用します。

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="identify-storage-path-from-ambari"></a>Ambari からストレージ パスを特定する

* 構成済みの既定ストアへの完全パスを特定するには、

    **[HDFS]** 、 **[構成]** の順に移動し、フィルター入力ボックスに「`fs.defaultFS`」と入力します。

* Wasb ストアがセカンダリ ストレージとして構成されているかどうかを確認するには、

    **[HDFS]** 、 **[構成]** の順に移動し、フィルター入力ボックスに「`blob.core.windows.net`」と入力します。

## <a name="blob-containers"></a>BLOB コンテナー

BLOB を使用するには、まず、[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md)を作成します。 その一環として、ストレージ アカウントを作成する Azure リージョンを指定します。 クラスターとストレージ アカウントは、同じリージョンに置く必要があります。 Hive メタストア SQL Server データベースと Apache Oozie メタストア SQL Server データベースも、同じリージョンに配置する必要があります。

作成される各 BLOB は、どこにあるとしても、Azure ストレージ アカウント内のコンテナーに属します。 このコンテナーは、HDInsight の外部で作成された既存の BLOB ストレージ コンテナーか、HDInsight クラスター用に作成されたコンテナーであってもかまいません。

既定の BLOB コンテナーには、ジョブ履歴やログなどのクラスター固有の情報が格納されます。 既定の BLOB コンテナーと複数の HDInsight クラスターを共有しないでください。 ジョブ履歴が破損する場合があります。 各クラスターで別のコンテナーを使用し、既定のストレージ アカウントではなく、関連するすべてのクラスターのデプロイで指定された、リンクされているストレージ アカウントに共有データを格納することをお勧めします。 リンクされているストレージ アカウントの構成の詳細については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 ただし、元の HDInsight クラスターを削除した後でも既定のストレージ コンテナーを再利用できます。 HBase クラスターでは、削除された HBase クラスターで使用される既定の BLOB コンテナーを使用して、新しい HBase クラスターを作成することで、HBase テーブルのスキーマとデータを実際に保持できます。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Azure マシンとの対話

Microsoft では、Azure Storage を操作する次のツールを提供しています。

| ツール | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>追加ストレージ アカウントの使用

HDInsight クラスターを作成しているときに、そのクラスターに関連付ける Azure ストレージ アカウントを指定します。 作成プロセス時またはクラスターが作成された後に、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属するストレージ アカウントをさらに追加することもできます。 ストレージ アカウントをさらに追加する手順については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。

> [!WARNING]  
> HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

## <a name="next-steps"></a>次のステップ

この記事では、HDInsight で HDFS と互換性のある Azure Storage を使う方法について説明しました。 これにより、収集したデータを長期にわたって格納できるスケーラブルなソリューションを構築できます。さらに HDInsight を使用すると、格納されている構造化データと非構造化データから有益な情報を得ることができます。

詳細については、次を参照してください。

* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure Data Lake Storage の概要](../data-lake-store/data-lake-store-get-started-portal.md)
* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [Azure Storage の Shared Access Signature を使用した HDInsight でのデータへのアクセスの制限](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [チュートリアル:Azure HDInsight で対話型クエリを使用してデータの抽出、変換、読み込みを行う](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
