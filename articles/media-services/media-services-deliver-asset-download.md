---
title: "コンピューターへの Media Services 資産のダウンロード - Azure | Microsoft Docs"
description: "コンピューターへのアセットのダウンロードについて説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 19f6ea37f892054ee5d7bf793a32364dff264058
ms.lasthandoff: 03/14/2017


---
# <a name="how-to-deliver-an-asset-by-download"></a>方法: ダウンロードによってアセットを配信する
このトピックでは、Media Services にアップロードされたメディア アセットを配信するためのオプションについて説明します。 メディア サービスのコンテンツ配信には、さまざまな方法を適用できます。 メディア アセットをダウンロードするか、ロケーターを使用してアクセスすることができます。 メディア コンテンツは、別のアプリケーションまたは別のコンテンツ プロバイダーに送ることができます。 パフォーマンスとスケーラビリティを高めるために、Content Delivery Network (CDN) を使用してコンテンツを配信することもできます。

この例では、メディア アセットを Media Services からローカル コンピューターにダウンロードする方法を紹介しています。 このコードは、ジョブ ID によってメディア サービス アカウントに関連付けられたジョブのクエリを実行し、 **OutputMediaAssets** コレクション (ジョブの実行の結果である&1; つまたは複数の出力メディア アセットのセット) にアクセスします。 この例では、ジョブから出力メディア アセットをダウンロードしていますが、同じ方法で他のアセットをダウンロードすることもできます。

>[!NOTE]
>さまざまな AMS ポリシー (ロケーター ポリシーや ContentKeyAuthorizationPolicy など) に 1,000,000 ポリシーの制限があります。 常に同じ日数、アクセス許可などを使う場合は、同じポリシー ID を使う必要があります (たとえば、長期間存在するように意図されたロケーターのポリシー (非アップロード ポリシー))。 詳細については、 [こちらの](media-services-dotnet-manage-entities.md#limit-access-policies) トピックを参照してください。

    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }



## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[ストリーミング コンテンツを配信する](media-services-deliver-streaming-content.md)


