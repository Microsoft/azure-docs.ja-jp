---
title: ナレッジ ベースに関する分析
titleSuffix: Azure Cognitive Services
description: QnA Maker サービスの作成中に App Insights を有効にした場合、QnA Maker はすべてのチャット ログとその他のテレメトリを格納します。 サンプル クエリを実行して、App Insights からチャット ログを取得してみましょう。
services: cognitive-services
author: tulasim88
manager: cgronlun
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim88
ms.openlocfilehash: f3ca99a92d12acbf880a3a95098a90569e2a0f84
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309401"
---
# <a name="get-analytics-on-your-knowledge-base"></a>ナレッジ ベースに関する分析の取得

[QnA Maker サービスの作成](./set-up-qnamaker-service-azure.md)中に App Insights を有効にした場合、QnA Maker はすべてのチャット ログとその他のテレメトリを格納します。 サンプル クエリを実行して、App Insights からチャット ログを取得してみましょう。

1. App Insights リソースに移動します。

    ![Application Insights リソースを選択します](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. **[分析]** を選択します。 QnA Maker テレメトリのクエリを実行できる新しいウィンドウが開きます。

    ![Analytics を選択する](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. 次のクエリを貼り付けて、実行します。

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse name with *"/knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    **[実行]** を選択して、クエリを実行します。

    ![Run query](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>QnA Maker ナレッジ ベースに関する他の分析についてのクエリを実行します

### <a name="total-90-day-traffic"></a>90 日間のトラフィックの合計

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>指定の期間における質問トラフィックの合計

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>ユーザー トラフィック

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>質問の配布の待ち時間

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [キーの管理](./key-management.md)
