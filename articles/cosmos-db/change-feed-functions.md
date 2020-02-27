---
title: Azure Cosmos DB の変更フィードを Azure Functions と共に使用する方法
description: Azure Functions を使用して、Azure Cosmos DB の変更フィードに接続します。 後で、すべての新しいイベントでトリガーされる対応型の Azure 関数を作成できます。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 3487de958df100cd43d4191028d0a15d7007067a
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605002"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB と Azure Functions を使用したサーバーレスなイベントベースのアーキテクチャ

Azure Functions には、[変更フィード](change-feed.md)に接続する最も簡単な方法が用意されています。 Azure Cosmos コンテナーの変更フィード内の新しい各イベントに基づいて自動的にトリガーされる小規模な対応型 Azure 関数を作成できます。

![Cosmos DB 用 Azure Functions トリガーと連携するサーバーレスなイベントベースの Functions](./media/change-feed-functions/functions.png)

[Cosmos DB 用 Azure Functions トリガー](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)を使用すると、[ワーカー インフラストラクチャ](./change-feed-processor.md)を維持することなく、[変更フィード プロセッサ](./change-feed-processor.md)のスケーリングと信頼性の高いイベント検出機能を活用できます。 イベントソーシング パイプラインの残りの部分を気にすることなく、自分の Azure 関数のロジックにのみ専念しましょう。 トリガーは、その他の [Azure Functions のバインド](../azure-functions/functions-triggers-bindings.md#supported-bindings)と組み合わせることもできます。

> [!NOTE]
> 現在、Cosmos DB 用 Azure Functions トリガーは、Core (SQL) API と共に使用する場合にのみサポートされます。

## <a name="requirements"></a>必要条件

サーバーレスなイベントベースのフローを実装するには、以下が必要です。

* **監視対象コンテナー**:監視対象コンテナーは監視されている Azure Cosmos コンテナーであり、そこには、変更フィードの生成元となるデータが含まれています。 監視対象コンテナーに対する挿入と更新が、コンテナーの変更フィードに反映されます。
* **リース コンテナー**:リース コンテナーは、複数の動的なサーバーレス Azure 関数インスタンス全体で状態を維持し、動的スケーリングを可能にします。 このリース コンテナーは、Cosmos DB 用 Azure Functions トリガーによって、手動または自動で作成できます。 リース コンテナーを自動的に作成するには、[構成](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)で *CreateLeaseCollectionIfNotExists* フラグを設定します。 パーティション分割されたリース コンテナーには、`/id` パーティション キー定義が必要です。

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Cosmos DB 用 Azure Functions トリガーを作成する

Cosmos DB 用 Azure Functions トリガーを使用した Azure 関数の作成は、すべての Azure Functions IDE および CLI 統合にわたってサポートされるようになりました。

* Visual Studio ユーザー用の [Visual Studio 拡張機能](../azure-functions/functions-develop-vs.md)。
* Visual Studio Code ユーザー用の [Visual Studio コア拡張機能](/azure/javascript/tutorial-vscode-serverless-node-01)。
* 最後に、クロスプラットフォーム IDE に非依存なエクスペリエンスのための [Core CLI ツール](../azure-functions/functions-run-local.md#create-func)。

## <a name="run-your-trigger-locally"></a>ローカルでトリガーを実行する

[Azure Cosmos DB Emulator](./local-emulator.md) を使用して自分の [Azure 関数をローカルで](../azure-functions/functions-develop-local.md)実行し、Azure サブスクリプションまたはコストの発生なしで、サーバーレスなイベントベースのフローを作成および開発できます。

クラウドでライブ シナリオをテストしたい場合は、[Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)ことができます。クレジット カードや Azure サブスクリプションは必要ありません。

## <a name="next-steps"></a>次のステップ

以下の記事では、変更フィードについて引き続き詳しく知ることができます。

* [変更フィードの概要](change-feed.md)
* [変更フィードを読み取る方法](read-change-feed.md)
* [Change Feed Processor ライブラリの使用](change-feed-processor.md)
* [変更フィード プロセッサ ライブラリの操作方法](change-feed-processor.md)
* [Azure Cosmos DB と Azure Functions を使用したサーバーレス データベース コンピューティング](serverless-computing-database.md)
