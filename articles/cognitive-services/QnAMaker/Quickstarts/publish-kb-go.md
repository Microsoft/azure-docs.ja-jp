---
title: クイック スタート:ナレッジ ベースを公開する - REST、Go - QnA Maker
description: この Go REST ベースのクイックスタートでは、自分のナレッジ ベースを公開し、アプリケーションまたはチャット ボット内で呼び出すことができるエンドポイントを作成します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: d876be45789041cddba269784a9c1e62e5907bc2
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352254"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-go"></a>クイック スタート:Go を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Cognitive Search インデックスにプッシュされ、アプリケーションやチャット ボット内で呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [Go 1.10.1](https://golang.org/dl/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。

* URL の `kbid` クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースを作成します](create-new-kb-csharp.md)。

> [!NOTE]
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-go** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-a-go-file"></a>Go ファイルを作成する

VSCode を開き、`publish-kb.go` という名前の新しいファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`publish-kb.go` の先頭に次の行を追加して、プロジェクトに必要な依存関係を追加します。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="dependencies":::

## <a name="create-the-main-function"></a>main 関数を作成する

必要な依存関係の後に、次のクラスを追加します。

```Go
package main

func main() {

}
```

## <a name="add-post-request-to-publish-kb"></a>KB を公開するための POST 要求を追加する

次のコードを追加します。これは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

:::code language="go" source="~/cognitive-services-quickstart-code/go/QnAMaker/rest/publish-kb.go" id="main":::

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このコードでは、204 応答に対してコンテンツを追加しています。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

以下のコマンドを入力してファイルをコンパイルします。 このコマンド プロンプトから、ビルドの成功に関する情報は一切返されません。

```bash
go build publish-kb.go
```

コマンド ラインで次のコマンドを入力して、プログラムを実行します。 これによって、KB を公開するための要求が QnA Maker API に送信され、成功または失敗のための 204 が出力されます。

```bash
./publish-kb
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](./get-answer-from-knowledge-base-go.md) が必要になります。

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)