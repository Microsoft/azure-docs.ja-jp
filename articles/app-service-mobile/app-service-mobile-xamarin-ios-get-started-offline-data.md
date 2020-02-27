---
title: オフライン同期を有効にする (Xamarin iOS)
description: App Service Mobile App を使用して、Xamarin iOS アプリケーションのオフライン データをキャッシュおよび同期する方法を説明します。
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 3a5128f6918b22be2ff1ef6adf3e453b1f373ea6
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461302"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Xamarin iOS モバイル アプリのオフライン同期を有効にする
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>概要
このチュートリアルでは、Xamarin iOS 向けの Azure モバイル アプリのオフライン同期機能について説明します。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。 変更は、ローカル データベースに格納されます。 デバイスが再びオンラインになると、これらの変更がリモート サービスと同期されます。

このチュートリアルでは、「[Xamarin.iOS アプリを作成する]」で作成した Xamarin iOS アプリ プロジェクトを更新し、Azure Mobile Apps のオフライン機能をサポートできるようにします。 ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。 サーバーの拡張機能パッケージの詳細については、「 [Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## <a name="update-the-client-app-to-support-offline-features"></a>オフライン機能をサポートするようにクライアント アプリを更新する
Azure モバイル アプリのオフライン機能を使用すると、オフラインになっている状況でも、ローカル データベースとやり取りすることができます。 アプリケーションでこれらの機能を使用するには、[SyncContext] をローカル ストアに初期化します。 [IMobileServiceSyncTable] インターフェイスを使用してテーブルを参照します。 SQLite は、デバイス上のローカル ストアとして使用されます。

1. 「[Xamarin.iOS アプリを作成する]」チュートリアルで作成したプロジェクトの NuGet パッケージ マネージャーを開き、**Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet パッケージを検索してインストールします。
2. QSTodoService.cs ファイルを開き、`#define OFFLINE_SYNC_ENABLED` の定義をコメント解除します。
3. クライアント アプリの再構築と実行 アプリは、オフライン同期を有効にする前と同じように動作します。ただし今度は、オフライン シナリオで使用できるデータがローカル データベースに格納されます。

## <a name="update-sync"></a>アプリを更新してバックエンドから切断する
ここでは、オフライン状況をシミュレートするために、モバイル アプリ バックエンドへの接続を解除します。 データ項目を追加すると、例外ハンドラーによって、アプリがオフライン モードであることが通知されます。 この状態では、新しい項目はローカル ストアに追加され、プッシュが次に接続状態で実行したときに、モバイル アプリ バックエンドに同期されます。

1. 共有プロジェクトの QSToDoService.cs を編集します。 **applicationURL** を、無効な URL を指すように変更します。

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    また、デバイス上で Wi-Fi および移動体通信ネットワークを無効にするか、機内モードを使用して、オフライン動作をデモンストレーションすることもできます。
2. アプリケーションをビルドし、実行します。 アプリを起動した際の更新時には同期が失敗することに注意してください。
3. 新しい項目を入力し、 **[保存]** をクリックするたびに [CancelledByNetworkError] ステータスでプッシュが失敗することを確認します。 ただし、新しい todo 項目は、モバイル アプリ バックエンドにプッシュされるまでは、ローカル ストア内に存在します。  運用アプリでは、これらの例外を抑制した場合、クライアント アプリはモバイル アプリ バックエンドにまだ接続されているかのように動作します。
4. アプリケーションを終了し、再起動して、作成した新しい項目がローカル ストアに保存されていることを確認します。
5. (省略可能) PC に Visual Studio がインストールされている場合は、**サーバー エクスプローラー**を開きます。 **[Azure]** ->  **[SQL Databases]** を選択して、データベースに移動します。 データベースを右クリックし、 **[SQL Server オブジェクト エクスプローラーで開く]** を選択します。 これで SQL データベースのテーブルとその内容を参照できます。 バックエンド データベース内のデータが変更されていないことを確認します。
6. (省略可能) Fiddler や Postman などの REST ツールを使用して、モバイルのバックエンドをクエリします。その際、`https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem` の形式で、GET クエリを使用します。

## <a name="update-online-app"></a>モバイル アプリ バックエンドに再接続するようにアプリケーションを更新する
ここでは、アプリをモバイル アプリ バックエンドに再接続します。 これは、アプリケーションがオフライン状態から、モバイル アプリ バックエンドとのオンライン状態に移行したことをシミュレートします。   ネットワーク接続を無効にしてネットワーク破損をシミュレートした場合、コードを変更する必要はありません。
ネットワークを再び有効にします。  初めてアプリケーションを実行すると、`RefreshDataAsync` メソッドが呼び出されます。 これが次に `SyncAsync` を呼び出し、ローカル ストアとバックエンドのデータベースを同期します。

1. 共有プロジェクトの QSToDoService.cs を開き、**applicationURL** プロパティの変更を元に戻します。
2. アプリケーションを再構築して実行します。 アプリは、`OnRefreshItemsSelected` メソッドが実行されると、プッシュとプルの操作によって、ローカルでの変更を Azure Mobile Apps バックエンドに同期します。
3. (省略可能) SQL Server Object Explorer または Fiddler などの REST ツールを使用して、更新データを表示します。 データが同期されるのは、Azure モバイル アプリのバックエンドのデータベースとローカル ストアの間であることに注意してください。
4. アプリケーションで、ローカル ストアで完了させる項目の横にあるチェック ボックスをクリックします。

   `CompleteItemAsync` は `SyncAsync` を呼び出し、完了した各項目をモバイル アプリ バックエンドと同期します。 `SyncAsync` はプッシュとプルの両方を呼び出します。
   **クライアントが変更を行ったテーブルに対してプルを実行するたびに、クライアントの同期コンテキストへのプッシュが常に最初に自動的に実行されます**。 暗黙的なプッシュは、ローカル ストアのすべてのテーブルとリレーションシップの一貫性を確実に保つためです。 この動作については、「 [Azure Mobile Apps でのオフライン データ同期]」を参照してください。

## <a name="review-the-client-sync-code"></a>クライアント同期コードの確認
チュートリアル「 [Xamarin.iOS アプリを作成する] 」を完了した際にダウンロードした Xamarin クライアント プロジェクトには、ローカルの SQLite データベースを使用したオフライン同期をサポートするコードがすでに含まれてます。 チュートリアルのコードにすでに含まれているものの概要を示します。 機能の概念的な概要については、「 [Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

* テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。 `QSTodoListViewController.ViewDidLoad()` が `QSTodoService.InitializeStoreAsync()` を実行すると、ローカル ストアのデータベースが初期化されます。 このメソッドにより、Azure Mobile Apps クライアント SDK で提供される `MobileServiceSQLiteStore` クラスを使用して、新しいローカルの SQLite データベースが作成されます。

    `DefineTable` メソッドを実行すると、提供された型のフィールドに一致するテーブルがローカル ストアに作成されます。この例では、`ToDoItem` になります。 この型に、リモート データベース内のすべての列を含める必要はありません。 列のサブセットのみ格納できます。

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* `QSTodoService` の `todoTable` メンバーは、`IMobileServiceTable` ではなく、`IMobileServiceSyncTable` 型です。 IMobileServiceSyncTable は、テーブルの作成、読み取り、更新、削除 (CRUD) などのすべての操作がローカル ストア データベースに対して行われるようにします。

    `IMobileServiceSyncContext.PushAsync()`を呼び出すことによって、これらの変更がいつ Azure モバイル アプリ バックエンドにプッシュされるかを決定します。 同期コンテキストは、 `PushAsync` が呼び出されたときに、クライアント アプリが変更を行ったすべてのテーブルで、変更を追跡およびプッシュすることで、テーブルの関係を保持するのに役立ちます。

    todoitem リストの更新、または todoitem の追加や完了があれば、提供されているコードは `QSTodoService.SyncAsync()` を呼び出して同期します。 アプリは、ローカルの変更があるたびに同期されます。 コンテキストによって追跡された保留中のローカル更新のあるテーブルに対してプルが実行される場合、そのプル操作はコンテキストのプッシュを最初に自動的にトリガーします。

    提供されたコードでは、リモートの `TodoItem` テーブルのすべてのレコードはクエリされますが、クエリ ID やクエリを `PushAsync` に渡すことでレコードをフィルター処理することも可能です。 詳細については、「 *Azure Mobile Apps でのオフライン データ同期* 」の「 [Azure Mobile Apps でのオフライン データ同期]」セクションを参照してください。

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>その他のリソース
* [Azure Mobile Apps でのオフライン データ同期]
* [Azure Mobile Apps .NET SDK の使用方法][8]

<!-- Images -->

<!-- URLs. -->
[Xamarin.iOS アプリを作成する]: app-service-mobile-xamarin-ios-get-started.md
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
