---
title: Azure Percept Vision のデータシート
description: 詳細なデバイス仕様については、Azure Percept Vision のデータシートを確認してください
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660523"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision のデータシート

以下に示す仕様は、Azure Percept Vision デバイス用です ([Azure Percept DK](./azure-percept-dk-datasheet.md) に含まれています)。

|製品仕様           |値     |
|--------------------------------|---------------------|
|対象業界               |製造 <br> スマート ビルディング <br> 自動 <br> 小売 |
|主要なシナリオ                  |買い物客分析 <br> 棚在庫 <br> 収縮減少 <br> セキュリティおよび監視 <br> 職場の安全|
|Dimensions                      |42 mm x 42 mm x 40 mm (Azure Percept Vision の SoM アセンブリとハウジング) <br> 42 mm x 42 mm x 6 mm (Vision の SoM チップ)|
|管理コントロール プレーン        |Azure Device Update (ADU)          |
|サポートされているソフトウェアとサービス |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|AI アクセラレーション                 |Intel Movidius Myriad X (MA2085) Vision Processing Unit (VPU) と、統合された Intel Camera ISP、0.7 TOPS |
|センサーと視覚インジケーター   |Omni Vision 5670 カメラ <br> GSO GS8882AA レンズ (解像度: 30FPS で 5MP、距離: 50 cm - 無限大、FoV: 120 度、色: ワイド ダイナミック レンジ、固定フォーカス ローリング シャッター)          |
|カメラ サポート                  |RGB、IR、および深度カメラ <br> 2 つのカメラを同時に実行できます |
|セキュリティ Crypto-Controller      |ST-Micro STM32L462CE      |
|バージョン管理および ID コンポーネント       |64 kb の EEPROM |
|メモリ                          |LPDDR4 2 GB     |
|Power                           |3.5 W     |
|ポート                           |1 x USB 3.0 Type C <br> 2 x MIPI 4 レーン (レーンごとに最大 1.5 Gbps)     |
|コントロール インターフェイス              |2 x I2C <br> 2 x SPI <br> 6 x PWM (GPIO: 2 x クロック、2 x フレーム同期、2 x 未使用) <br> 2 x スペア GPIO |
|認定資格                   |CE <br> FCC      |
|動作温度           |0 から 27°C (Azure Percept Vision の SoM アセンブリとハウジング) <br> -10 から 70°C (Vision の SoM チップ) |
|タッチ温度               |<= 48°C |
|相対湿度               |8% から 90%    |