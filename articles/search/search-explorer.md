---
title: Azure portal の Search エクスプローラー クエリ ツール
titleSuffix: Azure Cognitive Search
description: この Azure portal クイックスタートでは、Search エクスプローラーを使用して、クエリ構文を学習したり、クエリ式をテストしたり、検索ドキュメントを検査したりします。 Search エクスプローラーは、Azure Cognitive Search のインデックスにクエリを実行します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 06/07/2020
ms.openlocfilehash: 8f004ff8118829ebf2d245a136cf22cdde23accc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929790"
---
# <a name="quickstart-use-search-explorer-to-run-queries-in-the-portal"></a>クイック スタート:Search エクスプローラーを使用してポータルでクエリを実行する

**Search エクスプローラー**は、Azure Cognitive Search の検索インデックスに対してクエリを実行するために使用される組み込みのクエリ ツールです。 このツールを使用すると、クエリ構文の学習、クエリまたはフィルター式のテスト、あるいはインデックス内の新しいコンテンツの有無を確認することによるデータ更新の確認が簡単になります。

このクイックスタートでは、既存のインデックスを使用して Search エクスプローラーをデモンストレーションします。 要求は [Search REST API](/rest/api/searchservice/) を使用して作成され、応答は JSON ドキュメントとして返されます。

## <a name="prerequisites"></a>前提条件

開始する前に、次の項目を用意する必要があります。

+ アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。

+ Azure Cognitive Search サービス。 [サービスを作成する](search-create-service-portal.md)か、現在のサブスクリプションで[既存のサービスを探してください](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。 

+ このクイックスタートでは、*realestate-us-sample-index* を使用します。 [ **[データのインポート]** ](search-import-data-portal.md) ウィザードを使用して、このインデックスを作成してください。 最初の手順でデータ ソースの入力を求められたら、 **[サンプル]** 、**realestate-us-sample** データ ソースの順に選択します。 インデックスの作成にあたっては、ウィザードの既定値をすべてそのまま使用してください。

## <a name="start-search-explorer"></a>Search エクスプローラーの起動

1. [Azure portal](https://portal.azure.com) で、ダッシュボードから検索サービス ページを開くか、または[自分のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。

1. コマンド バーから Search エクスプローラーを開きます。

   ![ポータルでの Search エクスプローラーのコマンド](./media/search-explorer/search-explorer-cmd2.png "ポータルでの Search エクスプローラーのコマンド")

    または、開かれたインデックスで組み込みの **[Search エクスプローラー]** タブを使用します。

   ![[Search エクスプローラー] タブ](./media/search-explorer/search-explorer-tab.png "[Search エクスプローラー] タブ")

## <a name="unspecified-query"></a>指定されていないクエリ

最初に内容を見るために、用語を指定せずに **[検索]** をクリックすることによって空の検索を実行します。 空の検索は最初のクエリとして役に立ちます。ドキュメント全体が返されるので、ドキュメントの構成を確認できるからです。 空の検索では検索順位がなく、ドキュメントが任意の順序で返されます (すべてのドキュメントで `"@search.score": 1`)。 既定では、50 個のドキュメントが検索要求で返されます。

空の検索に相当する構文は `*` または `search=*` です。
   
   ```http
   search=*
   ```

   **結果**
   
   ![空のクエリの例](./media/search-explorer/search-explorer-example-empty.png "非修飾または空のクエリの例")

## <a name="free-text-search"></a>フリー テキスト検索

自由形式のクエリは、演算子の有無に関係なく、カスタム アプリから Azure Cognitive Search に送信されるユーザー定義のクエリをシミュレートするのに便利です。 一致をスキャンされるのは、インデックス定義で**検索可能**の属性が付けられたフィールドだけです。 

検索語やクエリ式などの検索条件を指定すると、検索順位が機能するようになることに注意してください。 次にフリー テキスト検索の例を示します。

   ```http
   Seattle apartment "Lake Washington" miele OR thermador appliance
   ```

   **結果**

   CTRL + F キーを使用して、関心のある特定の語句を結果内で検索できます。

   ![フリー テキストのクエリの例](./media/search-explorer/search-explorer-example-freetext.png "フリー テキストのクエリの例")

## <a name="count-of-matching-documents"></a>一致するドキュメントのカウント 

インデックスで見つかった一致の数を取得するには、 **$count=true** を追加します。 空の検索では、カウントはインデックス内のドキュメントの合計数です。 修飾された検索では、これはクエリ入力と一致するドキュメントの数です。

   ```http
   $count=true
   ```

   **結果**

   ![ドキュメントの数の例](./media/search-explorer/search-explorer-example-count.png "インデックス内の一致するドキュメントの数")

## <a name="limit-fields-in-search-results"></a>検索結果内のフィールドを制限する

**Search エクスプローラー**の出力を読みやすくするために明示的に指定されたフィールドに結果を制限するには、[ **$select**](search-query-odata-select.md) を追加します。 検索文字列と **$count=true** を維持するには、引数の前に **&** を付けます。 

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true
   ```

   **結果**

   ![フィールドの制限の例](./media/search-explorer/search-explorer-example-selectfield.png "検索結果のフィールドの制限")

## <a name="return-next-batch-of-results"></a>結果の次のバッチを返す

Azure Cognitive Search は、検索順位に基づいた上位 50 の一致を返します。 一致するドキュメントの次のセットを取得するには、「 **$top=100,&$skip=50**」を追加して、結果セットを 100 個のドキュメントに増やし (既定値は 50、最大は 1,000)、最初の 50 個のドキュメントをスキップします。 順位付けされた結果を取得するには、クエリ語句や式など、検索条件を指定する必要があることを思い出してください。 取得する検索結果が低位になるほど検索スコアが減少することに注目してください。

   ```http
   search=seattle condo&$select=listingId,beds,baths,description,street,city,price&$count=true&$top=100&$skip=50
   ```

   **結果**

   ![バッチ検索の結果](./media/search-explorer/search-explorer-example-topskip.png "結果の次のバッチを返す")

## <a name="filter-expressions-greater-than-less-than-equal-to"></a>フィルター式 (より大きい、より小さい、等しい)

正確な条件を指定するには、フリー テキスト検索ではなく [ **$filter**](search-query-odata-filter.md) パラメーターを使用します。 このフィールドには、インデックスで**フィルター可能**の属性が付けられている必要があります。 この例では、3 より大きいベッドルームを検索します。

   ```http
   search=seattle condo&$filter=beds gt 3&$count=true
   ```
   
   **結果**

   ![フィルター式](./media/search-explorer/search-explorer-example-filter.png "条件によるフィルター")

## <a name="order-by-expressions"></a>orderby 式

検索スコアに加えて別のフィールドで結果を並べ替えるには、[ **$orderby**](search-query-odata-orderby.md) を追加します。 このフィールドには、インデックスで**並べ替え可能**の属性が付けられている必要があります。 これをテストするために使用できる式の例:

   ```http
   search=seattle condo&$select=listingId,beds,price&$filter=beds gt 3&$count=true&$orderby=price asc
   ```
   
   **結果**

   ![Orderby 式](./media/search-explorer/search-explorer-example-ordery.png "並べ替え順の変更")

**$filter** 式と **$orderby** 式はどちらも OData 構文です。 詳細については、[フィルターの OData 構文](/rest/api/searchservice/odata-expression-syntax-for-azure-search)に関するページを参照してください。

<a name="start-search-explorer"></a>

## <a name="takeaways"></a>重要なポイント

このクイックスタートでは、**Search エクスプローラー**を使用して、REST API でインデックスにクエリを実行しました。

+ 結果は詳細な JSON ドキュメントとして返されます。そのため、ドキュメントの構成と内容を完全に確認できます。 例で示されているように、クエリ式を使用して、返されるフィールドを制限できます。

+ ドキュメントは、インデックスで**取得可能**としてマークされているすべてのフィールドで構成されます。 ポータルでインデックスの属性を確認するには、検索の概要ページの **[インデックス]** 一覧で *realestate-us-sample* をクリックします。

+ 商用 Web ブラウザーで入力することがあるような自由形式のクエリは、エンドユーザーのエクスペリエンスをテストするのに便利です。 たとえば、組み込みの不動産サンプル インデックスがあるとしたら、「Seattle apartments lake washington」と入力できます。そして、CTRL + F キーを使用して検索結果内で語句を見つけることができます。 

+ クエリ式とフィルター式は、Azure Cognitive Search でサポートされている構文で表されます。 既定値は[単純な構文](/rest/api/searchservice/simple-query-syntax-in-azure-search)です。しかし、必要に応じて[完全な Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) を使用し、より強力なクエリを実行できます。 [フィルター式](/rest/api/searchservice/odata-expression-syntax-for-azure-search)は OData 構文です。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。 

## <a name="next-steps"></a>次のステップ

クエリ構造やクエリ構文について学習するには、Postman または同等のツールを使用して、API のさらに多くの部分を利用するクエリ式を作成してください。 [Search REST API](/rest/api/searchservice/) は、学習や調査に特に役立ちます。

> [!div class="nextstepaction"]
> [Postman で基本的なクエリを作成する](search-query-simple-examples.md)