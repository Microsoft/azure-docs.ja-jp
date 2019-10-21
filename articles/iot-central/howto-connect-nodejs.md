---
title: 汎用の Node.js クライアント アプリケーションを Azure IoT Central に接続する | Microsoft Docs
description: デバイス開発者として、汎用の Node.js デバイスを Azure IoT Central アプリケーションに接続する方法。
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: ccded68cfaa00e6e13e2bb32e114b81108742829
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71686666"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>汎用のクライアント アプリケーションを Azure IoT Central アプリケーションに接続する (Node.js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

この記事では、デバイス開発者として、実際のデバイスを表す汎用の Node.js アプリケーションを Microsoft Azure IoT Central アプリケーションに接続する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事の手順を完了するには、次のものが必要です。

- Azure IoT Central アプリケーション。 詳細については、[アプリケーションの作成のクイック スタート](quick-deploy-iot-central.md)に関するページをご覧ください。
- [Node.js](https://nodejs.org/) バージョン 4.0.0 以降がインストールされた開発用コンピューター。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。

## <a name="create-a-device-template"></a>デバイス テンプレートを作成する

Azure IoT Central アプリケーションでは、次の測定値、デバイスのプロパティ、設定、およびコマンドが定義されたデバイス テンプレートが必要です。

### <a name="telemetry-measurements"></a>テレメトリ測定

**[Measurements]\(測定)** ページで、次のテレメトリを追加します。

| 表示名 | フィールド名  | Units | Min | Max | 小数点以下の桁数 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 気温  | 温度 | F     | 60  | 110 | 0              |
| 湿度     | 湿度    | %     | 0   | 100 | 0              |
| 圧力     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> テレメトリ測定のデータ型は浮動小数点数です。

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、テレメトリをアプリケーションに表示できません。

### <a name="state-measurements"></a>状態測定

**[Measurements]\(測定)** ページで、次の状態を追加します。

| 表示名 | フィールド名  | 値 1 | 表示名 | 値 2 | 表示名 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| Fan Mode     | fanmode     | 1       | 実行中      | 0       | 停止済み      |

> [!NOTE]
> 状態測定のデータ型は文字列です。

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、状態をアプリケーションに表示できません。

### <a name="event-measurements"></a>イベント測定

**[Measurements]\(測定)** ページで、次のイベントを追加します。

| 表示名 | フィールド名  | 重大度 |
| ------------ | ----------- | -------- |
| 過熱  | overheat    | Error    |

> [!NOTE]
> イベント測定のデータ型は文字列です。

### <a name="location-measurements"></a>場所測定

**[Measurements]\(測定)** ページで、次の場所測定を追加します。

| 表示名 | フィールド名  |
| ------------ | ----------- |
| Location     | location    |

場所測定のデータ型は、経度と緯度用の 2 つの浮動小数点数と、高度用の省略可能な浮動小数点数で構成されています。

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、場所をアプリケーションに表示できません。

### <a name="device-properties"></a>デバイスのプロパティ

**[プロパティ]** ページで、次のデバイスのプロパティを追加します。

| 表示名        | フィールド名        | データ型 |
| ------------------- | ----------------- | --------- |
| Serial Number       | serialNumber      | text      |
| デバイスの製造元 | manufacturer      | text      |

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、プロパティをアプリケーションに表示できません。

### <a name="settings"></a>設定

**[設定]** ページで、次の **[回数]** の設定を追加します。

| 表示名    | フィールド名     | Units | 10 進数 | Min | Max  | Initial |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| ファン速度       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| Set Temperature | setTemperature | F     | 0        | 20  | 200  | 80      |

デバイス テンプレートに、表に示すように正確にフィールド名を入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、デバイスが設定値を取得できません。

### <a name="commands"></a>command

**[コマンド]** ページで次のコマンドを追加します。

| 表示名    | フィールド名     | [Default Timeout]\(既定のタイムアウト\) | データ型 |
| --------------- | -------------- | --------------- | --------- |
| Countdown       | countdown      | 30              | number    |

Countdown コマンドに次の入力フィールドを追加します。

| 表示名    | フィールド名     | データ型 | 値 |
| --------------- | -------------- | --------- | ----- |
| Count from      | countFrom      | number    | 10    |

フィールド名を、表に示されているとおりに正確にデバイス テンプレートに入力します。 フィールド名が対応するデバイス コード内のプロパティ名と一致しない場合は、デバイスはコマンドを処理できません。

## <a name="add-a-real-device"></a>実デバイスの追加

Azure IoT Central アプリケーションで、前のセクションで作成したデバイス テンプレートに実デバイスを追加します。

**[デバイスの接続]** ページで、次のデバイスの接続情報をメモしておきます。**スコープ ID**、**デバイス ID**、**主キー**。 これらの値は、この攻略ガイドで後述するデバイス コードに追加します。

![デバイスの接続情報](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js アプリケーションの作成

次の手順は、アプリケーションに追加した実デバイスを実装するクライアント アプリケーションを作成する方法を示しています。 ここで、Node.js アプリケーションは実際のデバイスを表しています。

1. マシンに `connected-air-conditioner-adv` という名前のフォルダーを作成します。 コマンドライン環境でそのフォルダーに移動します。

1. Node.js プロジェクトを初期化するには、次のコマンドを実行します。

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. `connected-air-conditioner-adv` フォルダー内に **connectedAirConditionerAdv.js** という名前のファイルを作成します。

1. **connectedAirConditionerAdv.js** ファイルの先頭に次の `require` ステートメントを追加します。

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. このファイルに次の変数宣言を追加します。

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    `{your Scope ID}`、`{your Device ID}`、`{your Primary Key}` の各プレースホルダーを、前に書き留めた値で更新します。 このサンプルでは、`targetTemperature` を 0 に初期化します。デバイスから現在の読み取りを取得したり、デバイス ツインから値を取得したりできます。

1. テレメトリ、状態、イベント、および場所測定を Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

1. デバイスのプロパティを Azure IoT Central アプリケーションに送信するには、ファイルに次の関数を追加します。

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. デバイスが応答する設定を定義するには、次の定義を追加します。

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central アプリケーションから更新された設定を処理するには、ファイルに次を追加します。

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. IoT Central アプリケーションから送信された countdown コマンドを処理するために次のコードを追加します。

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
        }
      });
    }
    ```

1. Azure IoT Central への接続を完了し、クライアント コードの関数をフックする次のコードを追加します。

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Node.js アプリケーションを実行する

コマンドライン環境で次のコマンドを実行します。

```cmd/sh
node connectedAirConditionerAdv.js
```

Azure IoT Central アプリケーションのオペレーターとして、実デバイスに関して次のことを実行できます。

* **[Measurements] (測定)** ページで、テレメトリを表示します。

    ![利用統計情報データを表示する](media/howto-connect-nodejs/viewtelemetry.png)

* **[Measurements]\(測定\)** ページで、場所を表示します。

    ![場所測定を表示する](media/howto-connect-nodejs/viewlocation.png)

* **[プロパティ]** ページで、デバイスから送信されたデバイス プロパティ値を表示します。 デバイスを接続すると、デバイスのプロパティ タイルが更新されます。

    ![デバイスのプロパティを表示する](media/howto-connect-nodejs/viewproperties.png)

* **[設定]** ページから、ファン速度とターゲット温度を設定します。

    ![ファン速度を設定する](media/howto-connect-nodejs/setfanspeed.png)

* **[コマンド]** ページから、countdown コマンドを呼び出します。

    ![countdown コマンドを呼び出す](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>次の手順

ここでは、汎用の Node.js クライアントを Azure IoT Central アプリケーションに接続する方法について説明しました。推奨される次の手順は、お使いの IoT デバイス用に[カスタム デバイス テンプレートをセットアップする](howto-set-up-template.md)方法を確認することです。
