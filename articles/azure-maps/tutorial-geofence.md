---
title: チュートリアル:マップにジオフェンスを作成してデバイスを追跡する |Microsoft Azure Maps
description: Microsoft Azure Maps 空間サービスを使用して、ジオフェンスを設定し、そのジオフェンスを基準にしてデバイスを追跡する方法について説明します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e0e918a2508bb65de176b030a73598e221aff676
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2020
ms.locfileid: "77370184"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>チュートリアル:Azure Maps を使用してジオフェンスを設定する

このチュートリアルでは、Azure Maps を使用してジオフェンスを設定するための基本的な手順について説明します。 建設現場の管理者が危険を伴うおそれのある機材を監視しなければならないシナリオを考えてみましょう。 管理者は、選択された建設区域全体に機材が置かれるよう徹底する必要があります。 この建設区域全体は、確実に遵守すべきパラメーターです。 このパラメーターの範囲内に機材を留めておくことが法令で義務付けられており、違反は Operations Manager にレポートされます。  

ここでは、Data Upload API を使用してジオフェンスを保存し、Geofence API を使用して、ジオフェンスを基準とした機材の位置を確認します。 Data Upload API と Geofence API は、どちらも Azure Maps に用意されています。 また、Azure Event Grid を使用してジオフェンスの結果をストリーム配信し、そのジオフェンスの結果に基づく通知を設定します。 Event Grid の詳細については、[Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) に関するページを参照してください。

このチュートリアルでは、次の方法について取り上げます。

> [!div class="checklist"]
> * Data Upload API を使用して Azure Maps のデータ サービスにジオフェンス領域をアップロードする。
> *   ジオフェンス イベントを処理する Event Grid を設定する。
> *   ジオフェンス イベントのハンドラーを設定する。
> *   ジオフェンス イベントに反応するアラートを Logic Apps を使用して設定する。
> *   Azure Maps のジオフェンス サービス API シリーズを使用して、建設用資産が建設現場内に存在するかどうかを追跡する。


## <a name="prerequisites"></a>前提条件

### <a name="create-an-azure-maps-account"></a>Azure Maps アカウントを作成する 

[アカウントの作成](quick-demo-map-app.md#create-an-account-with-azure-maps)手順に従い、S1 価格レベルで Azure Maps アカウントのサブスクリプションを作成します。 アカウントの主キーを取得する方法は、[プライマリ キーの取得](quick-demo-map-app.md#get-the-primary-key-for-your-account)手順で紹介しています。 Azure Maps での認証の詳細については、「[Azure Maps での認証の管理](./how-to-manage-authentication.md)」を参照してください。

## <a name="upload-geofences"></a>ジオフェンスのアップロード

メインのジオフェンスはサブサイト 1 であるとします。これには、設定された有効期限があります。 実際の要件に従って、入れ子になったジオフェンスをさらに作成することができます。 それらの一連のフェンスを使用すれば、建設区域全体の中で各建設区域を追跡することができます。 たとえば、サブサイト 1 はスケジュールの第 1 週から第 4 週にかけて作業が発生します。 サブサイト 2 は第 5 週から第 7 週にかけて作業が発生します。 そのすべてのフェンスは、単一のデータセットとしてプロジェクトの開始時に読み込むことができます。 これらのフェンスは、時間と空間に基づくルールの追跡に使用されます。 

Data Upload API を使用して建設現場のジオフェンスをアップロードするために、ここでは Postman アプリケーションを使用します。 [Postman アプリケーション](https://www.getpostman.com/)をインストールし、無料アカウントを作成してください。 

Postman アプリがインストールされたら、次の手順に従い、Azure Maps の Data Upload API を使用して建設現場のジオフェンスをアップロードします。

1. Postman アプリを開き、[new]\(新規\)、[Create new]\(新規作成\) の順にクリックして、[Request]\(要求\) を選択します。 ジオフェンス データのアップロードに対する要求名を入力して、これを保存するコレクションまたはフォルダーを選択し、[Save]\(保存\) をクリックします。

    ![Postman を使用してジオフェンスをアップロードする](./media/tutorial-geofence/postman-new.png)

2. [builder]\(ビルダー\) タブで POST HTTP メソッドを選択し、POST 要求を行うための次の URL を入力します。

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL パス内の GEOJSON パラメーターは、アップロードするデータの形式を表します。

3. **[Params]\(パラメーター\)** をクリックして、POST 要求の URL に使用する次のキーと値のペアを入力します。 {subscription-key} は、Azure Maps のサブスクリプション キー (主キーともいいます) に置き換えます。
   
    ![Postman でデータ (ジオフェンス) をアップロードするためのパラメーター](./media/tutorial-geofence/postman-key-vals.png)

4. **[Body]\(本文\)** をクリックして未加工入力形式を選択し、入力形式として JSON をドロップダウン リストから選択します。 アップロードするデータとして次の JSON を入力します。

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. [send]\(送信\) をクリックして、応答ヘッダーを確認します。 要求が成功すると、**Location** ヘッダーには、状態 URI が格納されます。 この状態 URI は次の形式になります。 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 自分の状態 URI をコピーし、サブスクリプション キーを追加します。 状態 URI の形式は次のようになります。 以下の形式では、{subscription-key} ({ } を含む) を実際のサブスクリプション キーで変更することに注意してください。

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId` を取得するには、Postman アプリで新しいタブを開き、[builder]\(ビルダー\) タブで GET HTTP メソッドを選択します。前の手順で取得した状態 URI で GET 要求を行います。 データのアップロードが成功した場合は、応答本文で udId が返されます。 後で使用するために udId をコピーします。

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>イベント ハンドラーの設定

このセクションでは、通知を受信するイベント ハンドラーを作成します。 このイベント ハンドラーでは、任意の機材の enter イベントと exit イベントについて Operations Manager に通知する必要があります。

ここでは、enter イベントと exit イベントを処理するための 2 つの [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) サービスを作成します。 Logic Apps 内のイベントがトリガーされると、その他のイベントが順にトリガーされます。 Operations Manager にアラート (この場合はメール) を送信することがその考えです。 次の図は、ジオフェンスの enter イベントに使用するロジック アプリの作成を示します。 同様に、exit イベント用にもう 1 つ作成できます。 詳細については、[サポートされているすべてのイベント ハンドラー](https://docs.microsoft.com/azure/event-grid/event-handlers)に関するページを参照してください。

1. Azure portal でロジック アプリを作成します。 Azure Marketplace でロジック アプリを選択します。 次に、 **[作成]** ボタンを選択します。

   ![ジオフェンス イベントを処理する Azure Logic Apps を作成する](./media/tutorial-geofence/logic-app.png)

2. ロジック アプリの設定メニューで **[ロジック アプリ デザイナー]** に移動します。

3. HTTP 要求トリガーを選択し、[新しいステップ] を選択します。 Outlook Connector で、アクションとして [電子メールの送信] を選択します。
  
   ![Logic Apps スキーマ](./media/tutorial-geofence/logic-app-schema.png)

4. メール送信に関するフィールドに値を入力します。 HTTP URL はそのままにしてください。[保存] をクリックした後に自動的に生成されます。

   ![Logic Apps エンドポイントを生成する](./media/tutorial-geofence/logic-app-endpoint.png)

5. ロジック アプリを保存して HTTP URL エンドポイントを生成し、HTTP URL をコピーします。

## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps イベントのサブスクリプションの作成

Azure Maps では、3 種類のイベントがサポートされています。 Azure Maps でサポートされるイベントの種類は、[こちら](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)で確認できます。 ここでは 2 つのイベント サブスクリプションが必要となります。1 つは enter イベント用、もう 1 つは exit イベントです。

以下の手順に従って、ジオフェンスの enter イベント用にサブスクリプションを作成します。 ジオフェンスの exit イベントも同様の方法でサブスクライブできます。

1. Azure Maps アカウントに移動します。 ダッシュボードで [サブスクリプション] を選択します。 使用するサブスクリプション名をクリックし、設定メニューから **[イベント]** を選択します。

   ![Azure Maps アカウントのイベントに移動する](./media/tutorial-geofence/events-tab.png)

2. イベント サブスクリプションを作成するには、[イベント] ページの [イベント サブスクリプション] を選択します。

   ![Azure Maps イベントのサブスクリプションの作成](./media/tutorial-geofence/create-event-subscription.png)

3. イベント サブスクリプションに名前を付け、イベントの種類として Enter をサブスクライブします。 ここで、[エンドポイントのタイプ] として [webhook] を選択します。 [エンドポイントの選択] をクリックし、ロジック アプリの HTTP URL エンドポイントを "{Endpoint}" にコピーします。

   ![Azure Maps イベントのサブスクリプションの詳細](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence API の使用

Geofence API を使用すると、**デバイス** (この場合は機材) がジオフェンスの内側にあるのか外側にあるのかを確認することができます。 特定の機材が時間の経過に伴って移動される状況下で、さまざまな場所に対して Geofence GET API を照会してみましょう。 以下の図は、5 つの場所と 5 つの建設機械を示したものです。 

> [!Note]
> このシナリオと動作は、同じ**デバイス ID** に基づいています。以下の図のように 5 つの場所が、この ID で示されます。

"deviceId" は、GET 要求でデバイスの場所を照会するときにデバイスに指定する一意の ID です。 **Search Geofence Get API** に対して非同期要求を行う際、"deviceId" があると、指定したジオフェンスを基準に対応するデバイスのジオフェンス イベントを発行するのに役立ちます。 このチュートリアルでは、一意の "deviceId" を使用して、API に対して非同期要求を行いました。 図を見るとわかるように、このチュートリアルでは要求が時系列で行われています。 応答の "isEventPublished" プロパティは、デバイスがジオフェンスを出入りするたびに発行されます。 このチュートリアルで追跡するデバイスを登録する必要はありません。

もう一度図を見てみましょう。 5 つの場所はそれぞれ、フェンスに対する enter と exit の状態の変化を評価する目的で使用されます。 状態の変化が生じた場合、ジオフェンス サービスからイベントがトリガーされ、そのイベントが Event Grid によってロジック アプリに送信されます。 その結果、対応する enter または exit の通知が、作業の管理者にメールで送信されます。

![Azure Maps のジオフェンス マップ](./media/tutorial-geofence/geofence.png)

Postman アプリで、先ほど作成したコレクションの新しいタブを開きます。 [builder]\(ビルダー\) タブで GET HTTP メソッドを選択します。

以下は、5 つの HTTP GET Geofencing API 要求です。それぞれ、機材の位置座標を指定しています。 座標は、時系列で観察されています。 それぞれの要求に続けて応答本文を示しています。
 
1. 場所 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![ジオフェンス クエリ 1](./media/tutorial-geofence/geofence-query1.png)

   上の応答では、メイン ジオフェンスからの距離が負数になっていますが、これは機材がメイン ジオフェンス内にあることを意味します。 サブサイトのジオフェンスからの距離は正数になっていることから、サブサイトのジオフェンスの外にあることがわかります。 

2. 場所 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![ジオフェンス クエリ 2](./media/tutorial-geofence/geofence-query2.png)

   前の JSON 応答をよく見ると、機材がサブサイトの外側、かつメイン フェンスの内側にあります。 イベントがトリガーされないため、メールは送信されません。

3. 場所 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![ジオフェンス クエリ 3](./media/tutorial-geofence/geofence-query3.png)

   状態の変化が発生しました。機材はメイン ジオフェンスの内側、かつサブサイト ジオフェンスの内側にあります。 この変更によりイベントが発行され、Operations Manager に通知メールが送信されます。

4. 場所 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![ジオフェンス クエリ 4](./media/tutorial-geofence/geofence-query4.png)

   対応する応答をよく観察すると、機材がサブサイト ジオフェンスから出たにもかかわらず、イベントが発行されていません。 GET 要求内でユーザーが指定した時刻を見てみると、この時刻に対して、サブサイト ジオフェンスは有効期限が切れていることがわかります。 機材は依然としてメイン ジオフェンス内に存在します。 また、サブサイト ジオフェンスのジオメトリ ID は応答本文の `expiredGeofenceGeometryId` で確認できます。


5. 場所 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![ジオフェンス クエリ 5](./media/tutorial-geofence/geofence-query5.png)

   建設現場のメイン ジオフェンスから機材が移動されたことが確認できます。 イベントが発行され、Operations Manager にアラート メールが送信されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Data Upload API を使用して Azure Maps と Data Service にジオフェンスをアップロードすることで、ジオフェンスを設定する方法を学習しました。 また、Azure Maps の Event Grid を使用してジオフェンス イベントをサブスクライブし、処理する方法も学習しました。 

* ロジック アプリを使用して JSON を解析し、より複雑なロジックを構築する方法については、「[Azure Logic Apps における各種コンテンツの扱い](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)」を参照してください。
* Event Grid のイベント ハンドラーの詳細については、[Event Grid でサポートされるイベント ハンドラー](https://docs.microsoft.com/azure/event-grid/event-handlers)に関するページを参照してください。
