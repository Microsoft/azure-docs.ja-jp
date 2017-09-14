---
title: "Azure Portal で Media Encoder Standard を使用した資産のエンコード | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal で Media Encoder Standard を使用して資産をエンコードする手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: efe7db8a36273b4755dd057139bb1c673af868d3
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="encode-an-asset-using-media-encoder-standard-with-the-azure-portal"></a>Azure Portal で Media Encoder Standard を使用した資産のエンコード
> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
> 
> 

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。 Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、Smooth Streaming、および MPEG DASH です。 アダプティブ ビットレート ストリーミング用にビデオを準備するには、ソース ビデオをマルチビットレートのファイルにエンコードする必要があります。 ビデオのエンコードには **Media Encoder Standard** エンコーダーを使用する必要があります。  

また、Media Services にはダイナミック パッケージ機能があり、マルチビットレート MP4 でエンコードされたコンテンツを、MPEG DASH、HLS、Smooth Streaming のストリーミング形式でそのまま配信できます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。 動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

ダイナミック パッケージを活用するには、ソース ファイルを一連のマルチビットレート MP4 ファイルにエンコードする必要があります (エンコードの手順はこのセクションで後ほど説明します)。

メディア処理をスケール調整する場合は、 [こちら](media-services-portal-scale-media-processing.md) のトピックをご覧ください。

## <a name="encode-with-the-azure-portal"></a>Azure Portal でのエンコード
ここでは、Media Encoder Standard でコンテンツをエンコードする手順について説明します。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで、**[資産]** を選択します。  
3. **[資産]** ウィンドウで、エンコードする資産を選択します。
4. **[エンコード]** ボタンをクリックします。
5. **[資産のエンコード]** ウィンドウで、"Media Encoder Standard" プロセッサとプリセットを選択します。 プリセットについては、[ビットレート ラダーの自動生成](media-services-autogen-bitrate-ladder-with-mes.md)に関するページと [MES 用のタスク プリセット](media-services-mes-presets-overview.md)に関するページを参照してください。 どちらのエンコード プリセットを使用するかを制御する場合は、入力ビデオに最適なプリセットを選択することが重要である点を覚えておいてください。 たとえば、入力ビデオの解像度が 1920 x 1080 ピクセルであるとわかっている場合は、"H264 Multiple Bitrate 1080p" のプリセットを使用できます。 低解像度 (640 x 360) のビデオの場合は、"H264 Multiple Bitrate 1080p" のプリセットは使用しないでください。
   
   出力資産とジョブの名前を編集するオプションを利用すると、効率よく管理を行えます。
   
   ![Encode assets](./media/media-services-portal-vod-get-started/media-services-encode1.png)
6. **[作成]**をクリックします。

## <a name="next-step"></a>次のステップ
[こちら](media-services-portal-check-job-progress.md) の記事で説明するように、Azure Portal を使用してエンコード ジョブの進行状況を監視できます。  

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


