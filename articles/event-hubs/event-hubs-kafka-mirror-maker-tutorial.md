---
title: Apache Kafka MirrorMaker を使用する - Azure Event Hubs | Microsoft Docs
description: この記事では、Kafka MirrorMaker を使用して Azure Event Hubs で Kafka クラスターをミラーリングする方法について説明します。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aea8ebcfa65d5f4c90aa1908d03f0fcde8906bba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320192"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Apache Kafka 用の Event Hubs で Kafka MirrorMaker を使用する

このチュートリアルでは、Kafka MirrorMaker を使用してイベント ハブ内の Kafka ブローカーをミラーリングする方法について説明します。

   ![Kafka MirrorMaker とイベント ハブ](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) で入手できます。


このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトをクローンする
> * Kafka クラスターを設定する
> * Kafka MirrorMaker を構成する
> * Kafka MirrorMaker を実行する

## <a name="introduction"></a>はじめに
最新のクラウド スケール アプリに関する 1 つの主要な考慮事項は、サービスを中断することなくインフラストラクチャを更新、強化、変更できることです。 このチュートリアルでは、イベント ハブと Kafka MirrorMaker を使用し、Kafka 入力ストリームを Event Hubs サービスに "ミラーリング" することで Azure に Kafka の既存のパイプラインを統合する方法について説明します。 

Azure Event Hubs Kafka エンドポイントでは、Kafka プロトコル (つまり Kafka クライアント) を使って Azure Event Hubs に接続できます。 Kafka アプリケーションに最小限の変更を行うことで、Azure Event Hubs に接続し、Azure エコシステムの利点を活用することができます。 Event Hubs は現在、Kafka バージョン 1.0 以降をサポートしています。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。

* [Apache Kafka 用の Event Hubs](event-hubs-for-kafka-ecosystem-overview.md) に関する記事を読む。 
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
* [Java Development Kit (JDK) 1.7 以降](https://aka.ms/azure-jdks)
    * Ubuntu で `apt-get install default-jdk` を実行して JDK をインストールします。
    * 必ず、JDK のインストール先フォルダーを指すように JAVA_HOME 環境変数を設定してください。
* Maven バイナリ アーカイブの[ダウンロード](https://maven.apache.org/download.cgi)と[インストール](https://maven.apache.org/install.html)
    * Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
* [Git](https://www.git-scm.com/downloads)
    * Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します

Event Hubs サービスとの間で送受信を行うには、イベント ハブの名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 後で使うので、イベント ハブの接続文字列をコピーしておきます。

## <a name="clone-the-example-project"></a>サンプル プロジェクトをクローンする

Event Hubs の接続文字列が用意できたので、Kafka 用 Azure Event Hubs リポジトリをクローンし、`mirror-maker` サブフォルダーに移動します。

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Kafka クラスターを設定する

[Kafka クイック スタート ガイド](https://kafka.apache.org/quickstart)を使って、必要な設定でクラスターをセットアップします (または、既存の Kafka クラスターを使います)。

## <a name="configure-kafka-mirrormaker"></a>Kafka MirrorMaker を構成する

Kafka MirrorMaker を使うと、ストリームを "ミラーリング" できます。 ミラー元とミラー先の Kafka クラスターを指定すると、MirrorMaker は、ミラー元クラスターに送信されたメッセージを、ミラー元とミラー先の両方のクラスターが受信するようにします。 この例では、ミラー元の Kafka クラスターをミラー先のイベント ハブにミラーリングする方法を示します。 このシナリオを使って、データのフローを中断することなく、既存の Kafka パイプラインからイベント ハブにデータを送信できます。 

Kafka MirrorMaker について詳しくは、[Kafka ミラーリング/MirrorMaker ガイド](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)に関するページをご覧ください。

Kafka MirrorMaker を構成するには、Kafka クラスターをコンシューマーつまりミラー元として指定し、イベント ハブをプロデューサーつまりミラー先として指定します。

#### <a name="consumer-configuration"></a>コンシューマーの構成

コンシューマー構成ファイル `source-kafka.config` を更新し、ミラー元 Kafka クラスターのプロパティを MirrorMaker に提供します。

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>プロデューサーの構成

次に、プロデューサー構成ファイル `mirror-eventhub.config` を更新し、複製された (つまり "ミラーリングされた") データを Event Hubs サービスに送るよう MirrorMaker に指示します。 具体的には、`bootstrap.servers` と `sasl.jaas.config` を、Event Hubs Kafka エンドポイントを指すように変更します。 Event Hubs サービスにはセキュリティで保護された (SASL) 通信が必要であり、これは次の構成の最後の 3 つのプロパティを設定することによって実現します。 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Kafka MirrorMaker を実行する

新しく更新した構成ファイルを使って、Kafka のルート ディレクトリから Kafka MirrorMaker スクリプトを実行します。 構成ファイルを Kafka のルート ディレクトリにコピーするか、または次のコマンドを使って構成ファイルのパスを更新します。

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

イベント ハブにイベントが届いていることを確認するには、[Azure portal](https://azure.microsoft.com/features/azure-portal/) でイングレス統計を確認するか、またはイベント ハブに対してコンシューマーを実行します。

MirrorMaker が実行されている場合、ミラー元の Kafka クラスターに送信されたイベントはすべて、Kafka クラスターとミラーリングされたイベント ハブの両方によって受信されます。 MirrorMaker と Event Hubs Kafka エンドポイントを使うと、既存のクラスターを変更したり、実行中のデータ フローを中断したりすることなく、既存の Kafka パイプラインを、マネージド Azure Event Hubs サービスに移行できます。

## <a name="samples"></a>サンプル
GitHub の次のサンプルを参照してください。

- [GitHub 上のこのチュートリアルに対するサンプル コード](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure コンテナー インスタンスで実行されている Azure Event Hubs Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>次のステップ
Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [Kafka Connect をイベント ハブと統合する](event-hubs-kafka-connect-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)
