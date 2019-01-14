---
title: スマート検出 - Azure Application Insights におけるトレースの重大度の比率の低下 | Microsoft Docs
description: トレースのテレメトリで異常なパターンを検出するために、Azure Application Insights でアプリケーション トレースを監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: mbullwin
ms.openlocfilehash: a593e42ed87ab985d4f4584ed6b73109716120bf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54027814"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>トレースの重大度の比率の低下 (プレビュー)

トレースはバックグラウンドの状況を把握するのに役立つため、アプリケーションで広く使用されています。 問題が発生したときに、望ましくない状態の原因となる一連のイベントを可視化するうえでトレースは極めて重要です。 通常、トレースは構造化されていませんが、トレースから具体的にわかることが 1 つあります。トレースの重大度レベルです。 アプリケーションの安定状態で、安定した状態を保つための "良好な" トレース (*Info*、*Verbose*) と "問題のある" トレース (*Warning*、 *Error*、*Critical*) の比率を予想します。 さまざまな理由 (ネットワークの一時的な問題など) によって "問題のある" トレースがある程度まで定期的に発生する可能性があることが想定されています。 しかし、実際の問題が大きくなり始めると、通常は "問題のある" トレースと "良好な" トレースの相対的比率の増加として現れます。 Application Insights のスマート検出では、アプリケーションによって記録されたトレースを自動的に分析し、トレースのテレメトリの重大度における異常なパターンについて警告できます。

この機能には、アプリのトレース ログを構成する以外に特別な設定は不要です ([.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-trace-logs) または [Java](https://docs.microsoft.com/azure/application-insights/app-insights-java-trace-logs) のトレース ログ リスナーを構成する方法を参照してください)。 この機能は、アプリが十分な例外のテレメトリを生成するとアクティブになります。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>スマート検出によるこの種の通知が送信されるのはどのような場合ですか
過去 7 日間に計算されたベースラインと比較して、"良好な" トレース (*Info* または *Verbose* レベルで記録されたトレース) と "問題のある" トレース (*Warning*、*Error、または *Fatal* レベルで記録されたトレース) の比率が特定の日に低下している場合に、この種の通知が送信されます。

## <a name="does-my-app-definitely-have-a-problem"></a>アプリに問題があるのは確かですか
いいえ。通知は、アプリに確実に問題があることを示すものではありません。 "良好な" トレースと "問題のある" トレースの比率の低下がアプリケーションの問題を示している場合もありますが、比率のこの変化が無害の場合もあります。 たとえば、アプリケーションの新しいフローで既存のフローよりも多くの "問題のある" トレースが生成されたことが原因で変化する場合があります。

## <a name="how-do-i-fix-it"></a>どのように修正すればよいですか
通知には、診断プロセスでサポートされる診断情報が含まれています。
1. **トリアージ**:  通知では影響を受ける操作の数が示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 問題の影響を受けるのはすべてのトラフィックか、それとも一部の操作だけか。 この情報を通知から取得できます。
3. **診断**。 サポート情報にリンクしている関連項目とレポートを使用して、問題の詳細な診断に役立てることができます。


