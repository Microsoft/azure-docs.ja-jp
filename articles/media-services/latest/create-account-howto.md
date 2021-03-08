---
title: Azure Media Services アカウントの作成
description: このチュートリアルでは、Azure Media Services アカウントを作成する手順について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/4/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9234b27e2f08e65f569393bde342cba3f37adee
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "95971333"
---
# <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure で暗号化、エンコード、分析、管理、およびメディア コンテンツのストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントは、1 つまたは複数のストレージ アカウントに関連付ける必要があります。 この記事では、新しい Azure Media Services アカウントを作成する手順について説明します。

[!INCLUDE [note 2020-05-01 API](./includes/note-2020-05-01-account-creation.md)]

 Azure portal または CLI のいずれかを使用して、Media Services アカウントを作成できます。 使用するメソッドのタブを選択します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

<!-- NOTE: The following are in the includes folder and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

## <a name="portal"></a>[ポータル](#tab/portal/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [the media services account and storage account must be in the same subscription](./includes/note-account-storage-same-subscription.md)]

## <a name="set-the-azure-subscription"></a>Azure サブスクリプションを設定する

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

## <a name="create-a-media-services-account"></a>Media Services アカウントを作成する

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

---

## <a name="next-steps"></a>次のステップ

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)