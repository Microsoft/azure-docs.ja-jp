---
title: クイック スタート:ナレッジ ベースを公開する、REST、Java - QnA Maker
titleSuffix: Azure Cognitive Services
description: この Java REST ベースのクイック スタートでは、ナレッジ ベースを公開する手順を紹介しています。公開時に、テスト済みのナレッジ ベースの最新バージョンが、公開済みのナレッジ ベースを表す専用の Azure Search インデックスにプッシュされます。 また、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: diberry
ms.openlocfilehash: 4ee622c944c5ccd39331ab395eca7b6ff9692b35
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71836080"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>クイック スタート: Java を使用して QnA Maker のナレッジ ベースを公開する

この REST ベースのクイック スタートでは、ナレッジ ベース (KB) をプログラムから公開する手順を紹介しています。 公開すると、ナレッジ ベースの最新バージョンが、専用の Azure Search インデックスにプッシュされ、アプリケーションやチャット ボットで呼び出すことができるエンドポイントが作成されます。

このクイック スタートで呼び出す QnA Maker API は次のとおりです。
* [公開](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) - この API は、要求の本文に情報を必要としません。

## <a name="prerequisites"></a>前提条件

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit Standard Edition)
* このサンプルでは、HTTP Components の Apache [HTTP クライアント](https://hc.apache.org/httpcomponents-client-ga/)を使用しています。 以下の Apache HTTP クライアント ライブラリをプロジェクトに追加する必要があります。 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)が必要です。 キーと (リソース名を含む) エンドポイントを取得するには、Azure portal で対象のリソースの **[クイックスタート]** を選択します。
* URL の kbid クエリ文字列パラメーターに含まれている QnA Maker ナレッジ ベース (KB) ID (下図)。

    ![QnA Maker ナレッジ ベース ID](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    まだナレッジ ベースがない場合は、このクイック スタート用のサンプルを作成できます。[新しいナレッジ ベースの作成](create-new-kb-csharp.md)に関するページを参照してください。

> [!NOTE] 
> 完全なソリューション ファイルは、[**Azure-Samples/cognitive-services-qnamaker-java** GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base)から入手できます。

## <a name="create-a-java-file"></a>Java ファイルを作成する

VSCode を開き、`PublishKB.java` という名前の新しいファイルを作成します。

## <a name="add-the-required-dependencies"></a>必要な依存関係を追加する

`PublishKB.java` の先頭のクラスの上に次の行を追加して、プロジェクトに必要な依存関係を追加します。

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>main メソッドを持つ PublishKB クラスを作成する

依存関係の後に、次のクラスを追加します。

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>必要な定数を追加する

**main** メソッド内に、QnA Maker にアクセスするために必要な定数を追加します。 サンプルの値は実際の値に変更してください。

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>ナレッジ ベースを公開するための POST 要求を追加する

必要な定数の後に、次のコードを追加します。このコードは、ナレッジ ベースを公開するための HTTPS 要求を QnA Maker API に送信し、その応答を受信します。

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

正常に公開されると、この API 呼び出しから状態 204 が返されます。応答の本文にはコンテンツが含まれません。 このコードでは、204 応答に対してコンテンツを追加しています。

それ以外の応答の場合は、その応答がそのまま返されます。

## <a name="build-and-run-the-program"></a>プログラムをビルドして実行する

コマンド ラインでプログラムをビルドし、実行します。 QnA Maker API に要求が自動的に送信され、コンソール ウィンドウへの出力が行われます。

1. ファイルをビルドします。

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. ファイルを実行します。

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>次の手順

ナレッジ ベースが公開された後、[回答を生成するためのエンドポイント URL](../Tutorials/create-publish-answer.md#generating-an-answer) が必要になります。  

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)
