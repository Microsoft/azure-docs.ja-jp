---
title: ODBC ドライバーを使用した Excel と Apache Hadoop - Azure HDInsight
description: Excel 用の Microsoft Hive ODBC ドライバーを使用できるようにセットアップし、Microsoft Excel から HDInsight クラスターのデータを照会する方法を説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/11/2019
ms.openlocfilehash: 883192e1d041014c23445b7a2fa0ece45eb76f10
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435812"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC ドライバーを使用して Excel を Azure HDInsight 上の Apache Hadoop に接続する

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft のビッグ データ ソリューションでは、Azure HDInsight にデプロイされた Apache Hadoop クラスターと Microsoft Business Intelligence (BI) コンポーネントが統合されます。 たとえば、Microsoft Hive Open Database Connectivity (ODBC) ドライバーを使用すれば、HDInsight で Hadoop クラスターの Hive データ ウェアハウスに Excel を接続できます。

また、Microsoft Power Query for Excel アドインを使用して Excel から HDInsight クラスターや、その他の (HDInsight 以外の) Hadoop クラスターなどのデータ ソースを接続することもできます。 Power Query のインストール方法と使用方法については、[Power Query を使用した Excel から HDInsight への接続](../hdinsight-connect-excel-power-query.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

この記事の操作を始める前に、以下を用意する必要があります。

* HDInsight Hadoop クラスター。 その作成方法については、[Azure HDInsight の概要](apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
* Office 2010 Professional Plus 以降または Excel 2010 以降を使用するワークステーション。

## <a name="install-microsoft-hive-odbc-driver"></a>Microsoft Hive ODBC ドライバーのインストール

ODBC ドライバーを使用するアプリケーションのバージョンと一致した [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886) のバージョンをダウンロードしてインストールします。  この記事では、Office Excel に対してこのドライバーを使用します。

## <a name="create-apache-hive-odbc-data-source"></a>Apache Hive ODBC データ ソースを作成する

次の手順に従って、Hive ODBC データ ソースを作成します。

1. Windows で、[スタート]、[Windows 管理ツール]、[ODBC データ ソース (32 ビット)/(64 ビット)] の順に移動します。  これにより、 **[ODBC データ ソース アドミニストレーター]** ウィンドウが開きます。

    ![ODBC データ ソース アドミニストレーター](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "ODBC データ ソース アドミニストレーターを使用して DSN を構成する")

1. **[ユーザー DSN]** タブで、 **[追加]** を選択して **[データ ソースの新規作成]** ウィンドウを開きます。

1. **[Microsoft Hive ODBC Driver]** を選択してから、 **[完了]** を選択して **Microsoft Hive ODBC Driver DSN セットアップ** ウィンドウを開きます。

1. 次の値を入力または選択します。

   | プロパティ | 説明 |
   | --- | --- |
   |  データ ソース名 |データ ソースに名前を付けます。 |
   |  ホスト |「`HDInsightClusterName.azurehdinsight.net`」と入力します。 たとえば、「 `myHDICluster.azurehdinsight.net` 」のように入力します。 注: クライアント VM が同じ仮想ネットワークにピアリングされている限り、`HDInsightClusterName-int.azurehdinsight.net` がサポートされます。 |
   |  Port |**443** を使用します。 (このポートは 563 から 443 に変更されました)。 |
   |  データベース |**既定値**を使用します。 |
   |  メカニズム |**[Microsoft Azure HDInsight Service]** を選択します |
   |  [ユーザー名] |HDInsight クラスター ユーザーの HTTP ユーザー名を入力します。 既定のユーザー名は **admin**です。 |
   |  Password |HDInsight クラスター ユーザーのパスワードを入力します。 **[Save Password (Encrypted)]\(パスワードの保存 (暗号化済み)\)** チェック ボックスをオンにします。|

1. 省略可能: **[詳細オプション]** を選択します。  

   | パラメーター | 説明 |
   | --- | --- |
   |  ネイティブ クエリの使用 |これを選択すると、ODBC ドライバーは TSQL を HiveQL に変換しません。 純粋な HiveQL ステートメントを送信していることが 100% 確実な場合にのみ、使用する必要があります。 SQL Server または Azure SQL Database に接続している場合は、オフのままにします。 |
   |  ブロック単位でフェッチされた行 |大量のレコードをフェッチする場合、このパラメーターを調整してパフォーマンスを最適化する必要がある場合があります。 |
   |  既定の文字列の列の長さ、バイナリ列の長さ、10 進数の列の桁数 |データ型の長さおよび精度は、データが返される方法に影響する可能性があります。 精度が失われたり、切り捨てられたりするために間違った情報が返されます。 |

    ![DSN 詳細構成オプション](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "DSN 詳細構成オプション")

1. **[テスト]** を選択して、データ ソースをテストします。 データ ソースが正しく構成された場合、テスト結果に "**成功!** " と表示されます。  

1. **[OK]** を選択して、[テスト] ウィンドウを閉じます。  

1. **[OK]** を選択して、**Microsoft Hive ODBC Driver DSN セットアップ**  ウィンドウを閉じます。  

1. **[OK]** を選択して、 **[ODBC データ ソース アドミニストレーター]** ウィンドウを閉じます。  

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight から Excel へのデータのインポート

ここでは、前のセクションで作成した ODBC データ ソースを使用して、Hive テーブルから Excel ブックへデータをインポートする方法を説明します。

1. Excel で新しいブックまたは既存のブックを開きます。

2. **[データ]** タブで **[データの取得]**  >  **[その他のデータ ソース]**  >  **[ODBC]** の順に移動して、 **[ODBC]** ウィンドウを起動します。

    ![Excel データ接続ウィザードを開く](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "Excel データ接続ウィザードを開く")

3. ドロップダウン リストから、前のセクションで作成したデータ ソース名を選択して、 **[OK]** を選択します。

4. 初めて使用する場合は、 **[ODBC ドライバー]** ダイアログ ボックスが開きます。 左側のメニューで **[Windows]** を選択します。 次に、 **[接続]** を選択して **[ナビゲーター]** ウィンドウを開きます。

5. **[ナビゲーター]** で、 **[HIVE]**  >  **[既定値]**  >  **[hivesampletable]** の順に移動し、次に **[読み込み]** を選択します。 データが Excel にインポートされるまでに、しばらく時間がかかります。

    ![HDInsight Excel Hive ODBC ナビゲーター](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight Excel Hive ODBC ナビゲーター")

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Hive ODBC ドライバーを使用して HDInsight サービスから Excel にデータを取得する方法を学習しました。 同様に、SQL Database に HDInsight サービスからデータを取得することもできます。 また、HDInsight サービスにデータをアップロードすることもできます。 詳細については、次を参照してください。

* [Azure HDInsight の Microsoft Power BI で Apache Hive データを視覚化する](apache-hadoop-connect-hive-power-bi.md)。
* [Azure HDInsight の Power BI で対話型クエリの Hive データを視覚化する](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [Azure HDInsight で Apache Zeppelin を使用して Apache Hive クエリを実行する](../interactive-query/hdinsight-connect-hive-zeppelin.md)。
* [Power Query を使用して Excel を Apache Hadoop に接続する](apache-hadoop-connect-excel-power-query.md)。
* [Data Lake Tools for Visual Studio を使用して Azure HDInsight に接続し、Apache Hive クエリを実行する](apache-hadoop-visual-studio-tools-get-started.md)。
* [Azure HDInsight Tool for Visual Studio Code の使用](../hdinsight-for-vscode.md)。
* [HDInsight にデータをアップロードする](./../hdinsight-upload-data.md)。
