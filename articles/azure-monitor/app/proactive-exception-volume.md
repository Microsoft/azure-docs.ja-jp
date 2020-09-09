---
title: 例外数の異常な上昇 - Azure Application Insights
description: 例外数の異常なパターンを検出するために、Azure Application Insights でアプリケーションの例外をスマート検出で監視します。
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499428"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>例外数の異常な上昇 (プレビュー)

Application Insights は、アプリケーションでスローされた例外を自動的に分析し、例外テレメトリの異常なパターンについて警告することができます。

この機能を使うには、アプリの[例外レポートを構成する](./asp-net-exceptions.md#set-up-exception-reporting)以外に、特別なセットアップは必要ありません。 この機能は、アプリが十分な例外のテレメトリを生成するとアクティブになります。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>この種類のスマート検出通知はどのような場合に取得されますか。
アプリで 1 日に発生した特定の種類の例外の数が、過去 7 日間にわたって計算されたベースラインと比較して異常に上昇している場合、この種の通知を受け取ることがあります。
例外数の上昇の検出には機械学習アルゴリズムが使われ、アプリケーションの使用状況における自然な増加が考慮されます。

## <a name="does-my-app-definitely-have-a-problem"></a>アプリに問題があるのは確かですか
いいえ。通知は、アプリに確実に問題があることを示すものではありません。 多すぎる例外数は通常はアプリケーションの問題を示しますが、問題によるものではなくアプリケーションで正しく処理される可能性もあります。

## <a name="how-do-i-fix-it"></a>どのように修正すればよいですか
通知には、診断プロセスでサポートされる診断情報が含まれています。
1. **トリアージ**。 通知は、影響を受けるユーザーまたは要求の数を示します。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 問題の影響を受けるのはすべてのトラフィックか、それとも一部の操作だけか。 この情報を通知から取得できます。
3. **診断**。 検出には、例外がスローされたメソッドと、例外の種類に関する情報が含まれています。 サポート情報にリンクしている関連項目とレポートを使用して、問題の詳細な診断に役立てることもできます。
