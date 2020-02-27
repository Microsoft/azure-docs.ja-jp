---
title: HDInsight 上の Hadoop サービスで使用されるポート- Azure
description: この記事では、Azure HDInsight で実行されている Apache Hadoop サービスによって使用されるポートの一覧を示します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 67cafbb7934381cd4c2936d6e6dfe7fb19d70735
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314693"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>HDInsight 上の Apache Hadoop サービスで使用されるポート

このドキュメントでは、HDInsight クラスター上で実行されている Apache Hadoop サービスによって使用されるポートの一覧を示します。 また、SSH を使用したクラスターへの接続に使用されるポートの情報も提供します。

## <a name="public-ports-vs-non-public-ports"></a>パブリック ポートと非パブリック ポート

Linux ベースの HDInsight クラスターでは、22、23、443 の 3 つのポートだけがインターネット上で公開されます。 これらのポートは、SSH を使用してクラスターに安全にアクセスし、セキュリティで保護された HTTPS プロトコルを介して公開されるサービスにアクセスする際に使用されます。

内部的には、HDInsight は Azure Virtual Network 上で実行される複数の Azure Virtual Network (クラスター内のノード) によって実装されます。 仮想ネットワーク内から、インターネット経由で公開されていないポートにアクセスできます。 たとえば、SSH を使用してヘッド ノードのいずれかに接続すると、そのヘッド ノードから、クラスター ノードで実行されているサービスに直接アクセスできます。

> [!IMPORTANT]  
> HDInsight の構成オプションとして Azure Virtual Network を指定しないと、Azure Virtual Network が自動的に作成されます。 ただし、この仮想ネットワークに他のコンピューター (他の Azure Virtual Machines やクライアント開発用コンピューターなど) を参加させることはできません。

仮想ネットワークに他のマシンを参加させるには、まず仮想ネットワークを作成し、HDInsight クラスターの作成時にその仮想ネットワークを指定する必要があります。 詳細については、「[Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md)」 (HDInsight 用の仮想ネットワークの計画) を参照してください。

## <a name="public-ports"></a>パブリック ポート

HDInsight クラスター内のすべてのノードは Azure Virtual Network 内に配置されており、インターネットから直接アクセスすることはできません。 パブリック ゲートウェイにより、すべての HDInsight クラスターの種類に共通する次のポートへのインターネット アクセスが提供されます。

| サービス | Port | Protocol | 説明 |
| --- | --- | --- | --- |
| sshd |22 |SSH |プライマリ ヘッドノードの sshd にクライアントを接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 |
| sshd |22 |SSH |エッジ ノードの sshd にクライアントを接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 |
| sshd |23 |SSH |セカンダリ ヘッドノードの sshd にクライアントを接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 |
| Ambari |443 |HTTPS |Ambari Web UI。 [Apache Ambari Web UI を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)に関するページをご覧ください |
| Ambari |443 |HTTPS |Ambari REST API。 「[Apache Ambari REST API を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari-rest-api.md)」をご覧ください |
| WebHCat |443 |HTTPS |HCatalog REST API。 [MapReduce と Curl の使用](hadoop/apache-hadoop-use-mapreduce-curl.md)に関する記事を参照してください。 |
| HiveServer2 |443 |ODBC |ODBC を使用して Hive に接続します。 [Microsoft ODBC ドライバーを使用した Excel から HDInsight への接続](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)に関する記事をご覧ください。 |
| HiveServer2 |443 |JDBC |JDBC を使用して Apache Hive に接続します。 [Hive JDBC ドライバーを使用した HDInsight の Apache Hive への接続](hadoop/apache-hadoop-connect-hive-jdbc-driver.md)に関するページをご覧ください |

次のポートは、特定のクラスターの種類で使用できます。

| サービス | Port | Protocol | クラスターの種類 | 説明 |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |hbase |HBase REST API。 [Apache HBase の使用開始](hbase/apache-hbase-tutorial-get-started-linux.md)に関するページをご覧ください |
| Livy |443 |HTTPS |Spark |Spark REST API。 [Apache Livy を使用したリモートからの Apache Spark ジョブの送信](spark/apache-spark-livy-rest-interface.md)に関するページをご覧ください |
| Spark Thrift サーバー |443 |HTTPS |Spark |Hive クエリを送信するために使用される Spark Thrift サーバー。 [HDInsight での Beeline と Apache Hive の使用](hadoop/apache-hadoop-use-hive-beeline.md)に関する記事をご覧ください |
| Storm |443 |HTTPS |Storm |Storm Web UI。 「[HDInsight での Apache Storm トポロジのデプロイと管理](storm/apache-storm-deploy-monitor-topology-linux.md)」をご覧ください |
| Kafka REST プロキシ |443 |HTTPS |Kafka |Kafka REST API。 「[REST プロキシを使用して Azure HDInsight で Apache Kafka クラスターを操作する](kafka/rest-proxy.md)」をご覧ください。 |

### <a name="authentication"></a>認証

インターネット上で公開されるすべてのサービスを認証する必要があります。

| Port | [資格情報] |
| --- | --- |
| 22 または 23 |クラスターの作成時に指定した SSH ユーザー資格情報 |
| 443 |ログイン名 (既定値: admin) と、クラスターの作成時に設定したパスワード |

## <a name="non-public-ports"></a>非パブリック ポート

> [!NOTE]  
> 一部のサービスは、特定のクラスターの種類でのみ利用できます。 たとえば、HBase を利用できるのは、クラスターの種類が HBase の場合のみです。

> [!IMPORTANT]  
> 一部のサービスは、一度に 1 つのヘッド ノード上でしか実行されません。 プライマリのヘッド ノード上のサービスに接続しようとしてエラーが発生した場合は、セカンダリのヘッド ノードを使用して再試行してください。

### <a name="ambari"></a>Ambari

| サービス | Nodes | Port | URL パス | Protocol |
| --- | --- | --- | --- | --- |
| Ambari Web UI | ヘッド ノード | 8080 | / | HTTP |
| Ambari REST API | ヘッド ノード | 8080 | /api/v1 | HTTP |

例 :

* Ambari REST API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| NameNode Web UI |ヘッド ノード |30070 |HTTPS |状態を表示する Web UI |
| NameNode メタデータ サービス |ヘッド ノード |8020 |IPC |ファイル システム メタデータ |
| DataNode |すべての worker ノード |30075 |HTTPS |状態、ログなどを表示する Web UI |
| DataNode |すべての worker ノード |30010 |&nbsp; |データ転送 |
| DataNode |すべての worker ノード |30020 |IPC |メタデータ操作 |
| セカンダリ NameNode |ヘッド ノード |50090 |HTTP |NameNode メタデータのチェックポイント |

### <a name="yarn-ports"></a>YARN ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| Resource Manager Web UI |ヘッド ノード |8088 |HTTP |Resource Manager の Web UI |
| Resource Manager Web UI |ヘッド ノード |8090 |HTTPS |Resource Manager の Web UI |
| Resource Manager 管理インターフェイス |ヘッド ノード |8141 |IPC |アプリケーション送信用 (Hive、Hive サーバー、Pig など) |
| Resource Manager スケジューラ |ヘッド ノード |8030 |HTTP |管理インターフェイス |
| Resource Manager アプリケーション インターフェイス |ヘッド ノード |8050 |HTTP |アプリケーション マネージャー インターフェイスのアドレス |
| NodeManager |すべての worker ノード |30050 |&nbsp; |コンテナー マネージャーのアドレス |
| NodeManager Web UI |すべての worker ノード |30060 |HTTP |Resource Manager インターフェイス |
| Timeline アドレス |ヘッド ノード |10200 |RPC |Timeline サービスの RPC サービス |
| Timeline Web UI |ヘッド ノード |8188 |HTTP |Timeline サービス Web UI |

### <a name="hive-ports"></a>Hive ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| HiveServer2 |ヘッド ノード |10001 |Thrift |Hive に接続するためのサービス (Thrift/JDBC) |
| Hive メタストア |ヘッド ノード |9083 |Thrift |Hive メタデータに接続するためのサービス (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| WebHCat サーバー |ヘッド ノード |30111 |HTTP |HCatalog および他の Hadoop サービス上の Web API |

### <a name="mapreduce-ports"></a>MapReduce ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| JobHistory |ヘッド ノード |19888 |HTTP |MapReduce JobHistory Web UI |
| JobHistory |ヘッド ノード |10020 |&nbsp; |MapReduce JobHistory サーバー |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |中間 Map 出力を要求元 Reducer に転送 |

### <a name="oozie"></a>Oozie

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| Oozie サーバー |ヘッド ノード |11000 |HTTP |Oozie サービスの URL |
| Oozie サーバー |ヘッド ノード |11001 |HTTP |Oozie 管理用ポート |

### <a name="ambari-metrics"></a>Ambari メトリック

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| TimeLine (アプリケーション履歴) |ヘッド ノード |6188 |HTTP |Timeline サービス Web UI |
| TimeLine (アプリケーション履歴) |ヘッド ノード |30200 |RPC |Timeline サービス Web UI |

### <a name="hbase-ports"></a>HBase ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| HMaster |ヘッド ノード |16000 |&nbsp; |&nbsp; |
| HMaster 情報 Web UI |ヘッド ノード |16010 |HTTP |HBase Master Web UI のポート |
| リージョン サーバー |すべての worker ノード |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |クライアントが ZooKeeper への接続に使用するポート |

### <a name="kafka-ports"></a>Kafka ポート

| サービス | Nodes | Port | Protocol | 説明 |
| --- | --- | --- | --- | --- |
| ブローカー |ワーカー ノード |9092 |[Kafka Wire Protocol](https://kafka.apache.org/protocol.html) |クライアント通信に使用 |
| &nbsp; |Zookeeper ノード |2181 |&nbsp; |クライアントが ZooKeeper への接続に使用するポート |
| REST プロキシ | Kafka 管理ノード |9400 |HTTPS |[Kafka REST 仕様](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) |

### <a name="spark-ports"></a>Spark ポート

| サービス | Nodes | Port | Protocol | URL パス | 説明 |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift サーバー |ヘッド ノード |10002 |Thrift | &nbsp; | Spark SQL に接続するためのサービス (Thrift/JDBC) |
| Livy サーバー | ヘッド ノード | 8998 | HTTP | &nbsp; | ステートメント、ジョブ、およびアプリケーションを実行するためのサービス |
| Jupyter Notebook | ヘッド ノード | 8001 | HTTP | &nbsp; | Jupyter Notebook Web サイト |

例 :

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. この例の `10.0.0.11` は、Livy サービスをホストするヘッド ノードの IP アドレスです。
