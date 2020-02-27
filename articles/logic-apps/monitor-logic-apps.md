---
title: 状態を監視し、履歴を表示し、アラートを設定する
description: 実行状態の確認、トリガー履歴の確認、およびアラートの有効化による、 Azure Logic Apps でのロジック アプリのトラブルシューティング
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 495877f1c839de2cf3583a37180054c91bd9f139
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907222"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Azure Logic Apps での実行状態の監視、トリガー履歴の確認、アラートの設定

[ロジック アプリを作成して実行する](../logic-apps/quickstart-create-first-logic-app-workflow.md)と、ロジック アプリの実行状態、[実行の履歴](#review-runs-history)、[トリガー履歴](#review-trigger-history)、およびパフォーマンスを確認できます。 エラーやその他考えられる問題に関する通知を受け取るには、[アラート](#add-azure-alerts)を設定します。 たとえば、"1 時間に 5 件を超える実行が失敗したとき" を検出するアラートを作成できます。

リアルタイムでのイベントの監視と高度なデバッグを行うには、[Azure Monitor ログ](../azure-monitor/overview.md)を使用してロジック アプリの診断ログを設定します。 この Azure サービスを使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 これにより、トリガー イベント、実行イベント、アクション イベントなどのイベントを検索して表示できます。 この情報を [Azure Monitor ログ](../azure-monitor/platform/data-platform-logs.md)に格納することで、この情報の検索と分析に役立つ[ログ クエリ](../azure-monitor/log-query/log-query-overview.md)を作成できます。 この診断データは、Azure Storage や Azure Event Hubs などの他の Azure サービスで使用することもできます。 詳細については、「[Azure Monitor を使用してロジック アプリを監視する](../logic-apps/monitor-logic-apps-log-analytics.md)」を参照してください。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>実行履歴を確認する

項目またはイベントに対してトリガーが発生する度に、Logic Apps エンジンによって項目またはイベントごとに個別のワークフロー インスタンスが作成され、実行されます。 既定では、各ワークフロー インスタンスは並列で実行されるため、ワークフローは待ち時間なしで実行が開始されます。 実行中に何が発生したか、たとえば、ワークフローの各ステップの状態や、各ステップでの入出力を確認することができます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで、ロジック アプリを探して開きます。

   ロジック アプリを検索するには、Azure のメイン検索ボックスに「`logic apps`」と入力し、 **[Logic Apps]** を選択します。

   !["Logic Apps" サービスを検索して選択する](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal には、所有する Azure サブスクリプションに関連付けられているロジック アプリがすべて表示されます。 この一覧は、名前、サブスクリプション、リソース グループ、場所などに基づいてフィルター処理できます。

   ![サブスクリプションに関連付けられているロジック アプリの表示](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. ロジック アプリを選択して、 **[概要]** を選択します。

   [概要] ウィンドウの **[実行の履歴]** の下に、ロジック アプリの過去、現在、および待機中の実行がすべて表示されます。 一覧に多数の実行が表示され、必要なエントリが見つからない場合は、一覧をフィルター処理してみてください。 必要なデータが見つからない場合は、ツール バーの **[更新]** を選択してみます。

   ![概要、実行の履歴、およびその他のロジック アプリ情報](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   ロジック アプリの実行には、次のような状態があります。

   | Status | 説明 |
   |--------|-------------|
   | **取り消し済み** | ワークフローは実行中でしたが、キャンセル要求を受け取りました |
   | **Failed** | 少なくとも 1 つのアクションに失敗し、そのエラーを処理するワークフロー内の後続のアクションが設定されていません |
   | **実行中** | ワークフローは現在実行中です。 <p>この状態は、ワークフローのスロットル、または現行の価格プランが原因で発生している可能性もあります。 詳細については、[価格ページのアクション制限](https://azure.microsoft.com/pricing/details/logic-apps/)に関するページをご覧ください。 [診断ログ](../logic-apps/monitor-logic-apps.md)を設定すると、発生するスロットル イベントに関する情報を取得することができます。 |
   | **Succeeded** | アクションはすべて成功しています。 <p>**注**:特定のアクションでエラーが発生した場合、そのエラーは、ワークフローの後続のアクションによって処理済みです。 |
   | **待機中** | たとえば、その前のワークフローがまだ実行中である等の理由で、ワークフローが開始されていないか、または一時停止しています。 |
   |||

1. 特定の実行についてのステップとその他の情報を確認するには、 **[実行の履歴]** でその実行を選択します。

   ![確認する特定の実行を選択する](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   **[ロジック アプリの実行]** ウィンドウには、選択した実行の各ステップ、各ステップの実行状態、各ステップの実行にかかった時間が表示されます。次に例を示します。

   ![特定の実行の各アクション](./media/monitor-logic-apps/logic-app-run-pane.png)

   この情報をリスト形式で表示するには、 **[ロジック アプリの実行]** ツール バーで **[実行の詳細]** を選択します。

   ![ツール バーで [実行の詳細] を選択する](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   [実行の詳細] ビューには、各ステップ、その状態、およびその他の情報が表示されます。

   ![実行の各ステップの詳細を確認する](./media/monitor-logic-apps/review-logic-app-run-details.png)

   たとえば、その実行の**関連付け ID** プロパティを取得できます。これは、[Logic Apps 用の REST API](https://docs.microsoft.com/rest/api/logic) を使用する際に必要になる場合があります。

1. 特定のステップに関する詳細情報を取得するには、次のいずれかのオプションを選択します。

   * **[ロジック アプリの実行]** ウィンドウで、ステップを選択して図形を展開します。 入力、出力、およびそのステップで発生したエラーなどの情報を表示できるようになりました。次に例を示します。

     ![[ロジック アプリの実行] ウィンドウで、失敗したステップを表示する](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * **[ロジック アプリの実行の詳細]** ウィンドウで、目的のステップを選択します。

     ![[実行の詳細] ウィンドウで、失敗したステップを表示する](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     そのステップの入力や出力などの情報を表示できるようになりました。次に例を示します。

   > [!NOTE]
   > Logic Apps サービス内では、実行時の詳細情報とイベントはすべて暗号化されます。 これらの暗号化が解除されるのは、ユーザーがそのデータの表示を要求したときのみです。 [実行履歴の入力と出力を非表示にしたり](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate)、[Azure のロールベースのアクセス制御 (RBAC)](../role-based-access-control/overview.md) を使用して、この情報へのユーザー アクセスを制御したりすることができます。

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>トリガー履歴を確認する

各ロジック アプリの実行は、トリガーによって開始されます。 このトリガー履歴には、ロジック アプリによるすべてのトリガーの試行と、各トリガーの試行の入出力に関する情報が一覧表示されます。

1. [Azure portal](https://portal.azure.com) のロジック アプリ デザイナーで、ロジック アプリを探して開きます。

   ロジック アプリを検索するには、Azure のメイン検索ボックスに「`logic apps`」と入力し、 **[Logic Apps]** を選択します。

   !["Logic Apps" サービスを検索して選択する](./media/monitor-logic-apps/find-your-logic-app.png)

   Azure portal には、所有する Azure サブスクリプションに関連付けられているロジック アプリがすべて表示されます。 この一覧は、名前、サブスクリプション、リソース グループ、場所などに基づいてフィルター処理できます。

   ![サブスクリプションに関連付けられているロジック アプリの表示](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. ロジック アプリを選択して、 **[概要]** を選択します。

1. ロジック アプリのメニューで、 **[概要]** を選択します。 **[概要]** セクションの **[評価]** で、 **[See trigger history]\(トリガー履歴を表示する\)** を選択します。

   ![ロジック アプリのトリガー履歴を表示する](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   [トリガー履歴] ウィンドウには、ロジック アプリが行ったすべてのトリガーの試行が表示されます。 項目またはイベントに対してトリガーが発生する度に、Logic Apps エンジンによってワークフローを実行するロジック アプリ インスタンスが個別に作成されます。 既定では、各インスタンスが並列で実行されるため、ワークフローは待ち時間なしで実行が開始されます。 したがって、ロジック アプリが複数の項目を同時にトリガーする場合は、同じ日付と時刻をもつトリガー エントリが各項目に対して表示されます。

   ![さまざまな項目に対する複数のトリガーの試行](./media/monitor-logic-apps/logic-app-trigger-history.png)

   トリガー試行には次のような状態があります。

   | Status | 説明 |
   |--------|-------------|
   | **Failed** | エラーが発生しました。 失敗したトリガーに対して生成されたエラー メッセージを確認するには、そのトリガー試行を選択し、 **[出力]** を選択します。 たとえば、無効な入力が見つかる場合があります。 |
   | **Skipped** | トリガーによりエンドポイントがチェックされましたが、データが見つかりませんでした。 |
   | **Succeeded** | トリガーによりエンドポイントがチェックされ、使用可能なデータが見つかりました。 通常、この状態は、"起動済み" と共に表示されます。 表示されない場合、トリガー定義に、十分でない条件または `SplitOn` コマンドが含まれる可能性があります。 <p>この状態は、手動トリガー、繰り返しトリガー、またはポーリング トリガーに適用できます。 トリガーは正常に実行できますが、アクションによって未処理のエラーが生成されると、実行自体が引き続き失敗する可能性があります。 |
   |||

   > [!TIP]
   > トリガーの再チェックは、そのトリガーが次回起動されるのを待たずに実行できます。 [概要] ツール バーで、 **[トリガーの実行]** を選択してトリガーを選択すると、チェックが強制的に実行されます。 または、Logic Apps デザイナー ツール バーで **[実行]** を選択します。

1. 特定のトリガーの試行に関する情報を表示するには、[トリガー] ウィンドウで、そのトリガー イベントを選択します。 一覧に多数のトリガー試行が表示され、必要なエントリが見つからない場合は、一覧にフィルターを適用してみます。 必要なデータが見つからない場合は、ツール バーの **[更新]** を選択してみます。

   ![特定のトリガーの試行を表示する](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   選択したトリガー イベントに関する情報を確認できるようになりました。次に例を示します。

   ![特定のトリガー情報を表示する](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>監視アラートを設定する

ロジック アプリの特定のメトリックまたはしきい値の超過に基づいてアラートを発生させるには、[Azure Monitor のアラート](../azure-monitor/platform/alerts-overview.md)を設定します。 [Azure のメトリック](../monitoring-and-diagnostics/monitoring-overview-metrics.md)について確認してください。 [Azure Monitor](../log-analytics/log-analytics-overview.md) を使用せずにアラートを設定するには、次の手順を実行します。

1. ロジック アプリのメニューで、 **[監視]** にある **[アラート]**  >  **[新しいアラート ルール]** を選択します。

   ![ロジック アプリのアラートを追加する](./media/monitor-logic-apps/add-new-alert-rule.png)

1. **[ルールの作成]** ウィンドウの **[リソース]** で、ロジック アプリを選択します (まだ選択されていない場合)。 **[条件]** の **[追加]** を選択して、アラートをトリガーする条件を定義できるようにします。

   ![ルールの条件を追加する](./media/monitor-logic-apps/add-condition-for-rule.png)

1. **[シグナル ロジックの構成]** ウィンドウで、アラートを発生させるシグナルを検索して選択します。 検索ボックスを使用できます。またはシグナルをアルファベット順に並べ替えるには、 **[シグナル名]** 列ヘッダーを選択します。

   たとえば、トリガーが失敗したときにアラートを送信する場合は、次の手順を実行します。

   1. **[シグナル名]** 列で、 **[失敗したトリガー]** シグナルを見つけて選択します。

      ![アラートを作成するためのシグナルを選択する](./media/monitor-logic-apps/find-and-select-signal.png)

   1. 選択したシグナルについて表示される情報ウィンドウの **[アラート ロジック]** で、次のように条件を設定します。

   1. **[演算子]** には、 **[次の値以上]** を選択します。

   1. **[集計の種類]** は、 **[カウント]** を選択します。

   1. **[しきい値]** には「`1`」を入力します。

   1. **[条件のプレビュー]** で、条件が正しく表示されることを確認します。

   1. **[評価基準]** で、アラート ルールを実行する間隔と頻度を設定します。 **[集約粒度 (期間)]** で、データをグループ化する期間を選択します。 **[評価の頻度]** で、条件を確認する頻度を選択します。

   1. 準備ができたら、 **[完了]** を選択します。

   完成した条件は次のとおりです。

   ![アラートの条件を設定する](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   **[ルールの作成]** ページに、作成した条件とそのアラートを実行するためのコストが表示されます。

   ![[ルールの作成] ページの新しいアラート](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. アラートの名前、オプションの説明、重大度のレベルを指定します。 **[ルールの作成時に有効にする]** 設定をオンのままにするか、ルールを有効にする準備が整うまでオフにします。

1. 完了したら、 **[アラート ルールの作成]** を選択します。

> [!TIP]
> アラートからロジック アプリを実行するために、[要求トリガー](../connectors/connectors-native-reqres.md)をワークフローに含めることができます。これにより、次の例のようなタスクを実行できます。
> 
> * [Slack に投稿する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [テキストを送信する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [メッセージをキューに追加する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor を使用してロジック アプリを監視する](../logic-apps/monitor-logic-apps-log-analytics.md)