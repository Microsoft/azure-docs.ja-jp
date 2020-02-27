---
title: Azure 無料サービスの使用状況を監視および追跡する
description: Azure portal で無料サービスの使用状況を確認する方法について説明します。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 0a62deecc6f70bb21059ae3ddda9eea173de65fe
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199808"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Azure 無料アカウントで無料サービスの使用状況を確認する

Azure 無料アカウントに含まれる無料サービスは、そのサービスの制限を超過しない限りは課金されません。 制限内に収めるには、Azure portal を使用して、無料サービスの使用状況を追跡します。

## <a name="check-usage-in-the-azure-portal"></a>Azure portal で使用状況を確認する

1.  [Azure portal](https://portal.azure.com) にサインインします。

2.  **サブスクリプション**を検索します。

    ![ポータルでのサブスクリプションの検索を示すスクリーン ショット](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Azure の無料アカウントにサインアップしたときに作成されたサブスクリプションを選択します。

4.  下へスクロールして、無料のサービス使用量を示す表を見つけます。

    ![無料サービスの使用状況を表示したスクリーンショット](./media/check-free-service-usage/subscription-usage-free-services.png)

    表には次の列が含まれています。

* **測定:** 使用しているサービスの測定単位を示します。
* **使用状況/使用制限:** 当月の使用状況とメーターの使用制限。
* **状態:** サービスの使用状況ステータス。 使用状況によって、次のいずれかの状態が表示されます。
  * **[未使用]:** メーターを使用しなかった、またはメーターの使用状況が課金システムに伝わらなかった。
  * **[\<日付> に超えました]:** \<日付> にメーターの使用制限を超過した。
  * **[超える可能性は低い]:** メーターの使用制限を超過する可能性が低い。
  * **[\<日付> に超えます]:** \<日付> にメーターの使用制限を超過する可能性がある。

> [!IMPORTANT]
>
> 無料サービスを利用できるのは、Azure 無料アカウントにサインアップしたときに作成されたサブスクリプションだけです。 サブスクリプションの概要ページに無料サービスの表が表示されない場合、そのサブスクリプションでは無料サービスを利用できません。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [Azure 無料アカウントをアップグレードする](upgrade-azure-subscription.md)
