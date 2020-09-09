---
title: YARN の高可用性 Spark Streaming ジョブ - Azure HDInsight
description: Azure HDInsight での高可用性シナリオ用に Apache Spark Streaming を設定する方法
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2ec0bf460a73f95e18e2e9221e8cbd8d4e14ff77
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086213"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>YARN で高可用性 Apache Spark Streaming ジョブを作成する

[Apache Spark](https://spark.apache.org/) Streaming を使用すると、データ ストリームを処理するためのスケーラブルで、かつ高スループットのフォールト トレラント アプリケーションを実装できます。 HDInsight Spark クラスター上の Spark Streaming アプリケーションを Azure Event Hubs、Azure IoT Hub、[Apache Kafka](https://kafka.apache.org/)、[Apache Flume](https://flume.apache.org/)、Twitter、[ZeroMQ](http://zeromq.org/)、RAW TCP ソケットなどの異なる種類のデータソース、または [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) ファイル システムの変更を監視することにより接続できます。 Spark Streaming は、ノード障害の場合でも、特定のイベントが厳密に 1 回だけ処理されることを保証して、フォールト トレランスをサポートします。

Spark Streaming が作成する実行時間の長いジョブの間に、データに変換を適用して、結果をファイル システム、データベース、ダッシュボード、コンソールにプッシュできます。 Spark Streaming は、定義された期間についてイベントのバッチを最初に収集することで、データのマイクロバッチを処理します。 次に、そのバッチを処理と出力のために送信します。 バッチの間隔は、通常、1 秒未満で定義されます。

![Spark Streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStream

Spark Streaming は、"*分離されたストリーム*" (DStream) を使ってデータの継続的なストリームを表します。 この DStream は、Event Hubs または Kafka などの入力ソースから作成するか、別の DStream に変換を適用して作成することができます。 イベントが Spark Streaming アプリケーションに到着すると、イベントは確実な方法で格納されます。 つまり、イベント データは複数のノードがコピーを持つようにレプリケートされます。 これにより、単一ノードに障害が発生しても、イベントが失われることはありません。

Spark コアは *Resilient Distributed Dataset* (RDD) を使います。 RDD は、クラスター内の複数のノードにデータを分散します。ここでは通常、パフォーマンスを最大限高めるため、各ノードがデータを完全にメモリ内に保持します。 各 RDD は、バッチ間隔の間に収集されたイベントを表します。 バッチ間隔が経過すると、Spark Streaming はその間隔内のすべてのデータを含む新しい RDD を生成します。 この継続的な一連の RDD が DStream に収集されます。 Spark Streaming アプリケーションは、各バッチの RDD に格納されているデータを処理します。

![Spark DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark Structured Streaming ジョブ

Spark Structured Streaming は、構造化データのストリーミングで使うための分析エンジンとして、Spark 2.0 で導入されました。 Spark Structured Streaming は、SparkSQL バッチ処理エンジン API を使います。 Spark Streaming と同様、Spark Structured Streaming は、連続的に到着するデータのマイクロバッチに対して計算を実行します。 Spark Structured Streaming は、無制限の行を含む入力テーブルとしてデータのストリームを表します。 つまり、新しいデータが到着することで入力テーブルは拡大し続けます。 この入力テーブルは実行時間の長いクエリによって継続的に処理され、結果は出力テーブルに書き込まれます。

![Spark Structured Streaming](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Structured Streaming では、システムに到着したデータは入力テーブルにすぐに取り込まれます。 この入力テーブルに対して操作を実行するクエリを記述します。 クエリの出力により、結果テーブルと呼ばれる別のテーブルが生成されます。 結果テーブルにはクエリの結果が含まれ、そこからデータを取り出してリレーショナル データベースなどの外部データ ストアに送信します。 "*トリガー間隔*" は、入力テーブルのデータを処理するタイミングを設定します。 既定では、Structured Streaming はデータが到着するとすぐに処理します。 ただし、長い間隔で実行するようにトリガーを構成し、時間ベースのバッチでストリーミング データを処理することもできます。 結果テーブル内のデータは、新しいデータが出現するたびに更新されますから、ストリーミング クエリが開始してからのすべての出力データを含むようにする (*完全モード*)、最後にクエリが処理されてからの新しいデータだけを含むようにすることも (*追加モード*)。

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>フォールト トレラントな Spark Streaming ジョブを作成する

Spark Streaming ジョブ用に高可用性環境を作成するには、最初に、個々のジョブを障害発生時に復旧するようにコーディングします。 このような自己復旧ジョブはフォールト トレラントです。

RDD には、高可用性でフォールト トレラントの Spark Streaming ジョブを支援するいくつかのプロパティがあります：

* DStream として RDD に格納される入力データのバッチは、フォールト トレランスのために自動的にメモリにレプリケートされます。
* ワーカーの障害により失われたデータは、別のワーカー ノードが利用可能な場合は、ワーカーでレプリケートされた入力データから再計算できます。
* 障害/脱落からの復旧はメモリ内の計算によって行われるので、高速障害復旧は 1 秒以内に実行できます。

### <a name="exactly-once-semantics-with-spark-streaming"></a>Spark Streaming での 1 回 (1 回だけ) のセマンティクス

各イベントを 1 回 (1 回だけ) 処理するアプリケーションを作成するには、問題が発生した後にすべてのシステム障害ポイントが再起動する方法、およびデータの損失を回避する方法を検討します。 厳密に 1 回のセマンティクスでは、いずれの時点でもデータが失われず、そのメッセージ処理は、障害が発生した場所に関係なく再開可能である必要があります。 「[イベント処理を 1 回のみ伴う Spark Streaming ジョブの作成](apache-spark-streaming-exactly-once.md)」を参照してください。

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming と Apache Hadoop YARN

HDInsight では、クラスターの処理は *Yet Another Resource Negotiator* (YARN) によって調整されます。 Spark Streaming の高可用性の設計には、Spark Streaming の技法だけでなく、YARN コンポーネントの技法も含まれます。  YARN を使った構成の例を次に示します。

![YARN のアーキテクチャ](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

次のセクションでは、この構成の設計に関する考慮事項について説明します。

### <a name="plan-for-failures"></a>障害に備えて計画する

高可用性対応の YARN の構成を作成するには、可能性のある Executor またはドライバーの障害に対して計画する必要があります。 一部の Spark Streaming ジョブには、追加の構成とセットアップを必要とするデータ保証要件も含まれます。 たとえば、ストリーミング アプリケーションには、ストリーミング ホスト システムまたは HDInsight クラスター内でどのようなエラーが発生してもデータ損失ゼロを保証するビジネス要件が設けられる場合があります。

**Executor** で障害が発生した場合、そのタスクとレシーバーは Spark によって自動的に再起動されるため、構成変更は必要ありません。

一方、**ドライバー**で障害が発生した場合は、それに関連付けられたすべての Executor が失敗し、受信したすべてのブロックと計算結果が失われます。 ドライバーの障害から復旧するには、*DStream のチェックポイント処理*を使用します。詳細については、[イベント処理を 1 回のみ伴う Spark Streaming ジョブの作成](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers)を参照してください。 DStream のチェックポイント処理は、DStream の "*有向非巡回グラフ*" (DAG) を、Azure Storage などのフォールト トレラント ストレージに定期的に保存します。  チェックポイント処理を使うと、Spark Structured Streaming は、障害が発生したドライバーをチェックポイント情報から再起動できます。  このドライバーの再起動により、新しい Executor が起動し、レシーバーも再起動します。

DStream チェックポイント処理でドライバーを復旧するには:

* 構成設定 `yarn.resourcemanager.am.max-attempts` を使って、YARN でのドライバーの自動再起動を構成します。
* `streamingContext.checkpoint(hdfsDirectory)` を使って、HDFS 互換のファイル システムにチェックポイント ディレクトリを設定します。
* 復旧にチェックポイントを使うように、ソース コードを作り直します。次はその例です。

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` で先書きログ (WAL) を有効にすることで失われたデータの復旧を構成し、`StorageLevel.MEMORY_AND_DISK_SER` で入力 DStream のメモリ内レプリケーションを無効にします。

要約すると、チェックポイント処理、WAL および信頼性の高いレシーバーを使うことで、"少なくとも 1 回" のデータ復旧が可能ということです：

* 受信したデータが失われておらず、出力がべき等またはトランザクション可能である限り、厳密に1 回だけです。
* レシーバーまたは WAL を使うのではなく、レプリケートされたログとして Kafka を使う新しい Kafka Direct アプローチでは、厳密に 1 回だけです。

### <a name="typical-concerns-for-high-availability"></a>高可用性に関する一般的な考慮事項

* バッチ ジョブよりストリーミング ジョブを監視する方がより困難です。 Spark Streaming ジョブは一般に実行時間が長く、YARN はジョブが終了するまでログを集計しません。  Spark のチェックポイントはアプリケーションまたは Spark のアップグレードの間に失われ、ユーザーはアップグレード中にチェックポイント ディレクトリをクリアする必要があります。

* クライアントで障害が発生した場合でもドライバーを実行するよう、YARN クラスター モードを構成します。 ドライバーの自動再起動を設定するには:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark と Spark Streaming UI には、構成可能なメトリック システムがあります。 Graphite/Grafana などの追加ライブラリを使って、"num records processed"、"memory/GC usage on driver & executors"、"total delay"、"utilization of the cluster" などのダッシュボード メトリックをダウンロードすることもできます。 Structured Streaming バージョン 2.1 以降では、`StreamingQueryListener` を使って追加メトリックを収集できます。

* 実行時間の長いジョブをセグメント化する必要があります。  Spark Streaming アプリケーションをクラスターに送信するときは、ジョブが実行される YARN キューを定義する必要があります。 [YARN Capacity Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) を使って、実行時間の長いジョブを個別のキューに送信できます。

* ストリーミング アプリケーションを正常にシャットダウンします。 オフセットがわかっていて、すべてのアプリケーション状態が外部に格納される場合は、プログラムによりストリーミング アプリケーションを適切な場所で停止することができます。 1 つの方法は、*n* 秒ごとに外部フラグをチェックして、Spark の "スレッド フック" を使うことです。 アプリケーションを開始するときに HDFS に作成されて、停止するときに削除される、"*マーカー ファイル*" を使うこともできます。 マーカー ファイルの方法では、次のようなコードを呼び出す独立したスレッドを Spark アプリケーションで使います。

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>次のステップ

* [Apache Spark Streaming の概要](apache-spark-streaming-overview.md)
* [イベント処理を 1 回だけ伴う Apache Spark Streaming ジョブを作成します](apache-spark-streaming-exactly-once.md)
* [YARN での実行時間の長い Apache Spark Streaming ジョブ](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Structured Streaming: フォールト トレラントなセマンティクス](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [分離されたストリーム: 拡張性の高いストリーム処理のためのフォールト トレラント モデル](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
