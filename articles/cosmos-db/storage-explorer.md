---
title: Azure Storage Explorer を使用した Azure Cosmos DB リソースの管理
description: Azure Cosmos DB に接続し、Azure Storage Explorer を使用してそのリソースを管理する方法について説明します。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: d1948ae186662c7f60f4d49c19a4d48b424a38f7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047481"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Azure Storage Explorer を使ってデータを操作する

Azure Cosmos DB を Azure Storage Explorer で使用することで、ストレージ BLOB やキューなどの他の Azure エンティティと一緒に Azure Cosmos DB エンティティの管理、データの操作、ストアド プロシージャとトリガーの更新を実行できます。 さまざまな Azure エンティティを、同じツールを使用して 1 か所で管理できるようになりました。 Azure Storage Explorer では現在、SQL、MongoDB、Graph、Table API に構成された Cosmos アカウントがサポートされます。


## <a name="prerequisites"></a>前提条件

SQL API または Azure Cosmos DB の MongoDB 用 API の Cosmos アカウント。 アカウントを持っていない場合は、Azure Portal で作成できます。「[Azure Cosmos DB:.NET と Azure Portal による SQL API Web アプリのビルド](create-sql-api-dotnet.md)」を参照してください。

## <a name="installation"></a>インストール

最新の Azure Storage Explorer を「[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)」ページからインストールします。現在、Windows、Linux、Mac バージョンがサポートされています。

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

1. **Azure Storage Explorer** をインストールしたら、次の図の左側にある **[プラグイン]** アイコンを選択します。

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="[プラグイン] アイコンを選択して接続する":::

2. **[Add an Azure Account]\(Azure アカウントの追加\)** を選択し、**[サインイン]** を選択します。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="必要な Azure サブスクリプションに接続する":::

2. **[Azure サインイン]** ダイアログ ボックスで、 **[サインイン]** を選択し、Azure 資格情報を入力します。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Azure サブスクリプションにサインインする":::

3. 一覧からサブスクリプションを選択し、 **[適用]** を選択します。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="フィルター処理するサブスクリプション ID を一覧から選択する":::

    エクスプローラー ウィンドウが更新され、選択したサブスクリプション内のアカウントが表示されます。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="利用可能な一覧から Azure Cosmos DB アカウントを選択する":::

    **Cosmos DB アカウント**から Azure サブスクリプションに正常に接続されています。

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>接続文字列を使用して Azure Cosmos DB に接続する

Azure Cosmos DB に接続する別の方法では、接続文字列を使用します。 接続文字列を使用して接続するには、次の手順を使用します。

1. 左側のツリーで **[Local and Attached]\(ローカルおよびアタッチ済み\)** を見つけ、 **[Cosmos DB Accounts]\(Cosmos DB アカウント\)** を右クリックして、 **[Connect to Cosmos DB]\(Cosmos DB に接続\)** を選択します。

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="接続文字列を使用して Azure Cosmos DB に接続する":::

2. 現在は SQL API とテーブル API のみサポートします。 API を選択して**接続文字列**を貼り付け、**アカウント ラベル**を入力して **[次へ]** を選択し、概要をチェックしたら、 **[接続]** を選択して Azure Cosmos DB アカウントに接続します。 プライマリ接続文字列の取得については、「[接続文字列を取得する](manage-with-powershell.md#list-keys)」を参照してください。

    :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="接続文字列を入力する":::

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>ローカル エミュレーターを使用して Azure Cosmos DB に接続する

Azure Cosmos DB にエミュレーターで接続するには次の手順を使用します。現在は、SQL アカウントのみサポートします。

1. エミュレーターをインストールして、起動します。 エミュレーターのインストール方法については、[Cosmos DB エミュレーター](https://docs.microsoft.com/azure/cosmos-db/local-emulator)に関するページを参照してください。

2. 左側のツリーで **[Local and Attached]\(ローカルおよびアタッチ済み\)** を見つけ、 **[Connect to Cosmos DB Emulator]\(Cosmos DB エミュレーターに接続\)** を右クリックして、 **[Connect to Cosmos DB]\(Cosmos DB に接続\)** を選択します。

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="エミュレーターから Azure Cosmos DB に接続する":::

3. 現在は SQL API のみサポートします。 **接続文字列**を貼り付け、**アカウント ラベル**を入力して **[次へ]** を選択し、概要をチェックしたら、 **[接続]** を選択して Azure Cosmos DB アカウントに接続します。 プライマリ接続文字列の取得については、「[接続文字列を取得する](manage-with-powershell.md#list-keys)」を参照してください。

    :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="エミュレーター ダイアログから Cosmos DB に接続する":::


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB のリソース管理

次の操作を実行することで、Azure Cosmos DB アカウントを管理できます。
* Azure ポータルでアカウントを開く
* クイック アクセスの一覧にリソースを追加する
* リソースを検索および更新する
* データベースを作成および削除する
* コレクションを作成および削除する
* ドキュメントを作成、削除、およびフィルター処理する
* ストアド プロシージャ、トリガー、およびユーザー定義関数を管理する

### <a name="quick-access-tasks"></a>クイック アクセス タスク

エクスプローラー ウィンドウでサブスクリプションを右クリックすることで、多数のクイック アクション タスクを実行できます。

* Azure Cosmos DB アカウントまたはデータベースを右クリックし、 **[ポータルで開く]** を選択して、Azure ポータルのブラウザーでリソースを管理できます。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="ポータルで開く":::

* Azure Cosmos DB アカウント、データベース、コレクションを **[クイック アクセス]** に追加することもできます。
* **[Search from Here]\(ここから検索\)** は、選択したパスのキーワード検索を有効にします。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="ここから検索":::

### <a name="database-and-collection-management"></a>データベースとコレクションの管理

#### <a name="create-a-database"></a>データベースを作成する

- Azure Cosmos DB アカウントを右クリックし、 **[データベースの作成]** を選択し、データベース名を入力し、**Enter** キーを押します。

  :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Azure Cosmos アカウント内にデータベースを作成する":::

#### <a name="delete-a-database"></a>データベースの削除

- データベースを右クリックし、 **[データベースの削除]** を選択し、ポップアップ ウィンドウで **[はい]** を選択します。 データベース ノードが削除され、Azure Cosmos DB アカウントが自動的に更新されます。

  :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="1 つ目のデータベースを削除する":::

  :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="2 つ目のデータベースを削除する":::

#### <a name="create-a-collection"></a>コレクションの作成

1. データベースを右クリックし、 **[コレクションの作成]** をクリックし、 **[コレクション ID]** や **[ストレージ容量]** などの情報を指定します。 **[OK]** をクリックして終了します。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="データベースに最初のコレクションを作成する":::

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="データベースに 2 つ目のコレクションを作成する":::

2. パーティション キーを指定できるように **[無制限]** を選択し、 **[OK]** を選択して完了します。

    コレクションの作成時にパーティション キーを使用している場合は、作成が完了した後でコレクションのパーティション キー値を変更することはできません。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="パーティション キーを構成する":::

#### <a name="delete-a-collection"></a>コレクションの削除

- コレクションを右クリックし、 **[コレクションの削除]** を選択し、ポップアップ ウィンドウで **[はい]** を選択します。

    コレクション ノードが削除され、データベースが自動的に更新されます。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="コレクションの 1 つを削除する":::

### <a name="document-management"></a>ドキュメントの管理

#### <a name="create-and-modify-documents"></a>ドキュメントの作成と変更

- 新しいドキュメントを作成するには、左側のウィンドウで **[ドキュメント]** を開き、右側のウィンドウで **[新しいドキュメント]** を選択し、内容を編集し、 **[保存]** を選択します。 既存のドキュメントを更新して **[保存]** を選択することもできます。 **[破棄]** をクリックすることで、変更を破棄できます。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="新しいドキュメントの作成":::

#### <a name="delete-a-document"></a>ドキュメントの削除

- 選択したドキュメントを削除するには、 **[削除]** ボタンをクリックします。

#### <a name="query-for-documents"></a>ドキュメントのクエリ

- [SQL クエリ](how-to-sql-query.md)を入力してドキュメントをフィルターし、 **[適用]** を選択します。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="特定のドキュメントを照会する":::

### <a name="graph-management"></a>Graph の管理

#### <a name="create-and-modify-vertex"></a>頂点の作成と変更

1. 新しい頂点を作成するには、左側のウィンドウから **Graph** を開き、 **[New Vertex]\(新しい頂点\)** を選択して内容を編集し、 **[OK]** を選択します。
2. 既存の頂点を変更するには、右側のウィンドウにあるペン アイコンを選択します。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="グラフの頂点を変更する":::

#### <a name="delete-a-graph"></a>グラフの削除

- 頂点を削除するには、頂点の名前の近くにあるごみ箱アイコンを選択します。

#### <a name="filter-for-graph"></a>グラフのフィルター選択

- [Gremlin クエリ](gremlin-support.md)を入力してグラフ フィルターを編集し、 **[フィルターの適用]** を選択します。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="グラフ クエリを実行する":::

### <a name="table-management"></a>テーブル管理

#### <a name="create-and-modify-table"></a>テーブルの作成と変更

1. 新しいテーブルを作成するには、左側のウィンドウから **[エンティティ]** を開き、 **[追加]** を選択して、 **[エンティティの追加]** ダイアログで内容を編集し、 **[プロパティの追加]** ボタンをクリックしてプロパティを追加したら、 **[挿入]** を選択します。
2. テーブルに変更を加えるには、 **[編集]** を選択して内容に変更を加え、 **[更新]** を選択します。

   :::image type="content" source="./media/storage-explorer/table.png" alt-text="テーブルを作成および変更する":::

#### <a name="import-and-export-table"></a>データのインポートとエクスポート

1. インポートするには、 **[インポート]** ボタンを選択して既存のテーブルを選択します。
2. エクスポートするには、 **[エクスポート]** ボタンを選択してエクスポート先を選択します。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="テーブルをインポートまたはエクスポートする":::

#### <a name="delete-entities"></a>エンティティの削除

- エンティティを選択し、 **[削除]** ボタンを選択します。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="テーブルを削除する":::

#### <a name="query-table"></a>テーブルの照会

- **[クエリ]** ボタンをクリックし、クエリ条件を入力したら、 **[クエリの実行]** ボタンを選択します。 **[Close Query]\(クエリを閉じる\)** ボタンをクリックして [クエリ] ウィンドウを閉じます。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="テーブルからデータを照会する":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>ストアド プロシージャ、トリガー、および UDF の管理

* ストアド プロシージャを作成するには、左側のツリーで **[ストアド プロシージャ]** を右クリックし、 **[ストアド プロシージャ]** を選択し、左側のウィンドウで名前を入力し、右側のウィンドウでストアド プロシージャのスクリプトを入力し、 **[作成]** を選択します。
* 既存のストアド プロシージャをダブルクリックし、更新を行った後、 **[更新]** をクリックして保存するか、 **[破棄]** を選択して変更を取り消すこともできます。

  :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="ストアド プロシージャを作成および管理する":::

* **トリガー**と **UDF** に対する操作は、**ストアド プロシージャ**に対する操作に似ています。

## <a name="troubleshooting"></a>トラブルシューティング

[Azure Storage Explorer の Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) は、Windows、macOS、または Linux から、Azure およびソブリン クラウドでホストされている Azure Cosmos DB アカウントに接続できるスタンドアロン アプリです。 これを使用すると、ストレージ BLOB やキューなど、他の Azure エンティティと一緒に Azure Cosmos DB エンティティの管理、データの操作、ストアド プロシージャとトリガーの更新を実行できます。

これらは、Storage Explorer の Azure Cosmos DB でよく見られる問題の解決策です。

### <a name="sign-in-issues"></a>サインインの問題

先に進む前に、アプリケーションを再起動して問題が解決されるかどうかを確認してください。

#### <a name="self-signed-certificate-in-certificate-chain"></a>証明書チェーンの自己署名証明書

このエラーが表示されるにはいくつかの理由があります。最も一般的な理由は次の 2 つです。

+ *透過型プロキシ* の内側にいる。この場合、(IT 部門などの) 他のユーザーが、HTTPS トラフィックを遮断してその暗号化を解除し、自己署名証明書を使ってそれを暗号化しています。

+ 受信した HTTPS メッセージに自己署名 TLS/SSL 証明書を挿入するソフトウェア (ウイルス対策ソフトウェアなど) が実行されている。

これらのいずれかの "自己署名証明書" を検出した場合、Storage Explorer は受信した HTTPS メッセージが改ざんされているかどうかを識別できなくなります。 ただし、自己署名証明書のコピーがある場合は、Storage Explorer にそのコピーを信頼させることができます。 証明書の挿入元がわからない場合、次の手順を実行して自分で確認できます。

1. OpenSSL のインストール
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (簡易バージョンでかまいません)
     - Mac と Linux:オペレーティング システムに付属
2. OpenSSL を実行します。
    - Windows: インストール ディレクトリ、 **/bin/** の順に移動して、**openssl.exe** をダブルクリックします。
    - Mac と Linux: ターミナルで **openssl** を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します
4. 自己署名証明書を検索します。 どれが自己署名かわからない場合は、証明書の発行先 ("s:") と証明書の発行者 ("i:") が同じものを検索します。
5.  自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6.  Storage Explorer を開いて、 **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動します。 ファイル ピッカーを使用して、作成された .cer ファイルを検索し、選択して開きます。

上記の手順で自己署名証明書が見つからない場合、フィードバックを送信してお問い合わせください。

#### <a name="unable-to-retrieve-subscriptions"></a>サブスクリプションを取得できない

正常にサインインした後にサブスクリプションを取得できない場合:

- [Azure portal](https://portal.azure.com/) にサインインして、お使いのアカウントにサブスクリプションへのアクセス権があることを確認します
- 適切な環境 ([Azure](https://portal.azure.com/)、[Azure China](https://portal.azure.cn/)、[Azure Germany](https://portal.microsoftazure.de/)、[Azure US Government](https://portal.azure.us/)、またはカスタム環境/Azure Stack) を使用してサインインしていることを確認します
- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します
- アカウントを削除してから再度追加します
- ホーム ディレクトリ (たとえば、C:\Users\ContosoUser) から次のファイルを削除して、アカウントを再度追加します。
  - .adalcache
  - .devaccounts
  - .extaccounts
- サインイン時にエラー メッセージが表示される場合は、開発者ツール コンソール (F12 キー) を開きます

:::image type="content" source="./media/storage-explorer/console.png" alt-text="開発者ツール コンソールでエラーを確認する":::

#### <a name="unable-to-see-the-authentication-page"></a>認証ページが表示されない

認証ページが表示されない場合:

- 接続速度によっては、サインイン ページの読み込みに時間がかかる場合があります。認証ダイアログを閉じる前に少なくとも 1 分はお待ちください
- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します
- F12 キーを押して開発者コンソールを表示します。 開発者コンソールからの応答を確認して、認証が機能しない原因の手がかりを見つけられるかどうかを調べます

#### <a name="cannot-remove-account"></a>アカウントを削除できない

アカウントを削除できない場合、または再認証のリンクが役に立たない場合

- ホーム ディレクトリから次のファイルを削除して、アカウントを再度追加します。
  - .adalcache
  - .devaccounts
  - .extaccounts
- SAS にアタッチされたストレージ リソースを削除する場合、次を削除します。
  - %AppData%/StorageExplorer フォルダー (Windows の場合)
  - /Users/<your_name>/Library/Application SUpport/StorageExplorer (Mac の場合)
  - ~/.config/StorageExplorer (Linux の場合)
  - これらのファイルを削除すると、**すべての資格情報を再入力する必要があります**


### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS プロキシの問題

ASE で HTTP/HTTPS プロキシを構成する際に、左側のツリーで Azure Cosmos DB ノードを一覧表示できません。 現時点では、Azure Portal の Azure Cosmos DB データ エクスプローラーを回避策として使用できます。

### <a name="development-node-under-local-and-attached-node-issue"></a>"ローカルで接続済み" ノードの下の "開発" ノードに関する問題

左側のツリーにある "ローカルで接続済み" ノードの下の "開発" ノードを選択した後、応答がありません。  これは期待される動作です。 Azure Cosmos DB ローカル エミュレーターは、次のリリースでサポートされる予定です。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="開発ノード":::

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>"ローカルで接続済み" ノードの Azure Cosmos DB アカウントのアタッチに関するエラー

"ローカルで接続済み" ノードの Azure Cosmos DB アカウントをアタッチした後に以下のエラーが表示される場合、正しい接続文字列を使用していることをチェックします。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="ローカルで接続済みの Azure Cosmos DB のアタッチに関するエラー":::

### <a name="expand-azure-cosmos-db-node-error"></a>Azure Cosmos DB ノードの展開に関するエラー

左側のツリー ノードを展開しようとしたときに、以下のエラーが表示される場合があります。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Azure Cosmos DB ノードの展開に関するエラー":::

次の推奨事項を試してください。

- Azure Cosmos DB アカウントのプロビジョニングが進行中であるかどうかをチェックし、アカウントが正常に作成されている場合は再試行します。
- アカウントが "クイック アクセス" ノードまたは "ローカルで接続済み" ノードにある場合、アカウントが削除されていることをチェックします。 その場合、ノードを手動で削除する必要があります。

## <a name="next-steps"></a>次のステップ

* 次のビデオを見て、Azure Storage Explorer を使用して Azure Cosmos DB を利用する方法を確認します。[Use Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) (Azure Storage Explorer で Azure Cosmos DB を使用する)。
* Storage Explorer と他のサービスへの接続の詳細については、[Storage Explorer の概要](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)に関するページをご覧ください。
