---
title: 複数ターン会話 - QnA Maker
description: プロンプトとコンテキストを使用して、一連の質問についてボットの複数ターンと呼ばれる複数回のターンを管理します。 複数ターンとは、前の質問のコンテキストが次の質問と応答に影響する会話をやりとりする機能です。
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 93bc6201cb627dc27071caf7beded5778b06ccb6
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2020
ms.locfileid: "86229348"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>フォローアップ プロンプトを使用して、複数のターンを含む会話を作成します。

フォローアップ プロンプトとコンテキストを使用して、一連の質問についてボットの "_複数ターン_" と呼ばれる複数回のターンを管理します。

複数ターンの動作を確認するには、次のデモンストレーション ビデオをご覧ください。

[![QnA Maker での複数ターン会話](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>複数ターン会話とは

1 回のターンでは複数の質問に応答できません。 クライアント アプリケーション (チャット ボット) の会話を設計するにあたり、場合によっては、ユーザーが行う質問をフィルター処理または調整してから正しい応答を決定する必要があります。 質問のこのフローは、ユーザーに*フォローアップ プロンプト*を示すことで実現します。

ユーザーが質問したとき、QnA Maker は応答 "_と_" フォローアップ プロンプトがあれば返します。 この応答では、フォローアップ質問を選択肢として提示できます。

> [!CAUTION]
> 複数ターンのプロンプトは、FAQ ドキュメントからは抽出されません。 複数ターンの抽出が必要な場合は、QnA のペアを FAQ として指定している疑問符を削除します。

## <a name="example-multi-turn-conversation-with-chat-bot"></a>チャット ボットとの複数ターン会話の例

複数ターンでは、次の図のように、チャット ボットがユーザーとの会話を管理して最終的な応答を決定します。

![会話を通じてユーザーを案内するプロンプトが表示された複数ターン ダイアログ](../media/conversational-context/conversation-in-bot.png)

上の画像では、ユーザーが「**My account**」と入力して会話を開始しました。 ナレッジ ベースには、3 つのリンクされた質問と応答のペアがあります。 答えを絞り込むために、ユーザーはナレッジ ベース内の 3 つの選択肢のうちの 1 つを選択します。 質問 (#1) には 3 つのフォローアップ プロンプトがあり、チャット ボットで 3 つのオプション (#2) として提示されます。

ユーザーがオプション (#3) を選ぶと、調整のための次のオプション (#4) が表示されます。 このシーケンスは、正しい最終応答 (#6) をユーザーが決定するまで続きます (#5)。


### <a name="use-multi-turn-in-a-bot"></a>ボットで複数ターンを使用する

KB を公開した後、 **[Create Bot]\(ボットの作成\)** ボタンを選択して、QnA Maker ボットを Azure Bot Service にデプロイできます。 ボットで有効にしたチャット クライアントにプロンプトが表示されます。

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>ドキュメントの構造から複数ターン会話を作成する

ナレッジ ベースを教えて作成するとき、 **[Populate your KB]\(KB のポピュレート\)** セクションに **[Enable multi-turn extraction from URLs, .pdf or .docx files]\(URL や.pdf または .docx ファイルからの複数ターン抽出を有効にする\)** チェック ボックスが表示されます。

![複数ターン抽出の有効化チェック ボックス](../media/conversational-context/enable-multi-turn.png)

このオプションを選択すると、QnA Maker でドキュメント構造内に存在する階層が抽出されます。 その階層はフォローアップ プロンプトに変換されて、階層のルートが親 QnA として機能します。 ドキュメントによっては、階層のルートに応答として機能する可能性のあるコンテンツが含まれていないため、代替の回答テキストとして使用する "既定の回答テキスト" を指定して、そのような階層を抽出できます。

複数ターン構造は、URL、PDF ファイル、または DOCX ファイルからのみ推論できます。 構造の例として、[Microsoft Surface ユーザー マニュアル PDF ファイル](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)の画像を表示します。

![![ユーザー マニュアル内の構造の例]\(../media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>独自の複数ターンのドキュメントのビルド

複数ターンのドキュメントを作成する場合は、次のガイドラインに留意してください。

* 階層を示すには、見出しとサブ見出しを使用する。 たとえば、親 QnA を示すために h1、プロンプトとして使用する必要がある QnA を示すために h2 を使用できます。 後続の階層を示すには、小さい見出しサイズを使用する。 スタイル、色、その他のメカニズムを、ドキュメント内の構造を暗黙に示すために使用しないでください。QnA Maker では複数ターンのプロンプトが抽出されることはありません。

* 見出しの最初の文字は大文字にしなくてはなりません。

* 見出しの末尾に疑問符 `?` を付けないでください。

* [サンプル ドキュメント](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)を独自の複数ターンのドキュメントを作成する参考として使用できます。

### <a name="adding-files-to-a-multi-turn-kb"></a>複数ターンの KB へのファイルの追加

階層ドキュメントを追加すると、QnA Maker ではその構造からフォローアップ プロンプトを決定し、会話フローを作成します。

1. QnA Maker で、 **[Enable multi-turn extraction from URLs, .pdf or .docx files]\(URL や .pdf または .docx ファイルからの複数ターン抽出を有効にする\)** を有効にして作成された既存のナレッジ ベースを選択します 。
1. **[設定]** ページにアクセスし、追加するファイルまたは URL を選択します。
1. ナレッジ ベースを**保存してトレーニングします**。

> [!Caution]
> 新規または空のナレッジ ベースのデータ ソースとしてエクスポートされた TSV または XLS の複数ターンのナレッジ ベース ファイルを使用することは、サポートされていません。 エクスポートされた複数ターン プロンプトをナレッジ ベースに追加するには、QnA Maker ポータルの **[設定]** ページから、そのファイルの種類を**インポート**する必要があります。


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>作成 API を使用して複数ターンのプロンプトを持つナレッジ ベースを作成する

[QnA Maker 作成 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create) を使用して、複数ターンのプロンプトを持つナレッジ ケースを作成できます。 プロンプトは `context` プロパティの `prompts` 配列に追加されます。

## <a name="show-questions-and-answers-with-context"></a>コンテキストを使用して質問と応答を表示する

表示される質問と応答のペアを減らして、コンテキスト会話の質問と応答のみにします。

**[オプションの表示]** を選択し、 **[コンテキストを表示]** を選択します。 一覧には、フォローアップ プロンプトを含む質問と応答のペアが表示されます。

![コンテキスト会話による質問と応答のペアのフィルター処理](../media/conversational-context/filter-question-and-answers-by-context.png)

複数ターンのコンテキストが最初の列に表示されます。

![!["Context (PREVIEW)" (コンテキスト (プレビュー) 列]\(../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

上の画像では、 **#1** が列内で太字テキストで表示され、現在の質問を示しています。 親の質問は、行の最上位の項目です。 それより下のすべての質問は、リンクされた質問と応答のペアです。 これらの項目は選択できるので、直ちに他のコンテキスト項目に移動できます。

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>既存の質問と応答のペアをフォローアップ プロンプトとして追加する

元の質問 **My account** には、**Accounts and signing in** (アカウントとサインイン) のようなフォローアップ プロンプトがあります。

!["Accounts and signing in" の応答とフォローアップ プロンプト](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

現在リンクされていない既存の質問と応答のペアにフォローアップ プロンプトを追加します。 質問がどの質問と応答のペアにもリンクされていないので、現在のビュー設定を変更する必要があります。

1. 既存の質問と応答のペアをフォローアップ プロンプトとしてリンクするには、質問と応答のペアの行を選択します。 Surface マニュアルの場合、**Sign out** を検索して一覧を減らします。
1. **Signout** の行の **[応答]** 列で、 **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択します。
1. **[Follow-up prompt]\(フォローアップ プロンプト\)** ポップアップ ウィンドウのフィールドに、次の値を入力します。

    |フィールド|値|
    |--|--|
    |表示テキスト|「**Turn off the device**」(デバイスの電源を切る) と入力します。 これは、フォローアップ プロンプトに表示するカスタム テキストです。|
    |Context-only (コンテキストのみ)| このチェック ボックスをオンにします。 応答は、質問がコンテキストを指定する場合にのみ返されます。|
    |Link to answer (応答へのリンク)|「**Use the sign-in screen**」(サインイン画面を使用する) と入力して、既存の質問と応答のペアを検索します。|


1.  1 つの一致が返されます。 この応答をフォローアップとして選択し、 **[保存]** を選択します。

    ![[Follow-up prompt (PREVIEW)] (フォローアップ プロンプト (プレビュー)) ページ](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. フォローアップ プロンプトを追加した後、上部のナビゲーションで **[Save and train]\(保存してトレーニング\)** を選択します。

### <a name="edit-the-display-text"></a>表示テキストを編集する

フォローアップ プロンプトを作成し、既存の質問と応答のペアを **[Link to Answer]\(応答へのリンク\)** として入力したら、新しい **[表示テキスト]** を入力できます。 このテキストは、既存の質問を置き換えず、新しい代替質問を追加しません。 これらの値とは別になります。

1. 表示テキストを編集するには、 **[コンテキスト]** フィールドで質問を検索し選択します。
1. その質問の行では、応答列でフォローアップ プロンプトを選択します。
1. 編集する表示テキストを選択し、 **[編集]** を選択します。

    ![表示テキストの編集コマンド](../media/conversational-context/edit-existing-display-text.png)

1. **[Follow-up prompt]\(フォローアップ プロンプト\)** ポップアップ ウィンドウで、既存の表示テキストを変更します。
1. 表示テキストの編集が完了したら、 **[保存]** を選択します。
1. 上部のナビゲーション バーで、 **[Save and train]\(保存してトレーニング\)** を選択します。


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>新しい質問と応答のペアをフォローアップ プロンプトとして追加する

新しい質問と応答のペアをナレッジ ベースに追加すると、各ペアがフォローアップ プロンプトとして既存の質問にリンクされるはずです。

1. ナレッジ ベースのツール バーで、「**Accounts and signing in**」(アカウントとサインイン) に対する既存の質問と応答のペアを検索して選択します。

1. この質問の **[応答]** 列で **[Add follow-up prompt]\(フォローアップ プロンプトの追加\)** を選択します。
1. **[Follow-up prompt (PREVIEW)]\(フォローアップ プロンプト (プレビュー)\)** で、次の値を入力して新しいフォローアップ プロンプトを作成します。

    |フィールド|値|
    |--|--|
    |表示テキスト|「*Create a Windows Account*.」(Windows アカウントを作成する。) フォローアップ プロンプトに表示するカスタム テキスト。|
    |Context-only (コンテキストのみ)|このチェック ボックスをオンにします。 この応答は、質問がコンテキストを指定する場合にのみ返されます。|
    |Link to answer (応答へのリンク)|応答として次のテキストを入力します。<br>*「[Create](https://account.microsoft.com/) a Windows account with a new or existing email account*.」(新規または既存の電子メール アカウントを使用して Windows アカウントを作成します。)<br>データベースを保存してトレーニングすると、このテキストが変換されます。 |
    |||

    ![新しいプロンプトの質問と応答を作成する](../media/conversational-context/create-child-prompt-from-parent.png)


1. **[新規作成]** を選択してから、 **[保存]** を選択します。

    このアクションによって、新しい質問と応答のペアが作成され、選択した質問がフォローアップ プロンプトとしてリンクされます。 両方の質問の **[コンテキスト]** 列は、フォローアップ プロンプトの関係を示します。

1. **[オプションの表示]** を選択し、[ **[Show context (PREVIEW)]\(コンテキストを表示 (プレビュー)\)** ](#show-questions-and-answers-with-context) を選択します。

    新しい質問は、どのようにリンクされているかを示します。

    ![新しいフォローアップ プロンプトを作成する](../media/conversational-context/new-qna-follow-up-prompt.png)

    親の質問は、その選択肢の 1 つとして新しい質問を表示します。

    ![![両方の質問の [コンテキスト] 列は、フォローアップ プロンプトの関係を示します]\(../media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. フォローアップ プロンプトを追加した後、上部のナビゲーション バーで **[Save and train]\(保存してトレーニング\)** を選択します。

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>フォローアップ プロンプトのテスト中に複数ターンを表示にする

**[テスト]** ペインでフォローアップ プロンプトを含む質問をテストすると、応答にフォローアップ プロンプトが含まれます。

![応答にはフォローアップ プロンプトが含まれる](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>最初の応答とフォローアップ プロンプトを返す JSON 要求

空の `context` オブジェクトを使用して、ユーザーの質問に対する応答を要求し、フォローアップ プロンプトを含めます。

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>最初の応答とフォローアップ プロンプトを返す JSON 応答

前のセクションでは、「**Accounts and signing in**」(アカウントとサインイン) に対する応答とフォローアップ プロンプト (ある場合) を要求しました。 応答には、*answers[0].context* にあるプロンプト情報と、ユーザーに表示するテキストが含まれます。

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts` 配列によって `displayText` プロパティのテキストと `qnaId` の値が提供されます。 これらの応答を会話フローで次に表示される選択肢として示し、選択された `qnaId` を次の要求で QnA Maker に送り返すことができます。

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>最初以外の応答とフォローアップ プロンプトを返す JSON 要求

前のコンテキストを `context` オブジェクトに含めます。

次の JSON 要求では、現在の質問は「*Use Windows Hello to sign in*」(Windows Hello を使ってサインインする) であり、前の質問は「*accounts and signing in*」(アカウントとサインイン) でした。

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>最初以外の応答とフォローアップ プロンプトを返す JSON 応答

QnA Maker _GenerateAnswer_ JSON 応答は、`answers`オブジェクトの最初の項目にある `context` プロパティのフォローアップ プロンプトを含みます。

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>QnA Maker ID でナレッジ ベースをクエリする

複数ターン機能を使用してカスタム アプリケーションをビルドする場合。 最初の質問の応答で、フォローアップ プロンプトとそれに関連付けられた `qnaId` が返されます。 ID が付与されたので、フォローアップ プロンプトの要求本文でこれを渡すことができます。 要求本文に `qnaId` とコンテキスト オブジェクト (これには以前の QnA Maker プロパティが含まれます) が含まれる場合、GenerateAnswer は、ランキング アルゴリズムを使用して質問テキストによる応答を見つけるのではなく、ID により正確な質問を返します。


## <a name="display-order-is-supported-in-the-update-api"></a>更新 API では表示の順序がサポートされている

JSON 応答で返される[表示テキストと表示の順序](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)は、[更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) による編集でサポートされています。

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>更新 API を使用して複数ターンのプロンプトを追加または削除する

[QnA Maker 更新 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) を使用して、複数ターンのプロンプトを追加または削除できます。  プロンプトは `context` プロパティの `promptsToAdd` 配列と `promptsToDelete` 配列に追加されます。

## <a name="export-knowledge-base-for-version-control"></a>バージョン管理用のナレッジ ベースのエクスポート

QnA Maker は、エクスポートされるファイルに複数ターンの会話ステップを含めることにより、バージョン管理をサポートしています。

## <a name="next-steps"></a>次のステップ

この[対話サンプル](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj)のコンテキスト会話について、または[複数ターン会話のための概念的ボット設計](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0)について詳しく学習します。

> [!div class="nextstepaction"]
> [ナレッジ ベースの移行](../Tutorials/migrate-knowledge-base.md)
