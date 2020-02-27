---
title: アプリケーションに Video Indexer ウィジェットを埋め込む
titleSuffix: Azure Media Services
description: この記事では、アプリケーションに Azure Media Services Video Indexer ウィジェットを埋め込む方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: ed3e2cf9830e3776886e662fd27f43f76728d6b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988721"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>アプリケーションに Video Indexer ウィジェットを埋め込む

この記事では、アプリケーションに Video Indexer ウィジェットを埋め込む方法について説明します。 Video Indexer では、次の 3 種類のウィジェットのアプリケーションへの埋め込みがサポートされています: *コグニティブな分析情報*、*プレーヤー*、*エディター*。 

バージョン 2 以降、ウィジェットのベース URL には、指定されたアカウントのリージョンが含まれています。 たとえば、米国西部リージョンのアカウントでは、`https://wus2.videoindexer.ai/embed/insights/...` が生成されます。

## <a name="widget-types"></a>ウィジェットの種類

### <a name="cognitive-insights-widget"></a>コグニティブな分析情報ウィジェット

コグニティブな分析情報ウィジェットには、ビデオのインデックス作成プロセスから抽出されたビジュアルな分析情報がすべて含まれます。 コグニティブな分析情報ウィジェットでは、次の省略可能な URL パラメーターがサポートされています。

|Name|定義|説明|
|---|---|---|
|`widgets`|コンマで区切られた文字列|レンダリングする分析情報を制御できます。 <br/> 例: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` の場合、人物とブランドの UI 分析情報のみがレンダリングされます。<br/>使用可能なオプション: people、keywords、annotations、brands、sentiments、transcript、search。<br/>バージョン 2 では、`widgets` URL パラメーターがサポートされていないことに注意してください。<br/>|
|`locale`|省略形の言語コード|分析情報言語を制御します。 既定値は `en` です。 <br/> 例: `locale=de`.|
|`tab`|既定で選択されるタブ|既定でレンダリングされる **[分析情報]** タブを制御します。 <br/> 例: `tab=timeline` の場合、選択した **[タイムライン]** タブで分析情報がレンダリングされます。|

### <a name="player-widget"></a>プレーヤー ウィジェット

プレーヤーウィジェットは、アダプティブ ビット レートを使ってビデオをストリーム配信するために使用できます。 プレーヤー ウィジェットでは、次の省略可能な URL パラメーターがサポートされています。

|Name|定義|説明|
|---|---|---|
|`t`|開始からの秒数|プレーヤーで、指定した時点から再生を開始します。<br/> 例: `t=60`.|
|`captions`|言語コード|ウィジェットを読み込むときに、指定された言語のキャプションを取り込んで、 **[キャプション]** メニューで使用できるようにします。<br/> 例: `captions=en-US`.|
|`showCaptions`|ブール値|既に有効になっているキャプションとともにプレーヤーを読み込みます。<br/> 例: `showCaptions=true`.|
|`type`||オーディオ プレーヤーのスキンをアクティブにします (ビデオ部分は削除されます)。<br/> 例: `type=audio`.|
|`autoplay`|ブール値|プレーヤーがビデオの読み込み時に、その再生を開始する必要があるかどうかを示します。 既定値は `true` です。<br/> 例: `autoplay=false`.|
|`language`|言語コード|プレーヤーの言語を制御します。 既定値は `en-US` です。<br/>例: `language=de-DE`.|

### <a name="editor-widget"></a>エディター ウィジェット

エディター ウィジェットは、新しいプロジェクトの作成とビデオの分析情報の管理に使用できます。 エディター ウィジェットでは、次の省略可能な URL パラメーターがサポートされています。

|Name|定義|説明|
|---|---|---|
|`accessToken`<sup>*</sup>|String|ウィジェットの埋め込みに使用されているアカウント内にのみ存在するビデオへのアクセスが提供されます。<br> エディター ウィジェットには `accessToken` パラメーターが必要です。|
|`language`|言語コード|プレーヤーの言語を制御します。 既定値は `en-US` です。<br/>例: `language=de-DE`.|
|`locale`|省略形の言語コード|分析情報言語を制御します。 既定値は `en` です。<br/>例: `language=de`.|

<sup>*</sup>所有者は、注意して `accessToken` を提供する必要があります。

## <a name="embedding-public-content"></a>パブリック コンテンツの埋め込み

1. [Video Indexer](https://www.videoindexer.ai/) の Web サイトにサインインします。
2. 作業するビデオを選択します。
3. ビデオの下に表示される **[埋め込み]** ボタンを選択 します。

    ![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    **[埋め込み]** ボタンを選択すると、アプリケーションに埋め込むウィジェットを選択できます。 
4. 目的のウィジェットの種類 (**コグニティブな分析情報**、**プレーヤー**、または**エディター**) を選択します。
 
5. 埋め込みコードをコピーし、アプリケーションに追加します。 

    ![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> ビデオの URL の共有に問題がある場合は、リンクに `location` パラメーターを追加します。 このパラメーターは、[Video Indexer が存在する Azure リージョン](regions.md)に設定する必要があります。 (例: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`)。

## <a name="embedding-private-content"></a>プライベート コンテンツの埋め込み

プライベート ビデオを埋め込むには、iframe の **src** 属性にアクセス トークンを渡す必要があります。

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
コグニティブな分析情報ウィジェットのコンテンツを取得するには、次のいずれかを使用します。<br/>
- [Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API。<br/>
- [Get Video Access Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?)。 これをクエリ パラメーターとして URL に追加します。 前に示したように、この URL を iframe の **src** 値として指定します。

埋め込みのウィジェットで分析情報の編集機能を提供するには、編集アクセス許可を含むアクセス トークンを渡す必要があります。 `&allowEdit=true` を指定して、[Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) または [Get Video Access Token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) を使用します。 

## <a name="widgets-interaction"></a>ウィジェットの対話

コグニティブな分析情報ウィジェットは、アプリケーション上でビデオと対話できます。 このセクションでは、この対話を実現する方法を示します。

![ウィジェット](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>クロスオリジン通信

Video Indexer ウィジェットが他のコンポーネントと通信できるようにするために、Video Indexer サービスは次のことを行います。

- クロス オリジン通信の HTML5 メソッド **postMessage** を使用します。 
- VideoIndexer.ai からのメッセージの発生元を検証します。 

独自のプレーヤー コードを実装してコグニティブな分析情報ウィジェットと統合する場合は、VideoIndexer.ai からのメッセージの発生元をお客様の責任で検証する必要があります。

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>ウィジェットをアプリケーションまたはブログに埋め込む (推奨) 

このセクションでは、2 つの Video Indexer ウィジェット間の対話を実現して、ユーザーがアプリケーションで分析情報コントロールを選択したときにプレーヤーが関連する場面にジャンプできるようにする方法を示します。

1. プレーヤー ウィジェットの埋め込みコードをコピーします。
2. コグニティブな分析情報ウィジェットの埋め込みコードをコピーします。
3. 2 つのウィジェット間の通信を処理する[メディエーター ファイル](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)を追加します。<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

これで、ユーザーがアプリケーションで分析情報コントロールを選択すると、プレーヤーが関連する場面にジャンプします。

詳細については、「[Video Indexer - 両方のウィジェットを埋め込むデモ](https://codepen.io/videoindexer/pen/NzJeOb)」を参照してください。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>コグニティブな分析情報ウィジェットを埋め込み、Azure Media Player を使用してコンテンツを再生する

このセクションでは、[AMP プラグイン](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)を使用してコグニティブな分析情報ウィジェットと Azure Media Player インスタンス間の対話を実現する方法を示します。
 
1. AMP プレーヤー用の Video Indexer プラグインを追加します。<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Video Indexer プラグインを含む Azure Media Player をインスタンス化します。

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. コグニティブな分析情報ウィジェットの埋め込みコードをコピーします。

これで Azure Media Player と通信できるようになります。

詳細については、「[Azure Media Player + VI 分析情報のデモ](https://codepen.io/videoindexer/pen/rYONrO)」をご覧ください。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Video Indexer のコグニティブな分析情報ウィジェットを埋め込み、別のビデオ プレーヤーを使用する

Azure Media Player 以外のビデオ プレーヤーを使用する場合、通信を実現するには、ビデオ プレーヤーを手動で操作する必要があります。 

1. ビデオ プレーヤーを挿入します。

    たとえば、標準の HTML5 プレーヤーです。

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. コグニティブな分析情報ウィジェットを埋め込みます。
3. "メッセージ" イベントをリッスンして、プレーヤー用の通信を実装します。 次に例を示します。

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

詳細については、「[Azure Media Player + VI 分析情報のデモ](https://codepen.io/videoindexer/pen/YEyPLd)」をご覧ください。

## <a name="adding-subtitles"></a>字幕の追加

Video Indexer の分析情報と独自の [Azure Media Player](https://aka.ms/azuremediaplayer) を埋め込む場合は、**GetVttUrl** メソッドを使用してクローズド キャプション (字幕) を取得できます。 Video Indexer の AMP プラグイン **getSubtitlesUrl** から JavaScript メソッドを呼び出すこともできます (前述のとおり)。 

## <a name="customizing-embeddable-widgets"></a>埋め込み可能なウィジェットのカスタマイズ

### <a name="cognitive-insights-widget"></a>コグニティブな分析情報ウィジェット

必要な分析情報の種類を選択できます。 これを行うには、(API または Web アプリから) 取得した埋め込みコードに追加される URL パラメーター `&widgets=<list of wanted widgets>` にそれらを値として指定します。

指定できる値は、**people**、**keywords**、**sentiments**、**transcript**、**search** です。

たとえば、people および search の分析情報のみを含むウィジェットを埋め込む場合、iframe の埋め込み URL は次のようになります。

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe ウィンドウのタイトルも、iframe の URL に `&title=<YourTitle>` を指定することでカスタマイズできます (これにより、HTML \<title> 値がカスタマイズされます)。
    
たとえば、iframe ウィンドウに "MyInsights" というタイトルを付けたい場合、URL は次のようになります。

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

このオプションは、新しいウィンドウで分析情報を開く必要がある場合にのみ該当することに注意してください。

### <a name="player-widget"></a>プレーヤー ウィジェット

Video Indexer プレーヤーを埋め込む場合は、iframe のサイズを指定することで、プレーヤーのサイズを選択できます。

次に例を示します。

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

既定では、Video Indexer プレーヤーで、ビデオのトランスクリプトに基づいたクローズド キャプションが自動生成されます。 トランスクリプトは、ビデオがアップロードされた時に選択されたソース言語でビデオから抽出されます。

別の言語で埋め込む場合は、`&captions=< Language | "all" | "false" >` を埋め込みプレーヤー の URL に追加します。 使用可能なすべての言語にキャプションが必要な場合は、値 `all` を使用してください。 既定でキャプションが表示されるようにする場合は、`&showCaptions=true` を渡します。

埋め込み URL は次のようになります。 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

キャプションを無効にする場合は、`captions` パラメーター値を `false` として渡します。

#### <a name="autoplay"></a>AutoPlay
既定では、プレーヤーでビデオの再生が開始されます。 前の埋め込み URL に `&autoplay=false` を渡すことによって、そうしないことを選択できます。

## <a name="code-samples"></a>コード サンプル

以下の Video Indexer API とウィジェットのサンプルを含む[コード サンプル](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) リポジトリを参照してください。

| ファイル/フォルダー                       | 説明                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | カスタムの Azure Media Player で Video Indexer のビデオを読み込みます                        |
| `azure-media-player-vi-insights`  | カスタムの Azure Media Player を使用して VI 分析情報を埋め込みます                             |
| `control-vi-embedded-player`      | VI プレーヤーを埋め込んで、外部から制御します                                    |
| `custom-index-location`           | カスタムの外部の場所 (お客様の blob を指定できます) から VI 分析情報を埋め込みます     |
| `embed-both-insights`             | プレーヤーと分析情報の両方での VI 分析情報の基本的な使用方法                            |
| `embed-insights-with-AMP`         | カスタムの Azure Media Player を使用して VI 分析情報ウィジェットを埋め込みます                      |
| `customize-the-widgets`           | カスタマイズされたオプションを使用して VI ウィジェットを埋め込みます                                     |
| `embed-both-widgets`              | VI プレーヤーと分析情報を埋め込んで、両者間の通信を行います                      |
| `url-generator`                   | ユーザー指定のオプションに基づいて、ウィジェットのカスタム埋め込み URL を生成します             |
| `html5-player`                    | 既定の HTML5 ビデオ プレーヤーを使用して VI 分析情報を埋め込みます                            |

## <a name="next-steps"></a>次のステップ

Video Indexer の分析情報を表示および編集する方法の詳細については、「[Video Indexer の分析情報の表示と編集](video-indexer-view-edit.md)」をご覧ください。

また、[Video Indexer Codepen](https://codepen.io/videoindexer/pen/eGxebZ) もご確認ください。
