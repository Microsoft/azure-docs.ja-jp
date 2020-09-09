---
title: 侵入テスト | Microsoft Docs
description: この記事では、侵入テスト プロセスの概要と、Azure インフラストラクチャで実行されているアプリに対して侵入テストを行う方法について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2020
ms.author: terrylan
ms.openlocfilehash: dfacf124f8db0e5323c9abff56c4a78f85f6f014
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816135"
---
# <a name="penetration-testing"></a>侵入テスト

アプリケーションのテストとデプロイに Azure を使用するメリットの 1 つが、環境をすばやく作成できることです。 オンプレミスのハードウェアの要求、取得、および “ラックとスタック” に関して心配する必要はありません。

環境をすばやく作成することは非常に良いことですが、通常のセキュリティで適切な注意を払う必要はあります。 多くの場合に必要となる操作の 1 つは、Azure にデプロイするアプリケーションに対する侵入テストです。

Microsoft が [Azure 環境の侵入テスト](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)を実行していることは、既にご存知かもしれません。 このテストは Azure の向上を促進するうえで役に立ちます。

お客様に代わってお客様のアプリケーションの侵入テストを行うことはありませんが、お客様が独自のアプリケーションにテストを実行する必要があると考えていることは理解しています。 お客様のアプリケーションのセキュリティを強化するときに、Azure エコシステム全体のセキュリティ保護を高めることができるため、これは良いことです。

2017 年 6 月 15 日時点で、Microsoft は Azure リソースに対する侵入テストを実施する際に事前承認を求めなくなりました。 このプロセスは Microsoft Azure にのみ関連するものであり、その他の Microsoft Cloud サービスには適用されません。

>[!IMPORTANT]
>侵入テストのアクティビティを Microsoft に通知する必要はなくなりましたが、お客様は「[Microsoft Cloud Unified Penetration Testing Rules of Engagement (Microsoft Cloud 統合侵入テストの活動規則)](https://technet.microsoft.com/mt784683)」に引き続き従う必要があります。

実行できる標準テストは、次のとおりです。

* [Open Web Application Security Project (OWASP) の上位 10 の脆弱性](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [ファジー テスト](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* [ポートのスキャン](https://en.wikipedia.org/wiki/Port_scanner)

実行できない侵入テストの種類の 1 つは、[サービス拒否 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻撃です。 このテストには、DoS 攻撃自体を開始したり、DoS 攻撃の種類の判断、デモンストレーション、またはシミュレートを実行する可能性がある関連テストを実行したりすることも含まれます。

>[!Note]
>Microsoft は BreakingPoint Cloud と提携して、シミュレーションのために DDoS Protection を有効にしたパブリック IP アドレスに対してトラフィックを生成できるインターフェイスを構築しました。 BreakPoint Cloud のシミュレーションの詳細については、「[DDoS 検出を検証する](../../virtual-network/manage-ddos-protection.md#validate-ddos-detection)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 「[侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=2)」について詳細を説明します。
