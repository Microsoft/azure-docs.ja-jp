---
title: Azure Security Center の推奨事項を使用してセキュリティを強化する |Microsoft Docs
description: " セキュリティ ポリシーと Azure Security Center の推奨事項を使用して、セキュリティ攻撃を軽減する方法を説明します。 "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: memildin
ms.openlocfilehash: 8039be7b69444cc32e763e9a1fb074e7dda4a5ba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783233"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center の推奨事項を使用してセキュリティを強化する
セキュリティ ポリシーを構成し、Azure Security Center によって提供される推奨事項を実装することで、セキュリティに関する重大なイベントの可能性を低減できます。 この記事では、セキュリティ ポリシーと Security Center の推奨事項を使用してセキュリティ攻撃を軽減する方法を説明します。 

Security Center は、継続的スキャンを自動的に実行し、Azure リソースのセキュリティの状態を分析します。 Security Center は、潜在的なセキュリティの脆弱性を識別すると、必要なセキュリティ管理を構成するプロセスを説明する推奨事項を作成します。 推奨事項は 24 時間以内に更新されます。ただし、次の場合を除きます。

- オペレーティング システムのセキュリティ構成に関する推奨事項は 48 時間以内に更新されます
- Endpoint Protection の問題に関する推奨事項は 8 時間以内に更新されます

## <a name="scenario"></a>シナリオ
このシナリオでは、Security Center の推奨事項を監視し対処することで、セキュリティ インシデントの可能性を減らすために Security Center を使用する方法を示します。 シナリオでは、架空の会社 Contoso、および「Azure Security Center [計画および運用ガイド](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)」で説明されているロールを使用します。 このシナリオでは、次の人物の役割を中心に説明していきます。

![シナリオのロール](./media/security-center-using-recommendations/scenario-roles.png)

Contoso では、最近、オンプレミス リソースの一部を Azure に移行しました。 Contoso では、社内のリソースを保護し、クラウド内のリソースの脆弱性を軽減したいと考えています。

## <a name="use-azure-security-center"></a>Azure Security Center の利用
David は Contoso の IT セキュリティ部門に所属しています。彼は、セキュリティの脆弱性を検出し、回避するため、Contoso のサブスクリプションに Azure Security Center をオンボードすることを選択しました。 

Security Center は、Contoso の Azure リソースに関するセキュリティの状態を自動で分析し、既定のセキュリティ ポリシーを適用します。 Security Center では、潜在的なセキュリティの脆弱性が特定されると、セキュリティ ポリシーに設定されているコントロールに基づいて**推奨事項**が作成されます。 

David は、Azure Security Standard レベルをすべてのサブスクリプションで実行し、推奨事項とセキュリティ機能のフル スイートを有効にしています。 Jeff も、使用している [Windows](quick-onboard-windows-computer.md) サーバーと [Linux](quick-onboard-linux-computer.md) サーバーで Security Center のハイブリッド サポートを利用できるように、まだクラウドに移行していない既存のオンプレミス サーバーすべてをオンボードしています。

Jeff はクラウド ワークロード所有者で、 Contoso のセキュリティ ポリシーに従ってセキュリティ制御を適用する責任があります。 

Jeff は、以下のタスクを実行します。

- Security Center によって提供されるセキュリティに関する推奨事項を管理する
- セキュリティに関する推奨事項を評価し、推奨事項を適用するか無視するか判断する
- セキュリティに関する推奨事項を適用する

### <a name="remediate-threats-using-recommendations"></a>推奨事項を使用して脅威を修復する
Jeff は、毎日の監視アクティビティの一環として、Azure にサインインし、Security Center を開きます。 

1. Jeff は、ワークロードのサブスクリプションを選択します。

2. Jeff は、**セキュリティ スコア** を確認し、サブスクリプションがどれほど保護されているか全体像を把握します。スコアが 548 であることを確認します。

3. Jeff は、最初に処理する推奨事項を決定する必要があります。 それで、セキュリティ スコアをクリックし、[セキュリティ スコア](secure-score-security-controls.md)がどれほど改善されるかに注目しながらレコメンデーションについて処理を始めます。

4. Jeff はたくさんの VM やサーバーに接続しているため、 **[計算とアプリ]** にターゲットを絞ることにします。

5. Jeff が **[計算とアプリ]** をクリックすると、レコメンデーションのリストが表示されます。セキュリティ スコアへの影響に照らしてそれらを処理します。

6. Jeff にはインターネットに接続された VM がたくさんあり、それらのポートが公開されているため、攻撃者によってサーバーが制御されてしまうのではないかと心配しています。 そこで Jeff は [**Just-In-Time VM アクセス**](security-center-just-in-time.md)の使用を選択します。

Jeff は、高優先度と中優先度の推奨事項を確認し、実装について判断します。 各レコメンデーションについて、どのリソースが影響を受けるか、セキュリティ スコアの影響はどれほどか、各レコメンデーションが何を意味するか、そして各問題を軽減するための修復手順について解釈するため、Security Center によって提供される詳細情報に注目します。

## <a name="conclusion"></a>まとめ
Security Center の推奨事項を監視することにより、攻撃が行われる前にセキュリティの脆弱性を排除できます。 レコメンデーションを修復すると、セキュリティ スコアとワークロードのセキュリティ体制が向上します。 Security Center は自動的に、デプロイした新しいリソースを検出し、それらをセキュリティ ポリシーに照らし合わせて評価し、それらを保護するための新しい推奨事項を提供します。


## <a name="next-steps"></a>次のステップ
時間が経ってもリソースを保護し続けられるように、Security Center の推奨事項を定期的にチェックできる、適切な監視プロセスが備わっていることを確かめます。

このシナリオでは、セキュリティ ポリシーと Security Center の推奨事項を使用してセキュリティ攻撃を軽減する方法を説明しました。

[セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)を行うことで、脅威に対応する方法を確認します。
