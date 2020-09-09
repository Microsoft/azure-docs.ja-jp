---
title: Azure Cosmos DB のデータへのアクセスをセキュリティで保護する方法
description: マスター キー、読み取り専用キー、ユーザー、アクセス許可など、Azure Cosmos DB のアクセス制御の概念について説明します。
author: thomasweiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4714ec9773b98887de483b7353eea9f4416eec19
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017755"
---
# <a name="secure-access-to-data-in-azure-cosmos-db"></a>Azure Cosmos DB のデータへのアクセスをセキュリティで保護する

この記事では、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) に格納されたデータへのアクセスをセキュリティ保護する方法の概要を説明します。

Azure Cosmos DB では、2 種類のキーを使用してユーザーを認証し、そのデータとリソースへのアクセスを提供しています。 

|キーの種類|リソース|
|---|---|
|[マスター キー](#master-keys) |次の管理リソースで使用されます。データベース アカウント、データベース、ユーザー、およびアクセス許可|
|[リソース トークン](#resource-tokens)|次のアプリケーション リソースで使用されます。コンテナー、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF|

<a id="master-keys"></a>

## <a name="master-keys"></a>マスター キー

マスター キーは、データベース アカウントのすべての管理リソースへのアクセスを提供します。 マスター キー:

- アカウント、データベース、ユーザー、およびアクセス許可へのアクセスを提供します。 
- コンテナーとドキュメントへのきめ細かいアクセスを提供するために使用することはできません。
- アカウントの作成時に作成されます。
- いつでも再生成することができます。

各アカウントは、プライマリ キーとセカンダリ キーという 2 つのマスター キーで構成されます。 二重キーの目的は、キーを再生成 (ロール) して、アカウントとデータに継続的にアクセスできるようにするためです。

Cosmos DB アカウント用の 2 つのマスター キーに加えて、2 つの読み取り専用キーがあります。 これらの読み取り専用キーは、アカウントの読み取り操作のみを許可します。 読み取り専用キーは、アクセス許可リソースを読み取るためのアクセスを提供しません。

プライマリ、セカンダリ、読み取り専用、および読み取り/書き込みのマスター キーは、Azure Portal で取得と再生成を行うことができます。 手順については、「[アクセス キーを表示、コピー、および再生成する](manage-with-cli.md#regenerate-account-key)」を参照してください。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Azure portal でのアクセス制御 (IAM) - NoSQL データベースのセキュリティのデモ":::

### <a name="key-rotation"></a>キーのローテーション<a id="key-rotation"></a>

マスター キーのローテーション プロセスは単純です。 

1. Azure portal に移動してセカンダリ キーを取得します。
2. アプリケーションで、プライマリ キーをセカンダリ キーに置き換えます。 全デプロイにわたるすべての Cosmos DB クライアントが直ちに再起動され、更新されたキーの使用が開始されることを確認します。
3. Azure portal でプライマリ キーをローテーションします。
4. 新しいプライマリ キーがすべてのリソースに対して動作することを検証します。 キーのローテーション プロセスには、Cosmos DB アカウントのサイズに応じて、1 分未満から数時間かかる場合があります。
5. セカンダリ キーを新しいプライマリ キーに置き換えます。

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-rotate-workflow.png" alt-text="Azure portal でのマスター キーのローテーション - NoSQL データベースのセキュリティのデモ" border="false":::

### <a name="code-sample-to-use-a-master-key"></a>マスター キーを使用するコード サンプル

次のコード サンプルは、Cosmos DB アカウントのエンドポイントとマスター キーを使用して、DocumentClient のインスタンス化とデータベースの作成を行う方法を示しています。

```csharp
//Read the Azure Cosmos DB endpointUrl and authorization keys from config.
//These values are available from the Azure portal on the Azure Cosmos DB account blade under "Keys".
//Keep these values in a safe and secure location. Together they provide Administrative access to your Azure Cosmos DB account.

private static readonly string endpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
private static readonly string authorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];

CosmosClient client = new CosmosClient(endpointUrl, authorizationKey);
```

次のコード サンプルは、Azure Cosmos DB アカウントのエンドポイントとマスター キーを使用して、`CosmosClient` オブジェクトのインスタンス化を行う方法を示しています。

:::code language="python" source="~/cosmosdb-python-sdk/sdk/cosmos/azure-cosmos/samples/access_cosmos_with_resource_token.py" id="configureConnectivity":::

## <a name="resource-tokens"></a>リソース トークン <a id="resource-tokens"></a>

リソース トークンは、データベース内のアプリケーション リソースへのアクセスを提供します。 リソース トークン:

- コンテナー、パーティション キー、ドキュメント、添付ファイル、ストアド プロシージャ、トリガー、UDF へのアクセスを提供します。
- [ユーザー](#users)が特定のリソースへの[アクセス許可](#permissions)を付与されたときに作成されます。
- アクセス許可リソースが POST、GET、または PUT 呼び出しで動作するときに作成されます。
- ユーザー、リソース、およびアクセス許可用に特別に構築されたハッシュ リソース トークンを使用します。
- カスタマイズ可能な有効期間による時間の拘束があります。 既定の有効期間は 1 時間です。 ただし、トークンの有効期間は、最大 5 時間まで、明示的に指定することができます。
- マスター キーの代わりに使用できる安全な代替手段を提供します。
- クライアントが、付与されているアクセス許可に従って、Cosmos DB アカウント内のリソースを読み取り、書き込み、および削除できるようにします。

リソース トークンは、自分のマスター キーを知らせたくないクライアントに、自分の Cosmos DB アカウント内のリソースへのアクセスを許可する場合に (Cosmos DB ユーザーとアクセス許可を作成することによって) 使用できます。  

Cosmos DB リソース トークンにより、付与されたアクセス許可に従って、マスター キーや読み取り専用キーなしでクライアントによる Cosmos DB アカウント内のリソースの読み取り、書き込み、および削除を可能にする安全な代替手段が提供されます。

リソース トークンの要求、生成、およびクライアントへの配信に使用されることがある一般的な設計パターンを次に示します。

1. 中間層サービスは、ユーザーの写真を共有するモバイル アプリケーションを提供するために設定します。
2. 中間層サービスは、Cosmos DB アカウントのマスター キーを持ちます。
3. 写真アプリは、エンド ユーザーのモバイル デバイスにインストールされます。
4. ログイン時に、写真アプリは、中間層サービスを使用してユーザー ID を確立します。 この ID 確立のしくみは完全にアプリケーションに依存します。
5. いったん ID が確立されると、中間層サービスはその ID に基づいてアクセス許可を要求します。
6. 中間層サービスから、リソース トークンが電話アプリに返されます。
7. 電話アプリはリソース トークンを引き続き使用し、リソース トークンによって定義されたアクセス許可を使用して、リソース トークンによって許可された間隔で Cosmos DB リソースに直接アクセスすることができます。
8. リソース トークンの期限が切れると、それ以降の要求は、401 (承認されていない例外) を受け取ります。  この時点で、電話アプリは ID を再度確立し、新しいリソース トークンを要求します。

    :::image type="content" source="./media/secure-access-to-data/resourcekeyworkflow.png" alt-text="Azure Cosmos DB リソース トークンのワークフロー" border="false":::

リソース トークンの生成と管理は、ネイティブ Cosmos DB クライアント ライブラリによって処理されます。ただし、REST を使用する場合は、要求/認証ヘッダーを構築する必要があります。 REST 用の認証ヘッダーの作成については、[Cosmos DB リソースのアクセス制御](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)に関するページや、[.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos/src/AuthorizationHelper.cs) または [Node.js SDK](https://github.com/Azure/azure-cosmos-js/blob/master/src/auth.ts) のソース コードを参照してください。

ブローカー リソース トークンを生成するために使用する中間層サービスの例については、[ResourceTokenBroker アプリ](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems/ResourceTokenBroker/ResourceTokenBroker/Controllers)に関するページを参照してください。

## <a name="users"></a>ユーザー<a id="users"></a>

Azure Cosmos DB ユーザーは、Cosmos データベースに関連付けらていれます。  各データベースには、0 人以上の Cosmos DB ユーザーを含めることができます。 次のコード サンプルは、[Azure Cosmos DB .NET SDK v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement) を使用して Cosmos DB ユーザーを作成する方法を示しています。

```csharp
//Create a user.
Database database = benchmark.client.GetDatabase("SalesDatabase");

User user = await database.CreateUserAsync("User 1");
```

> [!NOTE]
> 各 Cosmos DB ユーザーには、ユーザーに関連付けられた[アクセス許可](#permissions)の一覧を取得するために使用できる ReadAsync() メソッドがあります。

## <a name="permissions"></a>アクセス許可<a id="permissions"></a>

アクセス許可リソースはユーザーに関連付けられ、コンテナーおよびパーティション キー レベルで割り当てられます。 各ユーザーには、0 個以上のアクセス許可を含めることができます。 アクセス許可リソースによって、ユーザーが特定のパーティション キー内の特定のコンテナーまたはデータにアクセスするときに必要なセキュリティ トークンへのアクセスが提供されます。 アクセス許可リソースによって提供できるアクセス レベルは 2 つあります。

- All:ユーザーはリソースに対して完全なアクセス許可を持ちます。
- Read:ユーザーは、リソースの内容の読み取りのみを行えますが、リソースへの書き込み、更新、または削除の操作を実行することはできません。

> [!NOTE]
> ストアド プロシージャを実行するには、ストアド プロシージャを実行するコンテナーの All 権限を持つ必要があります。

### <a name="code-sample-to-create-permission"></a>アクセス許可を作成するコード サンプル

次のコード サンプルは、アクセス許可リソースを作成し、アクセス許可リソースのリソース トークンを読み取って、先ほど作成した[ユーザー](#users)にアクセス許可を関連付ける方法を示しています。

```csharp
// Create a permission on a container and specific partition key value
Container container = client.GetContainer("SalesDatabase", "OrdersContainer");
user.CreatePermissionAsync(
    new PermissionProperties(
        id: "permissionUser1Orders",
        permissionMode: PermissionMode.All,
        container: benchmark.container,
        resourcePartitionKey: new PartitionKey("012345")));
```

### <a name="code-sample-to-read-permission-for-user"></a>ユーザーのアクセス許可を読み取るコード サンプル

次のコード スニペットは、上で作成したユーザーに関連付けられているアクセス許可を取得し、1 つのパーティション キーにスコープを指定して、ユーザーに代わって新しい CosmosClient をインスタンス化する方法を示しています。

```csharp
//Read a permission, create user client session.
PermissionProperties permissionProperties = await user.GetPermission("permissionUser1Orders")

CosmosClient client = new CosmosClient(accountEndpoint: "MyEndpoint", authKeyOrResourceToken: permissionProperties.Token);
```

## <a name="add-users-and-assign-roles"></a>ユーザーの追加とロールの割り当てを行う

ユーザー アカウントに Azure Cosmos DB アカウントの閲覧者アクセス権を追加するには、サブスクリプションの所有者が Azure Portal で以下の手順を実行します。

1. Azure Portal を開き、Azure Cosmos DB アカウントを選択します。
2. **[アクセス制御 (IAM)]** タブをクリックし、 **[+ ロール割り当ての追加]** をクリックします。
3. **[ロール割り当ての追加]** ウィンドウの **[ロール]** ボックスで、 **[Cosmos DB アカウントの閲覧者ロール]** を選択します。
4. **[アクセスの割り当て先]** ボックスで、 **[Azure AD のユーザー、グループ、またはアプリケーション]** を選択します。
5. ディレクトリで、アクセス権を付与するユーザー、グループ、またはアプリケーションを選択します。  ディレクトリは、表示名、電子メール アドレス、およびオブジェクト識別子を使用して検索できます。
    選択したメンバー、グループ、またはアプリケーションが、選択したメンバー一覧に表示されます。
6. **[保存]** をクリックします。

そのエンティティが Azure Cosmos DB リソースを閲覧できるようになります。

## <a name="delete-or-export-user-data"></a>ユーザー データの削除またはエクスポート

Azure Cosmos DB では、データベースまたはコレクションにあるすべての個人データを、検索、選択、変更、削除することができます。 Azure Cosmos DB には、個人データを検索および削除する API があります。しかし、個人データを消去する API を使用してロジックを定義するのはユーザーの責任です。 各マルチモデル API (SQL、MongoDB、Gremlin、Cassandra、Table) では、個人データを検索および削除するメソッドを含む SDK をさまざまな言語で提供しています。 [Time to Live (TTL)](time-to-live.md) 機能を有効にすると、追加のコストの発生なしに、指定した期間後に自動的にデータを削除するようにすることもできます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次のステップ

- Cosmos データベースのセキュリティの詳細については、[Cosmos DB: データベースのセキュリティ](database-security.md)に関する記事を参照してください。
- Azure Cosmos DB 認証トークンを作成する方法については、[Azure Cosmos DB リソースのアクセス制御](/rest/api/cosmos-db/access-control-on-cosmosdb-resources)に関するページをご覧ください。
- ユーザーとアクセス許可を使用したユーザー管理サンプルについては、[.NET SDK v3 ユーザー管理サンプル](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/UserManagement/UserManagementProgram.cs)を参照してください
