---
title: Azure の容量予約で SQL Data Warehouse 料金を節約する
description: コスト削減のために容量が予約された SQL Data Warehouse 料金のコストを節約する方法について説明します。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: c428472d5564393e9f9fcdc3103bb1506dc50936
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200369"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>容量が予約された SQL Data Warehouse 料金のコストを節約する

1 年分または 3 年分の cDWU の使用に関する予約をコミットすることによって、Azure SQL Data Warehouse にかかるコストを節約することができます。 SQL Data Warehouse の予約容量を購入するには、Azure リージョンと期間を選択する必要があります。 その後、SQL Data Warehouse SKU をカートに追加して、購入する cDWU ユニットの数量を選択します。

予約を購入すると、予約の属性に一致する SQL Data Warehouse 使用状況は従量課金制で課金されなくなります。

予約には、SQL Data Warehouse 使用状況に関連するストレージまたはネットワーク料金は含まれません。

予約容量を超過した場合、SQL Data Warehouse インスタンスは引き続き実行されますが、従量課金制の料金で課金されます。 予約は自動更新されません。

価格の詳細については、[SQL Data Warehouse の予約容量オファー](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/)に関するページを参照してください。

Azure SQL Data Warehouse の予約容量は、[Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) で購入できます。 予約の支払いは、[前払いまたは月払い](monthly-payments-reservations.md)で行います。 予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制サブスクリプションで所有者ロールを所持している必要があります。
- エンタープライズ サブスクリプションの場合、[EA ポータル](https://ea.azure.com/)で **[予約インスタンスを追加します]** を有効にする必要があります。 その設定が無効になっている場合は、EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが SQL Data Warehouse の予約容量を購入できます。

エンタープライズおよび従量課金制のお客様に対する予約購入の課金方法の詳細については、「[エンタープライズ加入契約に適用される Azure の予約の使用状況について](understand-reserved-instance-usage-ea.md)」および「[従量課金制サブスクリプションに適用される Azure の予約の使用状況について](understand-reserved-instance-usage.md)」をご覧ください。

## <a name="choose-the-right-size-before-purchase"></a>購入する前に適切なサイズを選択する

SQL Data Warehouse の予約サイズは、使用するコンピューティング Data Warehouse ユニット (cDWU) の合計に基づく必要があります。 購入は、100 cDWU 単位で行われます。

たとえば、SQL Data Warehouse の合計使用量が DW3000c とします。 そのすべてに対して予約容量を購入したいとします。 そのためには、cDWU 予約容量を 30 ユニット購入する必要があります。

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>SQL Data Warehouse の予約容量を購入する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[予約]** を選択します。
3. サブスクリプションを選択します。 [サブスクリプション] リストを使用して、予約容量の支払いに使用するサブスクリプションを選択します。 サブスクリプションの支払方法に対して、予約容量のコストが課金されます。 サブスクリプションの種類は、マイクロソフト エンタープライズ契約 (プラン番号:MS-AZR-0017P または MS-AZR-0148P) または従量課金制 (プラン番号:MS-AZR-0003P または MS-AZR-0023P)。
   - エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。
   - 従量課金制サブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。
4. スコープを選択します。 [スコープ] リストを使用して、サブスクリプション スコープを選択します。
   - **単一のリソース グループのスコープ** - 選択されたリソース グループ内の一致するリソースにのみ、予約割引を適用します。
   - **単一サブスクリプション** - 選択されたサブスクリプションの一致するリソースに予約割引を適用します。
   - **共有スコープ** - 課金コンテキスト内にある有効なサブスクリプションの一致するリソースに予約割引を適用します。 マイクロソフト エンタープライズ契約のお客様の場合、課金コンテキストは登録です。 従量課金制料金の個々のサブスクリプションの場合、課金スコープはアカウント管理者によって作成されるすべての有効なサブスクリプションです。
   - エンタープライズのお客様の場合、課金コンテキストは EA 登録です。
   - 従量課金制のお客様の場合、共有スコープは、アカウント管理者が作成するすべての従量課金制サブスクリプションです。
5. リージョンを選択して、予約容量でカバーされる Azure リージョンを選びます。
6. 数量を選択します。 購入する 100 Data Warehouse ユニット (cDWU) の数量を入力します。    
   たとえば、数量として 30 を入力すると、毎時間 3,000 cDWU の予約容量が割り当てられることになります。
7. **[コスト]** セクションで、SQL Data Warehouse の予約容量の予約コストを確認します。
8. **[購入]** を選択します。
9. **[この予約を表示する]** を選択すると、購入の状態が表示されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限付きで、予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](exchange-and-refund-azure-reservations.md)」を参照してください。

予約割引は、SQL Data Warehouse の予約容量のスコープとリージョンに一致する複数の SQL Data Warehouse インスタンスに自動的に適用されます。 SQL Data Warehouse の予約容量のスコープは、[Azure portal](https://portal.azure.com/)、PowerShell、CLI、または API で更新できます。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/)してください。

## <a name="next-steps"></a>次のステップ

- 予約割引の Azure SQL Data Warehouse への適用方法の詳細については、[Azure SQL Data Warehouse への予約割引の適用方法](prepay-sql-data-warehouse-charges.md)に関する記事を参照してください。

- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](save-compute-costs-reservations.md)
  - [Azure の予約の管理](manage-reserved-vm-instance.md)
  - [Azure の予約割引を理解する](understand-reservation-charges.md)
  - [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
  - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
