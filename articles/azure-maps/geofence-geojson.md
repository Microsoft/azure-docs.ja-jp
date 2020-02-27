---
title: ジオフェンスの GeoJSON データ形式 | Microsoft Azure Maps
description: この記事では、Microsoft Azure Maps の GET Geofence および POST Geofence API で使用できるジオフェンス データの準備方法について説明します。
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210023"
---
# <a name="geofencing-geojson-data"></a>GeoJSON データのジオフェンシング

Azure Maps の [GET Geofence](/rest/api/maps/spatial/getgeofence) および [POST Geofence](/rest/api/maps/spatial/postgeofence) API を使用すると、指定したジオフェンスまたはフェンスのセットを基準とする座標の近接度を取得できます。 この記事では、Azure Maps の GET および POST API で使用できるジオフェンス データを準備する方法を説明します。

ジオフェンスまたはジオフェンスのセットに対するデータは、`GeoJSON` 形式の `Feature` オブジェクトと `FeatureCollection` オブジェクトによって表されます。これは、[rfc7946](https://tools.ietf.org/html/rfc7946) で定義されています。 さらに、次のことが指定されています。

* 使用できる GeoJSON オブジェクト型は、`Feature` オブジェクトまたは `FeatureCollection` オブジェクトです。
* 使用できる Geometry オブジェクト型は、`Point`、`MultiPoint`、`LineString`、`MultiLineString`、`Polygon`、`MultiPolygon`、`GeometryCollection` です。
* すべてのフィーチャー プロパティには、`geometryId` が含まれる必要があります。これは、ジオフェンスの識別に使用されます。
* `Point`、`MultiPoint`、`LineString`、`MultiLineString` のフィーチャーには、`radius` プロパティが含まれる必要があります。 `radius` の値はメートル単位で測定され、`radius` の値の範囲は 1 ～ 10,000 です。
* `polygon` および `multipolygon` geometry 型のフィーチャーには、radius プロパティはありません。
* `validityTime` は省略可能なプロパティであり、ユーザーはジオフェンス データの期限切れ日時と有効期間を設定できます。 指定しない場合、データは期限切れになることがなく、常に有効です。
* `expiredTime` は、ジオフェンシング データの期限切れ日時です。 要求に含まれる `userTime` の値がこの値より後の場合、対応するジオフェンス データは期限切れデータと見なされ、クエリは実行されません。 その場合、このジオフェンス データの geometryId が、ジオフェンス応答内の `expiredGeofenceGeometryId` 配列に格納されます。
* `validityPeriod` は、ジオフェンスの有効期間のリストです。 要求に含まれる `userTime` の値が有効期間の範囲外の場合、対応するジオフェンス データは無効と見なされ、クエリは実行されません。 このジオフェンス データの geometryId は、ジオフェンス応答内の `invalidPeriodGeofenceGeometryId` 配列に格納されます。 validityPeriod 要素のプロパティを次の表に示します。

| 名前 | Type | Required  | 説明 |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | 有効期間の開始日時。 |
| endTime   | Datetime  | true |  有効期間の終了日時。 |
| recurrenceType | string | false |   期間の繰り返しの種類。 値は、`Daily`、`Weekly`、`Monthly`、`Yearly` のいずれかです。 既定値は `Daily` です。|
| businessDayOnly | Boolean | false |  データが営業日のみ有効かどうかを示します。 既定値は `false` です。|


* すべての座標値は、`WGS84` で定義されているように、[経度, 緯度] として表されます。
* `MultiPoint`、`MultiLineString`、`MultiPolygon`、または `GeometryCollection` を含む各フィーチャーでは、プロパティがすべての要素に適用されます。 次に例を示します。`MultiPoint` 内のすべてのポイントでは、同じ radius を使用して、複数の円ジオフェンスが形成されます。
* ポイントと円のシナリオでは、「[GeoJSON ジオメトリの拡張](https://docs.microsoft.com/azure/azure-maps/extend-geojson)」で詳しく説明されているプロパティを含む `Point` geometry オブジェクトを使用して、円のジオメトリを表すことができます。      

中心点と半径を使用して `GeoJSON` 内の円ジオフェンス ジオメトリとして表されたジオフェンスに対する要求本文のサンプルを次に示します。 ジオフェンス データの有効期間は、2018 年 10 月 22 日に開始し (午前 9 時から午後 5 時まで)、週末を除いて毎日繰り返されます。 `expiredTime` は、要求の `userTime` が `2019-01-01` より後の場合、このジオフェンス データは期限切れと見なされることを示します。  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
