---
title: Azure Functions での従量課金プランのコストの見積もり
description: Azure の従量課金プランで関数アプリを実行するときに発生する可能性があるコストをより正確に見積もる方法について説明します。
author: ggailey777
ms.author: glenga
ms.date: 9/20/2019
ms.topic: conceptual
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 0ff41eb511ad4513fc9bf5a2ded7ef47b08d12ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243302"
---
# <a name="estimating-consumption-plan-costs"></a>従量課金プランのコストの見積もり

現在、Azure Functions で実行されるアプリのホスティング プランには 3 つの種類があり、各プランには独自の価格モデルがあります。 

| プラン | 説明 |
| ---- | ----------- |
| [**従量課金**](functions-scale.md#consumption-plan) | 関数アプリが実行された時間に対してのみ課金されます。 このプランには、サブスクリプションごとの[無料提供][価格ページ]が含まれます。|
| [**Premium**](functions-scale.md#premium-plan) | 従量課金プランと同じ機能とスケーリング メカニズムが提供されますが、パフォーマンスと VNET アクセスが増強されています。 コストは、お客様が選択した価格レベルに基づきます。 詳細については、「[Azure Functions の Premium プラン](functions-premium-plan.md)」を参照してください。 |
| [**専用 (App Service)** ](functions-scale.md#app-service-plan) <br/>(Basic レベル以上) | 専用 VM または分離環境で実行する必要がある場合は、カスタム イメージつまり超過した App Service プラン容量を使用します。 [標準の App Service プランの料金](https://azure.microsoft.com/pricing/details/app-service/)を使用します。 コストは、お客様が選択した価格レベルに基づきます。|

関数のパフォーマンスとコストの要件に最適なプランを選択します。 詳細については、「[Azure Functions のスケールとホスティング](functions-scale.md)」を参照してください。

従量課金プランではコストが変動するため、この記事ではこのプランについてのみ説明します。 

Durable Functions も従量課金プランで実行できます。 Durable Functions を使用する場合のコストに関する考慮事項の詳細については、「[Durable Functions の課金](./durable/durable-functions-billing.md)」を参照してください。

## <a name="consumption-plan-costs"></a>従量課金プランのコスト

1 回の関数実行の実行 "*コスト*" は、"*GB 秒数*" で測定されます。 実行コストは、そのメモリ使用量と実行時間を組み合わせて計算されます。 実行時間が長い関数ほどコストが高くなり、メモリ消費量が多い関数ほどコストが高くなります。 

関数によって使用されるメモリの量が一定のままである場合を考えてみます。 この場合、コストの計算は単純な乗算です。 たとえば、関数が 0.5 GB を3 秒間消費したとします。 その場合の実行コストは `0.5GB * 3s = 1.5 GB-seconds` になります。 

メモリ使用量は時間の経過と共に変化するため、計算は実質的には時間経過に伴うメモリ使用量の積分になります。  システムでは、一定の間隔でプロセス (子プロセスを含む) のメモリ使用量をサンプリングすることによって、この計算が行われます。 [価格ページ]で説明されているように、メモリ使用量は最も近い 128 MB のバケットに切り上げられます。 プロセスで 160 MB 使用されている場合は、256 MB の料金が発生します。 計算では同時実行が考慮されます。同時実行とは、同じプロセス内で複数の関数が同時に実行することです。

> [!NOTE]
> CPU 使用率は実行コストでは直接考慮されませんが、関数の実行時間に影響する場合は、コストに影響を与える可能性があります。

## <a name="other-related-costs"></a>その他の関連コスト

プランでの関数実行の全体的なコストを見積もるときは、Functions のランタイムでは他の複数の Azure サービスが使用されており、各サービスが個別に課金されることに注意してください。 関数アプリの価格を計算する場合、他の Azure サービスと統合するトリガーとバインドでは、その追加サービスを作成して支払う必要があります。 

従量課金プランで実行される関数の場合、総コストは、関数の実行コストに、帯域幅と追加サービスのコストを加えたものです。 

関数アプリと関連サービスの全体的なコストを見積もるときは、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=functions)を使用します。 

| 関連コスト | 説明 |
| ------------ | ----------- |
| **ストレージ アカウント** | 各関数アプリには、General Purpose [Azure ストレージ アカウント](../storage/common/storage-introduction.md#types-of-storage-accounts)が関連付けられている必要があり、これは[別途課金](https://azure.microsoft.com/pricing/details/storage/)されます。 このアカウントは、Functions ランタイムによって内部的に使用されますが、ストレージのトリガーとバインドにも使用できます。 ストレージ アカウントを持っていない場合は、関数アプリの作成時に作成されます。 詳しくは、「[ストレージ アカウントの要件](functions-scale.md#storage-account-requirements)」をご覧ください。|
| **Application Insights** | Functions では、関数アプリに高パフォーマンスの監視エクスペリエンスを提供するために、[Application Insights](../azure-monitor/app/app-insights-overview.md) が利用されます。 必須ではありませんが、[Application Insights の統合を有効にする](functions-monitoring.md#enable-application-insights-integration)ことをお勧めします。 テレメトリ データの無料提供が毎月含まれます。 詳しくは、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページをご覧ください。 |
| **ネットワーク帯域幅** | 同じリージョン内の Azure サービス間のデータ転送に対する支払いはありません。 ただし、別のリージョンまたは Azure の外部への送信データ転送に対するコストが発生する可能性があります。 詳しくは、「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。 |

## <a name="behaviors-affecting-execution-time"></a>実行時間に影響を与える動作

関数の次の動作は、実行時間に影響を与える可能性があります。

+ **トリガーとバインド**: [関数バインド](functions-triggers-bindings.md)からの入力の読み取り、および関数バインドへの出力の書き込みにかかった時間は、実行時間としてカウントされます。 たとえば、Azure Storage キューにメッセージを書き込むために関数で出力バインドが使用されている場合、実行時間にはキューへのメッセージの書き込みにかかった時間が含まれ、これは関数のコストの計算に含まれます。 

+ **非同期実行**: 非同期要求 (C# では `await`) の結果に対する関数の待機時間は、実行時間としてカウントされます。 GB 秒数の計算は、関数の開始時刻と終了時刻、およびその期間のメモリ使用量に基づいています。 その間に発生した CPU アクティビティは、計算では考慮されません。 [Durable Functions](durable/durable-functions-overview.md) を使用することで、非同期操作中のコストを削減できます。 オーケストレーター関数での待機時間に対して課金されることはありません。

## <a name="view-execution-data"></a>実行データを表示する

[請求書](/azure/billing/billing-download-azure-invoice)では、 **[Total Executions - Functions]\(合計実行数 - Functions\)** および **[Execution Time - Functions]\(実行時間 - Functions\)** のコスト関連データと、実際に請求されたコストを見ることができます。 ただし、この請求データは過去の請求期間の月次集計です。 

関数のコストへの影響をより深く理解するには、Azure Monitor を使用することで、関数アプリによって現在生成されているコスト関連メトリックを表示できます。 このデータを取得するには、[Azure portal] の [Azure Monitor メトリックス エクスプローラー](../azure-monitor/platform/metrics-getting-started.md)または REST API を使用できます。

### <a name="monitor-metrics-explorer"></a>Monitor メトリックス エクスプローラー

従量課金プランの関数アプリのコスト関連データをグラフィック形式で表示するには、[Azure Monitor メトリックス エクスプローラー](../azure-monitor/platform/metrics-getting-started.md)を使用します。 

1. [Azure portal] の先頭にある **[リソース、サービス、ドキュメントの検索]** で「`monitor`」を検索し、 **[サービス]** の下にある **[モニター]** を選択します。

1. 左側で **[メトリック]**  >  **[リソースの選択]** を選択し、図の下にある設定を使用して、関数アプリを選択します。

    ![関数アプリ リソースを選択する](media/functions-consumption-costing/select-a-resource.png)

      
    |Setting  |推奨値  |説明  |
    |---------|---------|---------|
    | Subscription    |  該当するサブスクリプション  | 関数アプリのサブスクリプション。  |
    | Resource group     | 該当するリソース グループ  | 関数アプリが含まれる既存のリソース グループ。   |
    | リソースの種類     |  アプリケーション サービス | 関数アプリは App Services のインスタンスとしてモニターに表示されます。 |
    | リソース     |  関数アプリ  | 監視する関数アプリ。        |

1. **[適用]** を選択して、監視対象のリソースとして関数アプリを選択します。

1. **[メトリック]** から **[関数の実行回数]** を選択し、 **[集計]** から **[合計]** を選択します。 これにより、選択した期間の実行回数の合計がグラフに追加されます。

    ![グラフに追加する関数アプリのメトリックを定義する](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. **[メトリックの追加]** を選択し、手順 2 - 4 を繰り返して **[関数の実行単位]** をグラフに追加します。 

結果のグラフには、選択した時間範囲 (この例では 2 時間) に対する両方の実行メトリックの合計が含まれます。

![関数の実行回数と実行単位のグラフ](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

実行単位の数は実行回数よりはるかに多いため、グラフは実行単位だけのように見えます。

このグラフでは、MB ミリ秒数で測定して、2 時間に合計で 11.1 億の `Function Execution Units` が消費されたことが示されています。 GB 秒数に変換するには、1,024,000 で割ります。 この例の関数アプリでは、`1110000000 / 1024000 = 1083.98` GB 秒が消費されました。 この値を取得し、[Functions の価格ページ][価格ページ]で示されている実行時間の現在の料金を乗算することにより、この 2 時間のコストがわかります (実行時間の無料提供を既に使用してあるものとします)。 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) には、メトリックを取得するためのコマンドがあります。 ローカル コマンド環境から、または [Azure Cloud Shell](../cloud-shell/overview.md) を使用してポータルから直接、CLI を使用できます。 たとえば、次の [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) コマンドでは、前に使用したのと同じ期間に対する 1 時間あたりのデータが返されます。

`<AZURE_SUBSCRIPTON_ID>` は、コマンドを実行している Azure サブスクリプション ID に置き換えてください。

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

このコマンドでは、次の例のような JSON ペイロードが返されます。

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
この特定の応答では、`2019-09-11T21:46` から `2019-09-11T23:18` までの間に、アプリで 1,110,000,000 MB ミリ秒 (1083.98 GB 秒) が消費されたことが示されています。

## <a name="determine-memory-usage"></a>メモリの使用量を確認する

関数の実行単位は、実行時間とメモリ使用量を組み合わせたものであり、メモリ使用量を理解するのが困難なメトリックになっています。 現在、Azure Monitor では、メモリ データのメトリックは使用できません。 ただし、アプリのメモリ使用量を最適化したい場合は、Application Insights によって収集されるパフォーマンス カウンター データを使用できます。  

まだ行っていない場合は、[関数アプリで Application Insights を有効にします](functions-monitoring.md#enable-application-insights-integration)。 この統合を有効にすると、[ポータルでこのテレメトリ データのクエリを実行する](functions-monitoring.md#query-telemetry-data)ことができます。  

**[Monitoring]\(監視\)** の **[ログ (Analytics)]** を選択した後、次のテレメトリ クエリをコピーしてクエリ ウィンドウに貼り付け、 **[実行]** を選択します。 このクエリでは、サンプリングされた各時刻におけるメモリ使用量の合計が返されます。

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

結果は次の例のようになります。

| タイムスタンプ \[UTC\]          | 名前          | value       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14\.947 AM | Private Bytes | 209,932,288 |
| 9/12/2019, 1:06:14\.994 AM | Private Bytes | 212,189,184 |
| 9/12/2019, 1:06:30\.010 AM | Private Bytes | 231,714,816 |
| 9/12/2019, 1:07:15\.040 AM | Private Bytes | 210,591,744 |
| 9/12/2019, 1:12:16\.285 AM | Private Bytes | 216,285,184 |
| 9/12/2019, 1:12:31\.376 AM | Private Bytes | 235,806,720 |

## <a name="function-level-metrics"></a>関数レベルのメトリック

Azure Monitor ではリソース レベルでメトリックが追跡され、Functions の場合は関数アプリです。 Application Insights の統合では、関数ごとにメトリックが出力されます。 関数の平均継続時間を取得するための分析クエリの例を次に示します。

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| 名前                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [関数アプリの監視についての詳細情報](functions-monitoring.md)

[価格ページ]: https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
