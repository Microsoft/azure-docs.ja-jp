---
title: Azure Application Gateway 上の WAF でのボット保護の概要
titleSuffix: Azure Web Application Firewall
description: この記事では、Application Gateway 上の Web アプリケーション ファイアウォール (WAF) でのボット保護の概要について説明します
services: web-application-firewall
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/04/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 3bc481cfc35ac94699d2795862f1fe8e4decf875
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026515"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway-bot-protection-overview"></a>Azure Application Gateway 上の Azure Web Application Firewall でのボット保護の概要

すべてのインターネット トラフィックの約 20 % は、問題のあるボットからのものです。 それらは、スクレーピング、スキャン、Web アプリケーションでの脆弱性の検索などを行います。 これらのボットは、Web アプリケーション ファイアウォール (WAF) で停止されると攻撃できません。 また、バックエンドやその他の基盤となるインフラストラクチャなどのリソースやサービスを使い尽くすこともできません。

マネージド ボット保護規則セットを WAF に対して有効にし、既知の悪意のある IP アドレスからの要求をブロックしたりログに記録したりすることができます。 この IP アドレスのソースは、Microsoft の脅威インテリジェンス フィードです。 インテリジェント セキュリティ グラフは、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。

> [!IMPORTANT]
> ボット保護規則セットは、現在パブリック プレビュー段階であり、プレビュー サービス レベル アグリーメントで提供されます。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「 [Microsoft Azure プレビューの追加利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。 

## <a name="use-with-owasp-rulesets"></a>OWASP ルールセットで使用する

ボット保護ルールセットは、任意の OWASP ルールセット (2.2.9、3.0、および 3.1) と共に使用できます。 どの特定の時点でも使用できるのは、1 つの OWASP ルールセットのみです。 ボット保護ルールセットには、独自のルールセットに表示される追加のルールが含まれています。 タイトルは **Microsoft_BotManagerRuleSet_0.1** で、他の OWASP ルールと同様に有効または無効にすることができます。

![ボット ルールセット](../media/bot-protection-overview/bot-ruleset.png)

## <a name="ruleset-update"></a>ルールセットの更新

既知の問題のある IP アドレスのボット軽減ルールセット一覧は、ボットとの同期を維持するために、Microsoft の脅威インテリジェンス フィードから 1 日に複数回更新されます。 Web アプリケーションは、ボットの攻撃ベクトルが変更された場合でも継続的に保護されます。

## <a name="next-steps"></a>次のステップ

- [Azure Application Gateway での Web アプリケーション ファイアウォールのボット保護を構成する (プレビュー)](bot-protection.md)
