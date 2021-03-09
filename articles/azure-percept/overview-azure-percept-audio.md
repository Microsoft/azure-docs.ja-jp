---
title: Azure Percept Audio デバイスの概要
description: Azure Percept Audio について説明します。
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: a63f471498667f58fc80f89323ad93b0feb8bc95
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101678614"
---
# <a name="introduction-to-azure-percept-audio"></a>Azure Percept Audio の概要

Azure Percept Audio は、音声 AI 機能を Azure Percept DK に追加するアクセサリ デバイスです。 これには、事前に構成された音声 AI アクセラレータと 4 つのマイクの線形配列が含まれています。これにより、カスタム コマンド、キーワード認識、および遠距離音声をローカルのリスニング デバイスに適用できます。 Azure Percept Audio を使用すると、デバイス製造元は Azure Percept DK のビジョン機能を新しいスマート音声アクティブ化デバイスへ拡張できます。 Azure Percept DK、Azure Percept Studio、およびその他の Azure エッジ管理サービスに追加設定なしで統合されています。 [Microsoft オンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で購入できます。

![Azure Percept Audio デバイス。](./media/overview-azure-percept-audio/percept-audio.png)


## <a name="azure-percept-audio-components"></a>Azure Percept Audio コンポーネント

Azure Percept Audio には、次の主要なコンポーネントが含まれています。

- 4 つのマイクの線形配列を使用した、実稼働可能な Azure Percept Audio デバイス (SoM)
- 開発者ボード (2 個のボタン、3 個の LED、USB マイクロ、および 3.5 mm オーディオ ジャックを含む)
- 必要なケーブル: フレックス ケーブル、USB マイクロ タイプ B から USB-A
- ウェルカム カード
- 統合 80/20 1010 シリーズ マウントによる機械式マウント プレート


<!---

## How it works

Azure Percept Audio passes the audio input to the Azure Percept DK carrier board in a hybrid edge-cloud manner. Specifically,

- The Azure Percept Audio device: processes the incoming speech input to the clearest format by executing beam forming and echo cancellation befor sending the input to the Azure Percept DK. 
- The Azure Percept DK uses edge processing to perform keyword spotting and then sends the relevant inputs to Azure speech services.
- Cloud: Processing of natural language commands and phrases, in addition to keyword verification and retraining.
- Offline: If the device is offline it will detect the keyword and capture telemetry that there is no internet connection at the time of the command. It will not be able to weed out false accepts since it cannot perform keyword verification.

-->

## <a name="getting-started"></a>作業の開始

- [Azure Percept DK を組み立てる](./quickstart-percept-dk-unboxing.md)
- [Azure Percept DK セットアップ エクスペリエンスを完了する](./quickstart-percept-dk-set-up.md)
- [Azure Percept Audio デバイスを devkit に接続する](./quickstart-percept-audio-setup.md)

### <a name="build-a-no-code-prototype"></a>コードなしのプロトタイプをビルドする

Azure Percept の音声アシスタント テンプレートを使用して、接客、医療、在庫、自動車などのシナリオで、[コードなしの音声ソリューション](./tutorial-no-code-speech.md)を構築します。

## <a name="additional-technical-information"></a>その他の技術情報

- [Azure Percept Audio のデータシート](./azure-percept-audio-datasheet.md)

## <a name="next-steps"></a>次のステップ

[Microsoft のオンライン ストア](https://go.microsoft.com/fwlink/p/?LinkId=2155270)で Azure Percept Audio デバイスを注文します。