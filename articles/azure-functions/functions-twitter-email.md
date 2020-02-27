---
title: Azure Logic Apps と統合される関数を作成する
description: Azure Logic Apps および Azure Cognitive Services と統合し、ツイートの感情を分類して、感情が良くない場合に通知を送信する関数を作成します。
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: cshoe
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 7d121e9aeb897897322f1253c332e7a1baabdc9e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768964"
---
# <a name="create-a-function-that-integrates-with-azure-logic-apps"></a>Azure Logic Apps と統合される関数を作成する

Azure Functions は、Logic Apps デザイナーで Azure Logic Apps と統合できます。 この統合により、他の Azure サービスやサードパーティ製のサービスとのオーケストレーションにおいて、Functions のコンピューティング機能を使用することができます。 

このチュートリアルでは、Azure 上で Functions と Logic Apps、Cognitive Services を使用することで、Twitter 投稿の感情分析を実行する方法を説明します。 HTTP によってトリガーされる関数は、感情 スコアに基づいて、ツイートを緑、黄、赤に分類します。 ネガティブな感情が検出されると、電子メールが送信されます。 

![Logic Apps デザイナーでのアプリの最初の 2 つの手順の画像](media/functions-twitter-email/00-logic-app-overview.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Cognitive Services API リソースを作成します。
> * ツイートの感情を分類する関数を作成します。
> * Twitter に接続するロジック アプリを作成します。
> * 感情の検出をロジック アプリに追加します。 
> * ロジック アプリを関数に接続します。
> * 関数からの応答に基づいて電子メールを送信します。

## <a name="prerequisites"></a>前提条件

+ アクティブな [Twitter](https://twitter.com/) アカウント。 
+ [Outlook.com](https://outlook.com/) のアカウント (通知の送信用)。
+ この記事では、[Azure portal から初めての関数を作成する方法](functions-create-first-azure-function.md)に関するページで作成したリソースを使用して作業を開始します。  
リソースの作成が済んでいない場合は、すぐにこれらの手順に従って Function App を作成してください。

## <a name="create-a-cognitive-services-resource"></a>Cognitive Services リソースの作成

Cognitive Services API は、個々のリソースとして Azure で使用できます。 Text Analytics API を使用して、監視されているツイートの感情を検出します。

1. [Azure portal](https://portal.azure.com/) にサインインする

2. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

3. **[AI + 機械学習]**  >  **[テキスト分析]** の順にクリックします。 次に、表に記載した設定を使用してリソースを作成します。

    ![Cognitive リソースを作成するページ](media/functions-twitter-email/01-create-text-analytics.png)

    | 設定      |  推奨値   | 説明                                        |
    | --- | --- | --- |
    | **Name** | MyCognitiveServicesAccnt | 一意のアカウント名を選択します。 |
    | **Location** | 米国西部 | お近くの場所を使用します。 |
    | **[価格レベル]** | F0 | まずは低いレベルを選んでください。 呼び出し回数が不足する場合は、高いレベルにスケーリングします。|
    | **リソース グループ** | myResourceGroup | このチュートリアルでは、すべてのサービスで同じリソース グループを使用します。|

4. **[作成]** をクリックして、リソースを作成します。 

5. **[概要]** をクリックし、 **[エンドポイント]** の値をテキスト エディターにコピーします。 この値は、Cognitive Services API への接続を作成するときに使用します。

    ![Cognitive Services の設定](media/functions-twitter-email/02-cognitive-services.png)

6. 左側のナビゲーション列で **[キー]** をクリックし、 **[キー 1]** の値をコピーして、テキスト エディターにメモしておきます。 このキーは、ロジック アプリを Cognitive Services API に接続するために使用します。 
 
    ![Cognitive Services のキー](media/functions-twitter-email/03-cognitive-serviecs-keys.png)

## <a name="create-the-function-app"></a>Function App の作成

関数は、Logic Apps ワークフローの処理タスクをオフロードするのに役立ちます。 このチュートリアルでは、HTTP によってトリガーされる関数を使用して、Cognitive Services からのツイート 感情 スコアを処理し、カテゴリ値を返します。  

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

## <a name="create-an-http-triggered-function"></a>HTTP によってトリガーされる関数の作成  

1. Function App を展開し、 **[関数]** の横にある **[+]** ボタンをクリックします。 これが関数アプリの初めての関数の場合は、 **[ポータル内]** を選択します。

    ![Azure Portal での関数のクイック スタート ページ](media/functions-twitter-email/05-function-app-create-portal.png)

2. 次に、 **[Webhook + API]** を選択し、 **[作成]** をクリックします。 

    ![HTTP トリガーの選択](./media/functions-twitter-email/06-function-webhook.png)

3. この `run.csx` ファイルの内容を次のコードに置き換えて、 **[保存]** をクリックします。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        string category = "GREEN";
    
        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        log.LogInformation(string.Format("The sentiment score received is '{0}'.", requestBody));
    
        double score = Convert.ToDouble(requestBody);
    
        if(score < .3)
        {
            category = "RED";
        }
        else if (score < .6) 
        {
            category = "YELLOW";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(category)
            : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    }
    ```
    この関数コードは、要求で受信した感情 スコアに基づいて、色のカテゴリを返します。 

4. 関数をテストするには、一番右の **[テスト]** をクリックして [テスト] タブを展開します。 **[要求本文]** に「`0.2`」という値を入力し、 **[実行]** をクリックします。 応答本文で **RED** という値が返されます。 

    ![Azure Portal で関数をテストする](./media/functions-twitter-email/07-function-test.png)

これで、感情 スコアを分類する関数が作成できました。 次に、Twitter および Cognitive Services API に関数を統合するロジック アプリを作成します。 

## <a name="create-a-logic-app"></a>ロジック アプリを作成します   

1. Azure portal で、左上隅にある **[リソースの作成]** ボタンをクリックします。

2. **[Web]**  >  **[ロジック アプリ]** の順にクリックします。
 
3. 次に、 **[名前]** の値を入力し (`TweetSentiment` など)、表に記載した設定を使用します。

    ![Azure Portal でロジック アプリを作成する](./media/functions-twitter-email/08-logic-app-create.png)

    | 設定      |  推奨値   | 説明                                        |
    | ----------------- | ------------ | ------------- |
    | **Name** | TweetSentiment | アプリの適切な名前を選択します。 |
    | **リソース グループ** | myResourceGroup | 以前と同じ既存のリソース グループを選択します。 |
    | **Location** | East US | 近くの場所を選択します。 |    

4. 適切な設定値を入力したら、 **[作成]** をクリックしてロジック アプリを作成します。 

5. アプリが作成されたら、ダッシュボードにピン留めされた新しいロジック アプリをクリックします。 Logic Apps デザイナーで、下へスクロールして **[空のロジック アプリ]** テンプレートをクリックします。 

    ![空の Logic Apps テンプレート](media/functions-twitter-email/09-logic-app-create-blank.png)

これで、Logic Apps デザイナーを使用してサービスとトリガーをアプリに追加できるようになりました。

## <a name="connect-to-twitter"></a>Twitter への接続

最初に、Twitter アカウントへの接続を作成します。 ロジック アプリはツイートをポーリングし、これによりアプリの実行がトリガーされます。

1. デザイナーで、**Twitter** サービスをクリックし、 **[新しいツイートが投稿されたら]** トリガーをクリックします。 Twitter アカウントにサインインして、Logic Apps によるアカウントの使用を承認します。

2. 次の表で指定されている Twitter トリガーの設定を使用します。 

    ![Twitter コネクタの設定](media/functions-twitter-email/10-tweet-settings.png)

    | 設定      |  推奨値   | 説明                                        |
    | ----------------- | ------------ | ------------- |
    | **[検索テキスト]** | #Azure | 選択した間隔で新しいツイートが十分に投稿される程度に一般的なハッシュタグを使用します。 Free レベルを使用している状態で、使用頻度の高すぎるハッシュタグを使用すると、Cognitive Services API でのトランザクションのクォータがすぐに上限に達してしまう場合があります。 |
    | **間隔** | 15 | 頻度の単位での、Twitter に対する要求間の間隔です。 |
    | **頻度** | 分 | Twitter のポーリングに使用する頻度の単位です。  |

3.  **[保存]** をクリックして、Twitter アカウントに接続します。 

これで、アプリが Twitter に接続されました。 次はテキスト分析に接続し、収集されたツイートの感情を検出します。

## <a name="add-sentiment-detection"></a>感情の検出を追加する

1. **[新しいステップ]** 、 **[アクションの追加]** の順にクリックします。

2. **[アクションを選択してください]** で「**テキスト分析**」と入力し、 **[感情の検出]** アクションをクリックします。
    
    ![[新しいステップ]、[アクションの追加]](media/functions-twitter-email/11-detect-sentiment.png)

3. `MyCognitiveServicesConnection` などの接続名を入力し、テキスト エディターにメモしておいた Cognitive Services API と Cognitive Services エンドポイントのキーを貼り付けて、 **[作成]** をクリックします。

    ![[新しいステップ]、[アクションの追加]](media/functions-twitter-email/12-connection-settings.png)

4. 次に、テキスト ボックスに「**Tweet Text**」と入力し、 **[新しいステップ]** をクリックします。

    ![分析するテキストを定義する](media/functions-twitter-email/13-analyze-tweet-text.png)

これで感情の検出が構成されたので、感情スコアの出力を使用する関数への接続を追加できます。

## <a name="connect-sentiment-output-to-your-function"></a>感情の出力を関数に接続する

1. Logic Apps デザイナーで、 **[新しいステップ]**  >  **[アクションの追加]** の順にクリックし、 **[Azure Functions]** を条件としてフィルター処理して、 **[Azure 関数を選択する]** をクリックします。

    ![感情を検出する](media/functions-twitter-email/14-azure-functions.png)
  
4. 先ほど作成した関数アプリを選択します。

    ![関数の選択](media/functions-twitter-email/15-select-function.png)

5. このチュートリアルで作成した関数を選択します。

    ![関数の選択](media/functions-twitter-email/16-select-function.png)

4. **[要求本文]** で **[Score]\(スコア\)** をクリックし、 **[保存]** をクリックします。

    ![Score](media/functions-twitter-email/17-function-input-score.png)

これで、ロジック アプリから感情スコアが送信されたときに、関数がトリガーされます。 関数によって、色分けされたカテゴリがロジック アプリに返されます。 次に、関数から感情の値 **RED** が返されたときに送信される電子メール通知を追加します。 

## <a name="add-email-notifications"></a>電子メール通知を追加する

ワークフローの最後の部分では、感情スコアが _RED_ だったときに電子メールがトリガーされます。 このトピックでは Outlook.com コネクタを使用します。 Gmail や Office 365 の Outlook コネクタを使用して同様の手順を実行することもできます。   

1. Logic Apps デザイナーで、 **[新しいステップ]**  >  **[条件の追加]** をクリックします。 

    ![ロジック アプリに条件を追加する](media/functions-twitter-email/18-add-condition.png)

2. **[値の選択]** をクリックして、 **[本文]** をクリックします。 **[次の値に等しい]** を選択し、 **[値の選択]** をクリックして「`RED`」と入力し、 **[保存]** をクリックします。 

    ![条件に対するアクションを選択する](media/functions-twitter-email/19-condition-settings.png)    

3. **[true の場合]** で **[アクションの追加]** をクリックします。`outlook.com` を検索して、 **[電子メールの送信]** をクリックし、Outlook.com アカウントにサインインします。

    ![[電子メールの送信] アクション用に電子メールを構成する](media/functions-twitter-email/20-add-outlook.png)

    > [!NOTE]
    > Outlook.com アカウントを取得していない場合は、Gmail や Office 365 Outlook など、別のコネクタを選択できます。

4. **[電子メールの送信]** アクションでは、表で指定されている電子メール設定を使用します。 

    ![[電子メールの送信] アクション用に電子メールを構成する](media/functions-twitter-email/21-configure-email.png)
    
| 設定      |  推奨値   | 説明  |
| ----------------- | ------------ | ------------- |
| **To** | メール アドレスを入力します。 | 通知を受け取る電子メール アドレス。 |
| **[件名]** | "ネガティブなツイートの感情を検出しました"  | 電子メール通知の件名。  |
| **本文** | [ツイート テキスト]、[場所] | **[ツイート テキスト]** パラメーターと **[場所]** パラメーターをクリックします。 |

1. **[保存]** をクリックします。

これでワークフローが完成したので、ロジック アプリを有効にして、関数の動作を確認できます。

## <a name="test-the-workflow"></a>ワークフローをテストする

1. Logic Apps デザイナーで **[実行]** をクリックしてアプリを起動します。

2. 左の列で **[概要]** をクリックしてロジック アプリの状態を確認します。 
 
    ![ロジック アプリの実行の状態](media/functions-twitter-email/22-execution-history.png)

3. (省略可能) 実行中のいずれかをクリックすると、その実行に関する詳細が表示されます。

4. 関数に移動し、ログを表示して、感情スコアの受信と処理が行われたことを確認します。
 
    ![関数のログを表示する](media/functions-twitter-email/sent.png)

5. ネガティブである可能性のある感情が検出されると、電子メールが届きます。 電子メールが届かない場合は、関数コードを変更して、毎回 RED が返されるようにすることができます。

    ```csharp
    return (ActionResult)new OkObjectResult("RED");
    ```

    電子メール通知を確認した後、元のコードに戻してください。

    ```csharp
    return requestBody != null
        ? (ActionResult)new OkObjectResult(category)
        : new BadRequestObjectResult("Please pass a value on the query string or in the request body");
    ```

    > [!IMPORTANT]
    > このチュートリアルを完了した後は、ロジック アプリを無効にする必要があります。 アプリを無効にすることで、実行に対して課金されたり、Cognitive Services API でのトランザクションが上限に達したりするのを回避できます。

ここまでで、Functions を Logic Apps ワークフローと簡単に統合できることが確認できました。

## <a name="disable-the-logic-app"></a>ロジック アプリを無効にする

ロジック アプリを無効にするには、 **[概要]** をクリックし、画面上部の **[Disable]\(無効化\)** をクリックします。 アプリを無効にすれば、削除しなくても、その実行が停止され、課金が停止されます。

![関数のログ](media/functions-twitter-email/disable-logic-app.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Cognitive Services API リソースを作成します。
> * ツイートの感情を分類する関数を作成します。
> * Twitter に接続するロジック アプリを作成します。
> * 感情の検出をロジック アプリに追加します。 
> * ロジック アプリを関数に接続します。
> * 関数からの応答に基づいて電子メールを送信します。

次のチュートリアルに進み、関数用のサーバーレス API を作成する方法を学習してください。

> [!div class="nextstepaction"] 
> [Azure Functions を使用してサーバーレス API を作成する](functions-create-serverless-api.md)

Logic Apps の詳細については、[Azure Logic Apps](../logic-apps/logic-apps-overview.md) に関するページを参照してください。

