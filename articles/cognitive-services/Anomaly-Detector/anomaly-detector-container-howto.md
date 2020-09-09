---
title: Anomaly Detector API を使用するためのコンテナーをインストールして実行する方法
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API の高度なアルゴリズムを利用し、時系列データ内の異常を特定します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: aahi
ms.openlocfilehash: ee742f09f3fcc1bd283efbc346fea6a040e53f48
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548533"
---
# <a name="install-and-run-anomaly-detector-containers-preview"></a>Anomaly Detector コンテナー (プレビュー) のインストールと実行

Anomaly Detector には次のコンテナー機能があります。

| 機能 | 特徴 |
|--|--|
| Anomaly Detector | <li> リアルタイムで発生した異常を検出します。 <li> バッチとして設定されたデータ全体で異常を検出します。 <li> データの予想される通常の範囲を推論します。 <li> 実際のデータに合わせた異常検出の機密性調整をサポートします。 |

API の詳細情報については、以下を参照してください。
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

Anomaly Detector コンテナーを使用する前に、次の前提条件を満たす必要があります。

|必須|目的|
|--|--|
|Docker エンジン| [ホスト コンピューター](#the-host-computer)に Docker エンジンをインストールしておく必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上で Docker 環境の構成を行うパッケージが用意されています。 Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。<br><br> コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。 <br><br> **Windows では**、Linux コンテナーをサポートするように Docker を構成することも必要です。<br><br>|
|Docker に関する知識 | レジストリ、リポジトリ、コンテナー、コンテナー イメージなど、Docker の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。|
|Anomaly Detector リソース |これらのコンテナーを使用するためには、以下が必要です。<br><br>関連付けられている API キーとエンドポイント URI を取得するための Azure _Anomaly Detector_ リソース。 どちらの値も、Azure portal の **Anomaly Detector** の概要ページとキー ページで使用でき、コンテナーを開始するために必要です。<br><br>**{API_KEY}** : **[キー]** ページにある 2 つの利用可能なリソース キーのどちらか<br><br>**{ENDPOINT_URI}** : **[概要]** ページに提示されているエンドポイント|

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="the-host-computer"></a>ホスト コンピューター

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

<!--* [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/). For instructions of deploying Anomaly Detector module in IoT Edge, see [How to deploy Anomaly Detector module in IoT Edge](how-to-deploy-anomaly-detector-module-in-iot-edge.md).-->

### <a name="container-requirements-and-recommendations"></a>コンテナーの要件と推奨事項

次の表に、Anomaly Detector コンテナーに割り当てる CPU コアとメモリの最小値と推奨値を示します。

| QPS (秒間クエリ) | 最小値 | 推奨 |
|-----------|---------|-------------|
| 10 QPS | 4 コア、1 GB メモリ | 8 コア、2 GB メモリ |
| 20 QPS | 8 コア、2 GB メモリ | 16 コア、4 GB メモリ |

各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。

## <a name="get-the-container-image-with-docker-pull"></a>`docker pull` によるコンテナー イメージの取得

[`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用して、コンテナー イメージをダウンロードします。

| コンテナー | リポジトリ |
|-----------|------------|
| cognitive-services-anomaly-detector | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest` |

<!--
For a full description of available tags, such as `latest` used in the preceding command, see [anomaly-detector](https://go.microsoft.com/fwlink/?linkid=2083827&clcid=0x409) on Docker Hub.
-->
[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-anomaly-detector-container"></a>Anomaly Detector コンテナーの docker pull

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest
```

## <a name="how-to-use-the-container"></a>コンテナーを使用する方法

コンテナーを[ホスト コンピューター](#the-host-computer)上に用意できたら、次の手順を使用してコンテナーを操作します。

1. 必要な課金設定を使用して[コンテナーを実行](#run-the-container-with-docker-run)します。 `docker run` コマンドの他の[例](anomaly-detector-container-configuration.md#example-docker-run-commands)もご覧いただけます。
1. [コンテナーの予測エンドポイントに対するクエリを実行します](#query-the-containers-prediction-endpoint)。

## <a name="run-the-container-with-docker-run"></a>`docker run` によるコンテナーの実行

コンテナーを実行するには、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドを使用します。 `{ENDPOINT_URI}` と `{API_KEY}` の値を取得する方法の詳細については、「[必須パラメーターの収集](#gathering-required-parameters)」を参照してください。

`docker run` コマンドの[例](anomaly-detector-container-configuration.md#example-docker-run-commands)を利用できます。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/anomaly-detector:latest \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから Anomaly Detector コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](#billing)」を参照してください。

### <a name="running-multiple-containers-on-the-same-host"></a>同じホスト上で複数のコンテナーを実行する

公開されているポートを使って複数のコンテナーを実行する予定の場合、必ず各コンテナーを別のポートで実行してください。 たとえば、最初のコンテナーをポート 5000 上で、2 番目のコンテナーを 5001 上で実行します。

`<container-registry>` および `<container-name>` を、使用しているコンテナーの値に置き換えます。 これらを同一のコンテナーにする必要はありません。 HOST 上で実行している Anomaly Detector コンテナーと LUIS コンテナーを一緒に使用したり、複数の Anomaly Detector コンテナーを実行したりできます。

ポート 5000 上で最初のコンテナーを実行します。

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

ポート 5001 上で 2 番目のコンテナーを実行します。


```bash
docker run --rm -it -p 5000:5001 --memory 4g --cpus 1 \
<container-registry>/microsoft/<container-name> \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

後続の各コンテナーは、別のポート上になっている必要があります。

## <a name="query-the-containers-prediction-endpoint"></a>コンテナーの予測エンドポイントに対するクエリの実行

コンテナーには、REST ベースのクエリ予測エンドポイント API が用意されています。

コンテナーの API のホストとしては http://localhost:5000 を使用します。

<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>コンテナーの停止

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>トラブルシューティング

出力[マウント](anomaly-detector-container-configuration.md#mount-settings)とログを有効にした状態でコンテナーを実行すると、コンテナーによってログ ファイルが生成されます。これらはコンテナーの起動時または実行時に発生した問題のトラブルシューティングに役立ちます。

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>課金

Anomaly Detector コンテナーは、Azure アカウントの _Anomaly Detector_ リソースを使用して、Azure に課金情報を送信します。

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

これらのオプションの詳細については、「[コンテナーの構成](anomaly-detector-container-configuration.md)」を参照してください。

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>まとめ

この記事では、Anomaly Detector コンテナーの概念とそのダウンロード、インストール、実行のワークフローについて説明しました。 要約すると:

* Anomaly Detector には、Docker 用に 1 つの Linux コンテナーがあり、バッチ対ストリーミング、予想範囲推論、および感度調整を使用して異常検出をカプセル化します。
* コンテナー イメージは、コンテナー プレビュー専用のプライベート Azure Container Registry からダウンロードされます。
* コンテナー イメージを Docker で実行します。
* REST API または SDK のいずれかを使用して、コンテナーのホスト URI を指定することによって、Anomaly Detector コンテナーの操作を呼び出すことができます。
* コンテナーをインスタンス化するときは、課金情報を指定する必要があります。

> [!IMPORTANT]
> Cognitive Services コンテナーは、計測のために Azure に接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に計測サービスに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、顧客データ (解析対象の時系列データなど) を Microsoft に送信することはありません。

## <a name="next-steps"></a>次のステップ

* 構成設定について、[コンテナーの構成](anomaly-detector-container-configuration.md)を確認する
* [Anomaly Detector コンテナーを Azure Container Instances にデプロイする](how-to/deploy-anomaly-detection-on-container-instances.md)
* [Anomaly Detector API サービスの詳細情報](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)
