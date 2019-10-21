---
title: Azure IoT Central アプリケーションに実デバイスを追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションに実デバイスを追加します。
author: sandeeppujar
ms.author: sandeepu
ms.date: 08/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 2673d0d2c1cb174316e99a79a10a67347e2bc031
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001354"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Central アプリケーションに実デバイスを追加する

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

このチュートリアルでは、"*実デバイス*" を自分の Microsoft Azure IoT Central アプリケーションに追加して構成する方法について説明します。 このチュートリアルでは、Node.js を使用して実デバイスをコーディングし、そのコードをデスクトップ マシンで実行します。 このチュートリアルを完了するのに、Raspberry Pi や MXChip IoT DevKit デバイスなどの個別の IoT デバイスは必要ありません。

このチュートリアルは次の 2 つの部分で構成されています。

* まず、オペレーター向けに、Azure IoT Central アプリケーションで実デバイスを追加して構成する方法について説明します。 この部分の最後では、2 つ目の部分で使用する接続文字列を取得します。
* 次に、デバイス開発者向けに、実デバイスのコードについて説明します。 最初の部分で取得した接続文字列をサンプル コードに追加します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい実デバイスの追加
> * 実デバイスの構成
> * アプリケーションからの実デバイスの接続文字列の取得
> * クライアント コードをアプリケーションにマッピングする方法について
> * 実デバイスのクライアント コードの構成

## <a name="prerequisites"></a>前提条件

始める前に、作成者が少なくとも Azure IoT Central アプリケーションを作成する最初の作成者向けチュートリアルを完了する必要があります。[新しいデバイスの種類を定義する](tutorial-define-device-type.md) (必須)

開発用コンピューターに [Node.js](https://nodejs.org/) バージョン 8.0.0 以降をインストールします。 バージョンを確認するには、コマンド ラインで `node --version` を実行できます。 Node.js は、さまざまなオペレーティング システムで使用できます。

## <a name="add-a-real-device"></a>実デバイスの追加

実デバイスをアプリケーションに追加するには、「[新しいデバイスの種類を定義する](tutorial-define-device-type.md)」チュートリアルで作成した **Connected Air Conditioner** デバイス テンプレートを使用します。

1. オペレーターが新しいデバイスを追加するには、左側のナビゲーション メニューで **[デバイス]** を選択します。

   ![Connected Air Conditioner が表示されている Device Explorer のページ](media/tutorial-add-device/explorer.png)

   **デバイス エクスプローラー**に、**Connected Air Conditioner** デバイス テンプレートとシミュレートされたデバイスが表示されます。 デバイス テンプレートを作成すると、IoT Central によってシミュレートされたデバイスが自動的に作成されます。

2. **Connected Air Conditioner** デバイス テンプレートが**デバイス エクスプローラー**で選択されていることに注意してください。 このテンプレートを使用する空調機の実デバイスの接続を開始するには、 **[+]** を選択してから **[Real]\(実デバイス\)** を選択します。

   ![コネクテッド空調機の新しい実デバイスの追加の開始](media/tutorial-add-device/newreal.png)

3. 自分の**デバイス ID** (小文字のみ) を入力するか、推奨される値を使用します。 新しいデバイスの **[デバイス名]** を入力して、 **[作成]** を選択することもできます。

   ![デバイス名の変更](media/tutorial-add-device/rename.png)

## <a name="configure-a-real-device"></a>実デバイスの構成

実デバイスは、**Connected Air Conditioner** デバイス テンプレートから作成されます。 **[設定]** を使用して、デバイスを構成し、デバイスに関する情報を記録するプロパティ値を設定できます。

1. **[設定]** ページで、 **[Set Temperature]** 設定の状態が**更新なし**になっていることに注目してください。 実デバイスがアプリケーションに接続し、設定に従って動作していることを確認するまで、この状態のままになります。

    ![同期状態が表示されている [設定]](media/tutorial-add-device/settingssyncing.png)

2. 新しい実デバイスの **[プロパティ]** ページには、点検の場所と前回の点検日のプロパティがあります。どちらのプロパティも編集することができます。 シリアル番号フィールドとファームウェア バージョン フィールドは、デバイスがアプリケーションに接続されるまで空になります。 これらの読み取り専用の値はデバイスから送信されるものであり、編集することはできません。

    ![実デバイスのプロパティ](media/tutorial-add-device/setproperties1.png)

3. 実デバイスの **[Measurements]\(測定\)** 、 **[ルール]** 、 **[ダッシュボード]** の各ページを確認できます。

## <a name="prepare-the-client-code"></a>クライアント コードの準備

この記事のサンプル コードは [Node.js](https://nodejs.org/) で記述されており、次の用途で使用されるデバイス用の十分なコードを示しています。

* Azure IoT Central アプリケーションに接続する。
* コネクテッド空調デバイスとして温度テレメトリを送信する。
* Azure IoT Central アプリケーションにデバイス プロパティを送信する。
* **Set Temperature** 設定を使用するオペレーターに応答する。
* Azure IoT Central アプリケーションからの Echo コマンドを処理する。

「[次の手順](#next-steps)」セクションに示されている記事には、より完全なサンプルが含まれ、他のプログラミング言語が紹介されています。 Azure IoT Central へのデバイスの接続方法の詳細については、[デバイス接続](concepts-connectivity.md)に関する記事を参照してください。

以下の手順は、[Node.js](https://nodejs.org/) サンプルを準備する方法を示しています。

### <a name="get-the-device-connection-information"></a>デバイスの接続情報を取得する

1. アプリケーションにおけるデバイス インスタンスの接続文字列は、IoT Central によって提供されたデバイス情報から生成されます。

   コネクテッド空調機の実デバイスの [デバイス] 画面で、 **[接続]** を選択します。

   ![接続情報の表示リンクが表示されている [デバイス] ページ](media/tutorial-add-device/connectionlink.png)

1. **[デバイス接続]** ページで、 **[スコープ ID]** 、 **[デバイス ID]** 、 **[主キー]** の値をメモします。 これらの値を、このチュートリアルで後ほど使用します。

   ![接続の詳細](media/tutorial-add-device/device-connect.png)

### <a name="prepare-the-nodejs-project"></a>Node.js プロジェクトを準備する

1. 開発用コンピューターに `connectedairconditioner` という名前のフォルダーを作成します。

1. コマンド ライン環境で、作成した `connectedairconditioner` フォルダーに移動します。

1. Node.js プロジェクトを初期化するために、次のコマンドを実行し、既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
      ```

1. 必要なパッケージをインストールするために、次のコマンドを実行します。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. テキスト エディターを使用して、`connectedairconditioner` フォルダーに **ConnectedAirConditioner.js** というファイルを作成します。

1. **ConnectedAirConditioner.js** ファイルの先頭に次の `require` ステートメントを追加します。

    ```javascript
    'use strict';

    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. このファイルに次の変数宣言を追加します。 プレースホルダーの `{your Scope ID}`、`{your Device ID}`、`{your Primary Key}` は、先ほどメモしたデバイスの接続情報に置き換えてください。

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;
    var targetTemperature = 0;
    ```

1. これまでに行った変更を保存しますが、ファイルは開いたままにしておきます。

## <a name="review-client-code"></a>クライアント コードのレビュー

前のセクションでは、Azure IoT Central アプリケーションに接続するデバイス アプリケーションのスケルトン Node.js プロジェクトを作成しました。 次の手順では、次の処理を行うためのコードを追加します。

* Azure IoT Central アプリケーションに接続する。
* Azure IoT Central アプリケーションにテレメトリを送信する。
* Azure IoT Central アプリケーションにデバイス プロパティを送信する。
* Azure IoT Central アプリケーションから設定を受信する。
* Azure IoT Central アプリケーションからの Echo コマンドを処理する。

1. Azure IoT Central アプリケーションに温度テレメトリを送信するために、**ConnectedAirConditioner.js** ファイルに次のコードを追加します。

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    送信する JSON のフィールド名は、デバイス テンプレートの温度テレメトリに指定したフィールド名と一致する必要があります。 この例では、フィールド名は **temperature** です。

1. **firmwareVersion** や **serialNumber** などのデバイス プロパティを送信するには、次の定義を追加します。

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) =>
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. **setTemperature** など、デバイスがサポートする設定を定義するために、次の定義を追加します。

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Azure IoT Central から送信された設定を処理するために、適切なデバイス コードを見つけて実行する次の関数を追加します。

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

    この関数では、次の処理を実行します。

    * Azure IoT Central が目的のプロパティを送信するのを監視する。
    * 適切な関数を見つけて、呼び出し、設定の変更を処理する。
    * Azure IoT Central アプリケーションに確認応答を返送する。

1. Azure IoT Central アプリケーションから **echo** などのコマンドに応答するには、次の定義を追加します。

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        hubClient.onDeviceMethod('echo', onCommandEcho);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            sendDeviceProperties(twin);

            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };
    ```

1. デバイスを登録して IoT Central アプリケーションに接続します。

    ```javascript
    // Start the device (connect it to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log("error registering device: " + err);
      } else {
        console.log('registration succeeded');
        console.log('assigned hub=' + result.assignedHub);
        console.log('deviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

1. 変更を保存します。

## <a name="run-the-client-code"></a>クライアント コードを実行する

クライアント コードを実行する準備が整いました。IoT Central アプリケーションと対話するようすを確認してみましょう。

1. サンプルを実行するために、コマンド ライン環境で次のコマンドを入力します。

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > このコマンドを実行するときは、`connectedairconditioner` フォルダーにいることを確認してください。

1. アプリケーションからコンソールに次の出力が生成されます。

   ![クライアント アプリケーションの出力](media/tutorial-add-device/output.png)

1. 約 30 秒後に、デバイスの **[Measurements]\(測定\)** ページにテレメトリが表示されます。

   ![実際のテレメトリ](media/tutorial-add-device/realtelemetry.png)

1. **[設定]** ページでは、設定が同期されていることを確認できます。 デバイスは、初めて接続したときに、設定値を受け取り、変更を確認しました。

   ![設定の同期](media/tutorial-add-device/settingsynced.png)

1. **[設定]** ページで、デバイスの温度を **95** に設定し、 **[Update device]\(デバイスの更新\)** を選択します。 サンプル アプリケーションがこの変更を受け取り、処理します。

   ![設定の受信と処理](media/tutorial-add-device/receivesetting.png)

   > [!NOTE]
   > "setting update" メッセージが 2 つあります。 1 つは `pending` 状態が送信されたとき、もう 1 つは `completed` 状態が送信されたときです。

1. **[Measurements]\(測定\)** ページでは、デバイスがより高い温度値を送信していることを確認できます。

    ![高くなっている温度テレメトリ](media/tutorial-add-device/highertemperature.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="nextstepaction"]
> * 新しい実デバイスの追加
> * 新しいデバイスの構成
> * アプリケーションからの実デバイスの接続文字列の取得
> * クライアント コードをアプリケーションにマッピングする方法について
> * 実デバイスのクライアント コードの構成

ここでは、実デバイスを Azure IoT Central アプリケーションに接続しました。推奨される次の手順は以下のとおりです。

オペレーターは、以下の方法を学ぶことができます。

* [デバイスの管理](howto-manage-devices.md)
* [デバイス セットを使用する](howto-use-device-sets.md)
* [カスタム分析を作成する](howto-use-device-sets.md)

デバイス開発者は、以下の方法を学ぶことができます。

* [DevKit デバイスを準備して接続する (C)](howto-connect-devkit.md)
* [Raspberry Pi を準備して接続する (Python)](howto-connect-raspberry-pi-python.md)
* [Raspberry Pi を準備して接続する (C#)](howto-connect-raspberry-pi-csharp.md)
* [Windows 10 IoT Core デバイスを準備して接続する (C#)](howto-connect-windowsiotcore.md)
* [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
