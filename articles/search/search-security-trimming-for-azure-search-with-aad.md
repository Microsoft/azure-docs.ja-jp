---
title: Active Directory を使用して結果をトリミングするためのセキュリティ フィルター
titleSuffix: Azure Cognitive Search
description: セキュリティ フィルターと Azure Active Directory (AAD) ID を使用した Azure Cognitive Search の検索結果に対するドキュメント レベルのセキュリティ特権。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 87337cf22bdb388c5873a2811bb9913c3e7f4d4e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019778"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Active Directory ID を使用して Azure Cognitive Search の結果をトリミングするためのセキュリティ フィルター

この記事では、Azure Active Directory (AAD) セキュリティ ID と共に Azure Cognitive Search のフィルターを使用し、ユーザー グループのメンバーシップに基づいて検索結果をトリミングする方法について説明します。

この記事に含まれるタスクは次のとおりです。
> [!div class="checklist"]
> - AAD グループとユーザーを作成します
> - 作成したユーザーとグループを関連付けます
> - 新しいグループをキャッシュします
> - 関連付けられているグループでドキュメントのインデックスを作成します
> - グループ識別子フィルターでの検索要求を発行します
> 
> [!NOTE]
> この記事のサンプル コード スニペットは、C# で書かれています。 [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started)に完全なソース コードがあります。 

## <a name="prerequisites"></a>前提条件

Azure Cognitive Search のインデックスには、ドキュメントに対する読み取りアクセス権を持つグループの ID のリストを格納するための[セキュリティ フィールド](search-security-trimming-for-azure-search.md)が存在する必要があります。 このユース ケースでは、セキュリティ保護可能な項目 (個々の大学アプリケーションなど) とその項目へのアクセス権を持っているユーザー (入学担当者) を指定するセキュリティ フィールドの間に、一対一の対応があることを前提としています。

このチュートリアルを行うには、AAD でユーザー、グループ、および関連付けを作成するために必要な AAD 管理者アクセス許可を持っている必要があります。

また、以下の手順で説明するように、お使いのアプリケーションが AAD に登録されている必要もあります。

### <a name="register-your-application-with-aad"></a>AAD にアプリケーションを登録する

このステップでは、ユーザー アカウントとグループ アカウントのサインインを受け付けるために、アプリケーションを AAD と統合します。 組織の AAD 管理者ではない場合は、以下の手順を実行するために[新しいテナントを作成する](../active-directory/develop/quickstart-create-new-tenant.md)ことが必要な場合があります。

1. [ **[アプリケーション登録ポータル]** ](https://apps.dev.microsoft.com) >   **[Converged app]\(集中型アプリ\)**  >  **[Add an app]\(アプリの追加\)** に移動します。
2. アプリケーションの名前を入力して、 **[作成]** をクリックします。 
3. [マイ アプリケーション] ページで、新しく登録したアプリケーションを選びます。
4. アプリケーション登録ページの **[プラットフォーム]**  >  **[プラットフォームの追加]** で、 **[Web API]** を選びます。
5. 引き続きアプリケーション登録ページで、 **[Microsoft Graph のアクセス許可]**  >  **[追加]** に移動します。
6. [アクセス許可の選択] で、次の委任されたアクセス許可を追加し、 **[OK]** をクリックします。

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph に用意されている API では、REST API を使ってプログラムから AAD にアクセスできます。 このチュートリアルのコード サンプルでは、Microsoft Graph API を呼び出すアクセス許可を使って、グループ、ユーザー、および関連付けを作成します。 この API は、高速のフィルター処理用にグループ識別子をキャッシュするためにも使われます。

## <a name="create-users-and-groups"></a>ユーザーとグループを作成する

設定済みのアプリケーションに対する検索を追加する場合は、ユーザーとグループ識別子が AAD に既に存在していることがあります。 その場合は、次の 3 つのステップをスキップできます。 

ただし、既存のユーザーがいない場合は、Microsoft Graph API を使ってセキュリティ プリンシパルを作成できます。 次のコード スニペットでは、識別子を生成する方法を示します。この識別子は、Azure Cognitive Search のインデックスでセキュリティ フィールドのデータ値になります。 この架空の大学入学アプリケーションでは、これは入学スタッフのセキュリティ識別子です。

特に大規模な組織では、ユーザーとグループ メンバーシップが頻繁に変更される場合があります。 ユーザーとグループの ID を作成するコードは、組織のメンバーシップの変更を反映するのに十分な頻度で実行する必要があります。 また、Azure Cognitive Search インデックスについても、許可されたユーザーとリソースの現在の状態を反映するために同様の更新スケジュールが必要です。

### <a name="step-1-create-aad-group"></a>手順 1:[AAD グループ](/graph/api/group-post-groups?view=graph-rest-1.0)を作成する 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-user"></a>手順 2:[AAD ユーザー](/graph/api/user-post-users?view=graph-rest-1.0)を作成する
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>手順 3:ユーザーとグループを関連付ける
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>手順 4:グループ識別子をキャッシュする
ネットワークの待機時間を減らす必要がある場合は、ユーザーとグループの関連付けをキャッシュして、発行された検索要求に対し、AAD にラウンドトリップするのではなく、キャッシュからグループを返すことができます。 [AAD Batch API](/graph/json-batching) を使用して、複数のユーザーを含む単一の HTTP 要求を送信し、キャッシュを作成することができます。

Microsoft Graph は、大量の要求を処理できるように設計されています。 ただし、処理できないほど多数の要求が発生すると、Microsoft Graph は HTTP 状態コード 429 で要求を失敗させます。 詳しくは、「[Microsoft Graph 調整ガイド](/graph/throttling)」をご覧ください。

## <a name="index-document-with-their-permitted-groups"></a>許可されているグループでドキュメントにインデックスを付ける

Azure Cognitive Search のクエリ操作は、Azure Search インデックスを介して実行されます。 このステップでは、インデックス操作が、セキュリティ フィルターとして使われる識別子などの検索可能なデータを、インデックスにインポートします。 

Azure Cognitive Search では、ユーザー ID の認証は行われず、ユーザーが表示アクセス許可を持っているコンテンツを確立するためのロジックは提供されません。 セキュリティによるトリミングのユース ケースでは、機密性の高いドキュメントとそのドキュメントにアクセスできるグループ識別子の関連付けをユーザーが提供し、そのまま検索インデックスにインポートされるものと想定されています。 

この仮定の例では、Azure Cognitive Search インデックスに対する PUT 要求の本文には、志望者の小論文または成績証明書と共にそのコンテンツを表示するアクセス許可を持つグループ識別子が含まれます。 

このチュートリアルのコード サンプルで使われている汎用的な例のインデックス操作は次のようなものです。

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>検索要求を発行する

セキュリティによるトリミングが目的の場合、インデックスのセキュリティ フィールドの値は、検索結果にドキュメントを含めるか除外するために使われる静的な値です。 たとえば、入学者選考のグループ ID が "A11B22C33D44-E55F66G77-H88I99JKK" の場合、Azure Cognitive Search インデックスのセキュリティ フィールドがこの識別子であるドキュメントは、要求元に返送される検索結果に含まれるか、または検索結果から除外されます。

要求を発行したユーザーのグループに基づいて検索結果で返されるドキュメントをフィルター処理するには、次の手順のようにします。

### <a name="step-1-retrieve-users-group-identifiers"></a>手順 1:ユーザーのグループ識別子を取得する

ユーザーのグループがまだキャッシュされていない場合、またはキャッシュの有効期限を過ぎている場合は、[groups](/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) 要求を発行します。
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>手順 2:検索要求を作成する

ユーザーのグループ メンバーシップがある場合は、適切なフィルター値を指定して検索要求を発行できます。

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>手順 3:結果を処理する

応答には、ユーザーが表示アクセス許可を持つドキュメントだけにフィルター処理されたリストが含まれます。 検索結果ページの作成方法によっては、フィルター処理された結果セットを反映するための視覚的な合図を含めることができます。

## <a name="conclusion"></a>まとめ

このチュートリアルでは、AAD のサインインを使って Azure Cognitive Search の結果のドキュメントをフィルター処理し、要求で指定されたフィルターに一致しないドキュメントの結果をトリミングする方法を説明しました。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search フィルターを使用した ID ベースのアクセスの制御](search-security-trimming-for-azure-search.md)
+ [Azure Cognitive Search のフィルター](search-filters.md)
+ [Azure Cognitive Search 操作でのデータ セキュリティとアクセスの制御](search-security-overview.md)