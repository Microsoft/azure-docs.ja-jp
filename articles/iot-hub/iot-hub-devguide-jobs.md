---
title: "Azure IoT Hub ジョブについて | Microsoft Docs"
description: "開発者ガイド - IoT Hub に接続されている複数のデバイスで実行するジョブのスケジュール設定。 ジョブはタグと必要なプロパティを更新でき、複数のデバイス上でダイレクト メソッドを呼び出すことができます。"
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: 
ms.assetid: fe78458f-4f14-4358-ac83-4f7bd14ee8da
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: juanpere
translationtype: Human Translation
ms.sourcegitcommit: eac5027f39d15e355d1c9f60a31ded567272d5fa
ms.openlocfilehash: b94ceac2298509817020b32b65125c5f767d8089
ms.lasthandoff: 12/16/2016


---
# <a name="schedule-jobs-on-multiple-devices"></a>複数デバイスでのジョブをスケジュール設定する
## <a name="overview"></a>Overview
前の記事の説明のとおり、Azure IoT Hub では、多数の構成要素 ([デバイス ツインのプロパティとタグ][lnk-twin-devguide]および[ダイレクト メソッド][lnk-dev-methods]) を使用できます。  通常、デバイス管理者とオペレーターは、バックエンド アプリを使用して、IoT デバイスの更新と対話を、指定した時刻に一括で実行できます。  ジョブは、指定した時刻にデバイスに対して実行されるデバイス ツインの更新とダイレクト メソッドの実行をカプセル化します。  たとえば、オペレーターは、ビル 43 の 3 階にあるデバイスを、ビルの運用に悪影響を与えることがない時刻に再起動するジョブを開始して追跡するバックエンド アプリを使用できます。

### <a name="when-to-use"></a>使用時の注意
ジョブは、ソリューションのバックエンドで、一連のデバイスで実行される次のアクティビティのスケジュールを設定し、その進行状況を追跡する必要があるときに使用を検討します。

* 必要なプロパティを更新する
* タグを更新する
* ダイレクト メソッドを呼び出す

## <a name="job-lifecycle"></a>ジョブのライフサイクル
ジョブは、ソリューションのバック エンドによって開始され、IoT Hub によって管理されます。  ジョブは、サービス向け URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-11-14`) を通して開始でき、実行中のジョブの進行状況は、サービス向け URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-11-14`) を通して照会できます。  ジョブの開始後、バックエンド アプリは、ジョブを照会することで、実行中のジョブの状態を更新できます。

> [!NOTE]
> ジョブを呼び出すとき、プロパティ名と値には ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}`` を除く US-ASCII 印刷可能英数字のみを使用できます。
> 
> 

## <a name="reference-topics"></a>参照トピック:
以下の参照トピックは、ジョブの使用に関する詳細情報を提供しています。

## <a name="jobs-to-execute-direct-methods"></a>ダイレクト メソッドを実行するジョブ
ジョブを使用してデバイス上で[ダイレクト メソッド][lnk-dev-methods]を実行するための HTTP 1.1 要求の詳細を次に示します。

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            responseTimeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
次に示すように、クエリ条件は&1; つのデバイス ID にもデバイス ID のリストにも設定できます。

**例**
```
queryCondition = "deviceId = 'MyDevice1'"
queryCondition = "deviceId IN ['MyDevice1','MyDevice2']"
queryCondition = "deviceId IN ['MyDevice1']
```
IoT Hub クエリ言語の詳細については、[IoT Hub クエリ言語][lnk-query]に関するページをご覧ください。

## <a name="jobs-to-update-device-twin-properties"></a>デバイス ツインのプロパティを更新するジョブ
ジョブを使用してデバイス ツインのプロパティを更新するための HTTP 1.1 要求の詳細を次に示します。

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-11-14
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // query condition
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>ジョブの進行状況の照会
[ジョブを照会する][lnk-query]ための HTTP 1.1 要求の詳細を次に示します。

    ```
    GET /jobs/v2/query?api-version=2016-11-14[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```

ContinuationToken は、応答から提供されます。  

## <a name="jobs-properties"></a>ジョブのプロパティ
ジョブまたはジョブの結果を照会するときに使用できるプロパティとその説明の一覧を次に示します。

| プロパティ | 説明 |
| --- | --- |
| **jobId** |アプリケーションが提供するジョブの ID。 |
| **startTime** |アプリケーションが提供するジョブの開始時刻 (ISO&8601;)。 |
| **endTime** |IoT Hub が提供するジョブの完了時の日付 (ISO&8601;)。 ジョブが 'completed' 状態に達した後でのみ有効です。 |
| **type** |ジョブの種類: |
| **scheduledUpdateTwin**: 必要なプロパティまたはタグを更新するために使用するジョブ。 | |
| **scheduledDeviceMethod**: デバイス ツインでデバイス メソッドを呼び出すために使用するジョブ。 | |
| **状態** |ジョブの現在の状態。 状態の可能値: |
| **pending**: スケジュールが設定され、ジョブ サービスによって選択されるために待機しています。 | |
| **scheduled**: 将来の時刻のスケジュールが設定されています。 | |
| **running**: 現在アクティブなジョブです。 | |
| **cancelled**: ジョブは取り消されています。 | |
| **failed**: ジョブは失敗しました。 | |
| **completed**: ジョブは完了しています。 | |
| **deviceJobStatistics** |ジョブの実行に関する統計情報。 |

**deviceJobStatistics** プロパティ。

| プロパティ | 説明 |
| --- | --- |
| **deviceJobStatistics.deviceCount** |ジョブ内のデバイスの数。 |
| **deviceJobStatistics.failedCount** |ジョブが失敗したデバイスの数。 |
| **deviceJobStatistics.succeededCount** |ジョブが成功したデバイスの数。 |
| **deviceJobStatistics.runningCount** |現在ジョブが実行中であるデバイスの数。 |
| **deviceJobStatistics.pendingCount** |現在ジョブの実行が保留されているデバイスの数。 |

### <a name="additional-reference-material"></a>参考資料
IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-endpoints]: 各 IoT Hub でランタイムと管理の操作のために公開される、さまざまなエンドポイントについて説明します。
* [調整とクォータ][lnk-quotas]: IoT Hub サービスに適用されるクォータと、サービスを使用するときに想定される調整の動作について説明します。
* [Azure IoT device SDK とサービス SDK][lnk-sdks]: IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。
* [IoT Hub のツインおよびジョブ向けのクエリ言語][lnk-query]: IoT Hub からデバイス ツインおよびジョブに関する情報を取得する際に使用できる IoT Hub のクエリ言語について説明します。
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]: IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ
この記事で説明した概念を試す場合は、次の IoT Hub のチュートリアルをご利用ください。

* [Schedule and broadcast jobs (ジョブのスケジュールとブロードキャスト)][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-node-node-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-node-node-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md

