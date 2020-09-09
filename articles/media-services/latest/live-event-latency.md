---
title: Azure Media Services の LiveEvent の低待機時間設定 | Microsoft Docs
description: このトピックでは、LiveEvent の低待機時間設定の概要について説明し、低待機時間の設定方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 114efe668fba47e9d83741b8fa45e1f396725198
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291519"
---
# <a name="live-event-low-latency-settings"></a>ライブ イベントの低待機時間設定

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、[ライブ イベント](/rest/api/media/liveevents)に対して低待機時間を設定する方法を示します。 さらに、さまざまなプレーヤーで低待機時間の設定を使用したときの一般的な結果について説明します。 結果は、CDN およびネットワーク待機時間によって異なります。

新しい **LowLatency** 機能を使用するには、**LiveEvent** の **StreamOptionsFlag** を **LowLatency** に設定します。 HLS 再生用に [LiveOutput](/rest/api/media/liveoutputs) を作成する場合は、[LiveOutput.Hls.fragmentsPerTsSegment](/rest/api/media/liveoutputs/create#hls) を 1 に設定します。 ストリームが稼働状態になったら、[Azure Media Player](https://ampdemo.azureedge.net/) (AMP のデモ ページ) を使用して、"Low Latency Heuristic Profile" を使用するように再生オプションを設定できます。

> [!NOTE]
> 現時点では、Azure Media Player の LowLatency HeuristicProfile は、CSF または CMAF 形式 (`format=mdp-time-csf` または `format=mdp-time-cmaf` など) のMPEG-DASH プロトコでストリームを再生するために設計されています。 

次の .NET の例では、**LiveEvent** に対して **LowLatency** を設定する方法を示しています。

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

完全な例については次を参照してください:[MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126)。

## <a name="live-events-latency"></a>ライブ イベントの待機時間

次の表に、Media Services 内の待機時間の一般的な結果を示します (LowLatency フラグが有効になっている場合)。コントリビューション フィードがサービスに到達した時間から、ビューアーがプレーヤーで再生を見た時間までが測定されています。 低待機時間を最適に使用するには、エンコーダーの設定を "画像グループ" (GOP) の長さが 1 秒になるまで調整する必要があります。 高い GOP の長さを使用するときは、帯域幅の消費を最小限に抑えて、同じフレーム レートでのビットレートを減らします。 モーションの少ない動画で特に有効です。

### <a name="pass-through"></a>パススルー 

||2 秒 GOP の低待機時間が有効|1 秒 GOP の低待機時間が有効|
|---|---|---|
|**AMP での DASH**|10 秒|8 秒|
|**ネイティブ iOS プレーヤーでの HLS**|14 秒|10 秒|

### <a name="live-encoding"></a>ライブ エンコード

||2 秒 GOP の低待機時間が有効|1 秒 GOP の低待機時間が有効|
|---|---|---|
|**AMP での DASH**|14 秒|10 秒|
|**ネイティブ iOS プレーヤーでの HLS**|18 秒|13 秒|

> [!NOTE]
> エンドツーエンドの待機時間は、ローカル ネットワークの状態や、CDN キャッシュ レイヤーの導入によって変動する可能性があります。 正しい構成でテストする必要があります。

## <a name="next-steps"></a>次のステップ

- [ライブ ストリーミングの概要](live-streaming-overview.md)
- [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
