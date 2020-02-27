---
title: クイック スタート:QnA Maker ポータルで質問と回答を追加する
description: このクイックスタートでは、ユーザーが自分の質問に対する適切な回答を見つけられるように、質問と回答のセットをメタデータと共に追加する方法について説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/08/2020
ms.author: diberry
ms.openlocfilehash: 25c0fe549dfc850a53b06f79f348a87cba3b70a1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109931"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>クイック スタート:QnA Maker ポータルで質問と回答を追加する

ナレッジ ベースを作成したら、質問と回答 (QnA) のセットを、回答をフィルター処理するためのメタデータと共に追加します。 次の表には Azure サービスの制限についての質問が記載されていますが、関係する Azure サービスはそれぞれ異なります。

<a name="qna-table"></a>

|オン|疑問がある場合|Answer|Metadata|
|--|--|--|--|
|1 番|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|2 番|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

QnA セットにメタデータが追加されると、クライアント アプリケーションでは次のことを実行できます。

* 特定のメタデータとのみ一致する回答を要求します。
* すべての回答を受け取ったうえで、それぞれのメタデータに応じた後処理を回答に対して行います。


## <a name="prerequisites"></a>前提条件

* [前のクイックスタートを完了しておく](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>QnA Maker ポータルにサインインする

1. [QnA Maker](https://www.qnamaker.ai) ポータルにサインインします。

1. [前のクイックスタート](../how-to/create-knowledge-base.md)から既存のナレッジ ベースを選択します。

## <a name="add-additional-alternatively-phrased-questions"></a>言い回しを変えて質問を追加する

最新のナレッジ ベースには、QnA Maker のトラブルシューティングに関する QnA セットが含まれています。 これらのセットは、作成プロセスでナレッジ ベースに URL を追加したときに作成されました。

この URL をインポートした時点では、1 つの回答について質問が 1 つしか作成されていませんでした。 この手順では、別の質問を追加します。

1. **[編集]** ページで、質問と回答のセットの上にある検索ボックスを使用し、`How large a knowledge base can I create?` という質問を探します。

1. **[質問]** 列で、 **[+ Add alternative phrasing]\(+ 代わりの言い回しの追加\)** を選択し、次の表に示した新しい言い回しをそれぞれ追加します。

    |代わりの言い回し|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. **[保存してトレーニング]** を選択してナレッジ ベースを再トレーニングします。

1. **[テスト]** を選択し、新しく追加した、代わりの言い回しのいずれかに近い質問を入力します。ただし、まったく同じ言い回しは使用しません。

    `What GB size can a knowledge base be?`

    次のようにマークダウン形式で正しい回答が返されます:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    返された回答の下にある **[検査]** を選択すると、異なる信頼度で質問を満たした回答が他にも表示されます。

    代わりの言い回しとして考えられる組み合わせをすべて追加しようとしないでください。 QnA Maker の[アクティブ ラーニング](../how-to/improve-knowledge-base.md)を有効にすれば、ナレッジ ベースでユーザーのニーズを最もよく満たす代わりの言い回しが検出されます。

1. もう一度 **[テスト]** を選択して、テスト ウィンドウを閉じます。

## <a name="add-metadata-to-filter-the-answers"></a>回答をフィルター処理するためのメタデータを追加する

質問と回答のセットにメタデータを追加すると、フィルター処理済みの回答をクライアント アプリケーションが要求できるようになります。 このフィルターは、[1 つ目と 2 つ目のランカー](../concepts/query-knowledge-base.md#ranker-process)が適用される前に適用されます。

1. [このクイックスタートの最初の表](#qna-table)にある 2つ目の質問と回答のセットをメタデータなしで追加したうえで、次の手順を続行します。

1. **[オプションの表示]** を選択し、 **[メタデータの表示]** を選択します。

1. 追加した QnA セットについて、 **[Add metadata tags]\(メタデータ タグの追加\)** を選択し、`service` という名前と `search` という値を追加します。 `service:search` のようになります。

1. `link_in_answer` という名前と `false` という値で別のメタデータ タグを追加します。 `link_in_answer:false` のようになります。

1. 表から 1 つ目の回答を検索します (`How large a knowledge base can I create?`)。

1. 同じ 2 つのメタデータ タグに対し、メタデータのペアを追加します。

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    同じメタデータ タグで値が異なる 2 つの質問が完成しました。

1. **[保存してトレーニング]** を選択してナレッジ ベースを再トレーニングします。

1. トップ メニューの **[公開]** をクリックして公開ページに移動します。
1. **[公開]** ボタンを選択して、エンドポイントに最新のナレッジ ベースを公開します。
1. ナレッジ ベースが公開されたら、次のクイックスタートに進んで、ナレッジ ベースから回答を生成する方法を見てみましょう。

## <a name="what-did-you-accomplish"></a>ここで行ったこと

ここでは、より多くの質問をサポートするようにナレッジ ベースを編集しました。また、回答が返された後、上位の回答を検索したり後処理を行ったりする際にフィルタリングが利用できるよう、名前と値のペアを指定しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のクイックスタートに進まない場合は、Azure portal で QnA Maker と Bot フレームワークのリソースを削除します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Postman または cURL を使用して回答を取得する](get-answer-from-knowledge-base-using-url-tool.md)