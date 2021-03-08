---
title: デバイス開発者ガイド (C) - IoT プラグ アンド プレイ | Microsoft Docs
description: C デバイス開発者向けの IoT プラグ アンド プレイの説明
author: rido-min
ms.author: rmpablos
ms.date: 11/19/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
zone_pivot_groups: programming-languages-set-twenty-six
ms.openlocfilehash: 339ae9a0a799a0a03f1fcf37dadf2e1ffa724243
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028684"
---
# <a name="iot-plug-and-play-device-developer-guide"></a>IoT プラグ アンド プレイ デバイス開発者ガイド

IoT プラグ アンド プレイを使用すると、Azure IoT アプリケーションに機能を宣伝するスマート デバイスをビルドできます。 IoT プラグ アンド プレイ デバイスでは、ユーザーが IoT プラグ アンド プレイ対応アプリケーションに接続するときに、手動で構成する必要がありません。

スマート デバイスは、直接実装されているか、[モジュール](../iot-hub/iot-hub-devguide-module-twins.md)を使用しているか、[IoT Edge モジュール](../iot-edge/about-iot-edge.md)を使用している可能性があります。

このガイドでは、[IoT プラグ アンド プレイ規則](../iot-pnp/concepts-convention.md)に従ってデバイス、モジュール、または IoT Edge モジュールを作成するために必要な基本的な手順について説明します。

IoT プラグ アンド プレイ デバイス、モジュール、または IoT Edge モジュールを構築するには、次の手順に従います。

1. デバイスでは、MQTT または MQTT over WebSockets プロトコルを使用して Azure IoT Hub に接続していることを確認します。
1. デバイスを記述する [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) モデルを作成します。 詳細については、[IoT プラグ アンド プレイ モデルのコンポーネントの概要](concepts-components.md)に関するページを参照してください。
1. デバイス接続の一部として `model-id` をアナウンスするようにデバイスまたはモジュールを更新します。
1. [IoT プラグ アンド プレイ規則](concepts-convention.md)を使用してテレメトリ、プロパティ、コマンドを実装します

デバイスまたはモジュールの実装の準備ができたら、[Azure IoT エクスプローラー](howto-use-iot-explorer.md)を使用して、デバイスが IoT プラグ アンド プレイ規則に従っていることを確認します。

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-pnp-device-devguide-c](../../includes/iot-pnp-device-devguide-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-pnp-device-devguide-csharp](../../includes/iot-pnp-device-devguide-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-pnp-device-devguide-java](../../includes/iot-pnp-device-devguide-java.md)]

:::zone-end

:::zone pivot="programming-language-javascript"

[!INCLUDE [iot-pnp-device-devguide-node](../../includes/iot-pnp-device-devguide-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-pnp-device-devguide-python](../../includes/iot-pnp-device-devguide-python.md)]

:::zone-end

## <a name="next-steps"></a>次のステップ

IoT プラグ アンド プレイ デバイスの開発について学習したので、いくつかの追加リソースを次に示します。

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C デバイス SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [モデル コンポーネント](concepts-components.md)
- [DTDL 作成ツールをインストールして使用する](howto-use-dtdl-authoring-tools.md)
- [IoT プラグ アンド プレイ サービス開発者ガイド](concepts-developer-guide-service.md)