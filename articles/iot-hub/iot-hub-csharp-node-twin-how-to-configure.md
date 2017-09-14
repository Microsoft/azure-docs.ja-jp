---
title: "Azure IoT Hub デバイス ツインのプロパティの使用 (.NET/Node) | Microsoft Docs"
description: "Azure IoT Hub デバイス ツインを使用してデバイスを構成する方法。 Azure IoT device SDK for Node.js を使用して、シミュレートされたデバイス アプリを実装し、Azure IoT service SDK for .NET を使用して、デバイス ツインを使用するデバイス構成を変更するサービス アプリを実装します。"
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: elioda
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: ca5ee2733df51faa5025c4d8eb687c81df4a3b4f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---
# <a name="use-desired-properties-to-configure-devices"></a>必要なプロパティを使用してデバイスを構成する
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

このチュートリアルの最後には、次の 2 つのコンソール アプリが完成します。

* **SimulateDeviceConfiguration.js**: 必要な構成の更新を待機し、シミュレートされた構成の更新プロセスの状態を報告する、シミュレートされたデバイス アプリです。
* **SetDesiredConfigurationAndQuery**: .NET バックエンド アプリで、デバイス上に目的の構成を設定し、構成更新プロセスをクエリします。

> [!NOTE]
> デバイス アプリとバックエンド アプリ両方の作成に利用できる Azure IoT SDK に関する情報は、「[Azure IoT SDK][lnk-hub-sdks]」の記事で取り上げています。
> 
> 

このチュートリアルを完了するには、以下が必要です。

* Visual Studio 2015 または Visual Studio 2017。
* Node.js バージョン 0.10.x 以降。
* アクティブな Azure アカウント。 アカウントがない場合は、[無料アカウント][lnk-free-trial]を数分で作成することができます。

[デバイス ツインの概要][lnk-twin-tutorial]に関するチュートリアルを行った場合は、既に IoT Hub と、**myDeviceId** というデバイス ID があります。 その場合は、「[シミュレートされたデバイス アプリを作成する]」[lnk-how-to-configure-createapp]セクションをスキップできます。

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>シミュレートされたデバイスのアプリを作成する
このセクションでは、ハブに **myDeviceId** として接続し、必要な構成の更新を待機して、シミュレートされた構成の更新プロセスの情報を報告する、Node.js コンソール アプリを作成します。

1. **simulatedeviceconfiguration** という名前の新しい空のフォルダーを作成します。 **simulatedeviceconfiguration** フォルダー内に、コマンド プロンプトで次のコマンドを使用して新しい package.json ファイルを作成します。 すべての既定値をそのまま使用します。
   
    ```
    npm init
    ```
1. **simulatedeviceconfiguration** フォルダーのコマンド プロンプトに対して、次のコマンドを実行して、**azure-iot-device** と **azure-iot-device-mqtt** パッケージをインストールします。
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
1. テキスト エディターを使用して、**simulatedeviceconfiguration** フォルダーに新しい **SimulateDeviceConfiguration.js** ファイルを作成します。
1. **SimulateDeviceConfiguration.js** ファイルに次のコードを追加し、**{device connection string}** プレースホルダーを、**myDeviceId** のデバイス IDの作成時にコピーしたデバイス接続文字列で置き換えます。
   
        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;
   
        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);
   
        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });
   
    **Client** オブジェクトに、デバイスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 このコードは、**Client** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得して、*必要なプロパティ*の更新のハンドラーをアタッチします。 ハンドラーは、configIds を比較して実際に構成の変更リクエストがあることを確認してから、構成の変更を開始するメソッドを呼び出します。
   
    分かりやすくするため、このコードでは、ハード コーディングされた既定値を初期構成に使用しています。 実際のアプリでは、おそらくローカル ストレージから構成を読み込みます。
   
   > [!IMPORTANT]
   > 目的のプロパティ変更イベントは、デバイス接続時に常に 1 回生成されます。 アクションを実行する前に、必要なプロパティに実際の変更があることを確認してください。
   > 
   > 
1. `client.open()` の呼び出しの前に次のメソッドを追加します。
   
        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";
   
            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }
   
        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
   
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;
   
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };
   
    **initConfigChange** メソッドは、構成の更新要求を使用してローカル デバイス ツイン オブジェクトの報告されるプロパティを更新し、状態を**保留中**に設定し、サービス上のデバイス ツインを更新します。 デバイス ツインを正常に更新できたら、**completeConfigChange** を実行して停止する、長期間実行していたプロセスをシミュレートします。 このメソッドは、ローカルの報告されるプロパティを更新し、状態を**成功**に設定して、**pendingConfig** オブジェクトを削除します。 その後、サービス上のデバイス ツインを更新します。
   
    帯域幅を節約するため、報告されたプロパティの更新は、ドキュメント全体を置き換えるのではなく、変更するプロパティのみを指定して行われます (上のコードでは **patch**)。
   
   > [!NOTE]
   > このチュートリアルでは、同時実行の構成の更新動作はシミュレートしません。 一部の構成更新プロセスでは、更新の実行中に対象の構成に変更を加えることができますが、場合によってはキューに入れる必要があったり、エラー条件で拒否されたりすることがあります。 お使いの構成プロセスに必要な動作を考慮し、構成の変更を開始する前に適切なロジックを追加するようにしてください。
   > 
   > 
1. デバイス アプリを実行する:
   
        node SimulateDeviceConfiguration.js
   
    `retrieved device twin` というメッセージが表示されます。 そのままアプリを実行します。

## <a name="create-the-service-app"></a>サービス アプリケーションを作成する
このセクションでは、新しいテレメトリの構成オブジェクトを使用して、**myDeviceId** に関連付けられたデバイス ツインの*必要なプロパティ*を更新する、.NET コンソール アプリを作成します。 その後このアプリでは、IoT Hub に格納されているデバイス ツインにクエリを実行し、デバイスの必要な構成と報告される構成の違いを示します。

1. Visual Studio で、 **[コンソール アプリケーション]** プロジェクト テンプレートを使用し、Visual C# Windows クラシック デスクトップ プロジェクトを現在のソリューションに追加します。 プロジェクトに **SetDesiredConfigurationAndQuery** という名前を付けます。
   
    ![New Visual C# Windows Classic Desktop project][img-createapp]
1. ソリューション エクスプローラーで **SetDesiredConfigurationAndQuery** プロジェクトを右クリックし、**[NuGet パッケージの管理...]** をクリックします。
1. **[NuGet パッケージ マネージャー]** ウィンドウで **[参照]** を選択し、**microsoft.azure.devices** を検索します。**[インストール]** を選択して **Microsoft.Azure.Devices** パッケージをインストールし、使用条件に同意します。 この手順により、パッケージのダウンロードとインストールが実行され、[Azure IoT service SDK][lnk-nuget-service-sdk] NuGet パッケージへの参照とその依存関係が追加されます。
   
    ![NuGet Package Manager window][img-servicenuget]
1. **Program.cs** ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. **Program** クラスに次のフィールドを追加します。 プレースホルダーの値は、前のセクションで作成したハブの IoT Hub 接続文字列に置き換えてください。
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. **Program** クラスに次のメソッドを追加します。
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            try
            {
                while (true)
                {
                    var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                    var results = await query.GetNextAsTwinAsync();
                    foreach (var result in results)
                    {
                        Console.WriteLine("Config report for: {0}", result.DeviceId);
                        Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                        Console.WriteLine();
                    }
                    Thread.Sleep(10000);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Exception: {ex.Message}");
            }
        }
   
    **Registry** オブジェクトに、サービスからデバイス ツインとやりとりするのに必要なすべてのメソッドが表示されます。 このコードは、**Registry** オブジェクトを初期化し、**myDeviceId** のデバイス ツインを取得してから、新しいテレメトリ構成オブジェクトを使って必要なプロパティを更新します。
    その後、IoT Hub に格納されたデバイス ツインのクエリを 10 秒ごとに実行して、報告された目的のテレメトリ構成を出力します。 すべてのデバイスに関する豊富なレポートを生成する方法については、[IoT Hub のクエリ言語][lnk-query]に関するページを参照してください。
   
   > [!IMPORTANT]
   > このアプリケーションでは、例示を目的として 10 秒ごとに IoT Hub にクエリを実行します。 変更を検出するためではなく、あらゆるデバイスのユーザー向けのレポートを生成するためにクエリを使用します。 ソリューションにデバイス イベントのリアルタイム通知が必要な場合は、[ツイン通知][lnk-twin-notifications]を使用します。
   > 
   > 
1. 最後に、 **Main** メソッドに次の行を追加します。
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery().Wait();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. ソリューション エクスプローラーで、**[スタートアップ プロジェクトの設定...]** を開き、**SetDesiredConfigurationAndQuery** プロジェクトの **[アクション]** が **[開始]** になっていることを確認します。 ソリューションをビルドします。
1. **SimulateDeviceConfiguration.js** を実行した状態で、Visual Studio から **F5** を使用して .NET アプリケーションを実行します。報告される構成が、「**成功**」から「**保留中**」に変わり、24 時間ではなく 5分の新しいアクティブな送信頻度を使って再度「**成功**」に変わります。

 ![正常に構成されたデバイス][img-deviceconfigured]
   
   > [!IMPORTANT]
   > デバイスのレポート操作とクエリの結果までには、最大 1 分間の遅延が生じます。 これは、非常に大きな規模でクエリのインフラストラクチャを動作させるためです。 1 つのデバイス ツインの一貫したビューを取得するには、**Registry** クラスで **getDeviceTwin** メソッドを使用します。
   > 
   > 

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、必要な構成を*必要なプロパティ*としてソリューション バックエンドから設定し、その変更を検出して、報告されるプロパティを通してその状態を報告するマルチステップ更新プロセスをシミュレートするデバイス アプリを記述しました。

詳細については、次のリソースをご覧ください。

* [IoT Hub の概要][lnk-iothub-getstarted]に関するチュートリアルでデバイスからテレメトリを送信する。
* 多数のデバイスで操作をスケジュールまたは実行するには、[ジョブのスケジュールとブロードキャスト][lnk-schedule-jobs]に関するチュートリアルを参照してください。
* [ダイレクト メソッドの使用][lnk-methods-tutorial]に関するチュートリアルで、デバイスを対話形式で制御する (ユーザー制御アプリからファンをオンにするなど)。

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-node-twin-how-to-configure/deviceconfigured.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-csharp-node-twin-how-to-configure.md#create-the-simulated-device-app

