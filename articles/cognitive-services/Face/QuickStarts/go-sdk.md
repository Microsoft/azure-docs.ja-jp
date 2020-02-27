---
title: クイック スタート:Go 用 Face クライアント ライブラリ | Microsoft Docs
description: Go 用 Face クライアント ライブラリを使ってみます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: ''
ms.topic: quickstart
ms.date: 01/27/2020
ms.author: pafarley
ms.openlocfilehash: 2db40150167a8f16242b2feb15b77820fa1970a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76961229"
---
# <a name="quickstart-face-client-library-for-go"></a>クイック スタート:Go 用 Face クライアント ライブラリ

Go 用 Face クライアント ライブラリを使ってみます。 以下の手順に従って、ライブラリをインストールし、基本タスクの例を試してみましょう。 Face サービスは、画像内の人間の顔を検出および認識するための高度なアルゴリズムへのアクセスを提供します。

Go 用 Face サービス クライアント ライブラリは、次の目的で使用します。

* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [データ移行のためのスナップショットを作成する](#take-a-snapshot-for-data-migration)

[リファレンス ドキュメント](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face) | [SDK のダウンロード](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Go](https://golang.org/dl/)

## <a name="set-up"></a>設定

### <a name="create-a-face-azure-resource"></a>Face Azure リソースを作成する 

Azure リソースを作成して、Face サービスの使用を開始します。 適切なリソースの種類を選択してください。

* [試用版リソース](https://azure.microsoft.com/try/cognitive-services/#decision) (Azure サブスクリプションは不要): 
    * 7 日間有効です (無料)。 試用版のキーとエンドポイントは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。 
    * Face サービスを試してみたいものの Azure サブスクリプションをお持ちでない方に最適な方法です。
* [Face サービス リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace):
    * ご自身でリソースを削除するまでは Azure portal からご利用いただけます。
    * Free 価格レベルを使ってサービスを試用し、後から運用環境用の有料レベルにアップグレードします。
* [マルチサービス リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne):
    * ご自身でリソースを削除するまでは Azure portal からご利用いただけます。  
    * 複数の Cognitive Services 全体で同じキーとエンドポイントをアプリケーションに使用します。

### <a name="create-an-environment-variable"></a>環境変数を作成する

>[!NOTE]
> 2019 年 7 月 1 日より後に作成された非試用版リソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)」を参照してください。 

作成したリソースのキーとエンドポイントを使用して、認証用に 2 つの環境変数を作成します。
* `FACE_SUBSCRIPTION_KEY` - 要求を認証するためのリソース キー。
* `FACE_ENDPOINT` - API 要求を送信するためのリソース エンドポイント。 以下のようになります。 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

ご利用のオペレーティング システムの手順に従ってください。
<!-- replace the below endpoint and key examples -->
#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx FACE_SUBSCRIPTION_KEY <replace-with-your-product-name-key>
setx FACE_ENDPOINT <replace-with-your-product-name-endpoint>
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

#### <a name="macostabunix"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export FACE_SUBSCRIPTION_KEY=<replace-with-your-product-name-key>
export FACE_ENDPOINT=<replace-with-your-product-name-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。
***

### <a name="create-a-go-project-directory"></a>Go プロジェクト ディレクトリを作成する

コンソール ウィンドウ (cmd、PowerShell、ターミナル、Bash) で、Go プロジェクト用に `my-app` という名前の新しいワークスペースを作成し、そこに移動します。

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

ワークスペースには次の 3 つのフォルダーが格納されます。

* **src** - このディレクトリには、ソース コードとパッケージが格納されます。 `go get` コマンドを使用してインストールされるパッケージはすべてこのフォルダーに入ります。
* **pkg** - このディレクトリには、コンパイル済みの Go パッケージ オブジェクトが格納されます。 これらのファイルにはいずれも `.a` という拡張子が付きます。
* **bin** - このディレクトリには、`go install` を実行するときに作成されたバイナリ実行可能ファイルが格納されます。

> [!TIP]
> Go ワークスペースの構造の詳細については、[Go 言語のドキュメント](https://golang.org/doc/code.html#Workspaces)を参照してください。 このガイドには、`$GOPATH` と `$GOROOT` の設定に関する情報が記載されています。

### <a name="install-the-client-library-for-go"></a>Go 用クライアント ライブラリをインストールする

次に、Go 用クライアント ライブラリをインストールします。

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

または、dep を使用している場合は、リポジトリ内で次を実行します。

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v1.0/face
```

### <a name="create-a-go-application"></a>Go アプリケーションを作成する

次に、**src** ディレクトリに `sample-app.go` という名前のファイルを作成します。

```bash
cd src
touch sample-app.go
```

普段使用している IDE またはテキスト エディターで `sample-app.go` を開きます。 次に、パッケージの名前を追加し、次のライブラリをインポートします。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_imports)]

次に、さまざまな Face サービス操作を実行するコードの追加を開始します。

## <a name="object-model"></a>オブジェクト モデル

以下のクラスとインターフェイスにより、Face サービス Go SDK の主要な機能の一部が処理されます。

|Name|説明|
|---|---|
|[BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#BaseClient) | このクラスは、Face サービスを使用するための承認を表し、すべての Face 機能に必要です。 サブスクリプション情報を使用してこれをインスタンス化し、他のクラスのインスタンスを生成するために使用します。 |
|[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)|このクラスは、人間の顔に対して実行できる基本的な検出と認識のタスクを処理します。 |
|[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)|このクラスは、画像内の 1 つの顔から検出されたすべてのデータを表します。 これを使用して、顔に関する詳細情報を取得できます。|
|[ListClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#ListClient)|このクラスは、クラウドに格納された **FaceList** コンストラクトを管理します。これには、さまざまな顔のセットが格納されます。 |
|[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)| このクラスは、クラウドに格納された **Person** コンストラクトを管理します。これには、1 人の人物に属している顔のセットが格納されます。|
|[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)| このクラスは、クラウドに格納された **PersonGroup** コンストラクトを管理します。これには、さまざまな **Person** オブジェクトのセットが格納されます。 |
|[SnapshotClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient)|このクラスは、スナップショット機能を管理します。 これを使用すると、クラウドベースのすべての Face データを一時的に保存し、そのデータを新しい Azure サブスクリプションに移行することができます。 |

## <a name="code-examples"></a>コード例

これらのコード サンプルは、Go 用 Face サービス クライアント ライブラリを使用して基本的なタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [画像内の顔を検出する](#detect-faces-in-an-image)
* [似た顔を探す](#find-similar-faces)
* [人物グループを作成してトレーニングする](#create-and-train-a-person-group)
* [顔を識別する](#identify-a-face)
* [データ移行のためのスナップショットを作成する](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE] 
> このクイックスタートでは、それぞれ `FACE_SUBSCRIPTION_KEY` および `FACE_ENDPOINT` という名前の、Face キーとエンドポイントの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)してあることを前提としています。

**main** 関数を作成し、その関数に次のコードを追加して、エンドポイントとキーでクライアントをインスタンス化します。 キーを使用して **[CognitiveServicesAuthorizer](https://godoc.org/github.com/Azure/go-autorest/autorest#CognitiveServicesAuthorizer)** オブジェクトを作成し、それをエンドポイントと共に使用して、 **[Client](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client)** オブジェクトを作成します。 また、このコードでは、クライアント オブジェクトの作成に必要なコンテキスト オブジェクトをインスタンス化します。 さらに、このクイックスタートのいくつかのサンプル画像があるリモートの場所を定義しています。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_main_client)]


## <a name="detect-faces-in-an-image"></a>画像内の顔を検出する

**main** メソッド内に次のコードを追加します。 このコードは、リモートのサンプル画像を定義し、画像から抽出する顔の特徴を指定します。 また、検出された顔からどの AI モデルを使用してデータを抽出するかも指定しています。 それらの選択については、[認識モデルの指定](../Face-API-How-to-Topics/specify-recognition-model.md)に関するページを参照してください。 最後に、 **[DetectWithURL](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.DetectWithURL)** メソッドで画像に対する顔検出処理を実行し、その結果をプログラムのメモリに保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect)]

### <a name="display-detected-face-data"></a>検出された顔データを表示する

次のコード ブロックでは、 **[DetectedFace](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#DetectedFace)** オブジェクトの配列の最初の要素を取得し、その属性をコンソールに出力します。 複数の顔が写った画像を使用する場合は、配列を反復処理することになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_detect_display)]

## <a name="find-similar-faces"></a>似た顔の検索

以下のコードでは、検出された顔 (ソース) を 1 つ受け取って、他の顔のセット (ターゲット) から一致するものを見つけます。 一致するものが見つかると、一致した顔の ID がコンソールに出力されます。

### <a name="detect-faces-for-comparison"></a>比較の対象となる顔を検出する

まず、「[画像内の顔を検出する](#detect-faces-in-an-image)」セクションで検出した顔の参照を保存します。 この顔がソースになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_single_ref)]

さらに、別の画像から一連の顔を検出する次のコードを入力します。 これらの顔がターゲットになります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_multiple_ref)]

### <a name="find-matches"></a>一致するものを探す

次のコードでは、 **[FindSimilar](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.FindSimilar)** メソッドを使用して、ソースの顔と一致するターゲットの顔をすべて検索します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar)]

### <a name="print-matches"></a>一致するものを出力する

次のコードは、一致の詳細をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_similar_print)]


## <a name="create-and-train-a-person-group"></a>人物グループを作成してトレーニングする

このシナリオの手順を実行するには、プロジェクトのルート ディレクトリに画像 (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images ) を保存する必要があります。

この画像のグループには、3 人の異なる人物に対応する 3 種類の顔画像が含まれています。写真には、1 枚につき 1 人の顔が写っています。 このコードでは、3 つの **PersonGroup Person** オブジェクトを定義し、それらを `woman`、`man`、および `child` で始まる画像ファイルに関連付けます。

### <a name="create-persongroup"></a>PersonGroup を作成する

画像をダウンロードしたら、**main** メソッドの一番下に次のコードを追加します。 このコードは、 **[PersonGroupClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient)** オブジェクトを認証し、それを使用して新しい **PersonGroup** を定義します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_setup)]

### <a name="create-persongroup-persons"></a>PersonGroup Person を作成する

次のコード ブロックでは、 **[PersonGroupPersonClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupPersonClient)** を認証し、それを使用して、新しい **PersonGroup Person** オブジェクトを 3 つ定義します。 これらのオブジェクトはそれぞれ、一連の画像に含まれる 1 人の人物を表します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_setup)]

### <a name="assign-faces-to-persons"></a>顔を人物に割り当てる

次のコードは、画像をそのプレフィックスで並べ替えて顔を検出し、画像ファイルの名前に基づいて、それぞれ個別の **PersonGroup Person** オブジェクトに割り当てます。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pgp_assign)]

### <a name="train-persongroup"></a>PersonGroup をトレーニングする

顔を割り当てたら、**PersonGroup** をトレーニングして、その各 **Person** オブジェクトに関連付けられている視覚的特徴を識別できるようにします。 次のコードは、非同期の **train** メソッドを呼び出し、結果をポーリングして、状態をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_pg_train)]

## <a name="identify-a-face"></a>顔を識別する

次のコードは、複数の顔が含まれている画像を取得し、画像内の各人物の ID を特定します。 検出された顔はそれぞれ、顔の特徴が確認されているさまざまな **Person** オブジェクトのデータベース、つまり **PersonGroup** と比較されます。

> [!IMPORTANT]
> この例を実行するには、まず、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」のコードを実行する必要があります。

### <a name="get-a-test-image"></a>テスト画像を取得する

次のコードは、プロジェクトのルートから画像 _test-image-person-group.jpg_ を探して、プログラムのメモリに読み込みます。 この画像は、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」で使用した画像と同じリポジトリにあります (https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images )。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_get)]

### <a name="detect-source-faces-in-test-image"></a>テスト画像からソースの顔を検出する

次のコード ブロックでは、テスト画像に通常の顔検出を実行してすべての顔を取得し、それらを配列に保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_source_detect)]

### <a name="identify-faces"></a>顔を識別する

**[Identify](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#Client.Identify)** メソッドは、検出された顔の配列を受け取って、指定された **PersonGroup** (前セクションで定義、トレーニングしたもの) と比較します。 検出された顔がグループ内の **Person** と一致する場合は、結果を保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id)]

その後、詳細な一致結果をコンソールに出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_id_print)]


## <a name="verify-faces"></a>顔を確認する

確認操作では、顔 ID と、別の顔 ID または **Person** オブジェクトのいずれかを取得し、同じ人に属しているかどうかを判断します。

次のコードでは、2 つのソース画像から顔を検出し、それぞれターゲット画像から検出された顔と照らして確認します。

### <a name="get-test-images"></a>テスト イメージを取得する

次のコード ブロックでは、確認操作のターゲット画像とソース画像を指す変数を宣言します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_images)]

### <a name="detect-faces-for-verification"></a>確認対象の顔を検出する

次のコードは、ソース画像とターゲット画像から顔を検出して、それらを変数に保存します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_source)]

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver_detect_target)]

### <a name="get-verification-results"></a>確認の結果を取得する

次のコードでは、各ソース画像をターゲット画像と比較し、同じ人物のものであるかどうかを示すメッセージを出力します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_ver)]


## <a name="take-a-snapshot-for-data-migration"></a>データ移行のためのスナップショットを作成する

スナップショット機能を使用すると、トレーニング済みの **PersonGroup** などの保存した顔データを別の Azure Cognitive Services Face サブスクリプションに移動できます。 この機能を利用するのは、たとえば、無料試用版サブスクリプションを使用して **PersonGroup** オブジェクトを作成してあり、それを有料サブスクリプションに移行することが必要になった場合などです。 スナップショット機能の広範な概要については、[顔データの移行](../Face-API-How-to-Topics/how-to-migrate-face-data.md)に関するページを参照してください。

この例では、「[人物グループを作成してトレーニングする](#create-and-train-a-person-group)」で作成した **PersonGroup** を移行します。 先にそのセクションを完了することも、独自の Face データ コンストラクトを使用することもできます。

### <a name="set-up-target-subscription"></a>ターゲット サブスクリプションを設定する

まず、Face リソースを含む 2 つ目の Azure サブスクリプションが必要です。そのためには、「[設定](#set-up)」セクションの手順を繰り返します。 

次に、**main** メソッドの先頭付近で以下の変数を作成します。 また、お使いの Azure アカウントのサブスクリプション ID と新しい (ターゲット) アカウントのキー、エンドポイント、サブスクリプション ID のための、新しい環境変数を作成する必要があります。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_target_client)]

その後、次の手順のために、サブスクリプション ID の値を配列に追加します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_id)]

### <a name="authenticate-target-client"></a>ターゲット クライアントを認証する

スクリプトの後の方で、元のクライアント オブジェクトをソース クライアントとして保存し、ターゲット サブスクリプションのために新しいクライアント オブジェクトを認証します。 

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_target_auth)]

### <a name="take-a-snapshot"></a>スナップショットを取得する

次のステップは、 **[Take](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Take)** を使用して、スナップショットを取得することです。これにより、元のサブスクリプションの顔データが一時的なクラウドの場所に保存されます。 このメソッドは、操作の状態を照会するために使用する ID を返します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_take)]

次に、操作が完了するまでに ID を照会します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_query)]

### <a name="apply-the-snapshot"></a>スナップショットを適用する

**[Apply](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#SnapshotClient.Apply)** 操作を使用して、新しくアップロードした顔データをターゲット サブスクリプションに書き込みます。 このメソッドも、ID を返します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply)]

もう一度、操作が完了するまで ID を照会します。

[!code-go[](~/cognitive-services-quickstart-code/go/Face/FaceQuickstart.go?name=snippet_snap_apply_query)]

これらの手順を完了すると、新しい (ターゲット) サブスクリプションから顔データ コンストラクトにアクセスすることができます。

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリから `go run [arguments]` コマンドを使用して、Go アプリケーションを実行します。

```bash
go run sample-app.go
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

このクイックスタートで作成した **PersonGroup** を削除したい場合は、 **[Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face#PersonGroupClient.Delete)** メソッドを呼び出します。 このクイックスタートでスナップショット機能を使用してデータを移行した場合は、ターゲット サブスクリプションに保存されている **PersonGroup** も削除する必要があります。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Go 用の Face ライブラリを使用して基本的なタスクを行う方法について学習しました。 次は、リファレンス ドキュメントを参照して、ライブラリの詳細について学習してください。

> [!div class="nextstepaction"]
> [Face API リファレンス (Go)](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/face)

* [Face サービスとは](../overview.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/Face/FaceQuickstart.go) にあります。
