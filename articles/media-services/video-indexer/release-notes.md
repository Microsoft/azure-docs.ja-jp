---
title: Azure Media Services Video Indexer リリース ノート | Microsoft Docs
description: 常に最新の開発情報を把握していただけるよう、この記事では Azure Media Services Video Indexer の最新の更新情報を提供します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/05/2020
ms.author: juliako
ms.openlocfilehash: 1b11dc0ee17fd0686776a7262c4bc99bd98bcc3e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837311"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer リリース ノート

>URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` をコピーして、お使いの RSS フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

* 最新のリリース
* 既知の問題
* バグの修正
* 非推奨の機能

## <a name="july-2020"></a>2020 年 7 月

### <a name="ga-for-multi-language-identification"></a>GA になった複数言語の識別

複数言語の識別はプレビューから GA に移行され、運用環境で使用する準備ができています。

"プレビューから GA" への移行に関連する価格への影響はありません。

### <a name="video-indexer-website-improvements"></a>Video Indexer Web サイトの改善

#### <a name="adjustments-in-the-video-gallery"></a>ビデオ ギャラリーでの調整

詳細な分析情報の検索用に、追加のフィルター機能を備えた新しい検索バーが追加されました。 検索結果も拡張されています。

複数のファイルを含むビデオ アーカイブの並べ替えと管理を行う機能を備えた新しいリスト ビュー。

#### <a name="new-panel-for-easy-selection-and-configuration"></a>選択と構成が容易な新しいパネル

選択とユーザーの構成が容易なサイド パネルが追加され、シンプルで迅速なアカウントの作成と共有や、構成の設定が可能になっています。

サイド パネルは、ユーザー設定やヘルプにも使用されます。

## <a name="june-2020"></a>2020 年 6 月

### <a name="search-by-topics"></a>トピックで検索

検索 API を使用して、特定のトピックでビデオを検索できるようになりました (API のみ)。

トピックは、`textScope` (省略可能なパラメーター) の一部として追加されます。 詳細については [API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Search-Videos) を参照してください。  

### <a name="labels-enhancement"></a>ラベルの機能強化

ラベルのタグ付け機能がアップグレードされました。これにより、特定しやすいより視覚的なラベルが追加されます。

## <a name="may-2020"></a>2020 年 5 月

### <a name="video-indexer-deployed-in-the-east-us"></a>米国東部でデプロイされる Video Indexer

Video Indexer 有料アカウントを米国東部リージョン作成できるようになりました。
 
### <a name="video-indexer-url"></a>Video Indexer の URL

Video Indexer のリージョン エンドポイントはすべて、www でのみ始まるように統一されました。 必要な操作はありません。

今後は、ウィジェットを埋め込む場合も、Video Indexer Web アプリケーションにログインする場合も www.videoindexer.ai にアクセスします。

また、wus.videoindexer.ai も www にリダイレクトされます。 詳細については、「[お使いのアプリに Video Indexer ウィジェットを埋め込む](video-indexer-embed-widgets.md)」を参照してください。

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新しいウィジェット パラメーターの機能

**Insights** ウィジェットには、`language` と `control` という新しいパラメーターが含まれています。

**プレーヤー** ウィジェットには、新しい `locale` パラメーターがあります。 `locale` と `language` パラメーターは両方とも、プレーヤーの言語を制御します。

詳細については、「[ウィジェットの種類](video-indexer-embed-widgets.md#widget-types)」セクションを参照してください。 

### <a name="new-player-skin"></a>新しいプレーヤー スキン

新しいプレーヤー スキンが、更新されたデザインで利用可能になりました。

### <a name="prepare-for-upcoming-changes"></a>今後予定されている変更の準備をする

* 現在、次の API からアカウント オブジェクトが返されます。

    * [Create-Paid-Account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [Get-Account](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [Get-Accounts-With-Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    アカウント オブジェクトには、[Video Indexer Web サイト](https://www.videoindexer.ai/)の場所をポイントする `Url` フィールドがあります。
有料アカウントの場合、`Url` フィールドは現在、パブリック Web サイトではなく、内部 URL をポイントしています。
数週間のうちに、Microsoft はこれを変更し、すべてのアカウント (試用版および有料版) に [Video Indexer Web サイト](https://www.videoindexer.ai/)の URL を返すようになります。

    内部 URL を使用しないでください。[Video Indexer パブリック API](https://api-portal.videoindexer.ai/) を使用する必要があります。
* Video Indexer URL をアプリケーションに埋め込んでいて、URL が [Video Indexer Web サイト](https://www.videoindexer.ai/)も Video Indexer API エンドポイント (`https://api.videoindexer.ai`) もポイントしておらず、リージョン エンドポイント (`https://wus2.videoindexer.ai` など) をポイントしている場合は、URL を再生成してください。

   次のいずれかでこれを行うことができます。

    * URL を Video Indexer ウィジェット API をポイントする URL (たとえば、[Insights ウィジェット](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget)) に置き換える
    * Video Indexer Web サイトを使用して、新しい埋め込み URL を生成する
         
         **[再生]** を押してビデオのページに移動し、 **&lt;/&gt; [埋め込み]** ボタンをクリックして、その URL をご利用のアプリケーションにコピーします。
   
    リージョンの URL はサポートされておらず、今後数週間のうちにブロックされます。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>追加言語に対するカスタム言語サポート

Video Indexer で、`ar-SY`、`en-UK`、および `en-AU` のカスタム言語モデルがサポートされるようになりました (API のみ)。
 
### <a name="delete-account-timeframe-action-update"></a>アカウント削除アクションのタイムフレームの更新

アカウント削除アクションで、48 時間ではなく 90 日以内にアカウントが削除されるようになりました。
 
### <a name="new-video-indexer-github-repository"></a>新しい Video Indexer GitHub リポジトリ

さまざまなプロジェクトを含む新しい Video Indexer GitHub、ファースト ステップ ガイド、およびコード サンプルを使用できるようになりました。 https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger の更新

Video Indexer で、**認証**と**操作**が単一の[Video Indexer OpenAPI 仕様 (swagger)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson) に統合されました。 開発者は、[Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai/)で API を見つけることができます。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>新しい API を使用して音声テキストを更新する

[Update-Video-Index](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API を使用して、音声テキストの特定のセクションを更新します。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Video Indexer ポータルからアカウント構成を修正する

次のような問題を解決するために、Media Services 接続構成を更新できるようになりました。 

* Azure Media Services のリソースが正しくない
* パスワードの変更
* Media Services リソースがサブスクリプション間で移動された  

アカウント構成を修正するには、Video Indexer ポータルで、(所有者として) [設定] > [アカウント] タブの順に移動します。

### <a name="configure-the-custom-vision-account"></a>Custom Vision アカウントを構成する

Video Indexer ポータルを使用して、有料アカウントで Custom Vision アカウントを構成します (以前は API でのみサポートされていました)。 これを行うには、Video Indexer ポータルにサインインし、[モデルのカスタマイズ] > [アニメーション化されたキャラクター] > [構成] の順に選択します。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>シーン、ショット、キーフレーム - 1 つの分析情報ウィンドウに表示されるようになりました

シーン、ショット、およびキーフレームは、使用とナビゲーションを容易にするために 1 つの分析情報に結合されるようになりました。 目的のシーンを選択すると、そこに含まれるショットとキーフレームが表示されます。 

### <a name="notification-about-a-long-video-name"></a>長いビデオ名に関する通知

ビデオ名が 80 文字より長い場合、Video Indexer はアップロード時に状況を説明するエラーを表示します。

### <a name="streaming-endpoint-is-disabled-notification"></a>ストリーミング エンドポイントが無効になっているという通知

ストリーミング エンドポイントが無効になっている場合、Video Indexer には、プレーヤー ページに状況を説明するエラーが表示されます。

### <a name="error-handling-improvement"></a>エラー処理の改善

現在のインデックスの再作成の変更が誤って上書きされるのを防ぐために、ビデオにアクティブにインデックスが作成された場合は、[ビデオのインデックスを再作成](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) API と[ビデオ インデックス](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) API から状態コード 409 が返されるようになりました。

## <a name="november-2019"></a>2019 年 11 月
 
* 韓国語のカスタム言語モデルのサポート

    Video Indexer では、API とポータルの両方で韓国語 (`ko-KR`) のカスタム言語モデルがサポートされるようになりました。 
* 音声テキスト変換 (STT) でサポートされる新しい言語

    Video Indexer API では、アラビア語 (レバント) (ar-SY)、英語 (英国) 言語 (en-GB)、および英語 (オーストラリア) 言語 (en-AU) の STT がサポートされるようになりました。
    
    動画のアップロードでは、zh-HANS が zh-CN に置き換えられています。どちらもサポートされていますが、zh-CN の方がより正確であるため、推奨されています。
    
## <a name="october-2019"></a>2019 年 10 月
 
* ギャラリーでアニメーション化された文字を検索する

    アニメーション化された文字のインデックスを作成すると、アカウントのビデオ ギャラリーで検索できるようになりました。 詳細については、[アニメーション化された文字の認識](animated-characters-recognition.md)に関する記事を参照してください。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 で発表されたさまざまな進化:
 
* アニメーション文字の認識 (パブリック プレビュー)

    Custom Vision との統合で、アニメーション コンテンツでグループを検出したり、文字を認識したりできるようになりました。 詳細については、「[アニメーション キャラクターの検出](animated-characters-recognition.md)」を参照してください。
* 複数言語の識別 (パブリック プレビュー)

    オーディオ トラックに含まれる複数の言語からセグメントを検出し、それに基づいて多言語のトランスクリプトを作成します。 初回サポート:英語、スペイン語、ドイツ語、フランス語。 詳細については、「[複数言語のコンテンツを自動的に識別および文字起こしする](multi-language-identification-transcription.md)」を参照してください。
* 人と場所に関して名前付きのエンティティを抽出する

    自然言語処理 (NLP) を使用して、音声および視覚テキストからブランド、場所、および人物を抽出します。
* 編集タイプでショットを分類する

    クローズアップ、ミディアムショット、ツーショット、屋内、屋外など、編集タイプでショットにタグを付けます。詳細については、「[編集ショット タイプの検出](scenes-shots-keyframes.md#editorial-shot-type-detection)」を参照してください。
* トピック推論の機能強化 - 現在のレベル 2
    
    トピック推論モデルでは、さらに細かな IPTC 分類に対応できるようになりました。 詳しくは、「[Azure Media Services の AI を活用した新たなイノベーション](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)」をお読みください。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英国南部でデプロイされる Video Indexer

Video Indexer 有料アカウントを英国南部リージョンで作成できるようになりました。

### <a name="new-editorial-shot-type-insights-available"></a>利用可能になった新しい編集ショット タイプ分析情報

ビデオ ショットに追加された新しいタグにより、コンテンツ作成ワークフローで使用される次のような一般的な編集語句を識別するための "ショット タイプ" が提供されます: 超クローズアップ、クローズアップ、ワイド、中程度、2 ショット、屋外、室内、左の顔、右の顔 (JSON で使用可能)。

### <a name="new-people-and-locations-entities-extraction-available"></a>利用可能になった新しい人と場所のエンティティ抽出

Video Indexer では、ビデオの OCR と文字起こしからの自然言語処理 (NLP) により、名前付きの場所と人が識別されます。 Video Indexer では、機械学習アルゴリズムを使用して、特定の場所 (エッフェル塔など) や人 (John Doe など) などのビデオでの言及が認識されます。

### <a name="keyframes-extraction-in-native-resolution"></a>ネイティブ解像度でのキーフレームの抽出

Video Indexer によって抽出されたキーフレームを、ビデオの元の解像度で使用できます。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA になった画像からのカスタム顔モデルのトレーニング

画像からの顔のトレーニングが、プレビュー モードから GA になりました (API およびポータルで利用可能)。

> [!NOTE]
> "プレビューから GA" への移行に関連する価格への影響はありません。

### <a name="hide-gallery-toggle-option"></a>ギャラリー非表示の切り替えオプション

ユーザーは、ポータルからギャラリー タブを非表示にすることができます (サンプル タブの非表示と同様)。
 
### <a name="maximum-url-size-increased"></a>URL の最大サイズの拡大

ビデオのインデックス作成時にサポートされる URL クエリ文字列が (2048 文字ではなく) 4096 文字になります。
 
### <a name="support-for-multi-lingual-projects"></a>多言語プロジェクトのサポート

異なる言語でインデックス付けされた複数のビデオに基づいて、プロジェクトを作成できるようになりました (API のみ)。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>ウィジェットとしてのエディター

Video Indexer AI エディターが、お客様のアプリケーションに埋め込むためのウィジェットとして提供されています。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>ポータルでクローズド キャプション ファイルからカスタム言語モデルを更新する

お客様は、ポータルのカスタマイズ ページから、言語モデルの入力として VTT、SRT、TTML の各ファイル形式を指定できます。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer を東日本にデプロイ

Video Indexer 有料アカウントを東日本リージョンで作成できるようになりました。

### <a name="create-and-repair-account-api-preview"></a>アカウントの作成および修復 API (プレビュー)

[Azure Media Service の接続エンドポイントまたはキーを更新](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)できる新しい API が追加されました。

### <a name="improve-error-handling-on-upload"></a>アップロードでのエラー処理の向上 

基になる Azure Media Services アカウントの構成に誤りがある場合には、説明のメッセージが返されます。

### <a name="player-timeline-keyframes-preview"></a>プレーヤー タイムラインのキーフレーム プレビュー 

プレーヤーのタイムライン上の各時間に対するイメージ プレビューを表示できるようになりました。

### <a name="editor-semi-select"></a>エディターの半選択

エディターで特定の分析情報期間を選択した結果として選択されるすべての分析情報のプレビューを表示できるようになりました。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>クローズド キャプション ファイルからのカスタム言語モデルの更新

[カスタム言語モデルの作成](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)および[カスタム言語モデルの更新](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) API は、言語モデルの入力として、VTT、SRT、および TTML ファイル形式をサポートするようになりました。

[ビデオ トランスクリプトの更新 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript) を呼び出すと、トランスクリプトが自動的に追加されます。 ビデオに関連付けられたトレーニング モデルも自動的に更新されます。 使用する言語モデルをカスタマイズおよびトレーニングする方法については、「[Video Indexer で言語モデルをカスタマイズする](customize-language-model-overview.md)」を参照してください。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新しいダウンロード トランスクリプト形式 – TXT と CSV

Video Indexer は、既にサポートされているクローズド キャプション形式 (SRT、VTT、TTML) に加えて、TXT および CSV 形式でのトランスクリプトのダウンロードをサポートするようになりました。

## <a name="next-steps"></a>次のステップ

[概要](video-indexer-overview.md)
