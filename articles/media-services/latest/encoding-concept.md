---
title: Media Services を使用したビデオとオーディオのエンコード
titleSuffix: Azure Media Services
description: この記事では、Azure Media Services を使用したビデオとオーディオのエンコードについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 8533c99011232385e31f4e698743bbb1c6c00bc5
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300206"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Media Services を使用したビデオとオーディオのエンコード

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services では、エンコードという用語は、デジタル ビデオやデジタル オーディオを含むファイルをある標準形式から別の標準形式に変換するプロセスを表します。変換の目的は、(a) ファイルのサイズを減らしたり、(b) 広範なデバイスやアプリと互換性のある形式を生成したりすることにあります。 このプロセスは、ビデオ圧縮やコード変換とも呼ばれます。 この概念の詳細については、「[Data compression (データ圧縮)](https://en.wikipedia.org/wiki/Data_compression)」と「[What Is Encoding and Transcoding? (エンコードとコード変換とは)](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)」を参照してください。

通常、ビデオは[プログレッシブ ダウンロード](https://en.wikipedia.org/wiki/Progressive_download)または[アダプティブ ビットレート ストリーミング](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)を介してデバイスとアプリに配信されます。

> [!IMPORTANT]
> Media Services では、取り消されたジョブまたはエラーのあるジョブに対する課金は行われません。 たとえば、進行状況が 50% で取り消されたジョブが、ジョブの分数の 50% に対して課金されることはありません。 完了したジョブに対してのみ課金されます。

* プログレッシブ ダウンロードによって配信する場合、Azure Media Services を使用してデジタル メディア ファイル (中間ファイル) を [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) ファイルに変換できます。MP4 には、[H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) コーデックでエンコードされているビデオと、[AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) コーデックでエンコードされているオーディオが含まれています。 この MP4 ファイルは、ご使用のストレージ アカウントの資産に書き込まれます。 Azure Storage API または SDK ([Storage REST API](../../storage/common/storage-rest-api-auth.md)、[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md) など) を使用して、ファイルを直接ダウンロードできます。 ストレージで特定のコンテナー名を使用して出力資産を作成した場合は、その場所を使用してください。 それ以外の場合は、Media Services を使用して、[資産コンテナーの URL を一覧表示](/rest/api/media/assets/listcontainersas)できます。 
* アダプティブ ビットレート ストリーミングによって配信するコンテンツを準備するには、中間ファイルを複数のビット レート (高から低) でエンコードする必要があります。 品質を適切に推移させるために、ビットレートが低下するとビデオの解像度が低下します。 これにより、解像度とビットレートの表である、いわゆるエンコード ラダーが生成されます ([自動生成されたアダプティブ ビットレート ラダー](autogen-bitrate-ladder.md)に関するページを参照)。 Media Services を使用して、中間ファイルを複数のビットレートでエンコードできます。 これにより、一連の MP4 ファイルと、それらに関連付けられたストリーミング構成ファイルが得られ、ご利用のストレージ アカウント内のアセットに書き込まれます。 次に、Media Services の[ダイナミック パッケージ](dynamic-packaging-overview.md)機能を使用して、[MPEG DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) や [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming) などのストリーミング プロトコルを介して動画を配信します。 そのためには、[ストリーミング ロケーター](streaming-locators-concept.md)を作成し、サポートされているプロトコルに対応するストリーミング URL を構築します。次に、これらは機能に基づいてデバイスやアプリに渡すことができます。

次の図は、ダイナミック パッケージを使用した、オンデマンド エンコードのワークフローを示しています。

![ダイナミック パッケージを使用したオンデマンド エンコードのワークフロー](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

このトピックでは、Media Services v3 でコンテンツをエンコードする方法について説明します。

## <a name="transforms-and-jobs"></a>変換およびジョブ

Media Services v3 でエンコードするには、[変換](/rest/api/media/transforms)と[ジョブ](/rest/api/media/jobs)を作成する必要があります。 変換では、エンコード設定と出力のレシピを定義します。ジョブはレシピのインスタンスです。 詳しくは、「[Transform と Job](transforms-jobs-concept.md)」をご覧ください。

Media Services でエンコードする場合、プリセットを使用してエンコーダーに入力メディア ファイルの処理方法を指示します。 Media Services v3 では、Standard Encoder を使用してファイルをエンコードします。 たとえば、エンコードされたコンテンツに必要なビデオ解像度やオーディオ チャンネルの数を指定できます。

業界のベスト プラクティスに基づいて推奨される組み込みのプリセットのいずれかを使用して、すぐに始めることができます。また、特定のシナリオやデバイスの要件を対象とするカスタム プリセットを作成することもできます。 詳細については、[カスタム変換を使用するエンコード](customize-encoder-presets-how-to.md)に関するページを参照してください。

2019 年 1 月より、Standard Encoder でエンコードして MP4 ファイルを生成すると、新しい .mpi ファイルが生成され、出力アセットに追加されます。 この MPI ファイルの目的は、[ダイナミック パッケージ](dynamic-packaging-overview.md)とストリーミングのシナリオのパフォーマンスを向上させることです。

> [!NOTE]
> MPI ファイルを変更または削除したり、サービスにこのようなファイルが存在するかどうかに依存しないようにしてください。

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL からのジョブ入力の作成

自分のビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、HTTPS URL をジョブ入力として指定することです。 現在、Media Services v3 では HTTPS URL を介したチャンク転送エンコードはサポートされていません。

#### <a name="examples"></a>例

* [.NET を使用して HTTPS URL からエンコードする](stream-files-dotnet-quickstart.md)
* [REST を使用して HTTPS URL からエンコードする](stream-files-tutorial-with-rest.md)
* [CLI を使用して HTTPS URL からエンコードする](stream-files-cli-quickstart.md)
* [Node.js を使用して HTTPS URL からエンコードする](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>ローカル ファイルからのジョブ入力の作成

入力ビデオは Media Service 資産として格納できます。この場合は、(ローカルまたは Azure Blob ストレージに格納されている) ファイルに基づいて入力資産を作成します。

#### <a name="examples"></a>例

[組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>サブクリップを使用したジョブ入力の作成

ビデオをエンコードする際に、ソース ファイルをトリミングまたはクリッピングして、入力ビデオの目的の部分のみが含まれる出力を生成するように指定することもできます。 この機能は、[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) プリセットまたは [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) プリセットを使用して構築された[変換](/rest/api/media/transforms)で動作します。

オンデマンドまたはライブ アーカイブ (記録されたイベント) のビデオの単一のクリップを使用して[ジョブ](/rest/api/media/jobs/create)を作成するように指定できます。 資産または HTTPS URL をジョブ入力にすることができます。

> [!TIP]
> ビデオの再エンコードなしでビデオのサブクリップをストリーミングする場合は、[Dynamic Packager によるマニフェストの事前フィルター処理](filters-dynamic-manifest-overview.md)の使用を検討します。

#### <a name="examples"></a>例

次の例を参照してください。

* [.NET を使用してビデオをサブクリップする](subclip-video-dotnet-howto.md)
* [REST を使用してビデオをサブクリップする](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>組み込みのプリセット

Media Services は、次の組み込みのエンコード プリセットをサポートしています。  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) は、Standard Encoder で入力ビデオをエンコードする組み込みのプリセットの設定に使用されます。

現在サポートされているプリセットは次のとおりです。

- **EncoderNamedPreset.AACGoodQualityAudio**: 192 kbps でエンコードされたステレオ オーディオのみを含む単一の MP4 ファイルを生成します。
- **EncoderNamedPreset.AdaptiveStreaming** (推奨):詳細については、[ビットレート ラダーの自動生成](autogen-bitrate-ladder.md)に関するページを参照してください。
- **EncoderNamedPreset.ContentAwareEncoding**: コンテンツに対応したエンコードのプリセットを公開します。 入力コンテンツを指定すると、サービスにより、アダプティブ ストリーミングによる配信に最適なレイヤーの数と、適切なビットレートおよび解像度の設定の決定が、自動的に試みられます。 基になるアルゴリズムは、時間と共に進化を続けています。 出力には、ビデオとオーディオがインターリーブされた MP4 ファイルが含まれるようになります。 詳細については、[コンテンツに対応したエンコード](content-aware-encoding.md)に関する記事を参照してください。

  > [!NOTE]
  > ContentAwareEncodingExperimental ではなく、必ず **ContentAwareEncoding** を使用してください。
- **EncoderNamedPreset.H264MultipleBitrate1080p**: GOP がアラインメントされた、ビットレートが 6,000 kbps から 400 kbps、オーディオが AAC ステレオである 8 つの MP4 ファイルを生成します。 解像度の上限は 1080p で下限は 360p です。
- **EncoderNamedPreset.H264MultipleBitrate720p**: GOP がアラインメントされた、ビットレートが 3,400 kbps から 400 kbps、オーディオが AAC ステレオである 6 つの MP4 ファイルを生成します。 解像度の上限は 720p で下限は 360p です。
- **EncoderNamedPreset.H264MultipleBitrateSD**: GOP がアラインメントされた、ビットレートが 1,600 kbps から 400 kbps、オーディオが AAC ステレオである 5 つの MP4 ファイルを生成します。 解像度の上限は 480p で下限は 360p です。
- **EncoderNamedPreset.H264SingleBitrate1080p**: ビデオが 6750 kbps の H.264 コーデックと 1,080 ピクセルの画像の高さでエンコードされ、ステレオ オーディオが 64 kbps の AAC-LC コーデックでエンコードされた、MP4 ファイルが生成されます。
- **EncoderNamedPreset.H264SingleBitrate720p**: ビデオが 4,500 kbps の H.264 コーデックと 720 ピクセルの画像の高さでエンコードされ、ステレオ オーディオが 64 kbps の AAC-LC コーデックでエンコードされた、MP4 ファイルが生成されます。
- **EncoderNamedPreset.H264SingleBitrateSD**: ビデオが 2,200 kbps の H.264 コーデックと 480 ピクセルの画像の高さでエンコードされ、ステレオ オーディオが 64 kbps の AAC-LC コーデックでエンコードされた、MP4 ファイルが生成されます。

最新のプリセットの一覧を見るには、[ビデオのエンコードに使用される組み込みのプリセット](/rest/api/media/transforms/createorupdate#encodernamedpreset)に関する記事をご覧ください。

プリセットがどのように使われるかについては、[ファイルのアップロード、エンコード、ストリーミング](stream-files-tutorial-with-api.md)に関する記事を参照してください。

### <a name="standardencoderpreset"></a>BuiltInStandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) には、Standard Encoder を使用して入力ビデオをエンコードするときに使用する設定を記述します。 変換プリセットをカスタマイズする場合は、このプリセットを使用します。

#### <a name="considerations"></a>考慮事項

カスタム プリセットを作成するときは、次の考慮事項が適用されます。

- AVC コンテンツの高さと幅のすべての値は、4 の倍数である必要があります。
- Azure Media Services v3 では、エンコード ビットレートはすべてビット/秒単位で指定されます。 これは v2 API のプリセットとは異なります。v2 では、キロビット/秒が単位として使用されていました。 たとえば、v2 でビットレートが 128 (キロビット/秒) に指定されていた場合、v3 では 128000 (ビット/秒) に設定されます。

### <a name="customizing-presets"></a>プリセットのカスタマイズ

Media Services では、特定のエンコーディング ニーズと要件を満たすようにプリセットのすべての値をカスタマイズできます。 エンコーダー プリセットをカスタマイズする方法の例については、下記の一覧を参照してください。

#### <a name="examples"></a>例

- [.NET でプリセットをカスタマイズする](customize-encoder-presets-how-to.md)
- [CLI でプリセットをカスタマイズする](custom-preset-cli-howto.md)
- [REST でプリセットをカスタマイズする](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>プリセット スキーマ

Media Services v3 では、プリセットは API 自体で厳密に型指定されたエンティティです。 これらのオブジェクトの "スキーマ" 定義は、[Open API の仕様 (または Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) にあります。 プリセット定義 (**StandardEncoderPreset** など) は、[REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset)、[.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (またはその他の Media Services v3 SDK のリファレンス ドキュメント) でも確認できます。

## <a name="scaling-encoding-in-v3"></a>v3 でのエンコードのスケーリング

メディア処理のスケール設定を行うには、[CLI を使用したスケーリング](media-reserved-units-cli-how-to.md)に関するトピックをご覧ください。

## <a name="billing"></a>課金

Media Services では、取り消されたジョブまたはエラーのあるジョブに対する課金は行われません。 たとえば、進行状況が 50% で取り消されたジョブが、ジョブの分数の 50% に対して課金されることはありません。 完了したジョブに対してのみ課金されます。

詳細については、[価格](https://azure.microsoft.com/pricing/details/media-services/)に関するページを参照してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

* [Media Services を使用してアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
* [組み込みのプリセットを使用して HTTPS の URL をエンコードする](job-input-from-http-how-to.md)
* [組み込みのプリセットを使用してローカル ファイルをエンコードする](job-input-from-local-file-how-to.md)
* [自分の特定のシナリオまたはデバイス要件に対応するカスタム プリセットを構築する](customize-encoder-presets-how-to.md)。
