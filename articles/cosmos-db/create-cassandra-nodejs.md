---
title: クイック スタート:Cassandra API (Node.js) - Azure Cosmos DB
description: このクイックスタートでは、Azure Cosmos DB の Cassandra API を使って、Node.js でプロファイル アプリケーションを作成する方法を示します
author: snehagunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: a8698bf74d8be4a57d5928b8be74bcf35cee008f
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586139"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>クイック スタート:Node.js SDK と Azure Cosmos DB を使用して Cassandra アプリを構築する

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

このクイックスタートでは、GitHub から例を複製することで、Node.js と Azure Cosmos DB の [Cassandra API](cassandra-introduction.md) を使ってプロファイル アプリを作成する方法を示します。 このクイック スタートでは、Web ベースの Azure portal を使用して Azure Cosmos DB アカウントを作成する方法も示します。

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の中核をなすグローバルな分散と水平方向のスケール機能を利用して、ドキュメント、テーブル、キーと値、およびグラフ データベースをすばやく作成およびクエリできます。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]または、Azure サブスクリプションを使わず、課金も契約もなしで [Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。

さらに、次のものが必要です。
* [Node.js](https://nodejs.org/en/) バージョン v0.10.29 以降
* [Git](https://git-scm.com/)

## <a name="create-a-database-account"></a>データベース アカウントの作成

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ Cassandra アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

GitHub から Cassandra API アプリを複製し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

1. コマンド プロンプトを開きます。 `git-samples` という名前の新しいフォルダーを作成します。 その後、コマンド プロンプトを閉じます。

    ```bash
    md "C:\git-samples"
    ```

2. Git ターミナル ウィンドウ (Git Bash など) を開きます。 `cd` コマンドを使用して新しいフォルダーに移動し、サンプル アプリをインストールします。

    ```bash
    cd "C:\git-samples"
    ```

3. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コードでデータベース リソースを作成する方法に関心がある場合は、次のスニペットで確認できます。 スニペットはすべて `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started` フォルダーの `uprofile.js` ファイルからのものです。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。 

* ユーザー名とパスワードの値は、Azure portal の接続文字列ページを使って設定されています。 `path\to\cert` では、X509 証明書へのパスを指定します。 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client` は、contactPoint の情報で初期化されます。 ContactPoint は、Azure Portal から取得されます。

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` は、Azure Cosmos DB の Cassandra API に接続します。

    ```javascript
    client.connect(next);
    ```

* 新しいキースペースが作成されます。

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* 新しいテーブルが作成されます。

   ```javascript
   function createTable(next) {
    var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* キー/値エンティティが挿入されます。

    ```javascript
    ...
       {
          query: 'INSERT INTO  uprofile.user  (user_id, user_name , user_bcity) VALUES (?,?,?)',
          params: [5, 'IvanaV', 'Belgaum', '2017-10-3136']
        }
    ];
    client.batch(queries, { prepare: true}, next);
    ```

* クエリを実行して、すべてのキー値を取得します。

    ```javascript
   var query = 'SELECT * FROM uprofile.user';
    client.execute(query, { prepare: true}, function (err, result) {
      if (err) return next(err);
      result.rows.forEach(function(row) {
        console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
      }, this);
      next();
    });
    ```  
    
* クエリを実行して、キーの値を取得します。

    ```javascript
    function selectById(next) {
        console.log("\Getting by id");
        var query = 'SELECT * FROM uprofile.user where user_id=1';
        client.execute(query, { prepare: true}, function (err, result) {
        if (err) return next(err);
            result.rows.forEach(function(row) {
            console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
        }, this);
        next();
        });
    }
    ```  

## <a name="update-your-connection-string"></a>接続文字列を更新する

ここで Azure Portal に戻り、接続文字列情報を取得し、アプリにコピーします。 アプリはこの接続文字列によって、ホストされているデータベースと通信できます。

1. [Azure portal](https://portal.azure.com/) で **[接続文字列]** を選択します。 

    画面右側の ![コピー ボタン](./media/create-cassandra-nodejs/copy.png) ボタンを使って、一番上にある [CONTACT POINT]\(コンタクト ポイント\) の値をコピーします。

    ![Azure Portal の [接続文字列] ページに表示されているコンタクト ポイント、ユーザー名、パスワードをコピーする](./media/create-cassandra-nodejs/keys.png)

2. `config.js` ファイルを開きます。 

3. 4 行目の `<FillMEIN>` にポータルのコンタクト ポイントの値を貼り付けます。

    4 行目は次のようになります。 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

4. ポータルの [USERNAME]\(ユーザー名\) の値をコピーし、2 行目の `<FillMEIN>` に貼り付けます。

    2 行目は次のようになります。 

    `config.username = 'cosmos-db-quickstart';`
    
5. ポータルの [PASSWORD]\(パスワード\) の値をコピーし、3 行目の `<FillMEIN>` に貼り付けます。

    3 行目は次のようになります。

    `config.password = '2Ggkr662ifxz2Mg==';`

6. `config.js` ファイルを保存します。
    
## <a name="use-the-x509-certificate"></a>X509 証明書を使う

1. Baltimore CyberTrust Root 証明書を [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt) からローカルにダウンロードします。 ファイル拡張子 `.cer` を使用して、ファイルの名前を変更します。

   証明書のシリアル番号は `02:00:00:b9`、SHA1 フィンガープリントは `d4🇩🇪20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74` です。

2. `uprofile.js` を開き、`path\to\cert` を新しい証明書を指すように変更します。

3. `uprofile.js` を保存します。

## <a name="run-the-nodejs-app"></a>Node.js アプリを実行する

1. git ターミナル ウィンドウで `npm install` を実行して、必要な npm モジュールをインストールします。

2. `node uprofile.js` を実行して、node アプリケーションを起動します。

3. コマンド ラインから予想される結果を確認します。

    ![出力を表示して検証する](./media/create-cassandra-nodejs/output.png)

    Ctrl + C キーを押してプログラムの実行を停止し、コンソール ウィンドウを閉じます。 

4. Azure portal で **Data Explorer** を開き、この新しいデータのクエリ、変更、操作を行います。 

    ![データ エクスプローラーでのデータの表示](./media/create-cassandra-nodejs/data-explorer.png) 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、アプリを実行する方法を説明しました。 これで、Cosmos DB アカウントに追加のデータをインポートできます。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB への Cassandra データのインポート](cassandra-import-data.md)


