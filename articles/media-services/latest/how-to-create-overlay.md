---
title: Media Encoder Standard を使用してオーバーレイを作成する方法
description: Media Encoder Standard を使用してオーバーレイを作成する方法について説明します。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 85b1b6c56221deaa03057a7ccb658b4bf67124eb
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830582"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Media Encoder Standard を使用してオーバーレイを作成する方法

Media Encoder Standard では、画像を既存の動画に重ね合わせることができます。 現在サポートされている形式は png、jpg、gif、bmp です。

## <a name="prerequisites"></a>前提条件

* サンプルの *appsettings.json* ファイルを構成するために必要なアカウント情報を収集します。 これを行う方法がわからない場合は、「[クイックスタート:Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)」を参照してください。 *appsettings.json* ファイルには、次の値が必要です。

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Transforms についてまだよく理解していない場合は、次のアクティビティを完了することをお勧めします。

* 「[Media Services を使用したビデオとオーディオのエンコード](encoding-concept.md)」を読む
* 「[カスタム変換を使用してエンコードする方法 - .NET](customize-encoder-presets-how-to.md)」を読む この記事の手順に従って、Transform を操作するために必要な .NET を設定してから、ここに戻り、オーバーレイのプリセット サンプルを試してみてください。
* [Transforms のリファレンス ドキュメント](/rest/api/media/transforms)を参照してください。

Transforms について理解したら、オーバーレイのサンプルをダウンロードします。

## <a name="overlays-preset-sample"></a>オーバーレイのプリセット サンプル

オーバーレイの使用を開始するには、[media-services-overlay サンプル](https://github.com/Azure-Samples/media-services-overlays)をダウンロードしてください。

## <a name="next-steps"></a>次の手順

* [Media Services を使用してエンコードを実行する際にビデオをサブクリップする - .NET](subclip-video-dotnet-howto.md)