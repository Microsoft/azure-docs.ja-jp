---
title: Azure 外部サービスの課金について | Microsoft Docs
description: Azure 外部サービス (旧称 Marketplace) の課金について説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce385de408d4b0a8cf1531f762a126a6b9b2c126
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199077"
---
# <a name="understand-your-azure-external-services-charges"></a>Azure 外部サービスの料金について
外部サービスは、Azure Marketplace でサードパーティ ソフトウェア ベンダーによって公開されています。 たとえば、SendGrid は、Azure で購入できる外部サービスですが、Microsoft が公開したものではありません。 一部の Microsoft 製品も、Azure Marketplace で販売されています。

## <a name="how-external-services-are-billed"></a>外部サービスの請求方法

- Microsoft 顧客契約 (MCA) または Microsoft Partner Agreement (MPA) をお持ちの場合、サードパーティのサービスは、他の Azure サービスと一緒に請求されます。 MCA または MPA が利用できるかどうかを確かめるには、[課金アカウントの種類を確認](#check-billing-account-type)してください。
- MCA も MPA もお持ちでない場合、外部サービスは、Azure サービスとは別に請求されます。
- 外部サービスの課金モデルはサービスごとに異なります。 従量課金制で請求されるサービスもあれば、月額固定料金が設定されているサービスもあります。
- 外部サービスでは月々の無料クレジットは使用できません。 [無料クレジット](https://azure.microsoft.com/pricing/spending-limits/)付きの Azure サブスクリプションを使用している場合でも、外部サービスからの請求にこれらの無料クレジットを適用することはできません。 新しい外部サービスまたはリソースをプロビジョニングすると、警告が表示されます。

    ![Marketplace 購入警告](./media/understand-azure-marketplace-charges/credit-warning.png)

<!-- ## View external service spending and history in the Azure portal
You can view a list of the external services that are on each subscription within the [Azure portal](https://portal.azure.com/):

1. Sign in to the [Azure portal](https://portal.azure.com/) as the account administrator.
2. In the Hub menu, select **Subscriptions**.

    ![Select Subscriptions in the Hub menu](./media/understand-azure-marketplace-charges/sub-button.png)
3. In the **Subscriptions** blade, select the subscription that you want to view, and then select **External services**.

    ![Select a subscription in the billing blade](./media/understand-azure-marketplace-charges/select-sub-external-services.png)
4. You should see each of your external service orders, the publisher name, service tier you bought, name you gave the resource, and the current order status. To see past bills, select an external service.

    ![Select an external service](./media/understand-azure-marketplace-charges/external-service-blade2.png)
5. From here, you can view past bill amounts including the tax breakdown.

    ![View external services billing history](./media/understand-azure-marketplace-charges/billing-overview-blade.png) -->

## <a name="view-and-download-invoices-for-external-services"></a>外部サービスの請求書を表示してダウンロードする

Microsoft 顧客契約 (MCA) または Microsoft Partner Agreement (MPA) をお持ちの場合、サードパーティのサービスは、他の Azure サービスと一緒に請求されます。 MCA または MPA が利用できるかどうかを確かめるには、[課金アカウントの種類を確認](#check-billing-account-type)してください。 利用できる場合は、[Azure portal で請求書を表示してダウンロードする方法](download-azure-invoice.md)に関するページを参照して、ご利用のサードパーティの料金を確認してください。

MCA も MPA もお持ちでない場合、サードパーティの料金は別の請求書になります。 Azure Marketplace の請求書は、次の手順に従って、Azure portal から表示およびダウンロードできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。
1. 左側のメニューで **[請求書]** を選択します。
1. **[Azure Marketplace と Reservations]** タブをクリックします。![[Azure Marketplace と Reservations] タブの画像](./media/understand-azure-marketplace-charges/invoice-tabs.png)
1. サブスクリプション ドロップダウンで、請求書を表示したい外部サービスを含むサブスクリプションを選択します。

## <a name="external-spending-for-ea-customers"></a>EA のお客様の外部支出

EA のお客様は、EA ポータルで外部サービスの支払いを表示し、レポートをダウンロードすることができます。 最初に、[EA お客様向け Azure Marketplace](https://ea.azure.com/helpdocs/azureMarketplace) をご覧ください。

## <a name="manage-payment-for-external-services"></a>外部サービスの支払いを管理する

外部サービスを購入するときに、リソースの Azure サブスクリプションを選択します。 選択した Azure サブスクリプションの支払い方法が、外部サービスの支払い方法になります。 外部サービスの支払い方法を変更するには、外部サービスに関連付けられている [Azure サブスクリプションの支払い方法を変更する](../manage/change-credit-card.md)必要があります。 外部サービスの注文がどのサブスクリプションに関連付けられているかを確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション メニューで、 **[すべてのリソース]** をクリックします。
     ![すべてのリソース メニュー項目のスクリーンショット](./media/understand-azure-marketplace-charges/all-resources.png)
1. ご利用の外部サービスを検索します。
1. **[サブスクリプション]** 列でサブスクリプションの名前を探します。
    ![リソースのサブスクリプション名のスクリーンショット](./media/understand-azure-marketplace-charges/sub-selected.png)
1. サブスクリプション名をクリックし、[アクティブな支払い方法を更新](../manage/change-credit-card.md)します。

<!-- Update your payment methods for external service orders from the [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> If you purchased your subscription with a Work or School account, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to make changes to your payment method.

1. Sign in to the [Account Center](https://account.windowsazure.com/) and [navigate to the **marketplace** tab](https://account.windowsazure.com/Store)

    ![Select marketplace in the account center](./media/understand-azure-marketplace-charges/select-marketplace.png)
2. Select the external service you want to manage

    ![Select the external service you want to manage](./media/understand-azure-marketplace-charges/select-ext-service.png)
3. Click **Change payment method** on the right side of the page. This link brings you to a different portal to manage your payment method.

    ![Order summary](./media/understand-azure-marketplace-charges/change-payment.PNG)
4. Click **Edit info** and follow instructions to update your payment information.

    ![Select edit info](./media/understand-azure-marketplace-charges/edit-info.png) -->

## <a name="cancel-an-external-service-order"></a>外部サービスの注文をキャンセルする
外部サービスの注文をキャンセルする場合は、[Azure Portal](https://portal.azure.com) でリソースを削除します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション メニューで、 **[すべてのリソース]** をクリックします。
    ![すべてのリソース メニュー項目のスクリーンショット](./media/understand-azure-marketplace-charges/all-resources.png)
1. ご利用の外部サービスを検索します。
1. 削除するリソースの横にあるチェック ボックスをオンにします。
1. コマンド バーの **[削除]** を選択します。
    ![[削除] ボタンのスクリーンショット](./media/understand-azure-marketplace-charges/delete-button.png)
1. 確認ブレードに「*Yes*」を入力します。
    ![リソースの削除](./media/understand-azure-marketplace-charges/delete-resource.PNG)
1. **[削除]** をクリックします。

## <a name="check-billing-account-type"></a>課金アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [コストの分析を開始する](../costs/quick-acm-cost-analysis.md)
