---
title: Azure Digital Twins とは
titleSuffix: Azure Digital Twins
description: Azure Digital Twins を使用して実行できる操作の概要を説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: overview
ms.service: digital-twins
ms.openlocfilehash: d03a4865c8db52f74f4130c458fec3028f5b95a4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481669"
---
# <a name="what-is-azure-digital-twins"></a>Azure Digital Twins とは

**Azure Digital Twins** は、環境全体のデジタル モデルに基づいたナレッジ グラフの作成を可能にする、サービスとしてのプラットフォーム (PaaS) オファリングです。 これらの環境には、ビル、工場、農場、エネルギー ネットワーク、鉄道、スタジアムなどがあり、さらには都市全体が含まれます。 これらのデジタル モデルを使用して、より優れた製品、最適化された操作、コストの削減、および画期的なカスタマー エクスペリエンスを実現する分析情報を得ることができます。

Azure Digital Twins に加えて、ご自分の分野の専門知識を活用して、次のようなカスタマイズされた接続済みソリューションを構築します。
* あらゆる環境をモデル化し、デジタル ツインをスケーラブルで安全な方法で実現する
* IoT デバイスや既存のビジネス システムなどの資産を接続する
* 堅牢なイベント システムを使用して動的なビジネス ロジックとデータ処理を構築する
* Azure のデータ、分析、AI サービスと統合して、過去の追跡と将来の予測を支援する

## <a name="azure-digital-twins-capabilities"></a>Azure Digital Twins の機能

Azure Digital Twins で提供される機能の概要を次に示します。

### <a name="open-modeling-language"></a>オープン モデリング言語

Azure Digital Twins では、[**モデル**](concepts-models.md)と呼ばれるカスタム ツイン型を使用して、物理環境の人、場所、および物を表すデジタルエン ティティを定義します。 

これらのモデル定義は、ビジネスを説明するための特殊なボキャブラリと考えることができます。 たとえば、ビル管理ソリューションでは、"建物"、"階数"、"エレベーター" などのモデルを定義できます。 その後、これらのモデルに基づいて **デジタル ツイン** を作成して、特定の環境を表すことができます。

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

モデルは [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) と呼ばれる JSON に似た形式の言語で定義され、その状態プロパティ、テレメトリ イベント、コマンド、コンポーネント、およびリレーションシップの観点からツインを記述します。
* モデルは、エンティティ間のセマンティック **リレーションシップ** を定義して、それらのやり取りを反映するナレッジ グラフにツインを接続できるようにします。 モデルは自分の環境を説明する名詞で、リレーションシップは動詞として考えることができます。
* モデルの継承を使用してツインを特殊化することもできます。 一方のモデルがもう一方のモデルから継承できます。

DTDL は、[IoT プラグ アンド プレイ (PnP)](../iot-pnp/overview-iot-plug-and-play.md) および [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) などの他の Azure IoT サービス全体でデータ モデルに使用されます。 これにより、Azure Digital Twins ソリューションの接続と、Azure エコシステムの他の部分との互換性を維持することができます。

### <a name="live-execution-environment"></a>ライブ実行環境

Azure Digital Twins のデジタル モデルは、現実世界のライブの最新の表現です。 カスタム DTDL モデルのリレーションシップを使用して、ご自分の環境を表す **ライブ グラフ** にツインを接続します。

Azure Digital Twins グラフの視覚化は、サンプル アプリケーション [**Azure Digital Twins explorer**](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) を使用して確認できます。

次に、視覚化の例を示します。

:::image type="content" source="media/includes/azure-digital-twins-explorer.png" alt-text="デジタル ツインを表すノードのグラフが表示された Azure Digital Twins explorer サンプル アプリケーションのスクリーンショット" lightbox="media/includes/azure-digital-twins-explorer.png":::

Azure Digital Twins では、そのグラフをデータ処理とビジネス ロジックで最新の状態に保つための豊富な **イベント システム** を提供しています。 [Azure Functions](../azure-functions/functions-overview.md) などの外部のコンピューティング リソースを接続して、このデータ処理を柔軟でカスタマイズされた方法で行うことができます。

Azure Digital Twins の強力な **クエリ API** を使用して、ライブ実行環境から分析情報を抽出することもできます。 この API を使用すると、プロパティ値、リレーションシップ、リレーションシップ プロパティ、モデル情報など、豊富な検索条件を使用してクエリを実行できます。 また、クエリを結合して、ご自分の環境に関する幅広い分析情報を収集し、ご自分にとって重要なカスタムの質問に答えることもできます。

### <a name="input-from-iot-and-business-systems"></a>IoT およびビジネス システムからの入力

Azure Digital Twins のライブ実行環境を実際の最新の状態に保つために、[IoT Hub](../iot-hub/about-iot-hub.md) を使用して、ソリューションを IoT および IoT Edge デバイスに接続することができます。 これらのハブで管理されたデバイスはツイン グラフの一部として表され、モデルを駆動するデータを提供します。

Azure Digital Twins を使用してこの目的のための新しい IoT Hub を作成したり、既に管理しているデバイスと共に既存の IoT Hub を接続したりすることができます。

また、REST API や [Logic Apps](../logic-apps/logic-apps-overview.md) などのその他のサービスへのコネクタを使用して、他のデータ ソースから Azure Digital Twins を駆動することもできます。

### <a name="output-to-tsi-storage-and-analytics"></a>TSI、ストレージ、分析への出力

Azure Digital Twins モデルのデータは、追加の分析またはストレージのために下流の Azure サービスにルーティングできます。 これは、[イベント ハブ](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) を使用して、目的のデータ フローを駆動する、**イベント ルート** を通じて提供されます。

イベント ルートでは、次のようなことが可能です。
* [Azure Data Lake](../storage/blobs/data-lake-storage-introduction.md) に Azure Digital Twins データを格納する
* [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) またはその他の Microsoft Data Analytics ツールを使用して Azure Digital Twins データを分析する
* より大きなワークフローと Logic Apps を統合する
* Azure Digital Twins を Time Series Insights に接続して、各ツインの時系列の履歴を追跡する
* Time Series Insights のタイム シリーズ モデルを Azure Digital Twins のソースと合わせる

これは、Azure Digital Twins が大規模なソリューションに接続し、これらの分析情報を継続的に使用するためのカスタム ニーズをサポートするもう 1 つの方法です。

## <a name="azure-digital-twins-in-a-solution-context"></a>ソリューション コンテキストでの Azure Digital Twins

Azure Digital Twins は、大規模な IoT ソリューションの一部として、他の Azure サービスと組み合わせてよく使用されます。 

Azure Digital Twins を使用した完全なソリューションには、次の部分が含まれる場合があります。
* Azure Digital Twins サービス インスタンス。 これは、ツイン モデルとツイン グラフをその状態で格納し、イベント処理を調整します。
* モデルの構成、トポロジの作成、ツイン グラフからの分析情報の抽出によって Azure Digital Twins インスタンスを駆動する 1 つ以上のクライアント アプリ。
* Azure Digital Twins によって生成されるイベントや、デバイスなどの接続されたデータ ソースを処理するための 1 つ以上の外部コンピューティング リソース。 コンピューティング リソースを提供する一般的な方法の 1 つは、[Azure Functions](../azure-functions/functions-overview.md) を使用することです。
* デバイス管理と IoT データ ストリーム機能を提供する IoT ハブ。
* ワークフロー統合 ([Logic Apps](../logic-apps/logic-apps-overview.md)、コールド ストレージ、時系列統合、分析など) のタスクを処理するダウンストリーム サービス。

次の図は、大規模な Azure IoT ソリューションのコンテキスト内で Azure Digital Twins が存在する場所を示しています。

:::image type="content" source="media/overview/solution-context.png" alt-text="入力ソース、出力サービス、およびクライアント アプリと外部コンピューティング リソースとの双方向の通信を示す図。" border="false" lightbox="media/overview/solution-context.png":::

## <a name="service-limits"></a>サービスの制限

Azure Digital Twins の **サービスの制限** については、こちらを参照してください: [Azure Digital Twins サービスの制限](reference-service-limits.md)。 これは、サービスを操作する際、サービスの機能とレート制限、および必要に応じて調整できる制限を理解するうえで役立つことがあります。

## <a name="terminology"></a>用語

**一般的な IoT 用語** と、Azure Digital Twins を含む Azure IoT サービス全体におけるその使い方の一覧については、[Azure IoT の用語集](../iot-fundamentals/iot-glossary.md?toc=/azure/digital-twins/toc.json&bc=/azure/digital-twins/breadcrumb/toc.json)を参照してください。 これは、Azure Digital Twins を開始して IoT ソリューションを構築する際に役立つ参考資料となることがあります。

## <a name="next-steps"></a>次のステップ

* クイックスタートを使用して、Azure Digital Twins の使用について詳しく確認します。[*クイック スタート:サンプル シナリオを精査する*](quickstart-azure-digital-twins-explorer.md)"。

* または、Azure Digital Twins の概念についての [*概念: カスタム モデル*](concepts-models.md)に関するページを参照してください。