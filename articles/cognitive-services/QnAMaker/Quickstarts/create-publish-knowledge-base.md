---
title: ナレッジ ベースの作成、トレーニング、発行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この例では、BitLocker キー回復に関する質問に答えるよくあるご質問の単純な Web ページから QnA Maker ナレッジ ベースを作成します。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: diberry
ms.openlocfilehash: cf6b0cd99254d5b034687598c588ce5ef13dbd53
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59272854"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成、トレーニング、発行

QnA Maker のナレッジ ベース (KB) は、よくあるご質問や製品マニュアルなど、独自のコンテンツから作成できます。 この例では、BitLocker キー回復に関する質問に答えるよくあるご質問の単純な Web ページから QnA Maker ナレッジ ベースを作成します。

## <a name="prerequisite"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースの作成

1. ご自分の Azure の資格情報を使用して [QnAMaker.ai](https://QnAMaker.ai) ポータルにサインインします。

1. QnA Maker ポータルで、**[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択します。

   ![新しいナレッジ ベースを作成する](../media/qna-maker-create-kb.png)

1. 手順 1 として、**[作成]** ページで **[Create a QnA service]\(QnA サービスの作成)** を選択します。 サブスクリプションで QnA Maker サービスを設定するため、[Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) にリダイレクトされます。 Azure portal がタイムアウトした場合は、サイトで **[Try again]\(再試行)** を選択します。 接続されると、Azure ダッシュ ボードが表示されます。

1. Azure で新しい QnA Maker サービスが正常に作成できたら、qnamaker.ai/create に戻ります。 手順 2 として、ドロップダウン リストから QnA サービスを選択します。 新しい QnA サービスを作成した場合は、必ずページを更新します。

   ![QnA サービスのナレッジ ベースを選択する](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 手順 3 として、ご自分のナレッジ ベースに **My Sample QnA KB** という名前を付けます。

1. ご自分のナレッジ ベースにコンテンツを追加するために、3 種類のデータ ソースを選択します。 手順 4 として、**[Populate your KB]\(KB の入力)** の **[URL]** ボックスに [BitLocker Recovery FAQ](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) の URL を追加します。

   ![QnA サービスのナレッジ ベースを選択する](../media/qnamaker-quickstart-kb/add-datasources.png)

1. 手順 5 として、**[Create your KB]\(KB の作成)** を選択します。

1. ナレッジ ベースの作成中に、ポップアップ ウィンドウが表示されます。 抽出プロセスでは、HTML ページを読み取って質問と回答を識別します。これには数分かかります。

1. ナレッジ ベースの作成が成功すると、**[Knowledge base]\(ナレッジ ベース\)** ページが開きます。 このページで、ナレッジ ベースの内容を編集することができます。

## <a name="edit-the-knowledge-base"></a>ナレッジ ベースを編集する

1. QnA Maker ポータルの **[編集]** セクションにある **[Add QnA pair]\(QnA ペアの追加\)** を選択して、ナレッジ ベースに新しい行を追加します。 **[質問]** に、**Hi** と入力します。 **[回答]** に、**こんにちは。BitLocker について質問してください。**」と入力します。

    ![QnA ペアの追加](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. 右上の **[Save and train]\(保存してトレーニング\)** を選択し、編集内容を保存して QnA Maker のモデルをトレーニングします。 保存しないと、編集した内容は保持されません。

## <a name="test-the-knowledge-base"></a>ナレッジ ベースをテストする

1. QnA Maker ポータルの右上にある **[テスト]** を選択して、行った変更が反映されたかどうかをテストします。 ボックスに「`hi there`」と入力し、Enter キーを押します。 作成した応答が回答として表示されます。

1. **[検査]** を選択し、詳細に応答を確認します。 テスト ウィンドウは、発行前にナレッジ ベースへの変更をテストする際に使用します。

    ![テスト パネル](../media/qnamaker-quickstart-kb/inspect-panel.png)

1. もう一度 **[テスト]** を選択して、**[テスト]** ポップアップを閉じます。

## <a name="publish-the-knowledge-base"></a>ナレッジ ベースの公開

ナレッジ ベースを公開すると、ナレッジ ベースの質問と回答コンテンツが、Azure Search のテスト インデックスから実稼働インデックスへ移動されます。

![実稼働へのテスト インデックスの公開](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. QnA Maker ポータルの **[編集]** の隣にあるメニューから、**[発行]** を選択します。 確認のため、ページの **[発行]** を選択します。

1. QnA Maker サービスが正常に発行されました。 お使いのアプリケーションまたはボット コードで、エンドポイントを使用できます。

    ![発行](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>ボットの作成

ボットの作成は発行後にいつでも **[発行]** ページから行うことができます。 

* 複数のボットを迅速に作成し、個々のボットはリージョンや価格プランが異なっていてもすべて同じナレッジ ベースを指すことができます。 
* ナレッジ ベースのボットを 1 つだけにする場合は、**[View all your bots on the Azure portal]\(Azure portal 上で自分のボットをすべて表示する\)** リンクを使用して、ご自分の現在のボットの一覧を表示します。 
* ナレッジ ベースに変更を加えて再発行しても、ボットに対して追加のアクションを実行する必要はありません。 ナレッジ ベースと連携するように既に構成されており、ナレッジ ベースに対する今後のすべての変更に対応します。 ナレッジ ベースを発行するたびに、そのナレッジ ベースに接続されているすべてのボットが自動的に更新されます。

1. QnA Maker ポータルの **[発行]** ページで、**[Create bot]\(ボットの作成\)** を選択します。 このボタンは、ナレッジ ベースの発行後にのみ表示されます。

    ![QnA Maker ポータルで、[発行] ページに移動し、ご自分のナレッジ ベースを発行します。 [Create bot]\(ボットの作成\) を選択します。](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Azure portal の新しいブラウザー タブが開き、Azure Bot Service の作成ページが表示されます。 Azure Bot Service を構成します。 これらの構成設定の詳細については、[Azure Bot Service v4 による QnA ボットの作成](../tutorials/create-qna-bot.md)チュートリアルを参照してください。
    
    * ボットを作成するときに、Azure portal 上で次の設定を変更しないでください。 これらはご自分の既存のナレッジ ベースのために事前に設定されています。 
        * QnA 認証キー
        * App Service プラン/場所
        * Azure Storage
    * ボットと QnA Maker は、Web アプリ サービスの "_プラン_" を共有できますが、Web アプリを共有することはできません。 そのため、**アプリ名**は、QnA Maker サービスを作成したときに使用したアプリ名と異なる必要があります。 


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](../How-To/create-knowledge-base.md)
