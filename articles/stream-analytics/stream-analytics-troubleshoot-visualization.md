---
title: "Stream Analytics ジョブの視覚化とトラブルシューティング | Microsoft Docs"
description: "診断図機能を使用してセルフサービスのトラブルシューティングを行うために、Stream Analytics ジョブのパイプラインを視覚化する方法について説明します。"
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 8351217a29af20a10c64feba8ccd015702ff1b4e
ms.openlocfilehash: 820b73a5dbf9bb108e189313cf6ee2b924ab04c7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Stream Analytics ジョブの視覚化とトラブルシューティング
Stream Analytics では、他のクラウドベースのテクノロジと同様に、ジョブで予想される出力 (または任意の出力) が生成されない理由を調査するためにトラブルシューティングが必要な場合があります。 この点を考慮して、Stream Analytics にはストリーミング ジョブを視覚化する機能が用意されています。 これはモデリング ツールとしても便利であり、作業のドキュメントが必要な場合にもメリットがあります。

視覚化パネルでは、入力が表示されるだけでなく、実行中のクエリとクエリの結果として構成されたすべての出力が表示されます。 接続や構成の問題を明らかにすることができ、構成を視覚的に表示する際にも役立ちます。

## <a name="using-the-diagnosis-diagram-tool"></a>診断図ツールの使用
このビジュアライザーにアクセスするには、Stream Analytics ジョブの [設定] 領域で [診断図] をクリックします。

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

次に示すように、すべての入力と出力が色分けされ、そのコンポーネントの現在の状態が示されます。

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

ユーザーがジョブ内のデータ フローのパターンを把握するためにクエリの中間ステップを確認する場合、コンポーネントのステップに分解されたクエリとフロー シーケンスが表示されます。 各クエリ ステップをクリックすると、次のように、クエリ編集ウィンドウに対応するセクションが表示されます。 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


