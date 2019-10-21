---
title: クイック スタート:.NET 用 QnA Maker クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: .NET 用 QnA Maker クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 31bd85ca9b106758dbb7bfd399b7a493ea7fea9f
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803088"
---
# <a name="quickstart-qna-maker-client-library-for-net"></a>クイック スタート:.NET 用 QnA Maker クライアント ライブラリ

.NET 用 QnA Maker クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。  QnA Maker を使用すると、FAQ ドキュメント、URL、製品マニュアルなどの半構造化コンテンツからの質問と回答サービスをいっそう強力にできます。 

.NET 用 QnA Maker クライアント ライブラリは、次の目的で使用することができます。

* ナレッジ ベースの作成 
* ナレッジ ベースの管理
* ナレッジ ベースの公開
* ナレッジ ベースから回答を生成する

[リファレンス ドキュメント](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker?view=azure-dotnet) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Knowledge.QnAMaker) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker/) | [C# サンプル](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp)

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="setting-up"></a>設定

### <a name="create-a-qna-maker-azure-resource"></a>QnA Maker Azure リソースを作成する

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に QnA Maker のリソースを作成します。 

対象のリソースのキーとエンドポイントを取得した後、`QNAMAKER_SUBSCRIPTION_KEY` という名前で、そのキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。 リソース名は、エンドポイント URL の一部として使用されます。

### <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。 

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、dotnet `new` コマンドを使用し、`qna-maker-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (`Program.cs`) を使用する単純な "Hello World" C# プロジェクトが作成されます。 

```console
dotnet new console -n qna-maker-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-sdk"></a>SDK のインストール

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 QnA Maker クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Knowledge.QnAMaker --version 1.0.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。


## <a name="object-model"></a>オブジェクト モデル

QnA Maker クライアントは、自分のキーが含まれている Microsoft.Rest.ServiceClientCredentials を使用して Azure に対する認証を行う [QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) オブジェクトです。

クライアントが作成されたら、[ナレッジ ベース](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase) プロパティを使用して、ナレッジ ベースを作成、管理、および公開します。 

JSON オブジェクトを送信して、ナレッジ ベースを管理します。 即時操作の場合は、通常、状態を示す JSON オブジェクトがメソッドから返されます。 実行時間の長い操作の場合、応答は操作 ID になります。 [client.Operations.GetDetailsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.operationsextensions.getdetailsasync?view=azure-dotnet) メソッドを操作 ID と共に呼び出して、[要求の状態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operationstatetype?view=azure-dotnet)を確認します。 

 
## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 QnA Maker クライアント ライブラリを使用して次のことを実行する方法を示します。

* [ナレッジ ベースの作成](#create-a-knowledge-base)
* [ナレッジ ベースの更新](#update-a-knowledge-base)
* [ナレッジ ベースのダウンロード](#download-a-knowledge-base)
* [ナレッジ ベースの公開](#publish-a-knowledge-base)
* [ナレッジ ベースの削除](#delete-a-knowledge-base)
* [操作の状態の取得](#get-status-of-an-operation)
* [ナレッジ ベースから回答を生成する](#generate-an-answer-from-the-knowledge-base)

## <a name="add-the-dependencies"></a>依存関係を追加する

プロジェクト ディレクトリから、好みのエディターまたは IDE で **Program.cs** ファイルを開きます。 既存の `using` コードを次の `using` ディレクティブに置き換えます。

[!code-csharp[Using statements](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=Dependencies)]

## <a name="authenticate-the-client-for-authoring-the-knowledge-base"></a>ナレッジ ベースを作成するためのクライアントを認証する

**main** メソッド内で、`QNAMAKER_SUBSCRIPTION_KEY` という名前の環境変数から取得したリソースの Azure キーの変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、再読み込みしなければならない場合があります。 メソッドは後で作成します。

次に、自分のキーを使用して [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.apikeyserviceclientcredentials?view=azure-dotnet) オブジェクトを作成し、それを自分のエンドポイントと共に使用して、[QnAMakerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient?view=azure-dotnet) オブジェクトを作成します。

**Endpoint** の変数 `<your-custom-domain>` を、対象のカスタム ドメインの名前に変更します。 この場所は、Azure portal の QnA Maker リソースの **[概要]** ページで確認できます。

```csharp
var subscriptionKey = Environment.GetEnvironmentVariable("QNAMAKER_SUBSCRIPTION_KEY");
var client = new QnAMakerClient(new ApiKeyServiceClientCredentials(subscriptionKey)) { Endpoint = "https://<your-custom-domain>.api.cognitive.microsoft.com" };
```

## <a name="authenticate-the-runtime-for-generating-an-answer"></a>回答を生成するためのランタイムを認証する

**main** メソッドに、`QNAMAKER_ENDPOINT_HOSTNAME` および `QNAMAKER_ENDPOINT_KEY` という名前の環境変数から取得される、リソースの認証の変数を作成します。 ナレッジ ベースを公開すると、これらの値が返されます。 公開後は、QnA Maker ポータルの **[設定]** ページでこれらの設定を確認できます。 

ナレッジ ベースにクエリを実行して回答を生成したり、アクティブ ラーニングからトレーニングしたりするための [QnAMakerRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerruntimeclient?view=azure-dotnet) を作成します。

[!code-csharp[Authenticate the runtime](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=EndpointKey)]

## <a name="create-a-knowledge-base"></a>ナレッジ ベースの作成

ナレッジ ベースには、次の 3 つのソースの [CreateKbDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.createkbdto?view=azure-dotnet) オブジェクトに対する質問と回答のペアが格納されます。

* **本文**の場合は、[QnADTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadto?view=azure-dotnet) オブジェクトを使用します。
* **ファイル**の場合は、[FileDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.filedto?view=azure-dotnet) オブジェクトを使用します。 
* **URL** の場合は、文字列のリストを使用します。

[CreateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.createasync?view=azure-dotnet) メソッドを呼び出した後、返された操作 ID を [MonitorOperation](#get-status-of-an-operation) メソッドに渡して、状態をポーリングします。 

次のコードの最後の行では、MonitorOoperation の応答からナレッジ ベース ID を返しています。 

[!code-csharp[Create a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=CreateKB&highlight=29,30)]

ナレッジ ベースを適切に作成するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。 

## <a name="update-a-knowledge-base"></a>ナレッジ ベースの更新

ナレッジ ベースを更新するには、ナレッジ ベース ID と、[add](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoadd?view=azure-dotnet)、[update](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtoupdate?view=azure-dotnet)、および [delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdtodelete?view=azure-dotnet) DTO オブジェクトを含む [UpdatekbOperationDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.updatekboperationdto?view=azure-dotnet) を、[UpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.updateasync?view=azure-dotnet) メソッドに渡します。 更新が成功したかどうかを確認するには、[MonitorOperation](#get-status-of-an-operation) メソッドを使用します。

[!code-csharp[Update a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=UpdateKB&highlight=4,13)]

ナレッジ ベースを適切に更新するには、上記のコードで参照されている [`MonitorOperation`](#get-status-of-an-operation) 関数を必ず含めます。 

## <a name="download-a-knowledge-base"></a>ナレッジ ベースのダウンロード

データベースを [QnADocumentsDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.qnadocumentsdto?view=azure-dotnet) のリストとしてダウンロードするには、[DownloadAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.downloadasync?view=azure-dotnet) メソッドを使用します。 このメソッドの結果は TSV ファイルではないため、これは、QnA Maker ポータルの **[設定]** ページからのエクスポートと同等 "_ではありません_"。

[!code-csharp[Download a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DownloadKB&highlight=2)]

## <a name="publish-a-knowledge-base"></a>ナレッジ ベースの公開

[PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.publishasync?view=azure-dotnet) メソッドを使用して、ナレッジ ベースを公開します。 これにより、ナレッジ ベース ID によって参照される、最新の保存済みおよびトレーニング済みのモデルが取得され、エンドポイントで公開されます。 

[!code-csharp[Publish a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=PublishKB&highlight=2)]

## <a name="generate-an-answer-from-the-knowledge-base"></a>ナレッジ ベースから回答を生成する

[RuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.qnamakerclient.knowledgebase?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_QnAMakerClient_Knowledgebase).[GenerateAnswerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtimeextensions.generateanswerasync?view=azure-dotnet) メソッドを使用して公開済みのナレッジ ベースから回答を生成します。 このメソッドは、ナレッジ ベース ID と [QueryDTO](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto?view=azure-dotnet) を受け取ります。 さらに、[Top](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.top?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_QueryDTO_Top) や [Context](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.querydto.context?view=azure-dotnet) など、QueryDTO のプロパティにアクセスしてチャット ボットで使用することができます。 

[!code-csharp[Generate an answer from a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=GenerateAnswer&highlight=2)]


## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

[DeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebaseextensions.deleteasync?view=azure-dotnet) メソッドをナレッジ ベース ID のパラメーターと共に使用して、ナレッジ ベースを削除します。 

[!code-csharp[Delete a knowledge base](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=DeleteKB&highlight=2)]

## <a name="get-status-of-an-operation"></a>操作の状態の取得

create や update などのメソッドの中には、プロセスが終了するのを待つ代わりに、[操作](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation?view=azure-dotnet)が返されるのに十分な時間がかかるものがあります。 操作からの[操作 ID](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.models.operation.operationid?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Knowledge_QnAMaker_Models_Operation_OperationId) を使用して、(再試行ロジックを使用して) ポーリングし、元のメソッドの状態を判別します。 

次のコード ブロックの _loop_ および _Task.Delay_ は、再試行ロジックをシミュレートするために使用されています。 これらは自分の再試行ロジックに置き換える必要があります。 

[!code-csharp[Monitor an operation](~/samples-qnamaker-csharp/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs?name=MonitorOperation&highlight=10)]

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから、dotnet `run` コマンドを使用してアプリケーションを実行します。

この記事に記載されているすべてのコード スニペットは[入手できます](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/blob/master/documentation-samples/quickstarts/knowledgebase_quickstart/knowledgebase_quickstart.py)。また、1 つのファイルとして実行できます。

```console
dotnet run
```

[このクイックスタートのソース コード](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/blob/master/documentation-samples/quickstarts/Knowledgebase_Quickstart/Program.cs)は、QnA Maker C# のサンプル GitHub リポジトリから入手できます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[チュートリアル:KB を作成して回答する](../tutorials/create-publish-query-in-portal.md)

* [QnA Maker API とは](../Overview/overview.md)
* [ナレッジ ベースを編集する](../how-to/edit-knowledge-base.md)
* [利用状況分析を取得する](../how-to/get-analytics-knowledge-base.md)