---
title: 'パフォーマンスをチューニングする: Hive、HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Hive、HDInsight、および Azure Data Lake Storage Gen2 を使用する I/O 集中型クエリのチューニング ガイドラインについて説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fb908fe94f940073753ea8e1cde3da2b2a0c4b6b
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034772"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>パフォーマンスをチューニングする: Hive、HDInsight & Azure Data Lake Storage Gen2

既定の設定は、多種多様なユース ケースで適切なパフォーマンスを提供するように設定されています。  I/O 集中型クエリの場合、Hive は Azure Data Lake Storage Gen2 でパフォーマンスが高くなるように調整できます。  

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
* **Data Lake Storage Gen2 アカウント**。 アカウントの作成手順については、[クイック スタート:Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](data-lake-storage-quickstart-create-account.md)」を参照してください。
* Data Lake Storage Gen2 アカウントにアクセスできる **Azure HDInsight クラスター**。 「[Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)」をご覧ください
* **HDInsight での Hive の実行**。  HDInsight の Hive ジョブを実行する方法については、[HDInsight での Hive の使用](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)に関する記事を参照してください。
* **Data Lake Storage Gen2 のパフォーマンス チューニング ガイドライン**。  一般的なパフォーマンスの概念については、[Data Lake Storage Gen2 のパフォーマンス チューニング ガイダンス](data-lake-storage-performance-tuning-guidance.md)を参照してください。

## <a name="parameters"></a>パラメーター

Data Lake Storage Gen2 のパフォーマンスを向上するためのチューニングに重要な設定を次に示します。

* **hive.tez.container.size** – 各タスクで使用されるメモリの量

* **tez.grouping.min-size** – 各マッパーの最小サイズ

* **tez.grouping.max-size** – 各マッパーの最大サイズ

* **hive.exec.reducer.bytes.per.reducer** – 各レジューサーのサイズ

**hive.tez.container.size** - このコンテナーのサイズは、各タスクで使用可能なメモリの量を決定します。  これは、Hive でのコンカレンシーを制御するための主要な入力です。  

**tez.grouping.min-size** – このパラメーターを使用して、各マッパーの最小サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも小さい場合、Tez はここに設定された値を使用します。

**tez.grouping.max-size** – このパラメーターを使用して、各マッパーの最大サイズを設定できます。  Tez が選択したマッパーの数がこのパラメーターの値よりも大きい場合、Tez はここに設定された値を使用します。

**hive.exec.reducer.bytes.per.reducer** – このパラメーターは、各レジューサーのサイズを設定します。  既定では、各レジューサーは 256 MB です。  

## <a name="guidance"></a>ガイダンス

**hive.exec.reducer.bytes.per.reducer の設定** – データが圧縮されていない場合は、既定値で問題ありません。  圧縮されているデータの場合は、レジューサーのサイズを小さく必要があります。  

**hive.tez.container.size の設定** – 各ノードのメモリは yarn.nodemanager.resource.memory-mb によって指定されるため、既定で HDI クラスターに適切に設定されます。  YARN で適切なメモリを設定する方法の詳細については、こちらの[投稿](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom)を参照してください。

I/O 集中型のワークロードでは、Tez コンテナーのサイズの削減による並列処理の増加からメリットを得ることができます。 これにより、コンテナーの数が増え、コンカレンシーが高まります。  ただし、一部の Hive クエリでは、大量のメモリ が必要です (例: MapJoin)。  タスクに十分なメモリがない場合は、実行時にメモリ不足例外が発生します。  メモリ不足例外が発生した場合は、メモリを増やす必要があります。   

実行される同時実行タスクの数または並列処理は、YARN メモリの総量によって制限されます。  YARN コンテナーの数は、実行できる同時実行タスクの数を決定します。  ノードごとの YARN メモリを確認するには、Ambari を参照することができます。  YARN に移動し、[Configs] \(構成) タブを表示します。YARN メモリは、このウィンドウに表示されます。  

- YARN メモリの合計 = ノード数 * ノードごとの YARN メモリ
- YARN コンテナーの数 = YARN メモリの合計 / Tez コンテナーのサイズ

Data Lake Storage Gen2 を使用してパフォーマンスを向上させる鍵は、コンカレンシーをできるだけ高くすることです。  作成する必要があるタスクの数は Tez が自動的に計算するため、設定する必要はありません。   

## <a name="example-calculation"></a>計算例

たとえば、8 ノードの D14 クラスターがあるとします。  

- YARN メモリの合計 = ノード数 * ノードごとの YARN メモリ
- YARN メモリの合計 = 8 ノード * 96 GB = 768 GB
- YARN コンテナーの数 = 768 GB / 3072 MB = 256

## <a name="further-information-on-hive-tuning"></a>Hive のチューニングに関する他の情報

Hive クエリをチューニングする際に役立つ、いくつかのブログを次に示します。
* [HDInsight の Hadoop に対する Hive クエリの最適化](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Azure HDInsight で Apache Hive クエリを最適化する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-optimize-hive-query)
* [HDInsight での Hive の最適化に関する刺激的なトーク](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
