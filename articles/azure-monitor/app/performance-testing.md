---
title: Azure Application Insights によるパフォーマンスとロード テスト | Microsoft Docs
description: Azure Application Insights で、パフォーマンスとロード テストを設定します
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: 8885a2e511c11317f593706255437e52819adcae
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024352"
---
# <a name="performance-testing"></a>パフォーマンス テスト

> [!NOTE]
> クラウドベースのロード テスト サービスは非推奨になっています。 非推奨について、サービスを使用できるかどうか、代替のサービスについて詳しくは、[こちら](/azure/devops/test/load-test/overview?view=azure-devops)をご覧ください。

Application Insights を使用して、Web サイトに対するロード テストを生成できます。 [可用性テスト](monitor-web-app-availability.md)と同じように、世界各地の Azure テスト エージェントから基本的な要求または[複数ステップ要求](availability-multistep.md)を送信できます。 パフォーマンス テストでは、最大 60 分間に最大 20,000 人の同時ユーザーをシミュレートできます。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

パフォーマンス テストを作成するためには、まず、Application Insights リソースを作成する必要があります。 リソースが既に作成されている場合は、次のセクションに進んでください。

Azure portal で、 **[リソースの作成]**  >  **[開発者ツール]**  >  **[Application Insights]** を選択して、Application Insights リソースを作成します。

## <a name="configure-performance-testing"></a>パフォーマンス テストを構成する

今回、初めてパフォーマンス テストを作成する場合は、 **[組織の設定]** を選択し、パフォーマンス テストのソースとなる Azure DevOps 組織を選択します。

テストを作成するには、 **[構成]** の下の **[パフォーマンス テスト]** に移動して、 **[新規]** をクリックします。

![少なくとも自分の Web サイトの URL を入力](./media/performance-testing/new-performance-test.png)

基本的なパフォーマンス テストを作成するには、テストの種類として **[手動テスト]** を選択し、テストに必要な設定を入力します。

|設定| 最大値
|----------|------------|
| ユーザー ロード | 20,000 |
| 期間 (分)  | 60 |  

テストが作成されたら、 **[テストの実行]** をクリックします。

テストが完了すると、次のような結果が表示されます。

![テスト結果](./media/performance-testing/test-results.png)

## <a name="configure-visual-studio-web-test"></a>Visual Studio Web テストを構成する

Application Insights の高度なパフォーマンス テスト機能は、Visual Studio のパフォーマンスとロード テスト プロジェクトの上に構築されています。

![Visual Studio ](./media/performance-testing/visual-studio-test.png)

## <a name="next-steps"></a>次のステップ

* [複数ステップ Web テスト](availability-multistep.md)
* [URL ping テスト](monitor-web-app-availability.md)
