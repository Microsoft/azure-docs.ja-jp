---
title: Azure の予約のセルフサービスによる交換と払戻
description: Azure の予約を交換または払い戻す方法について説明します。 予約の交換または払戻を行うには、予約注文の所有者アクセス権を持っている必要があります。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 553652832f3f4ce84a0a141455551e4977215184
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684424"
---
# <a name="self-service-exchanges-and-refunds-for-azure-reservations"></a>Azure の予約のセルフサービスによる交換と払戻

Azure の予約は、変化するニーズを満たすために柔軟性を提供します。 変化するニーズを満たすために、予約を同じ種類の別の予約と交換できます。 たとえば、他の任意のサイズまたはリージョンの別の VM の予約を購入して、仮想マシンの予約と交換することができます。 同様に、任意の種類またはリージョンの SQL PaaS Database の予約を購入して、SQL PaaS Database の予約と交換することができます。 予約は払い戻しも可能ですが、ご自分の請求範囲 (EA、Microsoft 顧客契約、Microsoft Partner Agreement など) のすべての予約コミットメントのキャンセル額合計が、12 か月間のローリング ウィンドウで 50,000 米国ドルを超えることはできません。 Azure Databricks の予約容量、Azure VMware Solution by CloudSimple の予約、Azure Red Hat Open Shift の予約、Red Hat プラン、SUSE Linux プランは払い戻しの対象外となります。

セルフ サービスの交換およびキャンセルの機能は、米国政府のエンタープライズ契約のお客様は利用できません。 従量課金制やクラウド ソリューション プロバイダー (CSP) などの他の種類の米国政府機関向けサブスクリプションがサポートされています。

> [!NOTE]
> - **既存の予約を交換または払い戻しするには、予約注文の所有者のアクセス許可を持っている必要があります**。 [予約を管理できるユーザーを追加または変更する](https://docs.microsoft.com/azure/cost-management-billing/reservations/manage-reserved-vm-instance#add-or-change-users-who-can-manage-a-reservation)ことができます。
> - Microsoft では、現在予約の払戻の中途解約料は請求していません。 将来的には、払戻料金を請求するようになる場合もあります。 現在、この料金がいつから有効になるかは未定です。

## <a name="how-to-exchange-or-refund-an-existing-reservation"></a>既存の予約の交換または払戻を行う方法

予約の交換は [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) から行うことができます。

1. 払い戻したい予約を選択して **[交換]** を選択します。  
    [![返上する予約を示す例の画像](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-return.png#lightbox)
1. 購入する VM 製品を選択し、数量を入力します。 新しい購入合計が、返金合計より多くなるようにしてください。 [購入する前に適切なサイズを決定](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy)します。  
    [![交換による VM 製品の購入を示す例の画像](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-select-purchase.png#lightbox)
1. トランザクションを確認して、完了します。  
    [![交換による VM 製品の購入で返上を完了する例の画像](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png)](./media/exchange-and-refund-azure-reservations/exchange-refund-confirm-exchange.png#lightbox)

予約を払い戻すには、 **[予約の詳細]** に移動し、 **[払戻]** を選択します。

## <a name="exchange-non-premium-storage-for-premium-storage"></a>Premium Storage 以外のストレージを Premium Storage と交換する

Premium Storage をサポートしない VM サイズの購入済み予約を、Premium Storage をサポートする対応する VM サイズと交換できます。 たとえば、_F1_ を _F1s_ と交換できます。 交換を行う場合は、[予約の詳細] に移動し、 **[交換]** を選択します。 交換により、予約済みインスタンスの期間のリセットや、新しいトランザクションの作成が行われることはありません。 

## <a name="how-transactions-are-processed"></a>トランザクションの処理方法

最初に、Microsoft が既存の予約を取り消し、その予約の日割り金額を払い戻します。 交換がある場合は、新しい購入が処理されます。 アカウントの種類と支払い方法に応じて、Microsoft は次のいずれかの方法を使用して払戻を処理します。

### <a name="enterprise-agreement-customers"></a>エンタープライズ契約のお客様

元の購入が年額コミットメントを使用して購入されていた場合は、年額コミットメントに交換の金額が加算されて払い戻されます。 予約を購入するのに使用された年額コミットメントの期間がアクティブでなくなっている場合は、現在のエンタープライズ契約年額コミットメント期間にクレジットを追加できます。 クレジットは、返金日付から 90 日間有効です。 未使用のクレジットは、90 日後に有効期限が切れます。

元の購入が超過であった場合、予約の購入に使用された元の請求書が再度開かれて再調整されます。 払戻用のクレジット メモが Microsoft から発行されます。

### <a name="pay-as-you-go-invoice-payments-and-csp-program"></a>従量課金制の請求書支払いと CSP プログラム

元の予約購入請求書がキャンセルされ、払戻用の新しい請求書が作成されます。 交換の場合、新しい請求書には払戻と新しい購入が示されます。 払戻額は購入に対して調整されます。 予約の払戻のみの場合は、日割り金額が保持され、今後の予約購入に対して調整されます。 従量課金制料金で予約を購入し、その後 CSP に移動した場合、予約を返上して再購入できます。ペナルティはありません。

### <a name="pay-as-you-go-credit-card-customers"></a>従量課金制クレジット カードのお客様

元の請求書は取り消され、新しい請求書が作成されます。 元の購入に使用されたクレジット カードに金額が払い戻されます。 カードを変更した場合は、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="cancel-exchange-and-refund-policies"></a>キャンセル、交換、払戻ポリシー

Azure には、キャンセル、交換、払戻に関して次のポリシーがあります。

**交換ポリシー**

- 既存の複数の予約を返上して、同じ種類の新しい予約を 1 つ購入できます。 別の種類の予約と交換することはできません。 たとえば、VM の予約を返金して、SQL の予約を購入することはできません。 交換により、ファミリ、シリーズ、バージョン、SKU、リージョン、数量、期間などの予約のプロパティを変更できます。
- 交換を処理できるのは、予約の所有者のみです。 [予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)方法を参照してください。
- 交換は払戻と再購入として処理されます。キャンセルと新しい予約の購入には、異なるトランザクションが作成されます。 日割り計算された予約金額が、交換する予約に払い戻されます。 新しい購入は全額請求されます。 日割り計算された予約金額は、返金される予約の日割り計算された残余値です。
- 予約を購入するために使用したエンタープライズ契約の有効期限が切れて、新しい契約として更新された場合も、予約を交換または払い戻すことができます。
- 新しい予約の生涯コミットメントは、返上する予約の残りのコミットメントと同じか、それよりも大きくなければなりません。 たとえば、1 か月あたり 100 ドルの 3 年間の予約を 18 回目の支払い後に交換した場合、新しい予約の生涯コミットメントは 1,800 ドル以上である必要があります (月払いまたは前払い)。
- 交換の一部として購入された新しい予約には、交換の時点から開始される新しい期間が適用されます。
- 交換に対するペナルティや年間制限はありません。

**払戻ポリシー**

- 現在、中途解約料は課金していませんが、将来的には 12% の解約料が発生する可能性があります。
- 1 つの請求プロファイルまたは 1 回の加入契約のすべての予約コミットメントのキャンセル額合計は、12 か月間のローリング ウィンドウにおいて 50,000 米国ドルを超えることはできません。 たとえば、1 か月あたり 100 米国ドルの 3 年間の予約を 18 か月目に払い戻した場合、取り消したコミットメント額は 1,800 米国ドルとなります。 この払い戻し後は、48,200 米国ドルが、払い戻し可能な新しい限度額となります。 この払い戻しから 365 日以内に、48,200 米国ドルの上限が 1,800 米国ドル増やされ、ご自分の新しいプールは 50,000 米国ドルとなります。 その請求プロファイルまたは EA 加入契約のその他のすべての予約取り消しは同じプールから減額され、同じ補充ロジックが適用されます。
- 1 つの請求プロファイルまたは 1 回の EA 加入契約の 12 か月間の上限 50,000 米国ドルを超える払い戻しは、Azure では処理できません。
- 払い戻し額は、購入価格または予約の現在の価格のいずれかの最低価格に基づいて計算されます。
- 払戻を処理できるのは、予約注文の所有者のみです。 [予約を管理できるユーザーを追加または変更する](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation)方法を参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。

## <a name="next-steps"></a>次のステップ

- 予約を管理する方法については、「[Azure の予約の管理](manage-reserved-vm-instance.md)」をご覧ください。
- Azure の予約の詳細については、次の記事を参照してください。
    - [Azure の予約とは](save-compute-costs-reservations.md)
    - [Azure での予約の管理](manage-reserved-vm-instance.md)
    - [予約割引の適用方法について](../manage/understand-vm-reservation-charges.md)
    - [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)
    - [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
    - [予約に含まれない Windows ソフトウェアのコスト](reserved-instance-windows-software-costs.md)
    - [CSP プログラムにおける Azure の予約](/partner-center/azure-reservations)
