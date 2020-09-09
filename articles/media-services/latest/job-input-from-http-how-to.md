---
title: HTTPS URL から Azure Media Services ジョブの入力を作成する | Microsoft Docs
description: このトピックでは、HTTPS URL から Azure Media Services ジョブの入力を作成する手順を説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: e440e4393065d2bc1cad1a96b4f8c975624faa11
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89295514"
---
# <a name="create-a-job-input-from-an-https-url"></a>HTTPS URL からジョブの入力を作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、HTTPS URL をジョブの入力として指定することです (この例で示すように)。 現在、AMS v3では、HTTPS URLを介したチャンク転送のエンコーディングはサポートされていません。 完全な例については、こちらの [GitHub サンプル](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs)を参照してください。

> [!TIP]
> 開発を開始する前に、「[Media Services v3 API を使用した開発](media-services-apis-overview.md)」を確認してください (API や命名規則などへのアクセスに関する情報が含まれています)。

## <a name="net-sample"></a>.Net のサンプル

次のコードは、HTTPS URL 入力を使用してジョブを作成する方法を示しています。

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)。
