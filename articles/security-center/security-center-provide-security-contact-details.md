---
title: Azure Security Center でセキュリティ連絡先の詳細情報を指定する | Microsoft Docs
description: このドキュメントでは、Azure Security Center でセキュリティ連絡先の詳細情報を指定する方法について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 15029c3e0bd3959000786af484a42691f00bb704
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603572"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Azure Security Center でセキュリティ連絡先の詳細情報を指定する
Azure Security Center では、Azure サブスクリプションに対してセキュリティ連絡先の詳細を指定していない場合、それを指定するよう推奨されます。 この情報は、Microsoft セキュリティ レスポンス センター (MSRC) で、不正なユーザーまたは権限のないユーザーによる顧客データへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 MSRC では、Azure のネットワークとインフラストラクチャの選択的なセキュリティ監視を行い、第三者からの脅威インテリジェンスと不正使用の報告を受け取ります。

電子メール通知は、最初に警告が発生したとき、および重大度が高い警告に対してのみ送信されます。 また、電子メールの設定は、サブスクリプション ポリシーに対してのみ構成できます。 サブスクリプション内のリソース グループは、この設定を継承します。 アラートは、Azure Security Center の Standard レベルのみで利用できます。

アラート メール通知の送信:
- 重大度が高いアラートに関してのみ送信されます
- 1 日あたりアラートの種類ごとに 1 人のメール受信者に送信されます  
- 1 日に 1 人の受信者に送信されるメール メッセージは 3 件以下です
- 各メール メッセージには、アラートの集計ではなく単一のアラートが含まれます
 
たとえば、RDP 攻撃について警告するメール メッセージが既に送信されている場合、別のアラートがトリガーされた場合でも、同じ日に RDP 攻撃に関する別のメール メッセージを受信することはありません。 

> [!NOTE]
> このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。  ステップ バイ ステップ ガイドではありません。

## アラートの電子メール通知を設定する<a name="email"></a>

1. ポータルで、 **[Pricing & settings]\(価格と設定\)** を選択します。
1. サブスクリプションをクリックします。
1. **[メール通知]** をクリックします。

> [!NOTE]
> 推奨事項を実装する場合は、 **[推奨事項]** で **[セキュリティ連絡先の詳細の指定]** を選択し、連絡先情報を提供する Azure サブスクリプションを選択します。 この操作で **[メール通知]** が開きます。

   ![セキュリティの連絡先詳細の提供][2]

   * セキュリティ連絡先の電子メール アドレスを (複数の場合はコンマで区切って) 入力します。 入力できる電子メール アドレスの数に制限はありません。
   * セキュリティ連絡先の国際電話番号を 1 件入力します。
   * 重大度が高い警告に関する電子メールを受信するには、 **[Send me emails about alerts (警告に関する電子メールを受け取る)]** オプションをオンにします。
   * サブスクリプションの所有者 (従来のサービス管理者と共同管理者、およびサブスクリプションのスコープで RBAC 所有者ロール) に電子メール通知を送信するオプションがあります。
   * **[保存]** を選択して、セキュリティ連絡先の情報をサブスクリプションに適用します。

## <a name="see-also"></a>関連項目
セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure セキュリティ センターでのセキュリティに関する推奨事項の管理](security-center-recommendations.md) 」 -- 推奨事項に従って Azure リソースを保護する方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」-- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」-- パートナー ソリューションの正常性状態を監視する方法について説明しています。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
