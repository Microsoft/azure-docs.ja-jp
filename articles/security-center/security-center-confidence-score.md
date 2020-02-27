---
title: Azure Security Center の信頼度スコア | Microsoft Docs
description: Security Center では、脅威が正当であるかどうか、およびアラートのトリアージと優先順位付けの方法をチームが判断するのに役立つ信頼度スコアが生成されます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604382"
---
# <a name="alert-confidence-score-preview"></a>アラート信頼度スコア (プレビュー)

Azure Security Center は、Azure で実行するリソースに対する可視性を提供して、潜在的な問題を検出するとその旨を通知します。 アラートが大量になるとセキュリティ運用チームが個別に対応するのが困難になることがあるため、調査するアラートに優先度付けすることが必要になります。 アラートの調査は複雑で時間がかかることがあり、その結果、一部のアラートは無視されます。

Security Center の信頼度スコア (現在はプレビュー段階) は、チームがアラートのトリアージを行って優先順位を付けるのに役立ちます。 Security Center は、業界のベスト プラクティス、インテリジェント アルゴリズム、およびアナリストが使用するプロセスを自動的に適用して、脅威が本物かどうかを判断し、信頼度スコアの形で有意な分析情報を提供します。

## <a name="how-the-confidence-score-is-triggered"></a>信頼度スコアがトリガーされるしくみ

アラートは、仮想マシン上で疑わしいプロセスの実行が検出されたときに生成されます。 Security Center は、Azure で実行されている Windows 仮想マシンでこれらのアラートを確認および分析します。 Security Center は、組織全体の複数のエンティティとデータ ソース、およびすべての Azure リソースに対して高度なアルゴリズムを使用して自動チェックと関連付けを実行し、信頼度スコアを提示します。信頼度スコアは、アラートが本物であり、調査が必要だとする Security Center による判定の信頼性に関する尺度です。

## <a name="understanding-the-confidence-score"></a>信頼度スコアを理解する

信頼度スコアは 1 から 100 までの数字であり、アラートを調査する必要性を示す Security Center による判定の信頼度を表します。 スコアが高いほど、アラートが本物の悪意のあるアクティビティを示しているとする Security Center による判定の信頼度が高くなります。 信頼度スコアには、アラートにその信頼度スコアが与えられた主な理由が含まれています。 信頼度スコアを使用すると、セキュリティ アナリストは、アラートへの対応に容易に優先順位を付け、最も切迫している攻撃に最初に対応でき、最終的に攻撃やセキュリティ侵害に対応する時間を短縮することができます。

信頼度スコアを表示するには:
- Security Center ポータルで、[セキュリティ通知] ブレードを開きます。
-  アラートとインシデントは値が大きいものから小さいものの順に表示されます。つまり、脅威を示しているとする Security Center による判定の信頼度が高いアラートほど、ページ内で上に表示されます。 


 ![信頼度スコア][1]

アラートに対する Security Center の信頼度に貢献したデータを表示するには:
- [セキュリティ通知] ブレードの **[信頼度]** で、信頼度スコアに貢献した情報を表示して、アラートに関連する分析情報を取得します。 ここには、アラートの原因となったアクティビティの性質に関するより多くの分析情報が表示されます。

  ![疑わしい信頼度スコア][2]

Security Center の信頼度スコアを使用して、環境内のアラート トリアージの優先度付けを行います。 信頼度スコアは、業界のベスト プラクティスとインテリジェント アルゴリズムを適用して自動的にアラートを調査し、仮想アナリストとして機能して実際の脅威と注目すべき対象を判定することで、ユーザーの時間と労力を節約します。


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
