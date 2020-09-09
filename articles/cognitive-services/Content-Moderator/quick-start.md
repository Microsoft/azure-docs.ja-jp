---
title: クイック スタート:Web 上で Content Moderator を試す - Content Moderator
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、オンライン Content Moderator レビュー ツールを使用して、コードを記述せずに Content Moderator の基本的な機能をテストします。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 06/29/2020
ms.author: pafarley
ms.openlocfilehash: 550cec535343d48ad74dd35e6886af78bdd15701
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563279"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>クイック スタート:Web 上で Content Moderator を試す

このクイックスタートでは、オンライン Content Moderator レビュー ツールを使用して、コードを記述せずに Content Moderator の基本的な機能をテストします。 このサービスをより迅速にアプリに統合する場合、[次のステップ](#next-steps)のセクションのその他のクイック スタートを参照してください。

## <a name="prerequisites"></a>前提条件

- Web ブラウザー

## <a name="set-up-the-review-tool"></a>レビュー ツールのセットアップ
Content Moderator レビュー ツールは、レビューア担当者が意思決定における Cognitive Service を支援できる Web ベースのツールです。 このガイドでは、レビュー ツール セットアップの簡単なプロセスを説明し、Content Moderator サービスの動作方法を確認します。 [Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)のサイトに移動し、サインアップします。

![Content Moderator ホーム ページ](images/homepage.PNG)

## <a name="create-a-review-team"></a>レビュー チームを作成する

次に、レビュー チームを作成します。 作業のシナリオでは、これは、サービスのモデレート意思決定を手作業でレビューするユーザーのグループになります。 チームを作成するには、 **[リージョン]** を選択し、 **[チーム名]** と **[チーム ID]** を指定します。 仕事仲間をチームに招待する場合は、仲間のメール アドレスをここに入力します。

> [!NOTE]
> **[チーム名]** は、ご自分のレビュー チームのフレンドリ名です。 この名前は Azure portal に表示されます。 **[チーム ID]** は、ご自分のレビュー チームのプログラムでの識別に使用されます。

> [!div class="mx-imgBorder"]
> ![チーム メンバーを招待する](images/create-team.png)

カスタマー マネージド キー (CMK) を使用してデータを暗号化することを選択した場合は、E0 価格レベルのご自分の Content Moderator リソースの **[リソース ID]** の入力が求められます。 指定するリソースは新しいものである必要があります。 

> [!div class="mx-imgBorder"]
> ![カスタマー マネージド キーを使用してチーム メンバーを招待する](images/create-team-cmk.png)

Content Moderator のリソースを再利用しようとすると、次の警告が表示されます。 

> [!div class="mx-imgBorder"]
> ![CMK failure](images/create-team-cmk-fail.png)\(CMK エラー\)

## <a name="upload-sample-content"></a>サンプル コンテンツのアップロード

サンプル コンテンツをアップロードする準備ができました。 **[Try > Image] (トライ > イメージ)** 、 **[Try > Text] (トライ > テキスト)** 、または **[Try > Video] (トライ > ビデオ)** .を選択します。

![画像またはテキスト モデレーションを試す](images/tryimagesortext.png)

モデレート用にコンテンツを送信します。 レビュー ツールは内部で、モデレート API を呼び出し、コンテンツをスキャンします。 スキャンが完了したら、レビューを待機している結果があることを通知するメッセージが表示されます。

![ファイルをモデレートする](images/submitted.png)

## <a name="review-moderation-tags"></a>モデレート タグのレビュー

適用したモデレート タグをレビューします。 コンテンツに適用されたタグおよび各カテゴリのスコアを確認できます。 さまざまなコンテンツ タグが何を示すかについては、[イメージ](image-moderation-api.md)、[テキスト](text-moderation-api.md)、および[ビデオ](video-moderation-api.md)のモデレーション トピックを参照してください。

![結果の確認](images/reviewresults_text.png)

プロジェクトでは、ユーザーまたはユーザーのレビュー チームは、必要に応じてタグを変更したり、タグを追加したりできます。 **次へ**ボタンでこれらの変更を送信します。 ビジネス アプリケーションが Moderator API を呼び出すと、タグ付きコンテンツは、ここでキューに入り、人間のレビュー チームにレビューされる用意が整えられます。 このアプローチを使用して、大量のコンテンツをすばやくレビューできます。

ここまでで、Content Moderator レビュー ツールを使用して Content Moderator サービスが実行できる内容の例を確認しました。 次に、レビュー ツールについてさらに学習し、レビュー API を使用してソフトウェア プロジェクトに統合する方法を学習するか、または[次の手順](#next-steps)のセクションまでスキップし、アプリで Moderation API 自体を使用する方法を学習できます。

## <a name="learn-more-about-the-review-tool"></a>レビュー ツールについてさらに学習する

Content Moderator レビュー ツールを使用する方法についてさらに学習するには、[レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md) ガイドを参照し、レビュー ツール API を参照して人間のレビュー エクスペリエンスを微調整する方法について学習します。
- [ジョブ API](try-review-api-job.md) は、モデレーション API を使用することによりコンテンツをスキャンし、レビュー ツールでレビューを生成します。 
- [レビュー API](try-review-api-review.md) は、最初にコンテンツをスキャンすることなく、人間の モデレーター用の画像、テキスト、またはビデオ レビューを直接作成します。 
- [ワークフロー API](try-review-api-workflow.md) は、自分のチームが作成するカスタム ワークフローに関する詳細を作成、更新、取得します。

または、次の手順に進み、コードでの Moderation API の使用を開始します。

## <a name="next-steps"></a>次のステップ

アプリで Moderation API 自体を使用する方法を学習します。
- 画像のモデレートを実装します。 [API コンソール](try-image-api.md)を使用するか、または [.NET SDK クイックスタート](dotnet-sdk-quickstart.md)に従って、画像をスキャンして、タグ、信頼度スコア、および他の抽出された情報を使用することによって、成人向けまたはわいせつな可能性のあるコンテンツを検出します。
- テキストのモデレートを実装します。 [API コンソール](try-text-api.md)または [.NET SDK クイック スタート](dotnet-sdk-quickstart.md)を使用して、潜在的な不適切な表現、マシン支援の不要なテキスト分類 (プレビュー)、個人データに関してテキスト コンテンツをスキャンします。
- ビデオのモデレートを実装します。 [C# 用のビデオのモデレートに関するハウツー ガイド](video-moderation-api.md)に関するページ従ってビデオをスキャンし、成人向けやきわどいコンテンツの可能性があるものを検出します。 
