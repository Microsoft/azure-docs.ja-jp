---
title: Azure Media Services で CLI 使用してフィルターを作成する | Microsoft Docs
description: この記事では、Azure Media Services v3 で CLI を使用してフィルターを作成する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5ceff9eba0363ea2784c48e026807c53447f1e32
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296904"
---
# <a name="creating-filters-with-cli"></a>CLI を使用してフィルターを作成する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

コンテンツを顧客に配信 (ライブ イベントやビデオ オン デマンドをストリーム配信) する際、資産の既定のマニフェスト ファイルに記述された内容だけではクライアントのニーズに柔軟に対応できない場合があります。 Azure Media Services では、アカウント フィルターと、コンテンツのアセットフィルターを定義することができます。 

この機能と、この機能が使用されているシナリオの詳細については、[動的マニフェスト](filters-dynamic-manifest-overview.md)と[フィルター](filters-concept.md)に関する記事を参照してください。

このトピックでは、ビデオ オン デマンド資産用のフィルターを構成し、Media Services v3 用の CLI を使用して[アカウント フィルター](/cli/azure/ams/account-filter?view=azure-cli-latest)と[資産 フィルター](/cli/azure/ams/asset-filter?view=azure-cli-latest)を作成する方法について説明します。 

> [!NOTE]
> [presentationTimeRange](filters-concept.md#presentationtimerange) を必ず確認してください。

## <a name="prerequisites"></a>前提条件 

- [Media Services アカウントを作成する](./create-account-howto.md) リソース グループ名と Media Services アカウント名を覚えておいてください。 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>フィルターの定義 

次に、最終的なマニフェストに追加されるトラック選択条件を定義する例を示します。 このフィルターには、EC-3 のオーディオ トラックと、0 から 1,000,000 の範囲のビットレートのビデオ トラックが含まれます。

> [!TIP]
> REST で**フィルター**を定義する予定の場合は、"Properties" ラッパー JSON オブジェクトを含める必要があることに注意してください。  

```json
[
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Audio",
                "operation": "Equal"
            },
            {
                "property": "FourCC",
                "value": "EC-3",
                "operation": "NotEqual"
            }
        ]
    },
    {
        "trackSelections": [
            {
                "property": "Type",
                "value": "Video",
                "operation": "Equal"
            },
            {
                "property": "Bitrate",
                "value": "0-1000000",
                "operation": "Equal"
            }
        ]
    }
]
```

## <a name="create-account-filters"></a>アカウント フィルターの作成

次の [az ams account-filter](/cli/azure/ams/account-filter?view=azure-cli-latest) コマンドでは、[前に定義した](#define-a-filter)フィルター トラック選択を含むアカウント フィルターが作成されます。 

このコマンドを使用すると、トラック選択を表す JSON を含むオプションの `--tracks` パラメーターを渡すことができます。  ファイルから JSON を読み込むには、@ {ファイル} を使用します。 Azure CLI をローカルで使用している場合は、ファイルのパス全体を指定します。

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

[フィルターに関する JSON の例](/rest/api/media/accountfilters/createorupdate#create-an-account-filter)も参照してください。

## <a name="create-asset-filters"></a>アセットフィルターの作成

次の [az ams asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest) コマンドでは、[前に定義した](#define-a-filter)フィルター トラック選択を含む資産フィルターが作成されます。 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

[フィルターに関する JSON の例](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)も参照してください。

## <a name="associate-filters-with-streaming-locator"></a>フィルターをストリーミング ロケーターに関連付ける

アセットまたはアカウント フィルターの一覧を指定できます。これはストリーミング ロケーターに適用されます。 [ダイナミック パッケージャー (ストリーミング エンドポイント)](dynamic-packaging-overview.md) では、クライアントで URL に指定されるフィルターと共にこのフィルターの一覧が適用されます。 この組み合わせによって、URL 内のフィルターとストリーミング ロケーターに指定されたフィルターに基づく[動的マニフェスト](filters-dynamic-manifest-overview.md)が生成されます。 フィルターを適用したいものの URL でフィルター名を公開したくない場合は、この機能を使用することをお勧めします。

次の CLI コードは、ストリーミング ロケーターを作成し、`filters` を指定する方法を示します。 これは、アセット フィルター名やアカウント フィルター名のスペースで区切られたリストを受け取る省略可能なプロパティです。

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>フィルターを使用するストリーム

フィルターを定義すると、クライアントからストリーミング URL で使用できるようになります。 フィルターは、アダプティブ ビットレート ストリーミング プロトコル (Apple HTTP Live Streaming (HLS)、MPEG DASH、Smooth Streaming) に適用できます。

次の表に、フィルターを含んだ URL の例をいくつか示します。

|Protocol|例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|スムーズ ストリーミング|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>次のステップ

[ビデオのストリーム配信](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>参照

[Azure CLI](/cli/azure/ams?view=azure-cli-latest)
