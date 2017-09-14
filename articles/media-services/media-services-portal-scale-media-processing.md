---
title: "Azure Portal を使用したメディア処理のスケール設定 | Microsoft Docs"
description: "このチュートリアルでは、Azure Portal を使用したメディア処理のスケール設定の手順について説明します。"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/04/2017
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: e6ef52f7aee8b2a0d0dd6ebc99ad7a8c5c11f525
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017

---
# <a name="change-the-reserved-unit-type"></a>予約ユニットの種類の変更
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [ポータル](media-services-portal-scale-media-processing.md)
> * [REST ()](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>概要

Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。 予約ユニットの種類は、**S1**、**S2**、**S3** から選択できます。 たとえば、同じエンコード ジョブの場合に、予約ユニットの種類として **S1** よりも **S2** を使用する方が、ジョブの実行が高速になります。

予約ユニットの種類を指定するだけでなく、**予約ユニット** (RU) を使用したアカウントのプロビジョニングを指定することもできます。 プロビジョニングされた RU の数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。

>[!NOTE]
>RU は、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。 ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットを使用した高速処理は行われません。

> [!IMPORTANT]
> メディア処理のスケール設定の詳細については、 [概要](media-services-scale-media-processing-overview.md) に関するトピックを必ず確認してください。
> 
> 

## <a name="scale-media-processing"></a>メディア処理のスケール設定
予約ユニットの種類と予約ユニットの数を変更するには、以下の手順に従います。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで **[メディア占有ユニット]** をクリックします。
   
    選択した予約ユニットの種類の予約ユニット数を変更するには、 **[Media Serverd Units (メディア予約ユニット)]** スライダーを使用します。
   
    **[占有ユニットの種類]**を変更するには、[S1]、[S2]、または [S3] をクリックします。
   
    ![[プロセッサ] ページ](./media/media-services-portal-scale-media-processing/media-services-scale-media-processing.png)
3. [保存] ボタンを押して、変更を保存します。
   
    [保存] をクリックすると、新しい予約ユニットが割り当てられます。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


