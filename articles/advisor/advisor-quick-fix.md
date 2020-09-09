---
title: Advisor のクイック修正による修復の推奨事項
description: Advisor でクイック修正を使って一括修復を実行する
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: ebd993733c1aca9cd4f7d92f0a75e22d68a87ffe
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86518064"
---
# <a name="quick-fix-remediation-for-advisor"></a>Advisor のクイック修正
**クイック修正** を使用すると、複数のリソースに対する推奨事項の修復をすばやく簡単に実行できます。 リソースの一括修復を行う機能が用意され、リソースの大規模修復によってサブスクリプションをより迅速に最適化できます。
この機能は、Azure portal で、特定の推奨事項についてのみ使用できます。


## <a name="steps-to-use-quick-fix"></a>"クイック修正" を使用する手順

1. **クイック修正**ラベルが付いている推奨事項の一覧で、推奨事項をクリックします。

   ![Advisor のクイック修正](./media/quick-fix-1.png)
   
   "*画像内の価格は例示の目的でのみ使用されています*"

2. 推奨事項の詳細ページに、この推奨事項に対するリソースの一覧が表示されます。 推奨事項に対して修復を行うすべてのリソースを選択します。

   ![Advisor のクイック修正](./media/quick-fix-2.png)
   
   "*画像内の価格は例示の目的でのみ使用されています*"

3. リソースを選択したら、 **[クイック修正]** をクリックして一括修復します。

   > [!NOTE]
   > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。
   
   > [!NOTE]
   > Advisor に記載されているメリット以外の影響が他にある場合は、エクスペリエンスで通知され、これは情報に基づいた修復に関する意思決定を行ううえで役立ちます。
   
4. 修復完了の通知が届きます。 修復されていないリソースがある場合はエラーが表示されます。また、リソース リスト ビューに、選択されているモードのリソースが表示されます。  


## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor の優れた運用の推奨事項](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
