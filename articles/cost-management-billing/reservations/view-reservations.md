---
title: Azure リソースの予約を表示する | Microsoft Docs
description: Azure portal で Azure の予約を表示する方法について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199247"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Azure portal に Azure の予約を表示する

Azure に対する予約を表示する方法は、ご利用のサブスクリプションの種類とアクセス許可に応じて 2 つあります。

## <a name="view-purchased-reservations"></a>購入した予約を表示する

既定では、予約を購入すると、お客様とアカウント管理者は予約を表示できます。 お客様とアカウント管理者は、予約注文および予約に対する所有者ロールを自動的に取得します。 その他のユーザーが予約を表示できるようにするには、予約注文または予約に対してそのユーザーを**所有者**または**閲覧者**として追加する必要があります。

詳細については、「[予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)」を参照してください。

所有者または閲覧者として予約を表示するには

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **予約**を検索します。
    ![Azure portal の検索を表示しているスクリーンショット](./media/view-reservations/portal-reservation-search.png)  
3. 一覧には、ご自分が所有者または閲覧者ロールになっている予約がすべて表示されます。 各予約には、最新の既知の使用率のパーセンテージが示されます。  
    ![予約の一覧を表示した例](./media/view-reservations/view-reservations.png)
4. 1 つの予約を選択して、過去 5 日間の使用率の傾向を確認します。  
    ![予約の使用率の傾向を表示した例](./media/view-reservations/reservation-utilization.png)
5. Reserved Instance 使用量 API と [Microsoft Azure Consumption Insights Power BI コンテンツ パック](/power-bi/service-connect-to-azure-consumption-insights)を使用して、[予約の使用率](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)を取得することもできます。

予約の範囲を変更する必要がある場合は、予約を分割するか、予約を管理できるユーザーを変更します。[Azure の予約の管理](manage-reserved-vm-instance.md)に関するページを参照してください。

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>エンタープライズ登録に対する予約トランザクションを表示する

 エンタープライズ登録を先導するパートナーがいる場合は、EA ポータルの **[レポート]** に移動することで予約を表示します。 その他のエンタープライズ登録の場合は、EA ポータルおよび Azure portal で予約を表示できます。 予約トランザクションを表示するには、EA 管理者である必要があります。

Azure portal で予約トランザクションを表示するには

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/view-reservations/portal-cm-billing-search.png)

1. **[予約トランザクション]** を選択します。
1. 結果をフィルター処理するには、 **[期間]** 、 **[種類]** または **[説明]** を選択します。
1. **[適用]** を選択します。

    ![予約トランザクションの結果を示すスクリーンショット](./media/view-reservations/portal-billing-reservation-transaction-results.png)

API を使用してデータを取得するには、「[Get Reserved Instance transaction charges for enterprise customers](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)」 (エンタープライズ顧客向けの予約インスタンス トランザクションを取得する) を参照してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](save-compute-costs-reservations.md)
- [Azure の予約の管理](manage-reserved-vm-instance.md)

サービス プランの購入:

- [Cosmos DB 予約容量の前払い](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../../sql-database/sql-database-reserved-capacity.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

ソフトウェア プランの購入:

- [Azure の予約からの Red Hat ソフトウェア プランの前払い](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure の予約からの SUSE ソフトウェア プランの前払い](../../virtual-machines/linux/prepay-suse-software-charges.md)

使用状況の把握:

- [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
- [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
