---
title: "Azure IoT SDK について | Microsoft Docs"
description: "開発者ガイド - デバイス アプリやバックエンド アプリを構築するために使用できる、さまざまな Azure IoT device SDK およびサービス SDK に関する情報とリンク。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: bcbf4b9633f58293edb19aeb33dec6602ac4ec8f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="understand-and-use-azure-iot-sdks"></a>Azure IoT SDK の理解と使用

IoT Hub の操作には、3 つのカテゴリの SDK があります。

* **Device SDK** では、IoT デバイス上で実行するアプリを構築できます。 これらのアプリは IoT Hub にテレメトリを送信し、必要に応じて IoT Hub からメッセージを受信します。

* **Service SDK** では、IoT Hub を管理でき、必要に応じて IoT デバイスにメッセージを送信します。

* **Azure IoT Edge** では、サポートされているプロトコルのいずれも使用しないデバイスを有効にするために、またはエッジのメッセージを処理する必要がある場合に、ゲートウェイを構築します。

SDK は、複数のプログラミング言語をサポートするように提供されています。

## <a name="azure-iot-device-sdks"></a>Azure IoT device SDK

Microsoft Azure IoT device SDK には、Azure IoT Hub サービスに接続および管理されるデバイスやアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT device SDK は GitHub からダウンロードできます。

* [C 用 Azure IoT device SDK。][lnk-c-device-sdk]移植性と広範なプラットフォーム互換性を得るために、ANSI C (C99) で作成されています。 C のデバイス クライアント ライブラリには、低レベルの **iothub_client** と **serializer** の 2 つがあります。
* [.NET 用 Azure IoT device SDK][lnk-dotnet-device-sdk]
* [Java 用 Azure IoT device SDK][lnk-java-device-sdk]
* [Node.js 用 Azure IoT device SDK][lnk-node-device-sdk]
* [Python 用 Azure IoT device SDK][lnk-python-device-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>OS プラットフォームとハードウェアの互換性

特定のハードウェア デバイスとの SDK の互換性の詳細については、[Azure Certified for IoT デバイス カタログ][lnk-certified]を参照してください。

## <a name="azure-iot-service-sdks"></a>Azure IoT サービス SDK

Azure IoT サービス SDK には、IoT Hub と直接やりとりしてデバイスやセキュリティを管理するアプリケーションの作成を容易にするコードが含まれています。

次の Azure IoT サービス SDK は GitHub からダウンロードできます。

* [.NET 用 Azure IoT サービス SDK][lnk-dotnet-service-sdk]
* [Node.js 用 Azure IoT サービス SDK][lnk-node-service-sdk]
* [Java 用 Azure IoT サービス SDK][lnk-java-service-sdk]
* [Python 用 Azure IoT サービス SDK][lnk-python-service-sdk]
* [C 用 Azure IoT サービス SDK][lnk-c-service-sdk]

> [!NOTE]
> 言語およびプラットフォームに固有のパッケージ マネージャーを使用して、バイナリと依存関係を開発用コンピューターにインストールする方法については、GitHub リポジトリの readme ファイルを参照してください。

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge には、IoT ゲートウェイ ソリューションを作成するためのインフラストラクチャとモジュールが含まれています。 IoT Edge を拡張して、エンド ツー エンドのシナリオに合わせて調整されたゲートウェイを作成できます。

[Azure IoT Edge][lnk-iot-edge] は、GitHub からダウンロードできます。

## <a name="online-api-reference-documentation"></a>オンライン API リファレンス ドキュメント

Azure IoT デバイス、サービス、およびゲートウェイ ライブラリのオンライン API リファレンス ドキュメントへのリンクの一覧を次に示します。

* [Internet of Things (IoT) .NET (モノのインターネット (IoT) .NET)][lnk-dotnet-ref]
* [IoT Hub REST][lnk-rest-ref]
* [C 用 Azure IoT device SDK][lnk-c-ref]
* [Java 用 Azure IoT device SDK][lnk-java-ref]
* [Java 用 Azure IoT サービス SDK][lnk-java-service-ref]
* [Node.js 用 Azure IoT device SDK][lnk-node-ref]
* [Node.js 用 Azure IoT サービス SDK][lnk-node-service-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>次のステップ

この IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント][lnk-devguide-endpoints]
* [デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query]
* [クォータと調整][lnk-devguide-quotas]
* [IoT Hub の MQTT サポート][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

