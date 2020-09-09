---
title: Logic Apps と Power Automate を使用した Video Indexer コネクタに関するチュートリアル。
description: このチュートリアルでは、Logic Apps と Power Automate を使用して Video Indexer コネクタの新しいエクスペリエンスと収益化の可能性を開く方法について説明します。
author: anzaman
manager: johndeu
ms.author: alzam
ms.service: media-services
ms.subservice: video-indexer
ms.topic: tutorial
ms.date: 05/01/2020
ms.openlocfilehash: 5f29e616c0643914ca28921eee481105a5feb0c5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87047100"
---
# <a name="tutorial-use-video-indexer-with-logic-app-and-power-automate"></a>チュートリアル: Logic Apps および Power Automate と共に Video Indexer コネクタを使用する

Azure Media Services [Video Indexer v2 REST API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Delete-Video?) では、サーバー間の通信とクライアントとサーバーの間の通信の両方がサポートされています。Video Indexer のユーザーは、この API を利用して、ビデオとオーディオの分析情報を各自のアプリケーション ロジックに簡単に統合し、新しいエクスペリエンスと収益化の可能性を開くことができます。

統合をさらに容易にするために、Microsoft では、この API との互換性を備えた  [Logic Apps](https://azure.microsoft.com/services/logic-apps/)  および  [Power Automate](https://preview.flow.microsoft.com/connectors/shared_videoindexer-v2/video-indexer-v2/)  コネクタをサポートしています。 コネクタを使用することで、コードを 1 行も記述せずに、大量のビデオ ファイルやオーディオ ファイルにインデックスを付け、そこから効果的に分析情報を抽出するカスタム ワークフローを設定できます。 さらに、統合のためにコネクタを使用すると、ワークフローの正常性を確認しやすくなり、簡単にデバッグできるようになります。  

Video Indexer コネクタの使用をすぐに開始できるように、設定可能な Logic Apps および Power Automate ソリューションの例について順を追って説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 自動的にビデオをアップロードしてインデックスを付ける
> * ファイルのアップロード フローを設定する
> * JSON の抽出フローを設定する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

まず、Video Indexer アカウントと、API キーによる API へのアクセスが必要になります。 

また、Azure Storage アカウントも必要です。 Storage アカウントのアクセス キーをメモしてください。 2 つのコンテナーを作成します。1 つはビデオを格納するコンテナーで、もう 1 つは Video Indexer によって生成される分析情報を格納するコンテナーです。  

次に、Logic Apps または Power Automate で (どちらを使用しているかに応じて)、2 つの異なるフローを開く必要があります。  

## <a name="upload-and-index-your-video-automatically"></a>自動的にビデオをアップロードしてインデックスを付ける 

このシナリオは、連携する 2 つの異なるフローで構成されます。 最初のフローは、Azure Storage アカウントで BLOB が追加または変更されたときにトリガーされます。 インデックス付けの操作が完了したら通知を送信するためのコールバック URL と共に、新しいファイルを Video Indexer にアップロードします。 2 番目のフローは、コールバック URL に基づいてトリガーされ、抽出された分析情報を Azure Storage の JSON ファイルに保存し直します。 この 2 つのフローによるアプローチを使用して、大きなファイルの非同期的なアップロードとインデックス付けを効果的にサポートします。 

### <a name="set-up-the-file-upload-flow"></a>ファイルのアップロード フローを設定する 

最初のフローは、BLOB が Azure Storage コンテナーに追加されるたびにトリガーされます。 トリガーされると、Video Indexer にビデオをアップロードしてインデックスを付けるために使用できる SAS URI が作成されます。 まず、次のフローを作成します。 

![ファイルのアップロード フロー](./media/logic-apps-connector-tutorial/file-upload-flow.png)

最初のフローを設定するには、Video Indexer API キーと Azure Storage 資格情報を指定する必要があります。 

![Azure BLOB ストレージ](./media/logic-apps-connector-tutorial/azure-blob-storage.png)

![接続名と API キー](./media/logic-apps-connector-tutorial/connection-name-api-key.png)

ご自分の Azure Storage アカウントと Video Indexer アカウントに接続できたら、[BLOB が追加または変更されたとき] トリガーに移動し、ビデオ ファイルを配置するコンテナーを選択します。 

![コンテナー](./media/logic-apps-connector-tutorial/container.png)

次に、[パスを使用して SAS URI を作成する] アクションに移動し、[動的コンテンツ] のオプションから [List of Files Path]\(ファイル パスの一覧\) を選択します。  

![パスを使用した SAS URI](./media/logic-apps-connector-tutorial/sas-uri-by-path.jpg)

[自分のアカウントの場所と ID](./video-indexer-use-apis.md#account-id) を入力して、Video Indexer のアカウント トークンを取得します。

![アカウントのアクセス トークンを取得する](./media/logic-apps-connector-tutorial/account-access-token.png)

[Upload video and index]\(ビデオのアップロードとインデックス付け\) では、必須パラメーターとビデオの URL を入力します。 [新しいパラメーターの追加] を選択し、[コールバック URL] を選択します。 

![アップロードとインデックス付け](./media/logic-apps-connector-tutorial/upload-and-index.png)

ここでは、コールバック URL を空白のままにします。 これは、コールバック URL が作成される 2 番目のフローが完了してから追加します。 

他のパラメーターは、既定値を使用することも、自分のニーズに合わせて設定することもできます。 

アップロードとインデックス付けが完了したら [保存] をクリックして、分析情報を抽出する 2 番目のフローの構成に移りましょう。 

## <a name="set-up-the-json-extraction-flow"></a>JSON の抽出フローを設定する 

最初のフローにおけるアップロードとインデックス付けが完了したら、適切なコールバック URL を使用して HTTP 要求が送信され、2 番目のフローがトリガーされます。 その結果、Video Indexer によって生成された分析情報が取得されます。 この例では、インデックス付けジョブの出力が Azure Storage に格納されます。  ただし、この出力を使用して何を実行するかはユーザーの自由です。  

2 番目のフローは、最初のフローとは別に作成します。 

![JSON の抽出フロー](./media/logic-apps-connector-tutorial/json-extraction-flow.png)

このフローを設定するには、再度、Video Indexer API キーと Azure Storage 資格情報を指定する必要があります。 最初のフローで更新したのと同じパラメーターを更新する必要があります。 

トリガーには、[HTTP POST の URL] フィールドが表示されます。 この URL はフローを保存するまで生成されませんが、最終的にこの URL が必要になります。 ここにはもう一度戻ってきます。 

[自分のアカウントの場所と ID](./video-indexer-use-apis.md#account-id) を入力して、Video Indexer のアカウント トークンを取得します。  

[Get Video Index]\(ビデオ インデックスの取得\) アクションに移動し、必須パラメーターを入力します。 [ビデオ ID] には、次の式を入力します: triggerOutputs()['queries']['id'] 

![ビデオ インデクサー アクションの情報](./media/logic-apps-connector-tutorial/video-indexer-action-info.jpg)

この式は、コネクタに対して、トリガーの出力からビデオ ID を取得するように指示します。 この場合、トリガーの出力は、最初のトリガーの [Upload video and index]\(ビデオのアップロードとインデックス付け\) の出力になります。 

[BLOB の作成] アクションに移動し、分析情報を保存するフォルダーのパスを選択します。 作成する BLOB の名前を設定します。 [BLOB コンテンツ] には、次の式を入力します: body('Get_Video_Index') 

![[BLOB の作成] アクション](./media/logic-apps-connector-tutorial/create-blob-action.jpg)

この式は、このフローから [Get Video Index]\(ビデオ インデックスの取得\) アクションの出力を受け取ります。 

[フローの保存] をクリックします。 

フローを保存すると、トリガーに HTTP POST の URL が作成されます。 トリガーから URL をコピーします。 

![URL トリガーを保存する](./media/logic-apps-connector-tutorial/save-url-trigger.png)

次に、1 つ目のフローに戻り、[Upload video and index]\(ビデオのアップロードとインデックス付け\) アクションの [コールバック URL] パラメーターに URL を貼り付けます。 

両方のフローが保存されていることを確認したら、準備完了です。 

Azure BLOB コンテナーにビデオを追加して、新しく作成した Logic Apps または Power Automate ソリューションを試すことができます。数分後に分析情報が出力先フォルダーに表示されることを確認してください。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了した後も、必要に応じて、引き続きこの Logic Apps または Power Automate ソリューションを稼働させておくことができます。 ただし、稼働を止めて課金されるのを避けるには、Power Automate を使用している場合は両方のフローをオフにしてください。 Logic Apps を使用している場合も、両方のフローを無効にしてください。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Video Indexer コネクタの例を 1 つだけ紹介しました。 Video Indexer によって提供される任意の API 呼び出しに、Video Indexer コネクタを使用できます。 たとえば、分析情報のアップロードと取得、結果の変換、埋め込み可能なウィジェットの取得のほか、モデルのカスタマイズも行うこともできます。 また、ファイル リポジトリの更新や送信されたメールなど、さまざまなソースに基づいてこれらのアクションをトリガーすることもできます。 次に、その結果を使用して関連するインフラストラクチャやアプリケーションを更新したり、任意の数のアクション項目を生成したりできます。  

> [!div class="nextstepaction"]
> [Video Indexer API の使用](video-indexer-use-apis.md)
