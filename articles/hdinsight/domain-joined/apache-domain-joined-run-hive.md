---
title: Apache Ranger での Apache Hive ポリシー - Azure HDInsight
description: Enterprise セキュリティ パッケージを使用して Azure HDInsight サービスに Hive 用の Apache Ranger ポリシーを構成する方法について説明します。
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/27/2019
ms.openlocfilehash: 71c1306d1516d8af3fb16c0ba353ab8144de2562
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86202592"
---
# <a name="configure-apache-hive-policies-in-hdinsight-with-enterprise-security-package"></a>Enterprise セキュリティ パッケージを使用して HDInsight 上に Hive ポリシーを構成する

Apache Hive 用 Apache Ranger ポリシーを構成する方法について説明します。 この記事では、hivesampletable へのアクセスを制限する 2 つの Ranger ポリシーを作成します。 hivesampletable は HDInsight クラスターに付属しています。 ポリシーを構成したら、Excel と ODBC ドライバーを使用して HDInsight の Hive テーブルに接続します。

## <a name="prerequisites"></a>前提条件

* Enterprise セキュリティ パッケージを使用している HDInsight クラスター。 [ESP を使用した HDInsight クラスターの構成](apache-domain-joined-configure.md)に関するページをご覧ください。
* Office 2016、Office 2013 Professional Plus、Office 365 Pro Plus、Excel 2013 Standalone、または Office 2010 Professional Plus がインストールされたワークステーション。

## <a name="connect-to-apache-ranger-admin-ui"></a>Apache Ranger 管理 UI への接続
**Ranger 管理 UI に接続するには**

1. ブラウザーで Ranger 管理 UI (`https://CLUSTERNAME.azurehdinsight.net/Ranger/`) に移動します。CLUSTERNAME はクラスターの名前です。

   > [!NOTE]  
   > Ranger では、Apache Hadoop クラスターとは異なる資格情報を使用します。 ブラウザーで Hadoop のキャッシュされた資格情報が使用されないように、新しい InPrivate ブラウザー ウィンドウを使用して Ranger 管理 UI に接続してください。

2. クラスター管理者のドメイン ユーザー名とパスワードを使用してログインします。

    ![HDInsight ESP Ranger のホーム ページ](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-ranger-home-page.png)

    現在、Ranger は Yarn および Hive でのみ機能します。

## <a name="create-domain-users"></a>ドメイン ユーザーの作成

hiveruser1 と hiveuser2 を作成する方法については、「[ESP の HDInsight クラスターの作成](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)」をご覧ください。 この記事では、その 2 つのユーザー アカウントを使用します。

## <a name="create-ranger-policies"></a>Ranger ポリシーの作成

このセクションでは、hivesampletable にアクセスするための 2 つの Ranger ポリシーを作成します。 異なる列セットに対する select 権限を付与します。 両方のユーザーは、「[ESP の HDInsight クラスターの作成](apache-domain-joined-configure-using-azure-adds.md#create-an-hdinsight-cluster-with-esp)」で作成されました。 次のセクションでは、2 つのポリシーを Excel でテストします。

**Ranger ポリシーを作成するには**

1. Ranger 管理 UI を開きます。 「Apache Ranger 管理 UI への接続」をご覧ください。
2. **[Hive]** で **[CLUSTERNAME_Hive]** を選択します。 構成済みの 2 つのポリシーが表示されます。
3. **[新しいポリシーの追加]** を選択し、次の値を入力します。

    |プロパティ |値 |
    |---|---|
    |ポリシー名|read-hivesampletable-all|
    |Hive Database|default|
    |table|hivesampletable|
    |Hive Column|*|
    |ユーザーの選択|hiveuser1|
    |アクセス許可|select|

    ![HDInsight ESP Ranger での Hive ポリシーの構成](./media/apache-domain-joined-run-hive/hdinsight-domain-joined-configure-ranger-policy.png)。

    > [!NOTE]  
    > [Select User] にドメイン ユーザーが設定されていない場合は、Ranger が AAD と同期するまでしばらく待ってください。

4. **[Add]\(追加\)** をクリックしてポリシーを保存します。

5. 最後の 2 つの手順を繰り返して、次のプロパティを設定したもう 1 つのポリシーを作成します。

    |プロパティ |値 |
    |---|---|
    |ポリシー名|read-hivesampletable-devicemake|
    |Hive Database|default|
    |table|hivesampletable|
    |Hive column|clientid、devicemake|
    |ユーザーの選択|hiveuser2|
    |アクセス許可|select|

## <a name="create-hive-odbc-data-source"></a>Hive ODBC データ ソースの作成

手順については、「[Hive ODBC データ ソースの作成](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)」をご覧ください。  

 | プロパティ  |説明 |
 | --- | --- |
 | データ ソース名 | データ ソースに名前を付けます。 |
 | Host | CLUSTERNAME.azurehdinsight.net を入力します。 たとえば、「myHDICluster.azurehdinsight.net」と入力します。 |
 | Port | **443** を使用します。 (このポートは 563 から 443 に変更されました)。 |
 | データベース | **既定値**を使用します。 |
 | Hive サーバーの種類 | **Hive Server 2** を選択します。 |
 | メカニズム | **Azure HDInsight サービス**を選択します。 |
 | HTTP パス | 空白のままにします。 |
 | [ユーザー名] | 「hiveuser1@contoso158.onmicrosoft.com」と入力します。 ドメイン名が異なる場合は、ドメイン名を更新します。 |
 | Password | hiveuser1 のパスワードを入力します。 |

データ ソースを保存する前に、必ず **[Test]** をクリックしてください。

## <a name="import-data-into-excel-from-hdinsight"></a>HDInsight から Excel へのデータのインポート

最後のセクションで 2 つのポリシーを構成しました。  hiveuser1 にはすべての列に対する select 権限があり、hiveuser2 には 2 つの列に対する select 権限があります。 このセクションでは、2 人のユーザーを偽装して Excel にデータをインポートします。

1. Excel で新しいブックまたは既存のブックを開きます。

1. **[データ]** タブで **[データの取得]**  >  **[その他のデータ ソース]**  >  **[ODBC]** の順に移動して、 **[ODBC]** ウィンドウを起動します。

    ![データ接続ウィザードを開く](./media/apache-domain-joined-run-hive/simbahiveodbc-excel-dataconnection1.png)

1. ドロップダウン リストから、前のセクションで作成したデータ ソース名を選択して、 **[OK]** を選択します。

1. 初めて使用する場合は、 **[ODBC ドライバー]** ダイアログ ボックスが開きます。 左側のメニューで **[Windows]** を選択します。 次に、 **[接続]** を選択して **[ナビゲーター]** ウィンドウを開きます。

1. **[データベースとテーブルの選択]** ダイアログが開くのを待ちます。 この処理には数秒かかります。

1. **[hivesampletable]** を選択し、 **[次へ]** を選択します。

1. **[完了]** を選択します。

1. **[データのインポート]** ダイアログでは、クエリを変更または指定できます。 これを行うには、 **[プロパティ]** を選択します。 この処理には数秒かかります。

1. **[定義]** タブを選択します。コマンド テキストを次に示します。

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"`
    ```

   定義した Ranger ポリシーにより、hiveuser1 にはすべての列に対する select 権限があります。  そのため、このクエリは hiveuser1 の資格情報では機能しますが、hiveuser2 の資格情報では機能しません。

1. **[OK]** を選択して [接続プロパティ] ダイアログを閉じます。

1. **[OK]** を選択して **[データのインポート]** ダイアログを閉じます。  

1. hiveuser1 のパスワードを再入力し、 **[OK]** をクリックします。 データが Excel にインポートされるまでに、数秒かかります。 完了すると、11 列のデータが表示されます。

最後のセクションで作成した 2 つ目のポリシー (read-hivesampletable-devicemake) をテストするには

1. Excel で新しいシートを追加します。
2. 最後の手順に従ってデータをインポートします。  ここで行う唯一の変更は、hiveuser1 ではなく hiveuser2 の資格情報を使用することです。 hiveuser2 には 2 つの列を表示する権限しかないため、インポートは失敗します。 次のエラーが表示されます。

    ```output
    [Microsoft][HiveODBC] (35) Error from Hive: error code: '40000' error message: 'Error while compiling statement: FAILED: HiveAccessControlException Permission denied: user [hiveuser2] does not have [SELECT] privilege on [default/hivesampletable/clientid,country ...]'.
    ```

3. 同じ手順に従ってデータをインポートします。 今回は、hiveuser2 の資格情報を使用し、次の select ステートメント

    ```sql
    SELECT * FROM "HIVE"."default"."hivesampletable"
    ```

    を次のように変更します。

    ```sql
    SELECT clientid, devicemake FROM "HIVE"."default"."hivesampletable"
    ```

    完了すると、インポートされた 2 列のデータが表示されます。

## <a name="next-steps"></a>次のステップ

* Enterprise セキュリティ パッケージを使用した HDInsight クラスターの構成については、[ESP での HDInsight クラスターの構成](apache-domain-joined-configure.md)に関するページをご覧ください。
* ESP での HDInsight クラスターの管理については、「[Enterprise セキュリティ パッケージを使用して HDInsight クラスターを管理する](apache-domain-joined-manage.md)」をご覧ください。
* SSH を使用して ESP を含む HDInsight クラスターで Hive クエリを実行する方法については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md#authentication-domain-joined-hdinsight)」をご覧ください。
* Hive JDBC を使用して Hive に接続する方法については、[Hive の JDBC ドライバーを使用して Azure HDInsight の Apache Hive に接続する方法](../hadoop/apache-hadoop-connect-hive-jdbc-driver.md)に関するページを参照してください。
* Hive ODBC を使用して Excel を Hadoop に接続する方法については、[Microsoft Hive ODBC ドライバーを使用した Excel から Apache Hadoop への接続](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)に関するページを参照してください。
* Power Query を使用して Excel を Hadoop に接続する方法については、「[Power Query を使用して Excel を Apache Hadoop に接続する](../hadoop/apache-hadoop-connect-excel-power-query.md)」を参照してください。
