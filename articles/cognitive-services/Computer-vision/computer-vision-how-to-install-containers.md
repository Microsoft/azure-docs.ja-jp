---
title: コンテナーをインストールして実行する方法 - Computer Vision
titleSuffix: Azure Cognitive Services
description: このチュートリアルでの Computer Vision のコンテナーのダウンロード、インストール、および実行方法。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 383abc674674fc024052b2c04d3c538838b83856
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120214"
---
# <a name="install-and-run-read-containers-preview"></a>Read コンテナーのインストールと実行 (プレビュー)

コンテナーを使用すると、独自の環境で Computer Vision API を実行できます。 コンテナーは、特定のセキュリティ要件とデータ ガバナンス要件に適しています。 この記事では、Computer Vision コンテナーをダウンロード、インストール、実行する方法について説明します。

Computer Vision では、単一の Docker コンテナー *Read* を使用できます。 *Read* コンテナーを使用すると、レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトの画像から、"*印刷されたテキスト*" を検出して、抽出することができます。 さらに、*Read* コンテナーでは、画像内の "*手書きテキスト*" も検出され、PDF、TIFF、複数ページ ファイルがサポートされます。 詳細については、[Read](concept-recognizing-text.md#read-api) API のドキュメントを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

コンテナーを使用する前に、次の前提条件を満たす必要があります。

|Required|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。| 
|Computer Vision リソース |コンテナーを使用するためには、以下が必要です。<br><br>Azure **Computer Vision** リソースとその関連する API キーおよびエンドポイント URI。 どちらの値も、対象リソースの概要ページとキー ページで使用でき、コンテナーを開始するために必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント|

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

[!INCLUDE [Request access to public preview](../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="advanced-vector-extension-support"></a>Advanced Vector Extension のサポート

**ホスト** コンピューターとは、Docker コンテナーを実行するコンピューターのことです。 ホストは、[高度なベクター拡張機能](https://en.wikipedia.org/wiki/Advanced_Vector_Extensions#CPUs_with_AVX2) (AVX2) を *サポートしている必要があります*。 次のコマンドを使用して、Linux ホストでの AVX2 サポートを確認できます。

```console
grep -q avx2 /proc/cpuinfo && echo AVX2 supported || echo No AVX2 support detected
```
> [!WARNING]
> AVX2 をサポートするにはホスト コンピューターが*必須*です。 AVX2 サポートがないと、コンテナーは正しく機能*しません*。

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

読み取りのコンテナー イメージを入手できます。

| コンテナー | コンテナー レジストリ / リポジトリ / イメージ名 |
|-----------|------------|
| Read | `containerpreview.azurecr.io/microsoft/cognitive-services-read:latest` |

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。

### <a name="docker-pull-for-the-read-container"></a>読み取りコンテナー用の Docker pull

```bash
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-read:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](computer-vision-resource-container-config.md)もご覧いただけます。 
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。 

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{ENDPOINT_URI}` と `{API_KEY}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。

`docker run` コマンドの[例](computer-vision-resource-container-config.md#example-docker-run-commands)を利用できます。

```bash
docker run --rm -it -p 5000:5000 --memory 16g --cpus 8 \
containerpreview.azurecr.io/microsoft/cognitive-services-read \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから読み取りコンテナーを実行します。
* 8 つの CPU コアと 16 ギガバイト (GB) のメモリを割り当てます。
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます。
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

`docker run` コマンドの他の[例](./computer-vision-resource-container-config.md#example-docker-run-commands)もご覧いただけます。 

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。 

コンテナーの API のホストとしては `http://localhost:5000` を使用します。

### <a name="asynchronous-read"></a>非同期読み取り

Computer Vision サービスで該当する REST 操作を使用する方法と同じように、`POST /vision/v2.0/read/core/asyncBatchAnalyze` 操作と `GET /vision/v2.0/read/operations/{operationId}` 操作を同時に使用して、画像を非同期に読み取ることができます。 非同期 POST メソッドでは、HTTP GET 要求に対する識別子として使用される `operationId` が返されます。

Swagger UI で `asyncBatchAnalyze` を選択し、ブラウザーで展開します。 次に、 **[Try it out]\(試してみる\)**  >  **[Choose file]\(ファイルの選択\)** を選択します。 この例では、次の画像を使用します。

![タブとスペース](media/tabs-vs-spaces.png)

非同期 POST が正常に実行されると、**HTTP 202** 状態コードが返されます。 応答の一部として、要求の結果エンドポイントを保持する `operation-location` ヘッダーがあります。

```http
 content-length: 0
 date: Fri, 13 Sep 2019 16:23:01 GMT
 operation-location: http://localhost:5000/vision/v2.0/read/operations/a527d445-8a74-4482-8cb3-c98a65ec7ef9
 server: Kestrel
```

`operation-location` は完全修飾 URL であり、HTTP GET を介してアクセスされます。 次に示すのは、前の画像から `operation-location` URL を実行すると返される JSON 応答です。

```json
{
  "status": "Succeeded",
  "recognitionResults": [
    {
      "page": 1,
      "clockwiseOrientation": 2.42,
      "width": 502,
      "height": 252,
      "unit": "pixel",
      "lines": [
        {
          "boundingBox": [
            56,
            39,
            317,
            50,
            313,
            134,
            53,
            123
          ],
          "text": "Tabs VS",
          "words": [
            {
              "boundingBox": [
                90,
                43,
                243,
                53,
                243,
                123,
                94,
                125
              ],
              "text": "Tabs",
              "confidence": "Low"
            },
            {
              "boundingBox": [
                259,
                55,
                313,
                62,
                313,
                122,
                259,
                123
              ],
              "text": "VS"
            }
          ]
        },
        {
          "boundingBox": [
            221,
            148,
            417,
            146,
            417,
            206,
            227,
            218
          ],
          "text": "Spaces",
          "words": [
            {
              "boundingBox": [
                230,
                148,
                416,
                141,
                419,
                211,
                232,
                218
              ],
              "text": "Spaces"
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="synchronous-read"></a>同期読み取り

`POST /vision/v2.0/read/core/Analyze` 操作を使用して、画像を同期的に読み取ることができます。 画像全体が読み込まれたら、そのときにだけ、API から JSON 応答が返されます。 これに対する唯一の例外は、エラーが発生した場合です。 エラーが発生すると、次の JSON が返されます。

```json
{
    status: "Failed"
}
```

JSON 応答オブジェクトには、非同期バージョンと同じオブジェクト グラフが含まれます。 JavaScript を使用していて、タイプ セーフが必要な場合は、次の型を使用して、JSON 応答を `AnalyzeResult` オブジェクトとしてキャストできます。

```typescript
export interface AnalyzeResult {
    status: Status;
    recognitionResults?: RecognitionResult[] | null;
}

export enum Status {
    NotStarted = 0,
    Running = 1,
    Failed = 2,
    Succeeded = 3
}

export enum Unit {
    Pixel = 0,
    Inch = 1
}

export interface RecognitionResult {
    page?: number | null;
    clockwiseOrientation?: number | null;
    width?: number | null;
    height?: number | null;
    unit?: Unit | null;
    lines?: Line[] | null;
}

export interface Line {
    boundingBox?: number[] | null;
    text: string;
    words?: Word[] | null;
}

export enum Confidence {
    High = 0,
    Low = 1
}

export interface Word {
  boundingBox?: number[] | null;
  text: string;
  confidence?: Confidence | null;
}
```

ユースケースの例については、<a href="https://aka.ms/ts-read-api-types" target="_blank" rel="noopener noreferrer">こちらの TypeScript サンドボックス<span class="docon docon-navigate-external x-hidden-focus"></span></a>を参照し、 **[Run]\(実行\)** を選択してその使いやすさを確認してください。

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](./computer-vision-resource-container-config.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Cognitive Services コンテナーでは、Azure アカウントの対応するリソースを使用して、Azure に課金情報が送信されます。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](./computer-vision-resource-container-config.md)」を参照してください。

<!--blogs/samples/video course -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Computer Vision コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Computer Vision では、読み取りがカプセル化された、Docker 用の Linux コンテナーが提供されます。
* コンテナー イメージは、Azure の "コンテナー プレビュー" コンテナー レジストリからダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK を使用して、コンテナーのホスト URI を指定することによって、Read コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](computer-vision-resource-container-config.md)を確認する
* [Computer Vision の概要](Home.md)ページを読み、印刷されたテキストと手書きのテキストの認識の詳細について確認する
* コンテナーによりサポートされるメソッドの詳細を [Computer Vision API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) ページで確認する。
* [よく寄せられる質問 (FAQ)](FAQ.md) を参照して、Computer Vision 機能に関連する問題を解決する。
* さらに [Cognitive Services コンテナー](../cognitive-services-container-support.md)を使用する
