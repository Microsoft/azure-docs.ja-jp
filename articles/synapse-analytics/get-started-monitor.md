---
title: チュートリアル:Azure Synapse Analytics の使用を開始する - Synapse ワークスペースを監視する
description: このチュートリアルでは、ご利用の Synapse ワークスペースでのアクティビティを監視する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: monitoring
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 8c0cdcad9a7803e0d8063362ca62887990045c86
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209816"
---
# <a name="monitor-your-synapse-workspace"></a>Synapse ワークスペースを監視する

このチュートリアルでは、ご利用の Synapse ワークスペースでのアクティビティを監視する方法について説明します。 SQL、Apache Spark、および Pipelines の現在および過去のアクティビティを監視できます。 

## <a name="introduction-to-the-monitor-hub"></a>監視ハブの概要

Synapse Studio を開き、 **[監視]** ハブに移動します。 ここには、ワークスペースで発生したすべてのアクティビティの履歴と、現在アクティブになっているアクティビティが表示されます。 

* **[統合]** では、パイプライン、トリガー、統合ランタイムを監視できます。
* **[アクティビティ]** では、Spark と SQL のアクティビティを監視できます。 

## <a name="integration"></a>統合

1. **[Integration]\(統合\) > [パイプライン]** の順に移動します。 このビューでは、ご利用のワークスペース内で特定のパイプラインが実行された時刻をすべて確認できます。 
1. 前の手順で実行したパイプラインを見つけて、その **パイプライン名** をクリックし、詳細を表示します。
1. Synapse Studio の上部近くにある **[階層リンク バー]** をクリックします。 **[すべてのパイプラインの実行]** をクリックすると、前のビューに戻ります。

## <a name="apache-spark-activities"></a>Apache Spark アクティビティ

1. **[アクティビティ] > [Apache Spark applications]\(Apache Spark アプリケーション\)** の順に移動します。 これで、ご利用のワークスペース内で実行中の、または実行されたすべての Spark アプリケーションを確認できます。
1. 現在実行されていないアプリケーションを見つけて、その **[アプリケーション名]** をクリックします。 これで、その Spark アプリケーションの詳細を確認できます。
1. Apache Spark に慣れている場合は、 **[Spark History Server]** をクリックすることで、標準の Apache Spark History Server UI を見つけることができます。

## <a name="sql-activities"></a>SQL アクティビティ

1. **[アクティビティ] > [SQL requests]\(SQL 要求\)** の順に移動します。
1. このビューでは、SQL 要求を確認できます。
1. **[プール]** フィルターから、監視する **プール** を選択します。 これで、そのプールにあるご利用のワークスペース内で実行中の、または実行されたすべての SQL 要求を確認できます。
1. 特定の SQL 要求を検索し、その項目の上にマウス ポインターを置きます。 マウス ポインターを合わせると、SQL スクリプト アイコンが表示されるのがわかります。
1. SQL スクリプト アイコンをクリックして、SQL 要求の全文を表示します。

    > [!NOTE] 
    > ワークスペース対応の専用 SQL プール (以前の SQL DW) 内で Synapse Studio を介して送信される SQL 要求は、監視ハブで確認することができます。 その他のすべての監視アクティビティについては、Azure portal の専用 SQL プール (以前の SQL DW) の監視をご利用ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Knowledge Center を探索する](get-started-knowledge-center.md)
