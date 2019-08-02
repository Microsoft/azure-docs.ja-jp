---
title: REST API コンソールでモデレーション ジョブを使用する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Review API のジョブ操作を使用して、Azure Content Moderator で画像コンテンツまたはテキスト コンテンツの徹底的なコンテンツ モデレーション ジョブを開始します。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/18/2019
ms.author: sajagtap
ms.openlocfilehash: c6f3d9c1605dc97b315550d8b7e3fdf08144c1bc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561229"
---
# <a name="define-and-use-moderation-jobs-rest"></a>モデレーション ジョブ (REST) を定義して使用する

モデレーション ジョブはコンテンツ モデレーション、ワークフロー、レビューの機能に対するある種のラッパーとして機能します。 このガイドでは、ジョブの REST API を使ってコンテンツ モデレーション ジョブを開始し、レビューする方法を示します。 API の構造を理解すれば、これらの呼び出しを REST と互換性のあるプラットフォームに簡単に移植することができます。

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、またはアカウントを作成します。
- (省略可能) ジョブで使用するための[カスタム ワークフローを定義](./Review-Tool-User-Guide/Workflows.md)します。既定のワークフローを使用することもできます。

## <a name="create-a-job"></a>ジョブを作成する

モデレーション ジョブを作成するには、「[Job - Create](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)」API リファレンス ページに移動し、ご自分の主なリージョンのボタン ([レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)の **[資格情報]** ページのエンドポイント URL 内にあります) を選択します。 これにより API コンソールが開始されます。ここで REST API 呼び出しを簡単に構築し実行することができます。

![「Job - Create」ページ上のリージョンの選択](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST 呼び出しパラメーターを入力する

REST 呼び出しを作成するには、次の値を入力します。

- **teamName**:[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) アカウントの設定時に作成したチーム ID (レビュー ツールの [資格情報] 画面の **[ID]** フィールド内にあります)。
- **ContentType**: "Image"、"Text"、または "Video" を指定できます。
- **ContentId**:カスタム識別子の文字列。 この文字列は API に渡され、コールバックで返されます。 これは、内部識別子またはメタデータをモデレーション ジョブの結果に関連付けるために役立ちます。
- **Workflowname**: 以前に作成したワークフローの名前 (既定のワークフローの場合は "default")。
- **CallbackEndpoint**: (省略可能) レビューが完了したときにコールバック情報を受信する URL。
- **Ocp-Apim-Subscription-Key**: ご自分の Content Moderator キー。 [レビュー ツール](https://contentmoderator.cognitive.microsoft.com)の **[設定]** タブ内にあります。

### <a name="fill-in-the-request-body"></a>要求本文を入力する

REST 呼び出しの本文には、1 つのフィールド **[ContentValue]** が含まれています。 テキストのモデレーションを行っている場合は未加工のテキストの内容を貼り付け、イメージ/ビデオのモデレーションを行っている場合はイメージまたはビデオの URL を入力します。 サンプル イメージの URL ([https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)) を使用することができます。

![[Job - Create]\(Job - Create\) コンソールの [クエリ パラメーター]、[ヘッダー]、および [要求本文] ボックス](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>要求を送信する

**[送信]** を選択します。 操作が成功すると、 **[応答の状態]** は `200 OK` になり、 **[応答のコンテンツ]** ボックスにはジョブの ID が表示されます。 次の手順で使用するためにこの ID をコピーします。

![[Review - Create]\(Review - Create\) コンソールの [応答のコンテンツ] ボックスに表示されるレビュー ID](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>ジョブの状態の取得

実行中または完了したジョブの状態と詳細を取得するには、「[Job - Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3)」API リファレンス ページに移動し、ご自分のリージョン (キーが管理されているリージョン) のボタンを選択します。

![「Job - Get」のリージョン選択肢](images/test-drive-region.png)

上記のセクションと同様に、REST 呼び出しパラメーターを入力します。 この手順で、**JobId** はジョブを作成するときに受信した一意の ID 文字列です。 **[送信]** を選択します。 操作が成功すると、 **[応答の状態]** は `200 OK` になり、 **[応答のコンテンツ]** ボックスには次のような JSON 形式のジョブが表示されます。

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![「Job - Get」の REST 呼び出しの応答](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>新しいレビューを確認する

コンテンツ ジョブの結果、レビューが作成された場合は、これを[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)で確認することができます。 **[レビュー]**  >  **[Image]** / **[Text]** / **[Video]** を (使用したコンテンツに応じて) 選択します。 コンテンツが表示され、人によるレビューが可能な状態になります。 人間のモデレーターが自動割り当てタグと予測データをレビューして、最終的なモデレーション意思決定を送信した後で、ジョブ API がすべての情報を指定されたコールバック エンドポイントに送信します。

## <a name="next-steps"></a>次の手順

このガイドでは、REST API を使用してコンテンツ モデレーション ジョブの作成とクエリを行う方法について説明しました。 次は、ジョブを [eコマース モデレーション](./ecommerce-retail-catalog-moderation.md) チュートリアルのようなエンドツーエンドのモデレーション シナリオに統合します。