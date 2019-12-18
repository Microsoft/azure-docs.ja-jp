---
title: Apache Hadoop と安全な転送のストレージ - Azure HDInsight
description: 安全な転送が有効になっている Azure ストレージ アカウントで HDInsight クラスターを作成する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: 09a6b158c4390f881754c90d52a476f0bc249a5a
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947641"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight の安全な転送のストレージ アカウントで Apache Hadoop クラスターを作成する

"[安全な転送が必須](../storage/common/storage-require-secure-transfer.md)" 機能は、アカウントへのすべての要求にセキュリティで保護された接続を経由するように強制することで、Azure ストレージ アカウントのセキュリティを強化します。 この機能と wasbs スキーマは、HDInsight クラスター バージョン 3.6 以降でのみサポートされます。

## <a name="prerequisites"></a>前提条件

この記事を読み始める前に、以下を用意する必要があります。

* Azure サブスクリプション:1 か月間の無料試用版アカウントを [azure.microsoft.com/free](https://azure.microsoft.com/free) で作成できます。
* 安全な転送が有効になっている Azure Storage アカウント。 手順については、「[ストレージ アカウントの作成](../storage/common/storage-quickstart-create-account.md)」と「[安全な転送が必須](../storage/common/storage-require-secure-transfer.md)」を参照してください。 クラスターの作成後に安全なストレージ転送を有効にするには、この記事で説明されていない追加の手順が必要です。
* ストレージ アカウント上の BLOB コンテナー。

## <a name="create-cluster"></a>クラスターの作成

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

このセクションでは、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md)を利用して、HDInsight で Hadoop クラスターを作成します。 テンプレートは、[GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/) にあります。 この記事に従うために、Resource Manager テンプレートの使用経験は必要ありません。 その他のクラスター作成方法と、この記事で使うプロパティの詳細については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

1. 次の画像をクリックして Azure にサインインし、Azure Portal で Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. 手順に従って、以下の仕様のクラスターを作成します。

    * HDInsight バージョン 3.6 を指定します。 バージョン 3.6 以降が必須です。
    * 安全な転送が有効になっているストレージ アカウントを指定します。
    * ストレージ アカウントの短い名前を使用します。
    * ストレージ アカウントと BLOB コンテナーのどちらも、事前に作成しておく必要があります。

      手順については、「[クラスターの作成](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。

独自の構成ファイルを指定するためにスクリプト アクションを使用する場合は、以下の設定で wasbs を使用する必要があります。

* fs.defaultFS (コア サイト)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>追加のストレージ アカウントの追加

安全な転送が有効になっている追加のストレージ アカウントを追加するには、次のようないくつかのオプションがあります。

* Azure Resource Manager テンプレートの最後のセクションを変更します。
* [Azure Portal](https://portal.azure.com) を使用してクラスターを作成し、リンクされたストレージ アカウントを指定します。
* スクリプト アクションを使用して、既存の HDInsight クラスターに安全な転送が有効になっている追加のストレージ アカウントを追加します。 詳細については、「[HDInsight にストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、HDInsight クラスターを作成し、ストレージ アカウントへの安全な転送を有効にする方法について説明しました。

HDInsight でデータを分析する方法の詳細については、次の記事を参照してください。

* Visual Studio から [Apache Hive](https://hive.apache.org/) クエリを実行する方法など、HDInsight で Hive を使用する方法の詳細については、[HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)に関する記事を参照してください。
* Hadoop 上のデータを処理するプログラムを作成する方法の 1 つである [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) の詳細については、[HDInsight での Apache Hadoop MapReduce の使用](hadoop/hdinsight-use-mapreduce.md)に関する記事を参照してください。
* HDInsight Tools for Visual Studio を使用して HDInsight 上のデータを分析する方法については、[HDInsight Apache Hadoop Tools for Visual Studio の使用開始](hadoop/apache-hadoop-visual-studio-tools-get-started.md)に関するページを参照してください。

HDInsight のデータの格納方法や HDInsight にデータを取り込む方法の詳細については、以下の記事を参照してください。

* HDInsight で Azure Storage を使用する方法の詳細については、[HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。
* データを HDInsight にアップロードする方法については、[データを HDInsight にアップロードする方法](hdinsight-upload-data.md)に関する記事を参照してください。

HDInsight クラスターの作成または管理の詳細については、以下の記事を参照してください。

* Linux ベースの HDInsight クラスターを管理する方法については、[Apache Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページを参照してください。
* HDInsight クラスターの作成時に選択できるオプションの詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
* Linux と Apache Hadoop に精通しているが、HDInsight 上の Hadoop に関する詳細を知りたい場合は、[Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md)に関するページを参照してください。 この記事では、次のような情報を提供します。

  * クラスターでホストされる URL ([Apache Ambari](https://ambari.apache.org/) や [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) など)
  * [Apache Hadoop](https://hadoop.apache.org/) ファイルの場所とローカル ファイル システムの例
  * 既定のデータ ストアとして、[Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) ではなく Azure Storage (WASB) を使用する
