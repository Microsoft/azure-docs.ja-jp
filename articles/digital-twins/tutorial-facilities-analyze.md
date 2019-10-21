---
title: チュートリアル:Azure Digital Twins 設定からのイベントを分析する | Microsoft Docs
description: このチュートリアルの手順を使用して、Azure Time Series Insights を使って Azure Digital Twins 空間からのイベントを視覚化および分析する方法を学習します。
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/23/2019
ms.openlocfilehash: af776372026bf9affcda03fa9188b854ebedfc73
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949799"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-by-using-time-series-insights"></a>チュートリアル:Time Series Insights を使用して Azure Digital Twins 空間からのイベントを視覚化および分析する

Azure Digital Twins インスタンスをデプロイし、空間をプロビジョニングして、特定の条件を監視するカスタム関数を実装した後は、傾向や異常を見つけるために空間から受け取ったイベントやデータを視覚化することができます。

[最初のチュートリアル](tutorial-facilities-setup.md)では、モーション、二酸化炭素、温度のセンサーを備えた部屋がある架空の建物の空間グラフを構成しました。 [2 番目のチュートリアル](tutorial-facilities-udf.md)では、グラフとユーザー定義関数をプロビジョニングしました。 関数によってこれらのセンサーの値が監視され、適切な条件になると通知がトリガーされます。 その条件とは、部屋が空室で、温度と二酸化炭素のレベルが正常であるという条件です。

このチュートリアルでは、Azure Digital Twins 設定からの通知とデータを Azure Time Series Insights と統合する方法について説明します。 その後、時間の経過に伴うセンサーの値を視覚化することができます。 最も利用されている部屋や 1 日のうちで最も忙しい時間帯などの傾向を調べることができます。 さらに、どの部屋で風通しの悪さや暑さをより強く感じるかや、建物内のあるエリアが、空調設備の異常を示す高い温度値を常に送信しているかどうか、などの異常を検出することもできます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Event Hubs を使用したデータのストリーム配信
> * Time Series Insights を使用した分析

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、お客様が Azure Digital Twins の設定の[構成](tutorial-facilities-setup.md)と[プロビジョニング](tutorial-facilities-udf.md)を完了していることを前提としています。 次に進む前に、以下が準備されていることを確認します。

- [Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Digital Twins のインスタンスが実行中であること。
- [Digital Twins C# のサンプル](https://github.com/Azure-Samples/digital-twins-samples-csharp)がダウンロードされ、作業マシン上に展開されていること。
- サンプルを実行する開発マシンに [.NET Core SDK バージョン 2.1.403 以上](https://www.microsoft.com/net/download)がインストールされていること。 適切なバージョンがインストールされていることを確認するには、`dotnet --version` を実行します。

> [!TIP]
> 新しいインスタンスをプロビジョニングする場合は、一意の Digital Twins インスタンス名を使用します。

## <a name="stream-data-by-using-event-hubs"></a>Event Hubs を使用したデータのストリーム配信

[Event Hubs](../event-hubs/event-hubs-about.md) サービスを使用すると、データをストリーム配信するパイプラインを作成できます。 このセクションでは、Azure Digital Twins インスタンスと Time Series Insights インスタンスの間のコネクタとしてイベント ハブを作成する方法について説明します。

### <a name="create-an-event-hub"></a>イベント ハブの作成

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウで、 **[リソースの作成]** を選びます。

1. **[Event Hubs]** を見つけて選択します。 **作成** を選択します。

1. Event Hubs 名前空間の**名前**を入力します。 **Standard** **価格レベル**、**サブスクリプション**、Digital Twins インスタンスに使用した**リソース グループ**、**場所**を選択します。 **作成** を選択します。

1. Event Hubs 名前空間のデプロイで、 **[概要]** ウィンドウを選択し、 **[リソースに移動]** を選択します。

    [![デプロイ後の Event Hubs 名前空間](./media/tutorial-facilities-analyze/open-event-hub-ns.png)](./media/tutorial-facilities-analyze/open-event-hub-ns.png#lightbox)

1. Event Hubs 名前空間の **[概要]** ウィンドウで、最上部にある **[イベント ハブ]** を選択します。
    [![[イベント ハブ] ボタン](./media/tutorial-facilities-analyze/create-event-hub.png)](./media/tutorial-facilities-analyze/create-event-hub.png#lightbox)

1. イベント ハブの**名前**を入力し、 **[作成]** を選択します。

   イベント ハブは、デプロイ後、Event Hubs 名前空間の **[Event Hubs]** ウィンドウに **[アクティブ]** 状態として表示されます。 このイベント ハブを選択して、 **[概要]** ウィンドウを開きます。

1. 最上部にある **[コンシューマー グループ]** をクリックし、コンシューマー グループの名前 (たとえば、**tsievents**) を選択します。 **作成** を選択します。

    [![イベント ハブ コンシューマー グループ](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)](./media/tutorial-facilities-analyze/event-hub-consumer-group.png#lightbox)

   作成されたコンシューマー グループが、イベント ハブの **[概要]** ウィンドウの下部にある一覧に表示されます。

1. イベント ハブの **[共有アクセス ポリシー]** ウィンドウを開き、 **[追加]** ボタンを選択します。 ポリシーの名前として「**ManageSend**」を入力し、すべてのチェック ボックスがオンになっていることを確認して、 **[作成]** を選択します。

    [![イベント ハブの接続文字列](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)](./media/tutorial-facilities-analyze/event-hub-connection-strings.png#lightbox)

1. 作成した ManageSend ポリシーを開き、 **[接続文字列 - 主キー]** と **[接続文字列 - セカンダリ キー]** の値を一時ファイルにコピーします。 これらの値は、次のセクションでイベント ハブのエンドポイントを作成するために必要です。

### <a name="create-an-endpoint-for-the-event-hub"></a>イベント ハブのエンドポイントの作成

1. コマンド ウィンドウで、Azure Digital Twins サンプルの **occupancy-quickstart\src** フォルダーに移動していることを確認します。

1. エディターで **actions\createEndpoints.yaml** ファイルを開きます。 内容を次のものに置き換えます。

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hub
    ```

1. プレースホルダー `Primary_connection_string_for_your_event_hub` を、イベント ハブの **[接続文字列 - 主キー]** の値に置き換えます。 この接続文字列が次の形式になっていることを確認します。

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
   ```

1. プレースホルダー `Secondary_connection_string_for_your_event_hub` を、イベント ハブの **[接続文字列 - セカンダリ キー]** の値に置き換えます。 この接続文字列が次の形式になっていることを確認します。 

   ```plaintext
   Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
   ```

1. プレースホルダー `Name_of_your_Event_Hub` をイベント ハブの名前に置き換えます。

    > [!IMPORTANT]
    > 引用符なしですべての値を入力します。 YAML ファイル内のコロンの後に少なくとも 1 つの空白文字があることを確認してください。 YAML ファイルの内容は、[このツール](https://onlineyamltools.com/validate-yaml)のようなオンライン YAML 検証ツールを使用して検証することもできます。

1. ファイルを保存して閉じます。 コマンド ウィンドウで次のコマンドを実行し、メッセージが表示されたら Azure アカウントを使用してサインインします。

    ```cmd/sh
    dotnet run CreateEndpoints
    ```

   イベント ハブに対して 2 つのエンドポイントが作成されます。

   [![Event Hubs のエンドポイント](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png#lightbox)

## <a name="analyze-with-time-series-insights"></a>Time Series Insights を使用した分析

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[リソースの作成]** を選択します。 

1. **Time Series Insights** の一般公開 (GA) リソースを検索して選択します。 **作成** を選択します。

1. Time Series Insights インスタンスの**名前**を入力し、**サブスクリプション**を選択します。 Digital Twins インスタンスに対して使用した**リソース グループ**と**場所**を選択します。 **[次へ:イベント ソース]** ボタンまたは **[イベント ソース]** タブを選択します。

    [![Time Series Insights インスタンスを作成するための選択内容](./media/tutorial-facilities-analyze/create-tsi.png)](./media/tutorial-facilities-analyze/create-tsi.png#lightbox)

1. **[イベント ソース]** タブで、 **[名前]** を入力し、 **[ソースの種類]** として **[イベントハブ]** を選択し、他の値が適切に選択されていることを確認します。 **[イベント ハブ アクセス ポリシー名]** には、**ManageSend** を選択します。さらに、 **[イベント ハブ コンシューマー グループ]** には、前のセクションで作成したコンシューマー グループを選択します。 **[Review + create]\(レビュー + 作成\)** を選択します。

    [![イベント ソースを作成するための選択内容](./media/tutorial-facilities-analyze/tsi-event-source.png)](./media/tutorial-facilities-analyze/tsi-event-source.png#lightbox)

1. **[確認と作成]** ウィンドウで、入力した情報を確認し、 **[作成]** を選択します。

1. デプロイ ウィンドウで、先ほど作成した Time Series Insights リソースを選択します。 Time Series Insights 環境の **[概要]** ウィンドウが開きます。

1. 上部にある **[環境を開く]** ボタンを選択します。 "データ アクセスの警告" が表示された場合は、Time Series Insights インスタンスの **[データ アクセス ポリシー]** ウィンドウを開きます。 **[追加]** を選択し、ロールとして **[共同作成者]** を選択して、適切なユーザーを選択します。

1. **[環境を開く]** をクリックすると、[Time Series Insights エクスプローラー](../time-series-insights/time-series-insights-explorer.md)が開きます。 イベントが表示されない場合は、Digital Twins サンプルの **device-connectivity** プロジェクトに移動し、`dotnet run` を実行して、デバイス イベントをシミュレートします。

1. いくつかのシミュレートされたイベントが生成されたら、Time Series Insights エクスプローラーに戻り、最上部にある更新ボタンを選択します。 シミュレートされたセンサー データの分析グラフが作成されるのがわかります。 

    [![Time Series Insights エクスプローラーのグラフ](./media/tutorial-facilities-analyze/tsi-explorer.png)](./media/tutorial-facilities-analyze/tsi-explorer.png#lightbox)

1. 次に、Time Series Insights エクスプローラーを使用して、部屋、センサー、その他のリソースからのさまざまなイベントやデータについてグラフやヒートマップを生成できます。 独自の視覚化を作成するには、左側の **[メジャー]** と **[SPLIT BY]\(分割基準\)** ドロップダウン ボックスを使用します。 

   たとえば、 **[メジャー]** で **[イベント]** を選択し、 **[SPLIT BY]\(分割基準\)** で **[DigitalTwins-SensorHardwareId]** を選択して、各センサーのヒートマップを生成します。 次の画像のようなヒートマップが得られます。

   [![Time Series Insights エクスプローラーのヒートマップ](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png#lightbox)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これ以降 Azure Digital Twins の探索を中止する場合は、このチュートリアルで作成されたリソースを削除してかまいません。

1. [Azure portal](https://portal.azure.com) の左側のメニューの **[すべてのリソース]** をクリックし、目的の Digital Twins リソース グループを選択して **[削除]** を選択します。

    > [!TIP]
    > ご自分の Digital Twins インスタンスの削除で問題が発生していた場合は、サービス更新が修正と共にロールアウトされています。 ご自分のインスタンスの削除を再試行してください。

2. 必要に応じて、作業マシン上のサンプル アプリケーションを削除します。

## <a name="next-steps"></a>次の手順

Azure Digital Twins の空間インテリジェンス グラフとオブジェクト モデルについて学習するには、次の記事に進んでください。

> [!div class="nextstepaction"]
> [Digital Twins オブジェクト モデルと空間インテリジェンス グラフを理解する](concepts-objectmodel-spatialgraph.md)
