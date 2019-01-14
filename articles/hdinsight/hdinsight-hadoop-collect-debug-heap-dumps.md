---
title: ヒープ ダンプによる Apache Hadoop サービスのデバッグおよび分析
description: デバッグおよび分析に利用できるように、自動的に Apache Hadoop サービスのヒープ ダンプを収集して Azure BLOB ストレージ アカウント内に配置します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 698806bdedd9994f2c9de53118cb42c9df1c36cd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724404"
---
# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-apache-hadoop-services"></a>BLOB ストレージのヒープ ダンプを収集して Apache Hadoop サービスをデバッグおよび分析する
[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

ヒープ ダンプには、ダンプが作成された時点の変数の値を含む、アプリケーションのメモリのスナップショットが含まれています。 これらは、実行時に発生する問題を診断するのに便利です。 [Apache Hadoop](https://hadoop.apache.org/) サービスのヒープ ダンプを自動的に収集し、HDInsightHeapDumps 下のユーザーの Azure BLOB ストレージ アカウント内に置くことができます。

さまざまなサービスに対するヒープ ダンプの収集は、各クラスター上のサービスに対して、個別に有効にする必要があります。 既定では、クラスターに対してこの機能はオフになっています。 これらのヒープ ダンプのサイズは大きくなる可能性があるため、収集を有効にした後はヒープ ダンプの保存先である BLOB ストレージ アカウントを定期的に確認してください。

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。 この記事の情報は、Windows ベースの HDInsight にのみ適用されます。 Linux ベースの HDInsight については、「[Linux ベースの HDInsight で Apache Hadoop サービスのヒープ ダンプを有効にする](hdinsight-hadoop-collect-debug-heap-dump-linux.md)」をご覧ください。


## <a name="eligible-services-for-heap-dumps"></a>ヒープ ダンプの対象サービス
次のサービスのヒープ ダンプを有効にできます。

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2、metastore、derbyserver
* **mapreduce** - jobhistoryserver
* **Apache yarn** - resourcemanager、nodemanager、timelineserver
* **Apache hdfs** - datanode、secondarynamenode、namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>ヒープ ダンプを有効にする構成要素
サービスのヒープ ダンプを有効にするには、そのサービスのセクション内に適切な構成要素を設定する必要があります。これは **service_name** で指定します。

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

**service_name** の値には、上記のように、tempelton、hiveserver2、metastore、derbyserver、jobhistoryserver、resourcemanager、nodemanager、timelineserver、datanode、secondarynamenode、namenode のいずれかを指定できます。

## <a name="enable-using-azure-powershell"></a>Azure PowerShell を使用して有効にする
たとえば、Azure PowerShell を使用して jobhistoryserver のヒープ ダンプを有効にするには、次のスクリプトを実行します。

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>.NET SDK を使用して有効にする
たとえば、Azure HDInsight .NET SDK を使用して jobhistoryserver のヒープ ダンプを有効にするには、次のコードを使用します。

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
