---
title: クイック スタート:ナレッジ ベースから回答を取得する - REST (Node.js) - QnA Maker
description: この Node.js REST ベースのクイック スタートでは、ナレッジ ベースからプログラムによって回答を取得する手順を紹介しています。
ms.topic: quickstart
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCHANGE-20200128
ms.openlocfilehash: e8077235852e8776c4e52403cbac3e4bccc6c4f1
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109780"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-nodejs"></a>クイック スタート:Node.js を使用してナレッジ ベースから質問の回答を取得する

このクイック スタートでは、発行済みの QnA Maker ナレッジ ベースからプログラムによって回答を取得する手順を紹介しています。 ナレッジ ベースには、FAQ などの[データ ソース](../Concepts/knowledge-base.md)からの質問と回答が含まれます。 [質問](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration)は QnA Maker サービスに送信されます。 [応答](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties)には、予測上位の回答が含まれます。

[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime) | [サンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-nodejs/blob/master/documentation-samples/quickstarts/get-answer/get-answer.js)

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーを取得するには、QnA Maker リソースの Azure ダッシュボードで、 **[リソース管理]** の **[キー]** を選択します。
* **[発行]** ページの設定。 発行済みのナレッジ ベースがない場合は、空のナレッジ ベースを作成し、 **[設定]** ページでナレッジ ベースをインポートして発行してください。 [こちらの基本的なナレッジ ベース](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv)をダウンロードしてご使用ください。

    発行ページの設定には、POST ルート値、Host 値、EndpointKey 値が含まれています。

    ![Publish settings](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-nodejs-file"></a>Node.js ファイルを作成する

VSCode を開き、`get-answer.js` という名前の新しいファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

プロジェクトに必要な依存関係を `get-answer.js` ファイルの一番上に追加します。

[!code-nodejs[Add the required dependencies](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=1-4 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>必要な定数を追加する

次に、QnA Maker にアクセスするために必要な定数を追加します。 これらの値は、ナレッジ ベースを発行した後、 **[発行]** ページにあります。

[!code-nodejs[Add the required constants](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=6-22 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-an-answer"></a>質問を送信して回答を取得するための POST 要求を追加する

次のコードは、ナレッジ ベースに質問を送信するための HTTPS 要求を QnA Maker API に送信し、その応答を受信するものです。

[!code-nodejs[Add a POST request to send question to knowledge base](~/samples-qnamaker-nodejs/documentation-samples/quickstarts/get-answer/get-answer.js?range=24-49 "Add a POST request to send question to knowledge base")]

`Authorization` ヘッダーの値には、文字列 `EndpointKey` が含まれています。

## <a name="install-the-dependencies"></a>依存関係をインストールする

コマンド ラインから依存関係をインストールします。

```bash
npm install request request-promise
```

## <a name="run-the-program"></a>プログラムの実行

コマンド ラインからプログラムを実行します。 QnA Maker API に要求が自動的に送信され、コンソール ウィンドウへの出力が行われます。

ファイルを実行します。

```bash
node get-answer.js
```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]

[要求](../how-to/metadata-generateanswer-usage.md#generateanswer-request)と[応答](../how-to/metadata-generateanswer-usage.md#generateanswer-response)の詳細を確認してください。

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
