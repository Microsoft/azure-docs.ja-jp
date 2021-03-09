---
title: Azure Percept AI モデル
description: プロトタイプとデプロイに使用できる AI モデルについての詳細情報
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: template-concept
ms.openlocfilehash: 28a8de231f179cf69342da81e6a2ae1989d2a5d6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041588"
---
# <a name="azure-percept-ai-models"></a>Azure Percept AI モデル

Azure Percept を使用すると、[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) で AI モデルを開発し、Azure Percept DK に直接デプロイすることができます。 モデル デプロイでは、 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) と [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview) が利用されます。

## <a name="sample-ai-models"></a>AI モデルのサンプル

Azure Percept Studio には、次のアプリケーションのサンプル モデルが含まれています。

- 人物検出
- 車両検出
- 一般的な物体検出
- 棚の製品検出

事前トレーニング済みのモデルでは、コーディングやトレーニング データの収集は必要ありません。 ポータルから Azure Percept DK に目的のモデルをデプロイし、devkit のビデオ ストリームを開いて、モデル推論の動作を確認するだけです。 モデル推論テレメトリには、 [Azure IoT エクスプローラー](https://github.com/Azure/azure-iot-explorer/releases) ツールからアクセスすることもできます。

## <a name="pre-built-solutions"></a>事前構築済みのソリューション

また、[人物検出用に構築済みの空間分析ソリューション](https://github.com/george-moore/Santa-Cruz-AI-App)も利用できます。 事前に構築されたソリューションは、ユーザー定義のゾーン エントリ/エグジット イベントを使用してエッジベースの人物カウントを行うオープンソースの AI アプリケーションです。 オンプレミスのエッジデバイスからのビデオと AI の出力は、Azure Web サイトとして実行されているユーザー インターフェイスを使用して [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) にエグレスされます。 AI 推論は、人物検出のためにオープンソースの AI モデルによって提供されます。

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="事前に構築された空間分析ソリューションの gif。":::

## <a name="custom-no-code-solutions"></a>コードなしのカスタム ソリューション

Azure Percept Studio を使用すると、カスタムの[ビジョン](./tutorial-nocode-vision.md)およびスピーチ ソリューションを開発できます。コーディングは必要ありません。

カスタム ビジョン ソリューションでは、物体検出モデルと分類 AI モデルの両方を使用できます。 トレーニング イメージをアップロードしてタグ付けするだけで、必要に応じて、Azure Percept DK の Azure Percept Vision SoM で直接取得できます。 モデルのトレーニングと評価は、 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview) の一部である [Custom Vision](https://www.customvision.ai/) で簡単に実行できます。

カスタム スピーチ ソリューションについては、現在次の用途の音声アシスタント テンプレートを利用できます。

- 接客業: 音声制御スマート デバイスを備えたホテルの部屋。
- 医療機関: 音声制御スマート デバイスを備えた治療施設。
- インベントリ: 音声制御スマート デバイスを備えたインベントリ ハブ。
- 自動車: 音声制御スマート デバイスを備えた自動車ハブ。

事前に構築された音声アシスタントのキーワードとコマンドは、ポータルから直接入手できます。 カスタム キーワードとコマンドは、Azure Cognitive Services の一部でもある [Speech Studio](https://speech.microsoft.com/) で作成およびトレーニングできます。

## <a name="advanced-development"></a>高度な開発

高度な開発者にとって、使用可能な [Jupyter Notebook](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) は、物体検出用のカスタム データセットを備えた事前トレーニング済みの Python で記述された TensorFlow モデル (MobileNetSSDV2Lite) を使用して転移学習を実行します。 ノートブックは、[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/#product-overview) を通じてリモート コンピューティング インスタンスを利用し、AzureML ポータルを使用して、または [Visual Studio Code](https://code.visualstudio.com/) によってローカルで実行することができます。

また、データセットを管理するための便利な Python [スクリプト](https://github.com/microsoft/Project-Santa-Cruz-Preview/tree/main/Sample-Scripts-and-Notebooks/Official/Scripts)や、高度な AI ソリューションの開発に必要なすべてのツールをインストールして構成する [Dev tools Pack Installer](https://github.com/microsoft/Project-Santa-Cruz-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/dev-tools-installer.md) も含まれています。
