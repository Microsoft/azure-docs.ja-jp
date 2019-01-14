---
title: チュートリアル:eコマース製品画像をモデレートする - Content Moderator
titlesuffix: Azure Cognitive Services
description: アプリケーションを設定して、指定されたラベルによって製品画像を分析および分類し (Azure Computer Vision と Custom Vision を使用)、好ましくない画像にタグを付けてさらにレビューできるようにします (Azure Content Moderator を使用)。
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: pafarley
ms.openlocfilehash: 209fb3bba2b5462caad53d809c46eba0ebf4d836
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547900"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>チュートリアル:Azure Content Moderator を使用して eコマース製品画像をモデレートする

このチュートリアルでは、Content Moderator などの Azure Cognitive Services を使用し、eコマース シナリオ用の製品画像を効果的に分類してモデレートする方法について学習します。 Computer Vision と Custom Vision を使用してさまざまなタグ (ラベル) を画像に適用したうえで、チーム レビューを作成します。これにより、Content Moderator の機械学習に基づくテクノロジが人間のレビュー チームと組み合わされ、インテリジェントなモデレーション システムが実現します。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Content Moderator にサインアップして、レビュー チームを作成します。
> * Content Moderator の Image API を使用して、成人向けとわいせつ向けの可能性があるコンテンツをスキャンします。
> * Computer Vision サービスを使用して、著名人のコンテンツ (または、Computer Vision で検出可能なその他のタグ) をスキャンします。
> * Custom Vision サービスを使用して、旗、おもちゃ、ペン (またはその他のカスタム タグ) の存在をスキャンします。
> * 人間によるレビューと最終的な意思決定のために、組み合わされたスキャン結果を表示します。

完全なサンプル コードは、GitHub 上の [samples-eCommerceCatalogModeration](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) リポジトリで入手できます。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

- Content Moderator のサブスクリプション キー。 [Cognitive Services アカウントの作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に関するページの手順に従って、Content Moderator サービスをサブスクライブし、お客様のキーを取得してください。
- Computer Vision のサブスクリプション キー (上記と同じ手順)。
- [Visual Studio 2015 または 2017](https://www.visualstudio.com/downloads/) の任意のエディション。
- Custom Vision 分類器によって使用される各ラベル用の画像のセット (ここでは、おもちゃ、ペン、米国旗)。

## <a name="create-a-review-team"></a>レビュー チームを作成する

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)にサインアップしてレビュー チームを作成する方法の手順については、「[クイック スタート: Content Moderator の概要](quick-start.md)」を参照してください。 **[資格情報]** ページの **[チーム ID]** を書き留めておきます。

## <a name="create-custom-moderation-tags"></a>カスタム モデレーション タグの作成

次に、レビュー ツールのカスタム タグを作成します (この手順についてヘルプが必要な場合は、[タグ](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags)に関する記事を参照してください)。 ここでは、**著名人**、**米国**、**旗**、**おもちゃ**、**ペン**の各タグを追加します。 すべてのタグが Computer Vision で検出可能なカテゴリである必要はないことに注意してください (例: **著名人**)。後で検出するために Custom Vision 分類器をトレーニングするのであれば、お客様独自のカスタム タグを追加してかまいません。

![カスタム タグを構成する](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Visual Studio プロジェクトの作成

1. Visual Studio で、[新しいプロジェクト] ダイアログを開きます。 **[インストール済み]**、**[Visual C#]** の順に展開し、**[コンソール アプリ (.NET Framework)]** を選択します。
1. アプリケーションに「**EcommerceModeration**」という名前を付けて、**[OK]** をクリックします。
1. このプロジェクトを既存のソリューションに追加する場合、このプロジェクトをシングル スタートアップ プロジェクトとして選択します。

このチュートリアルでは、プロジェクトの中心となるコードが主に取り上げられますが、必要なすべてのコード行は網羅されません。 サンプル プロジェクト ([samples-eCommerceCatalogModeration](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) から _Program.cs_ のすべての内容をお客様の新しいプロジェクトの _Program.cs_ ファイルにコピーします。 次に、以下のセクションの手順に従って、プロジェクトのしくみとそれを自分で使用する方法について学習してください。

## <a name="define-api-keys-and-endpoints"></a>API キーとエンドポイントの定義

上記のとおり、このチュートリアルでは 3 つのコグニティブ サービスを使用します。そのため、対応する 3 つのキーと API エンドポイントが必要です。 **Program** クラスの次のフィールドを見てください。 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

`___Key` フィールドは、お客様のサブスクリプション キーの値で更新する必要があります (後で `CustomVisionKey` を取得します)。また、`___Uri` フィールドは、適切なリージョン識別子を含むように変更することが必要な場合があります。 `ReviewUri` フィールドの `YOURTEAMID` 部分には、お客様が先ほど作成したレビュー チームの ID を入力してください。 `CustomVisionUri` フィールドの最後の部分は後で入力します。

## <a name="primary-method-calls"></a>主要なメソッドの呼び出し

**Main** メソッドの次のコードを見てください。これにより、画像 URL の一覧がループ処理されます。 3 つの異なるサービスによって各画像が分析され、適用されたタグが **ReviewTags** 配列に記録されてから、人間のモデレーター用のレビューが作成されます (Content Moderator レビュー ツールに画像が送信されます)。 以下のセクションでは、これらのメソッドについて詳しく見ていきます。 どのタグが適用されたかをチェックするための条件付きステートメントで **ReviewTags** 配列を使用して、レビュー用に送信される画像をここで制御できることに注意してください。

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy メソッド

**Program** クラスの **EvaluateAdultRacy** メソッドを見てください。 このメソッドでは、画像 URL、およびキーと値のペアの配列がパラメーターとして受け取られます。 これによって Content Moderator の Image API が呼び出され (REST を使用)、画像の Adult と Racy のスコアが取得されます。 いずれかのスコアが 0.4 を超える場合 (範囲は 0 から 1)、**ReviewTags** 配列の対応する値が **True** に設定されます。

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags メソッド

次のメソッドでは、画像 URL とお客様の Computer Vision サブスクリプション情報が受け取られ、画像に著名人が存在するかどうかが分析されます。 1 人または複数の著名人が見つかった場合、**ReviewTags** 配列の対応する値が **True** に設定されます。 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateCustomVisionTags メソッド

次に、**EvaluateCustomVisionTags** メソッドを見てください。これにより、実際の製品 (ここでは、旗、おもちゃ、ペン) が分類されます。 画像内の旗、おもちゃ、ペン (またはお客様がご自分のカスタム タグとして選択した任意のもの) の存在を検出するために、[分類器の構築方法](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier)に関するガイドの手順に従って、お客様独自のカスタム画像分類器を構築します。

![ペン、おもちゃ、旗のトレーニング画像が含まれた Custom Vision の Web ページ](images/tutorial-ecommerce-custom-vision.PNG)

お客様の分類器をトレーニングできたら、予測キーと予測エンドポイント URL を取得し (これらの取得にヘルプが必要な場合は「[URL と予測キーを取得する](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key)」を参照してください)、それらの値をお客様の `CustomVisionKey` フィールドと `CustomVisionUri` フィールドにそれぞれ割り当てます。 メソッドによってこれらの値が使用され、分類器に対してクエリが実行されます。 分類器によって画像内にカスタム タグが 1 つまたは複数見つかった場合、このメソッドによって、**ReviewTags** 配列の対応する値が **True** に設定されます。 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>レビュー ツール用のレビューの作成

これまでのセクションでは、成人向けコンテンツとわいせつなコンテンツ (Content Moderator)、著名人 (Computer Vision)、さまざまな他のオブジェクト (Custom Vision) について受信画像をスキャンするメソッドに注目しました。 次は、**CreateReview** メソッドを見てください。これによって、適用されたすべてのタグ (_Metadata_ として渡されます) と共に画像が Content Moderator レビュー ツールにアップロードされ、人間がレビューに使用できるようになります。 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)の [レビュー] タブに画像が表示されます。

![複数の画像と強調表示されたそれらのタグが含まれた Content Moderator レビュー ツールのスクリーンショット](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>テスト画像の一覧の送信

**Main** メソッドを見るとわかるように、このプログラムでは、画像 URL の一覧が記載されている _Urls.txt_ ファイルが含まれた "C:Test" ディレクトリが検索されます。 このようなファイルとディレクトリを作成するか、またはパスを変更してお客様のテキスト ファイルを指定し、お客様がテストしたい画像の URL をそのファイルに入力します。

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>プログラムの実行

上記の手順をすべて完了したら、プログラムによって (関連するタグについて 3 つのサービスすべてに対してクエリが実行されて) 各画像が処理され、タグ情報と共に画像が Content Moderator レビュー ツールにアップロードされます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、製品の種類別にタグを付けてレビュー チームがコンテンツ モデレーションに関して情報に基づいた決定を行えるようにすることを目的として、製品画像を分析するプログラムを設定しました。 次は、画像のモデレーションの詳細について学習してください。

> [!div class="nextstepaction"]
> [モデレートされた画像のレビュー](./review-tool-user-guide/review-moderated-images.md)