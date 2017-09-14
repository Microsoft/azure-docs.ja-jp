---
title: "DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み | Microsoft Docs"
description: "このトピックでは、DASH.js を使用した HTML5 アプリケーションに MPEG-DASH アダプティブ ストリーミング ビデオを埋め込む方法を説明します。"
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: juliako
ms.translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 35ba9161f70a27a215685830d1a9e7c1881cc3bb
ms.contentlocale: ja-jp
ms.lasthandoff: 11/17/2016

---
# <a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>DASH.js を使用した HTML5 アプリケーションへの MPEG-DASH アダプティブ ストリーミング ビデオの埋め込み
## <a name="overview"></a>Overview
MPEG DASH は、高品質なアダプティブ ビデオ ストリーミング出力を配信する必要があるユーザーに多くのメリットを提供するビデオ コンテンツのアダプティブ ストリーミングの ISO 標準です。 MPEG DASH では、ネットワークが混雑すると自動的にビデオ ストリームが低解像度に変更されます。 これにより、プレーヤーが次に再生する数秒分をダウンロードする間 (バッファリング)、視聴者に「一時停止された」状態のビデオが表示される可能性が減少します。 ネットワークの混雑が緩和されると、ビデオ プレーヤーは高品質ストリームに戻ります。 この機能は必要な帯域幅に対応するもので、結果的にビデオの開始時間が高速化されます。 つまり、最初の数秒は高速にダウンロードされる低品質のセグメントで再生され、十分なコンテンツがバッファリングされると、より高い品質にステップアップします。

Dash.js は JavaScript で記述された、オープン ソースの MPEG-DASH ビデオ プレーヤーです。 ビデオ再生機能を必要とするアプリケーションで自由に再使用できる、堅牢なクロスプラットフォーム プレイヤーを提供することが目的です。 W3C Media Source Extensions (MSE) をサポートするどのブラウザーでも MPEG-DASH を再生できるようになります。現在では Chrome、Microsoft Edge、IE11 がこれに該当します (他のブラウザーも今後 MSE に対応する予定です)。 DASH.js の詳細については、「jGitHub dash.js リポジトリ」をご覧ください。

## <a name="creating-a-browser-based-streaming-video-player"></a>ブラウザー ベースのストリーミング ビデオ プレーヤーの作成
再生、一時停止、巻き戻しなどのコントロールを備えたビデオ プレーヤーを表示するシンプルな Web ページを作成するには:

1. HTML ページを作成します
2. ビデオ タグを追加します
3. Dash.js プレーヤーを追加します
4. プレイヤーを初期化します
5. CSS スタイルを追加します
6. MSE を実装するブラウザーに結果を表示します

プレーヤーの初期化は、数行の JavaScript コードで実行できます。 dash.js を使用すると、ブラウザー ベースのアプリケーションに MPEG-DASH ビデオを簡単に埋め込むことができます。

## <a name="creating-the-html-page"></a>HTML ページの作成
最初の手順では、**ビデオ** 要素を含む標準の HTML ページを作成し、以下の例のようにこのファイルを basicplayer.html に保存します。

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

## <a name="adding-the-dashjs-player"></a>DASH.js プレーヤーの追加
dash.js リファレンス実装をアプリケーションに追加するには、dash.js プロジェクトの 1.0 リリースから dash.all.js ファイルを入手する必要があります。 このファイルはアプリケーションの JavaScript フォルダーに保存します。 このファイルは、必要なすべての dash.js コードを 1 つのファイルにまとめることのできる便利なファイルです。 dash.js リポジトリを参照すると、個別のファイルやテスト コードなどさまざまな情報が見つかりますが、dash.js の使用のみが目的であれば、必要になるのは dash.all.js ファイルのみです。

dash.js プレーヤーをアプリケーションを追加するには、basicPlayer.html のヘッド セクションにスクリプト タグを追加します。

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


次に、ページの読み込み時にプレーヤーを初期化する関数を作成します。 dash.all.js を読み込む行の後ろに次のスクリプトを追加します。

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

この関数では、まず DashContext が作成されます。 これは、特定のランタイム環境でのアプリケーションの構成に使用されます。 技術的な観点では、この関数はアプリケーションの構築時に依存関係挿入フレームワークが使用するクラスを定義します。 ほとんどの場合、Dash.di.DashContext を使用することになります。

次に、dash.js フレームワークの MediaPlayer のプライマリ クラスをインスタンス化します。 このクラスには、再生や一時停止などの必要なコア メソッドが含まれ、ビデオ要素との関係の管理や再生するビデオを示す Media Presentation Description (MPD) ファイルの解釈が管理されます。

プレーヤーがビデオを再生する準備ができていることを確認するため、MediaPlayer クラスの startup() 関数が呼び出されます。 特に、この関数では、すべての必要な (コンテキストで定義されたとおりの) クラスが正常に読み込まれたことを確認できます。 プレイヤーの準備が整ったら、attachView() 関数を使用して、ビデオ要素をアタッチします。 これにより、MediaPlayer がビデオ ストリームを要素に挿入し、必要に応じて再生をコントロールできるようになります。

MediaPlayer に MPD ファイルの URL を渡して、再生予定のビデオについて通知します。作成した setupVideo() 関数は、ページを完全に読み込んだ後に実行する必要があります。 この操作は、Body 要素の OnLoad イベントを使用して行います。 <body> 要素を次に変更します。

    <body onload="setupVideo()">

最後に、CSS を使用してビデオ要素のサイズを設定します。 アダプティブ ストリーミング環境では、ネットワークの状況に応じて再生するビデオのサイズが変化するため、この操作は特に重要です。 以下の簡単なデモでは、次の CSS をページのヘッド セクションに追加して、ビデオ要素を利用可能なブラウザー ウィンドウの 80% に設定します。

    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

## <a name="playing-a-video"></a>ビデオの再生
ビデオを再生するには、basicPlayback.html ファイルでブラウザーをポイントし、表示されるビデオ プレーヤーで再生をクリックします。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>関連項目
[ビデオ プレーヤー アプリケーションの開発](media-services-develop-video-players.md)

[GitHub dash.js リポジトリ](https://github.com/Dash-Industry-Forum/dash.js) 


