---
title: Azure Percept DK の概要
description: Azure Percept DK の詳細情報
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 4fd0a7cb575a109d1393527b48de3fa4e3446167
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660703"
---
# <a name="azure-percept-dk-overview"></a>Azure Percept DK の概要

Azure Percept DK は、ビジョン AI 概念実証のために設計されたエッジ AI 開発キットです。 [Azure Percept Studio](./overview-azure-percept-studio.md) と組み合わせることで、さまざまなビジョン AI アプリケーション用のエッジ AI ソリューションを構築するための強力で、非常に使いやすいプラットフォームになります。 [Microsoft オンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で購入できます。

:::image type="content" source="./media/overview-azure-percept-dk/dk-image.png" alt-text="画像。":::

## <a name="key-features"></a>主な機能

- **AI をエッジで実行する機能**。 組み込みのハードウェア アクセラレータを使用すると、クラウドに接続しなくても、ビジョン AI モデルを実行できます。
- **信頼のハードウェア ルート セキュリティを組み込み**。 詳細については、[Azure Percept のセキュリティ](./overview-percept-security.md)の概要を参照してください。
- **[Azure Percept Studio](./overview-azure-percept-studio.md)** およびその他の Azure サービスとシームレスに統合。 Azure IoT Hub、Azure Cognitive Services、[Live Video Analytics](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/overview) など
- **主要な AI プラットフォームをサポート**。 ONNX や TensorFlow など。
- **80/20 レーリング システムとの統合**。 運用環境でプロトタイプを簡単に構築できます。 [8/20 統合](./overview-8020-integration.md)について、詳しくはこちらをご覧ください。

## <a name="hardware-components"></a>ハードウェア コンポーネント

- Azure Percept DK 通信事業者ボード
    - NXP iMX8m プロセッサ
    - トラステッド プラットフォーム モジュール (TPM) バージョン 2.0
    - WiFi および Bluetooth 接続
    - [データシート](./azure-percept-dk-datasheet.md)全体をご覧ください
- Azure Percept Vision システム オン モジュール (SoM)
    - Intel Movidius Myriad X (MA2085) ビジョン処理ユニット (VPU)
    - 2 つ目を追加する機能を備えた RGB カメラ センサー
    - [データシート](./azure-percept-vision-datasheet.md)全体をご覧ください

## <a name="get-started-with-the-azure-percept-dk"></a>Azure Percept DK の使用を開始する

- クイック スタートを完了する
    - [Azure Percept DK を箱から取り出して組み立てる](./quickstart-percept-dk-unboxing.md)
    - [Azure Percept DK を設定し、最初のビジョン AI モデルを実行する](./quickstart-percept-dk-set-up.md)
- これらのチュートリアルを使用して、概念実証の構築を開始する
    - [Azure Percept Studio でコードなしのビジョン ソリューションを作成する](./tutorial-nocode-vision.md)
    - [Azure Percept Studio で音声アシスタントを作成する](./tutorial-no-code-speech.md)

## <a name="next-steps"></a>次のステップ

[Microsoft のオンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で Azure Percept DK を注文します。