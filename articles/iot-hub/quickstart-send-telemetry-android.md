---
title: Azure IoT Hub への利用統計情報の送信に関するクイック スタート (Android) | Microsoft Docs
description: このクイック スタートでは、サンプルの Android アプリケーションを実行して、シミュレートされた利用統計情報を IoT ハブに送信し、それをクラウド上で処理するために IoT ハブから読み取ります。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/15/2019
ms.author: wesmc
ms.openlocfilehash: e213a92397240f2646ceda30688ecef422cdf29c
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166368"
---
# <a name="quickstart-send-iot-telemetry-from-an-android-device"></a>クイック スタート: IoT の利用統計情報を Android デバイスから送信する

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub は、保管や処理のために IoT デバイスから大量の利用統計情報をクラウドに取り込むことを可能にする Azure サービスです。 このクイック スタートでは、物理デバイスまたはシミュレートされたデバイス上で実行されている Android アプリケーションから IoT ハブに利用統計情報を送信します。

このクイック スタートでは、あらかじめ作成されている Android アプリケーションを使って利用統計情報を送信します。 Azure Cloud Shell を使用して、利用統計情報を IoT ハブから読み取ります。 このアプリケーションを実行する前に、IoT ハブを作成し、デバイスをハブに登録します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* Android Studio (https://developer.android.com/studio/ )。 Android Studio のインストールの詳細については、[Android のインストール](https://developer.android.com/studio/install)に関するページを参照してください。

* この記事のサンプルでは、Android SDK 27 を使用しています。

* 次のコマンドを実行して、Microsoft Azure IoT Extension for Azure CLI を Cloud Shell インスタンスに追加します。 IoT Hub、IoT Edge、IoT Device Provisioning Service (DPS) 固有のコマンドが Azure CLI に追加されます。

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* このクイック スタートで実行する[サンプル Android アプリケーション](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample)は、GitHub 上の azure-iot-samples-java リポジトリに含まれています。 [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリをダウンロードまたは複製してください。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>デバイスの登録

デバイスを IoT ハブに接続するには、あらかじめ IoT ハブに登録しておく必要があります。 このクイック スタートでは、Azure Cloud Shell を使用して、シミュレートされたデバイスを登録します。

1. Azure Cloud Shell で次のコマンドを実行してデバイス ID を作成します。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

   **MyAndroidDevice**: これは、登録するデバイスの名前です。 示されているように、**MyAndroidDevice** を使用することをお勧めします。 デバイスに別の名前を選択した場合は、この記事全体でその名前を使用する必要があります。また、サンプル アプリケーションを実行する前に、アプリケーション内のデバイス名を更新してください。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyAndroidDevice
    ```

2. Azure Cloud Shell で次のコマンドを実行して、登録したデバイスの "_デバイス接続文字列_" を取得します。

    **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyAndroidDevice --output table
    ```

    次のようなデバイス接続文字列をメモしておきます。

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    この値は、このクイックスタートの後の方で利用統計情報を送信するために使用します。

## <a name="send-simulated-telemetry"></a>シミュレートされた利用統計情報の送信

1. GitHub のサンプル Android プロジェクトを Android Studio で開きます。 このプロジェクトは、複製またはダウンロードした [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java) リポジトリのコピーの次のディレクトリにあります。

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. Android Studio でサンプル プロジェクトの *gradle.properties* を開き、**Device_Connection_String** プレースホルダーを、先ほどメモした自分のデバイスの接続文字列に置き換えます。

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. Android Studio で、 **[File]\(ファイル\)**  >  **[Sync Project with Gradle Files]\(プロジェクトを Gradle ファイルと同期\)** の順にクリックします。 ビルドが完了したことを確認します。

   > [!NOTE]
   > プロジェクトの同期に失敗した場合、次のいずれかの理由が原因として考えられます。
   >
   > * プロジェクトで参照されている Android Gradle プラグインと Gradle のバージョンが、ご使用の Android Studio のバージョンに対して古い。 [こちらの手順](https://developer.android.com/studio/releases/gradle-plugin)に従って、実際の環境に合った正しいバージョンのプラグインと Gradle を参照、インストールしてください。
   > * Android SDK のライセンス契約に署名していない。 ビルド出力に書かれている手順に従ってライセンス契約に署名し、SDK をダウンロードしてください。

4. ビルドが完了したら、 **[Run]\(実行\)**  >  **[Run 'app']\(<アプリ> の実行\)** をクリックします。 物理 Android デバイスまたは Android Emulator 上で実行されるようにアプリを構成します。 物理デバイスまたはエミュレーター上での Android アプリの実行について詳しくは、「[アプリを実行する](https://developer.android.com/training/basics/firstapp/running-app)」を参照してください。

5. アプリが読み込まれたら、 **[Start]\(開始\)** ボタンをクリックして、IoT ハブへの利用統計情報の送信を開始します。

    ![Application](media/quickstart-send-telemetry-android/sample-screenshot.png)


## <a name="read-the-telemetry-from-your-hub"></a>ハブから利用統計情報を読み取る

このセクションでは、[IoT 拡張機能](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)と共に Azure Cloud Shell を使用して、Android デバイスから送信されるデバイス メッセージを監視します。

1. Azure Cloud Shell を使用して、次のコマンドを実行して接続し、お使いの IoT ハブからのメッセージを読み取ります。

   **YourIoTHubName**: このプレースホルダーは、実際の IoT ハブに対して選んだ名前に置き換えてください。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    次のスクリーンショットは、Android デバイスから送信された利用統計情報を IoT ハブが受信しているときの出力を示しています。

      ![Azure CLI を使用してデバイス メッセージを読み取る](media/quickstart-send-telemetry-android/read-data.png)
## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT ハブを設定して、デバイスを登録しました。さらに、Android アプリケーションを使用して、シミュレートされた利用統計情報をハブに送信し、Azure Cloud Shell を使ってハブから利用統計情報を読み取りました。

バックエンド アプリケーションからシミュレートされたデバイスを制御する方法を学習するには、次のクイック スタートに進んでください。

> [!div class="nextstepaction"]
> [クイック スタート: IoT ハブに接続されたデバイスを制御する](quickstart-control-device-android.md)