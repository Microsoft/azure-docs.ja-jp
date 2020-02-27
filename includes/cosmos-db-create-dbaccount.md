---
title: インクルード ファイル
description: インクルード ファイル
services: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.custom: include file
ms.openlocfilehash: 8f8b8384b0c570e2b4925b0e84480b19a632187e
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75752263"
---
1. [Azure portal](https://portal.azure.com/) に移動して、Azure Cosmos DB アカウントを作成します。 **[Azure Cosmos DB]** を検索して、選択します。

   ![Azure Portal の [データベース] ウィンドウ](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. **[追加]** を選択します。
1. **[Azure Cosmos DB アカウントの作成]** ページで、新しい Azure Cosmos アカウントの基本的な設定を入力します。 

    |設定|値|説明 |
    |---|---|---|
    |サブスクリプション|サブスクリプション名|この Azure Cosmos アカウントに使用する Azure サブスクリプションを選択します。 |
    |リソース グループ|リソース グループ名|リソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの一意の名前を入力します。 |
    |アカウント名|一意の名前|自分の Azure Cosmos アカウントを識別するための名前を入力します。 指定した名前に *documents.azure.com* が付加されて URI が作成されるので、一意の名前を使用してください。<br><br>名前に含めることができるのは、英小文字、数字、ハイフン (-) のみです。 長さは 3 文字から 31 文字でなければなりません。|
    |API|作成するアカウントの種類。|ドキュメント データベースを作成し、SQL 構文を使用してクエリを実行するには、 **[コア (SQL)]** を選択します。 <br><br>API によって、作成するアカウントの種類が決まります。 Azure Cosmos DB には、5 種類の API が用意されています。ドキュメント データ用のコア (SQL) と MongoDB、グラフ データ用の Gremlin、Azure Table、Cassandra です。 現在は、API ごとに別のアカウントを作成する必要があります。 <br><br>[SQL API について詳しくは、こちらをご覧ください](../articles/cosmos-db/documentdb-introduction.md)。|
    |Location|ユーザーに最も近いリージョン|Azure Cosmos DB アカウントをホストする地理的な場所を選択します。 データに最も高速にアクセスできるよう、お客様のユーザーに最も近い場所を使用します。|

   ![Azure Cosmos DB の新しいアカウント ページ](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[ネットワーク]** セクションと **[タグ]** セクションはスキップできます。

1. アカウントの設定を確認し、 **[作成]** を選択します。 アカウントの作成には数分かかります。 ポータル ページに "**デプロイが完了しました**" と表示されるまで待ちます。 

    ![Azure Portal の [通知] ウィンドウ](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. **[リソースに移動]** を選択し、Azure Cosmos DB アカウント ページに移動します。 

    ![Azure Cosmos DB アカウント ページ](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
