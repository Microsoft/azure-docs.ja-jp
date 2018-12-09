---
title: 無効な Azure サブスクリプションを再度有効にする | Microsoft Docs
description: Azure サブスクリプションが無効になっているときに再度有効にする方法について説明します。
keywords: azure サブスクリプションの無効化
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 4529d685-3a87-49d9-8d7a-49baad552d76
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/11/2018
ms.author: cwatson
ms.openlocfilehash: 14e372ef8de0a3ec82d9a14d026487d72bfa80bd
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52583722"
---
# <a name="why-is-my-azure-subscription-disabled-and-how-do-i-reactivate-it"></a>私の Azure サブスクリプションが無効になっています。その理由と、再度有効にする方法を教えてください。

Azure サブスクリプションは、クレジットの有効期限が切れている、使用制限に達している、請求の支払い期限が過ぎている、請求がクレジット カードの制限を超えている、アカウント管理者によってサブスクリプションが取り消されているという理由で無効にされることがあります。 どの問題に該当するかを調べ、この記事の手順を参照してサブスクリプションを再度有効にできます。

## <a name="your-credit-is-expired"></a>クレジットの有効期限が切れている

Azure 無料アカウントにサインアップすると、30 日間試用できる 200 ドルの Azure クレジットと 12 か月の無料サービスを提供する無料試用版のサブスクリプションを入手できます。 30 日目の終了時点で、Azure はサブスクリプションを無効にします。 サブスクリプションに含まれるクレジットと無料サービスを超えた使用による意図しない料金の発生を防止するために、お使いのサブスクリプションは無効になります。 Azure サービスを引き続き使用するには、[サブスクリプションを従量課金制サブスクリプションにアップグレードする](billing-upgrade-azure-subscription.md)必要があります。 サブスクリプションのアップグレード後も、12 か月間の無料サービスを利用できます。 無料のサービスおよび数量の限度を超えた使用分にのみ課金されます。

## <a name="you-reached-your-spending-limit"></a>使用制限に達している

無料試用版などのクレジットを利用した Azure サブスクリプションと Visual Studio Enterprise では、使用制限を設定しています。 これは、含まれているクレジットの範囲でしかサービスを使用できないことを意味します。 使用率が使用制限に達すると、Azure では、該当の残りの請求期間中、お使いのサブスクリプションを無効にします。 サブスクリプションに含まれるクレジットを超えた使用による意図しない料金の発生を防止するために、お使いのサブスクリプションは無効になります。 使用制限を削除するには、[アカウント センターでの使用制限の削除](billing-spending-limit.md#remove)に関する記事をご覧ください。

> [!NOTE] 
> 無料試用版サブスクリプションを使用しているときに使用制限を削除すると、ご利用のサブスクリプションは、無料試用の終了時に従量課金制に変換されます。 残りのクレジットは、サブスクリプションの作成後、丸 30 日間保持されます。 また、12 か月間の無料サービスにアクセスすることもできます。

Azure の課金アクティビティの監視および管理については、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md)」を参照してください。


## <a name="your-bill-is-past-due"></a>請求の支払い期限が過ぎている

支払い期限を過ぎた未払い額を支払うには、[Azure から電子メールを受信した後の支払期限を過ぎた Azure サブスクリプション未払い額の支払い](billing-azure-subscription-past-due-balance.md)に関する記事をご覧ください。

## <a name="the-bill-exceeds-your-credit-card-limit"></a>請求がクレジット カードの制限を超えている

この問題を解決するには、[別のクレジット カードに切り替えます](billing-how-to-change-credit-card.md)。 ご自身が会社を代表するユーザーである場合は、[請求書による支払いに切り替える](billing-how-to-pay-by-invoice.md)ことができます。

## <a name="the-subscription-was-accidentally-canceled-and-you-want-to-reactivate"></a>誤って取り消さたサブスクリプションを再アクティブ化する

アカウント管理者として、誤って従量課金制サブスクリプションを取り消した場合、アカウント センターで再アクティブ化できます。

1. [アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
1. 取り消されたサブスクリプションを選択します。
1. **[再度有効にする ]** をクリックします。

    ![右側のウィンドウに [再度有効にする] リンクが表示されているスクリーンショット。](./media/billing-how-to-cancel-azure-subscription/reactivate-sub.png)

その他のサブスクリプションの種類については、[サポートに連絡して](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)、サブスクリプションを再度有効にしてもらいます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
