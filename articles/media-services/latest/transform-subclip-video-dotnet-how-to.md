---
title: Media Services を使用してエンコードを実行する際にビデオをサブクリップする
description: このトピックでは、Azure Media Services を .NET SDK で使用してエンコードを実行する際にビデオをサブクリップする方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/09/2019
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 333d9d7e23182a98e9b8ad5bcc9c2a63362b2293
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492360"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Media Services を使用してエンコードを実行する際にビデオをサブクリップする - .NET

[ジョブ](/rest/api/media/jobs)を使用してビデオをエンコードする際に、ビデオをトリミングまたはサブクリップすることができます。 この機能は、[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) プリセットまたは [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) プリセットを使用して構築された[変換](/rest/api/media/transforms)で動作します。

次の C# サンプルでは、エンコード ジョブを送信する際にアセット内のビデオをトリミングするジョブを作成します。 

## <a name="prerequisites"></a>前提条件

このトピックで説明する手順を完了するには以下を行う必要があります。

- [Azure Media Services アカウントを作成します](./account-create-how-to.md)
- 変換と入力アセットおよび出力アセットを作成します。 変換と入力アセットおよび出力アセットを作成する方法は、「[.NET を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)」チュートリアルでご覧いただけます。
- [エンコードの概念](encode-concept.md)に関するトピックを確認します。

## <a name="example"></a>例

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>次のステップ

[カスタム変換を使用してエンコードする方法](transform-custom-presets-how-to.md) 
