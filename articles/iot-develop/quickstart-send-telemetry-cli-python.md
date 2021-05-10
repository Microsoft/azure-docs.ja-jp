---
title: Azure IoT Hub にデバイス テレメトリを送信するクイックスタート (Python)
description: このクイックスタートでは、Python 用 Azure IoT Hub デバイス SDK を使用して、デバイスから IoT ハブにテレメトリを送信します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 03/24/2021
ms.openlocfilehash: ea0b161a9038666e1e7ddd5a6c6af2078afff8aa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766528"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-azure-iot-hub-python"></a>クイックスタート: デバイスから Azure IoT ハブにテレメトリを送信する (Python)

**適用対象**: [デバイス アプリケーション開発](about-iot-develop.md#device-application-development)

このクイックスタートでは、基本的な IoT デバイス アプリケーション開発のワークフローについて説明します。 Azure CLI を使用して Azure IoT ハブとデバイスを作成した後、Azure IoT Python SDK を使用して、シミュレートされたクライアント デバイスを作成し、ハブにテレメトリを送信します。 

## <a name="prerequisites"></a>前提条件
- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)作成してください。
- Azure CLI。 このクイックスタートのすべてのコマンドは、ブラウザーで実行されるインタラクティブ CLI シェルである Azure Cloud Shell を使用して実行できます。 Cloud Shell を使用する場合は、何もインストールする必要はありません。 CLI をローカルで使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.76 以降が必要です。 バージョンを確認するには、az --version を実行します。 インストールまたはアップグレードする必要には、「[Azure CLI のインストール]( /cli/azure/install-azure-cli)」をご覧ください。
- [Python 3.7 以上](https://www.python.org/downloads/)。 サポートされる他のバージョンの Python については、「[Azure IoT デバイスの機能](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features)」を参照してください。
    
    Python のバージョンが最新であることを確認するために `python --version` を実行します。 Python 2 と Python 3 の両方がインストールされた状態で Python 3 環境を使用する場合は、`pip3` を使用して、すべてのライブラリをインストールしてください。 このコマンドによって、Python 3 ランタイムに対するライブラリが確実にインストールされます。
    > [!IMPORTANT]
    > Python インストーラーで、 **[Add Python to PATH]\(PATH に Python を追加する\)** オプションを選択します。 既に Python 3.7 以上がインストールされている場合は、`PATH` 環境変数に Python のインストール フォルダーを追加してあることを確認してください。

[!INCLUDE [iot-hub-include-create-hub-cli](../../includes/iot-hub-include-create-hub-cli.md)]

## <a name="use-the-python-sdk-to-send-messages"></a>Python SDK を使用してメッセージを送信する
このセクションでは、Python SDK を使用して、シミュレートされたデバイスから IoT ハブにメッセージを送信します。

1. 新しいターミナル ウィンドウを開きます。 このターミナルを使用して Python SDK をインストールし、Python のサンプル コードを操作します。 これで 2 つのターミナルを開いたことになります。1 つは Python を操作するために先ほど開いたターミナル、もう 1 つは、前のセクションで Azure CLI コマンドを入力するために使用した CLI シェルです。       

1. [Azure IoT Python SDK デバイス サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)をローカル コンピューターにコピーします。

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```
1. *azure-iot-sdk-python/azure-iot-device/samples/pnp* ディレクトリに移動します。

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples/pnp
    ```
1. Azure IoT Python SDK をインストールします。

    ```console
    pip install azure-iot-device
    ```
1. シミュレートされたデバイスを Azure IoT に接続できるよう、次の両方の環境変数を設定します。
    * `IOTHUB_DEVICE_CONNECTION_STRING` という環境変数を設定します。 変数の値には、前のセクションで保存したデバイス接続文字列を使用します。
    * `IOTHUB_DEVICE_SECURITY_TYPE` という環境変数を設定します。 変数には、リテラル文字列値 `connectionString` を使用します。

    **Windows (cmd)**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    ```
    ```console
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```

    > [!NOTE]
    > Windows CMD では、各変数の文字列値を囲む引用符は入力しません。

    **PowerShell**

    ```azurepowershell
    $env:IOTHUB_DEVICE_CONNECTION_STRING='<your connection string here>'
    ```
    ```azurepowershell
    $env:IOTHUB_DEVICE_SECURITY_TYPE='connectionString'
    ```

    **Bash (Linux または Windows)**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    ```
    ```bash
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

1. 開いている CLI シェルで、[az iot hub monitor-events](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-monitor-events) コマンドを実行し、シミュレートされた IoT デバイス上のイベントの監視を開始します。  イベント メッセージは、到着した時点でターミナルに出力されます。

    ```azurecli
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```

1. インストールされたサンプル ファイル *simple_thermostat.py* のコードを Python ターミナルで実行します。 このコードは、シミュレートされた IoT デバイスにアクセスして、IoT ハブにメッセージを送信します。

    ターミナルから Python サンプルを実行するには:
    ```console
    python ./simple_thermostat.py
    ```
    > [!NOTE]
    > このコード サンプルでは、Azure IoT プラグ アンド プレイを使用しており、手動構成なしでソリューションにスマート デバイスを統合できます。  既定では、このドキュメントのほとんどのサンプルで IoT プラグ アンド プレイが使用されています。 IoT PnP の利点と、それを使用するケースと使用しないケースについて詳しくは、「[IoT プラグ アンド プレイとは](../iot-pnp/overview-iot-plug-and-play.md)」を参照してください。

 Python コードがデバイスから IoT ハブにメッセージを送信すると、イベントを監視している CLI シェルにそのメッセージが表示されます。

```output
Starting event monitor, use ctrl-c to stop...
event:
  component: ''
  interface: dtmi:com:example:Thermostat;1
  module: ''
  origin: <your device name>
  payload:
    temperature: 35
```

デバイスが安全に接続され、Azure IoT Hub にテレメトリを送信するようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートで作成した Azure リソースが不要になった場合は、Azure CLI を使用して削除できます。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。

名前でリソース グループを削除するには、以下の手順を実行します。
1. [az group delete](/cli/azure/group#az_group_delete) コマンドを実行します。 このコマンドにより、作成したリソース グループ、IoT Hub、デバイスの登録が削除されます。

    ```azurecli
    az group delete --name MyResourceGroup
    ```
1. [az group list](/cli/azure/group#az_group_list) コマンドを実行して、リソース グループが削除されていることを確認します。  

    ```azurecli
    az group list
    ```

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、クラウドに対してデバイスを安全に接続し、device-to-cloud テレメトリを送信するための Azure IoT アプリケーションの基本的なワークフローについて説明しました。 Azure CLI を使用して IoT ハブとデバイスを作成した後、Azure IoT Python SDK を使用して、シミュレートされたデバイスを作成し、ハブにテレメトリを送信しました。 

次のステップとして、アプリケーション サンプルを通じて Azure IoT Python SDK を掘り下げて見てみましょう。

- [非同期サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): このディレクトリには、その他の IoT Hub シナリオを想定した非同期 Python サンプルが含まれています。
- [同期サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): このディレクトリには、Python 2.7 (または Python 3.6 以上における同期の互換性シナリオ) 用の Python サンプルが含まれています。
- [IoT Edge サンプル](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): このディレクトリには、Edge モジュールとダウンストリーム デバイスを扱うための Python サンプルが含まれています。
