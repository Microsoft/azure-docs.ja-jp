---
title: クイック スタート:Java を使用して Azure IoT Hub からデバイスを制御する
description: このクイック スタートでは、2 つのサンプル Java アプリケーションを実行します。 1 つのアプリケーションは、ハブに接続されたデバイスをリモートで制御できるバックエンド アプリケーションです。 もう 1 つのアプリケーションは、ハブに接続されたリモートで制御できるデバイスをシミュレートします。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019, seo-java-september2019
ms.date: 06/21/2019
ms.openlocfilehash: f59a3409d508c63f232294d8d66ade5669815b3c
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71843337"
---
# <a name="quickstart-control-a-device-connected-to-an-azure-iot-hub-with-java"></a>クイック スタート:Azure IoT ハブに接続されたデバイスを Java で制御する

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

このクイックスタートでは、"*ダイレクト メソッド*" を使って、Azure IoT ハブに接続されているシミュレートされたデバイスを Java アプリケーションで制御します。 ダイレクト メソッドを使うと、IoT ハブに接続されたデバイスの動作をリモートで変更できます。 IoT Hub は、IoT デバイスからクラウドに大量の利用統計情報を取り込み、クラウドからデバイスを管理することができる、Azure サービスです。 

このクイック スタートでは、あらかじめ作成されている次の 2 つの Java アプリケーションを使います。

* シミュレートされたデバイス アプリケーションは、バックエンド アプリケーションから呼び出されたダイレクト メソッドに応答します。 ダイレクト メソッドの呼び出しを受け取るため、このアプリケーションは IoT ハブ上のデバイス固有のエンドポイントに接続します。

* バックエンド アプリケーションは、シミュレートされたデバイスでダイレクト メソッドを呼び出します。 デバイスでダイレクト メソッドを呼び出すため、このアプリケーションは IoT ハブ上のサービス側エンドポイントに接続します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートで実行する 2 つのサンプル アプリケーションは、Java を使って書かれています。 開発用マシン上に Java SE 8 が必要です。

複数のプラットフォーム向けの Java SE Development Kit 8 を「[Azure および Azure Stack の Java 長期サポート](https://docs.microsoft.com/en-us/java/azure/jdk/?view=azure-java-stable)」からダウンロードできます。 JDK 8 のダウンロードを利用するには、「**長期サポート**」の「**Java 8**」を選択します。

開発コンピューターに現在インストールされている Java のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
java -version
```

サンプルをビルドするには、Maven 3 をインストールする必要があります。 複数のプラットフォームに対応する Maven を [Apache Maven](https://maven.apache.org/download.cgi) からダウンロードできます。

開発コンピューターに現在インストールされている Maven のバージョンは、次のコマンドを使って確認できます。

```cmd/sh
mvn --version
```

次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

まだ行っていない場合は、 https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip からサンプル Java プロジェクトをダウンロードし、ZIP アーカイブを抽出します。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-java.md)を完了した場合は、この手順を省略できます。

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

前出の[デバイスから IoT ハブへの利用統計情報の送信に関するクイック スタート](quickstart-send-telemetry-java.md)を完了した場合は、この手順を省略できます。

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyJavaDevice**: 登録するデバイスの名前。 示されているように、**MyJavaDevice** を使用します。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用し、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新する必要があります。

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyJavaDevice \
      --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイック スタートの後の方で使います。

## <a name="retrieve-the-service-connection-string"></a>サービス接続文字列を取得する

また、バックエンド アプリケーションが IoT ハブに接続してメッセージを取得できるようにするには、"_サービス接続文字列_" が必要です。 次のコマンドを実行すると、IoT ハブのサービス接続文字列が取得されます。

**YourIoTHubName**:このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

次のようなサービス接続文字列をメモしておきます。

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

この値は、このクイック スタートの後の方で使います。 サービス接続文字列はデバイス接続文字列とは異なります。

## <a name="listen-for-direct-method-calls"></a>ダイレクト メソッドの呼び出しをリッスンする

シミュレートされたデバイス アプリケーションは、IoT ハブ上のデバイス固有エンドポイントに接続し、シミュレートされた利用統計情報を送信して、ハブからのダイレクト メソッド呼び出しをリッスンします。 このクイック スタートでは、ハブからのダイレクト メソッド呼び出しは、利用統計情報の送信間隔を変更するようデバイスに指示します。 シミュレートされたデバイスでは、ダイレクト メソッドを実行した後、ハブに受信確認が返送されます。

1. ローカル ターミナル ウィンドウで、サンプルの Java プロジェクトのルート フォルダーに移動します。 次に、**iot-hub\Quickstarts\simulated-device-2** フォルダーに移動します。

2. 適当なテキスト エディターで **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** ファイルを開きます。

    `connString` 変数の値を、前にメモしたデバイス接続文字列に置き換えます。 その後、変更を **SimulatedDevice.java** ファイルに保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、シミュレートされたデバイス アプリケーションをビルドします。

    ```cmd/sh
    mvn clean package
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行して、シミュレートされたデバイス アプリケーションを実行します。

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    次のスクリーンショットは、シミュレートされたデバイス アプリケーションが IoT ハブに利用統計情報を送信したときの出力を示しています。

    ![デバイスによって対象の IoT ハブに送信されたテレメトリからの出力](./media/quickstart-control-device-java/iot-hub-application-send-telemetry-output.png)

## <a name="call-the-direct-method"></a>ダイレクト メソッドを呼び出す

バックエンド アプリケーションは、IoT ハブ上のサービス側エンドポイントに接続します。 アプリケーションにより、IoT ハブを通してデバイスへのダイレクト メソッド呼び出しが行われた後、受信確認がリッスンされます。 通常、IoT Hub のバックエンド アプリケーションはクラウドで実行されます。

1. 別のローカル ターミナル ウィンドウで、サンプルの Java プロジェクトのルート フォルダーに移動します。 その後、**iot-hub\Quickstarts\back-end-application** フォルダーに移動します。

2. 適当なテキスト エディターで **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** ファイルを開きます。

    `iotHubConnectionString` 変数の値を、前にメモしたサービス接続文字列に置き換えます。 変更を **BackEndApplication.java** ファイルに保存します。

3. ローカル ターミナル ウィンドウで次のコマンドを実行して、必要なライブラリをインストールし、バックエンド アプリケーションをビルドします。

    ```cmd/sh
    mvn clean package
    ```

4. ローカル ターミナル ウィンドウで次のコマンドを実行して、バックエンド アプリケーションを実行します。

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    次のスクリーンショットは、アプリケーションによりデバイスに対してダイレクト メソッド呼び出しが行われ、受信確認が受診されたときの出力を示します。

    ![アプリケーションが対象の IoT ハブを介してダイレクト メソッド呼び出しを行ったときの出力](./media/quickstart-control-device-java/iot-hub-direct-method-call-output.png)

    バックエンド アプリケーションを実行した後、シミュレートされたデバイスを実行しているコンソール ウィンドウにメッセージが表示され、メッセージの送信速度が変わります。

    ![デバイスからのコンソール メッセージに変更された速度が示される](./media/quickstart-control-device-java/iot-hub-sent-message-change-rate.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、バックエンド アプリケーションからデバイス上のダイレクト メソッドを呼び出し、シミュレートされたデバイス アプリケーションでダイレクト メソッド呼び出しに応答しました。

デバイスからクラウドへのメッセージをクラウド内の異なる宛先にルーティングする方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [チュートリアル: 処理のために利用統計情報を異なるエンドポイントにルーティングする](tutorial-routing.md)
