---
title: Node.js から Azure Table Storage または Azure Cosmos DB Table API を使用する方法
description: Azure Table Storage または Azure Cosmos DB Table API を使用して、構造化データをクラウドに格納します。
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: b00c7168891c848f4b153acb1bf09728b7a78908
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106483"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Node.js から Azure Table Storage または Azure Cosmos DB Table API を使用する方法
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>概要
この記事では、Node.js アプリケーションで Azure Storage Table サービスまたは Azure Cosmos DB を使用して一般的なシナリオを実行する方法について説明します。

## <a name="create-an-azure-service-account"></a>Azure サービス アカウントを作成する

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure のストレージ アカウントの作成

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API アカウントを作成する

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Azure Storage または Azure Cosmos DB Table API にアクセスするようにアプリケーションを構成する
Azure Storage または Azure Cosmos DB を使用するには、Azure Storage SDK for Node.js が必要です。ここには、ストレージ REST サービスと通信するための便利なライブラリのセットが含まれています。

### <a name="use-node-package-manager-npm-to-install-the-package"></a>ノード パッケージ マネージャー (NPM) を使用してパッケージをインストールする
1. **PowerShell** (Windows)、**Terminal** (Mac)、**Bash** (Unix) などのコマンド ライン インターフェイスを使って、アプリケーションを作成したフォルダーに移動します。
2. コマンド ウィンドウに「 **npm install azure-storage** 」と入力します。 このコマンドの出力は次の例のようになります。

       azure-storage@0.5.0 node_modules\azure-storage
       +-- extend@1.2.1
       +-- xmlbuilder@0.4.3
       +-- mime@1.2.11
       +-- node-uuid@1.4.3
       +-- validator@3.22.2
       +-- underscore@1.4.4
       +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
       +-- xml2js@0.2.7 (sax@0.5.2)
       +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. 手動で **ls** コマンドを実行して、**node_modules** フォルダーが作成されたことを確認することもできます。 このフォルダーに **azure-storage** パッケージがあります。このパッケージには、ストレージにアクセスするために必要なライブラリが含まれています。

### <a name="import-the-package"></a>パッケージをインポートする
アプリケーションの **server.js** ファイルの先頭に次のコードを追加します。

```nodejs
var azure = require('azure-storage');
```

## <a name="add-an-azure-storage-connection"></a>Azure Storage 接続を追加する
Azure モジュールは、Azure Storage アカウントに接続するために必要な情報として、環境変数の AZURE_STORAGE_ACCOUNT と AZURE_STORAGE_ACCESS_KEY、または AZURE_STORAGE_CONNECTION_STRING を読み取ります。 これらの環境変数が設定されていない場合、 **TableService**を呼び出すときにアカウント情報を指定する必要があります。 たとえば、次のコードでは、**TableService** オブジェクトを作成します。

```nodejs
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

## <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB の接続を追加する
Azure Cosmos DB 接続を追加するには、**TableService** オブジェクトを作成し、アカウント名、主キー、およびエンドポイントを指定します。 これらの値は、自分の Cosmos DB アカウントの Azure Portal で **[設定]** > **[接続文字列]** を選択することによりコピーできます。 例: 

```nodejs
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```  

## <a name="create-a-table"></a>テーブルを作成する
次のコードは、 **TableService** オブジェクトを作成し、これを使用して新しいテーブルを作成します。 

```nodejs
var tableSvc = azure.createTableService();
```

**createTableIfNotExists** の呼び出しにより、指定した名前の新しいテーブルが (まだ存在していない場合は) 作成されます。 次の例では、"mytable" という名前のテーブルが存在しない場合、このテーブルを作成します。

```nodejs
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

新しいテーブルを作成する場合、`result.created` は `true` です。テーブルが既に存在する場合は `false` です。 `response` には要求に関する情報が含まれます。

### <a name="filters"></a>フィルター
オプションのフィルタリングは、**TableService** を使って行われる操作に適用できます。 フィルター操作には、ログや自動的な再試行などが含まれる場合があります。フィルターは、次のシグネチャを持つメソッドを実装するオブジェクトです。

```nodejs
function handle (requestOptions, next)
```

要求オプションに対するプリプロセスを行った後で、このメソッドは **next** を呼び出して、次のシグネチャのコールバックを渡す必要があります。

```nodejs
function (returnObject, finalCallback, next)
```

このコールバックで、**returnObject** (サーバーへの要求からの応答) の処理の後に、コールバックは **next** を呼び出すか (他のフィルターの処理を続けるために next が存在する場合)、単に **finalCallback** を呼び出す必要があります (サービス呼び出しを終了する場合)。

再試行のロジックを実装する 2 つのフィルター (**ExponentialRetryPolicyFilter** と **LinearRetryPolicyFilter**) が、Azure SDK for Node.js に含まれています。 次のコードは、**ExponentialRetryPolicyFilter** を使う **TableService** オブジェクトを作成します。

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>エンティティをテーブルに追加する
エンティティを追加するには、エンティティのプロパティを定義するオブジェクトを最初に作成します。 すべてのエンティティには、エンティティの一意の識別子である **PartitionKey** と **RowKey** が含まれます。

* **PartitionKey** - エンティティが格納されるパーティションを決定します。
* **RowKey** - パーティション内のエンティティを一意に識別します。

**PartitionKey** と **RowKey** は両方とも文字列値にする必要があります。 詳細については、「 [Table サービス データ モデルについて](https://msdn.microsoft.com/library/azure/dd179338.aspx)」を参照してください。

エンティティを定義する例を次に示します。 **dueDate** が **Edm.DateTime** の型として定義されている点に注意してください。 型の指定は省略可能です。型を指定しなかった場合、型は推測されます。

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> 各レコードには、エンティティが挿入または更新される場合に Azure により設定される **Timestamp** フィールドもあります。
>
>

**entityGenerator** を使用してエンティティを作成することもできます。 次の例では、 **entityGenerator**を使用して同じタスク エンティティを作成しています。

```nodejs
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

エンティティをテーブルに追加するには、エンティティ オブジェクトを **insertEntity** メソッドに渡します。

```nodejs
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

操作が成功した場合、`result` には挿入されたレコードの [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) が含まれ、`response` には操作に関する情報が含まれます。

応答の例:

```nodejs
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> 既定では、**insertEntity** は、`response` 情報の一部として、挿入されたエンティティを返しません。 このエンティティに対して他の操作を実行する予定がある場合、または情報をキャッシュする場合は、`result` の一部として返されるようにすると便利です。 そのためには、次のように **echoContent** を有効にします。
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`
>
>

## <a name="update-an-entity"></a>エンティティを更新する
既存のエンティティを更新するには、複数のメソッドがあります。

* **replaceEntity** - 既存のエンティティを置換することで更新します。
* **mergeEntity** - 新しいプロパティ値を既存のエンティティにマージすることで既存のエンティティを更新します。
* **insertOrReplaceEntity** - 既存のエンティティを、置換することで更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。
* **insertOrMergeEntity** - 既存のエンティティを、新しいプロパティ値を既存のエンティティにマージすることで更新します。 エンティティが存在しない場合は、新しいエンティティが挿入されます。

次の例に、 **replaceEntity**を使用してエンティティを更新する方法を示します。

```nodejs
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> 既定では、エンティティを更新するときに、更新対象のデータが以前に別のプロセスにより変更されているかどうかは確認されません。 同時更新をサポートするには、次の手順を実行します。
>
> 1. 更新するオブジェクトの ETag を取得します。 これは、任意のエンティティに関連する操作の `response` の一部として返され、`response['.metadata'].etag` を通じて取得できます。
> 2. エンティティで更新操作を実行する場合は、以前に取得した ETag 情報を新しいエンティティに追加します。 例: 
>
>       entity2['.metadata'].etag = currentEtag;
> 3. 更新操作を実行します。 アプリケーションの別のインスタンスなど、ETag 値を取得した後でエンティティが更新されている場合は、要求で指定された更新の条件が満たされていないことを示す `error` が返されます。
>
>

**replaceEntity** および **mergeEntity** では、更新するエンティティが存在しないと、更新操作が失敗します。したがって、更新するエンティティが既に存在するかどうかにかかわらずにエンティティを格納する場合は、**insertOrReplaceEntity** または **insertOrMergeEntity** を使用します。

成功した更新操作の `result` には、更新されたエンティティの **Etag** が含まれます。

## <a name="work-with-groups-of-entities"></a>エンティティのグループを操作する
状況によって、複数の操作をバッチとして送信し、サーバーによるアトミック処理を行うことが合理的である場合があります。 これを実現するには、**TableBatch** クラスを使ってバッチを作成し、**TableService** の **executeBatch** メソッドを使ってバッチ操作を実行します。

 次の例に、2 つのエンティティをバッチで送信する方法を示します。

```nodejs
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

バッチ操作が成功した場合、`result` にはバッチ内の各操作の情報が含まれます。

### <a name="work-with-batched-operations"></a>バッチ操作の処理
バッチに追加された操作を検査するには、`operations` プロパティを確認します。 次のメソッドを使用して操作を処理できます。

* **clear** - バッチのすべての操作をクリアします。
* **getOperations** - バッチから操作を取得します。
* **hasOperations** - バッチに操作が含まれている場合は true を返します。
* **removeOperations** - 操作を削除します。
* **size** - バッチ内の操作の数を返します。

## <a name="retrieve-an-entity-by-key"></a>キーを使用したエンティティを取得する
**PartitionKey** と **RowKey** に基づいて特定のエンティティを返すには、**retrieveEntity** メソッドを使います。

```nodejs
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

この操作を完了すると、`result` にはエンティティが含まれます。

## <a name="query-a-set-of-entities"></a>エンティティのセットを照会する
テーブルを照会するには、 **TableQuery** オブジェクトを使用し、以下の句を使用してクエリ式を作成します。

* **select** - クエリから返されるフィールド
* **where** - where 句

  * **and** - `and` where 条件
  * **or** - `or` where 条件
* **top** - 取得する項目の数

次の例では、"hometasks" の PartitionKey で最初の 5 つの項目を返すクエリを作成します。

```nodejs
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

**select** が使用されていないため、すべてのフィールドが返されます。 テーブルでクエリを実行するには、 **queryEntities**を使用します。 次の例では、このクエリを使用して "mytable" からエンティティを返します。

```nodejs
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

成功した場合は、`result.entries` にはクエリに一致するエンティティの配列が含まれます。 クエリですべてのエンティティを返すことができなかった場合、`result.continuationToken` は*null* 以外になり、さらに結果を取得するために、これを **queryEntities** の 3 番目のパラメーターとして使用できます。 最初のクエリでは、3 番目のパラメーターに *null* を使用します。

### <a name="query-a-subset-of-entity-properties"></a>エンティティ プロパティのサブセットを照会する
テーブルに対するクエリでは、ごくわずかのフィールドだけをエンティティから取得できます。
この方法では、帯域幅の使用が削減され、クエリのパフォーマンスが向上します。特に、大量のエンティティがある場合に役立ちます。 **select** 句を使って、返されるフィールドの名前を渡します。 たとえば、次のクエリでは **description** フィールドと **dueDate** フィールドのみを返します。

```nodejs
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>エンティティを削除する
パーティション キーと行キーを使用してエンティティを削除できます。 次の例では、**task1** オブジェクトに、削除するエンティティの **RowKey** と **PartitionKey** の値が格納されます。 次に、このオブジェクトが **deleteEntity** メソッドに渡されます。

```nodejs
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> 項目を削除する場合は、項目が別のプロセスによって変更されていないことを確認するために ETag を使用することを検討してください。 ETag の使用の詳細については、「 [エンティティを更新する](#update-an-entity) 」を参照してください。
>
>

## <a name="delete-a-table"></a>テーブルを削除する
次のコードは、ストレージ アカウントからテーブルを削除します。

```nodejs
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

テーブルが存在するかどうかが不明な場合は、 **deleteTableIfExists**を使用します。

## <a name="use-continuation-tokens"></a>継続トークンを使用する
テーブルに対するクエリによって大量の結果が返される場合は、継続トークンを探します。 クエリの対象となるデータは大量になる可能性があります。継続トークンが存在するときにわかるようにしていない場合はこの事実に気付かない可能性があります。

エンティティを照会したときに返される **results** オブジェクトは、このようなトークンが存在する場合に `continuationToken` プロパティを設定します。 クエリを実行するときにこれを使用して、後続のパーティションおよびテーブル エンティティに移動できます。

クエリを実行するとき、クエリ オブジェクト インスタンスとコールバック関数の間に `continuationToken` パラメーターを指定することができます。

```nodejs
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

`continuationToken` オブジェクトには `nextPartitionKey`、`nextRowKey`、`targetLocation` などのプロパティがあり、これらを使用してすべての結果を反復処理できます。

## <a name="work-with-shared-access-signatures"></a>共有アクセス署名を操作する
共有アクセス署名 (SAS) は、ストレージ アカウントの名前またはキーを指定せずにテーブルへの細密なアクセスを提供する安全な方法です。 多くの場合、SAS は、モバイル アプリでのレコードの照会などデータへの限定的なアクセスのために使用されます。

クラウドベースのサービスなどの信頼されたアプリケーションは、**TableService** の **generateSharedAccessSignature** を使って SAS を生成し、信頼されていないか、モバイル アプリなどの部分的に信頼されたアプリケーションにこれを提供します。 SAS は、SAS が有効である期間の開始日と終了日のほか、SAS の保有者に付与されたアクセス レベルを示したポリシーを使用して生成されます。

次の例では、SAS の保有者に対してテーブルのクエリ ('r') を許可し、作成時から 100 分後に期限が切れる、新しい共有アクセス ポリシーを作成しています。

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

SAS の保有者がテーブルにアクセスするときに必要なホスト情報も提供する必要があることに注意してください。

クライアント アプリケーションは、この SAS と **TableServiceWithSAS** を使用してテーブルに対する操作を実行します。 次の例では、テーブルに接続してクエリを実行しています。 tableSAS の形式については、[Shared Access Signature の使用](../storage/common/storage-dotnet-shared-access-signature-part-1.md#examples-of-sas-uris)に関する記事を参照してください。 

```nodejs
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

SAS がクエリ アクセスのみで生成されたので、エンティティの挿入、更新、または削除を試行するとエラーが返されます。

### <a name="access-control-lists"></a>アクセス制御リスト
SAS のアクセス ポリシーを設定するために、アクセス制御リスト (ACL) も使用できます。 複数のクライアントにテーブルへのアクセスを許可し、各クライアントに異なるアクセス ポリシーを提供する場合に便利です。

ACL は、アクセス ポリシーの配列と、各ポリシーに関連付けられた ID を使用して実装されます。 次の例では、2 つのポリシーを定義しています。1 つは "user1" 用、もう 1 つは "user2" 用です。

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

次の例では、現在の **hometasks** テーブルの ACL を取得し、**setTableAcl** を使って新しいポリシーを追加しています。 この手法で以下を実行できます。

```nodejs
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL を設定した後で、ポリシーの ID に基づいて SAS を作成できます。 次の例では、"user2" 用に新しい SAS を作成しています。

```nodejs
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>次の手順
詳細については、次のリソースを参照してください。

* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は、Windows、macOS、Linux で Azure Storage のデータを視覚的に操作できる Microsoft 製の無料のスタンドアロン アプリです。
* GitHub の [Azure Storage SDK for Node.js](https://github.com/Azure/azure-storage-node) リポジトリ
* [Node.js 開発者向けの Azure](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest)
* [Azure で Node.js Web アプリを作成する](../app-service/app-service-web-get-started-nodejs.md)
* [Node.js アプリケーションの構築と Azure クラウド サービスへのデプロイ](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (Windows PowerShell を使用)
