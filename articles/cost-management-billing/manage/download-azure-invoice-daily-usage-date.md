---
title: Azure の請求書と毎日の使用状況データをダウンロードする | Microsoft Docs
description: Azure の請求書と毎日の使用状況データをダウンロードまたは表示する方法について説明します。
keywords: 請求書,請求書のダウンロード,Azure の請求書,Azure の使用状況
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: af2184d7faa2ace8ed1043eaf2aaec0d525bc937
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200865"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Azure の請求書と毎日の使用状況データをダウンロードまたは表示する

ほとんどのサブスクリプションの場合は、請求書を [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) からダウンロードするか、メールで送信することができます。 エンタープライズ契約 (EA) の Azure のお客様 (EA のお客様) の場合、お客様の組織の請求書をダウンロードすることはできません。 請求書は、登録のために請求書を受信するよう設定されているユーザーに送信されます。

EA のお客様である場合や [Microsoft 顧客契約](#check-access-to-a-microsoft-customer-agreement)を結んでいる場合は、[Azure portal](https://portal.azure.com/) で使用量をダウンロードできます。

請求書と使用状況情報を取得するアクセス許可を持つのは、特定のロール (アカウント管理者やエンタープライズ管理者など) だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](manage-billing-access.md)に関するページをご覧ください。

Microsoft 顧客契約を結んでいる場合、請求書と使用量の情報を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。 Microsoft Customer Agreement の課金ロールの詳細については、「[課金プロファイルのロールとタスク](understand-mca-roles.md#billing-profile-roles-and-tasks)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Azure 請求書 (.pdf) のダウンロード

ほとんどのサブスクリプションでは、請求書を Azure portal からダウンロードできます。 Microsoft 顧客契約を結んでいる場合は、課金プロファイルの請求書のダウンロードに関するページを参照してください。

### <a name="download-invoices-for-an-individual-subscription"></a>個々のサブスクリプションの請求書のダウンロード

1. Azure portal の [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページから、[請求書へのアクセス権を持つユーザー](manage-billing-access.md)として自分のサブスクリプションを選択します。

2. **[請求書]** を選択します。

    ![Screenshot that shows the Billing & usage option](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. [ダウンロード] ボタンをクリックして、PDF 請求書のコピーをダウンロードし、 **[請求書のダウンロード]** を選択します。 "**使用できません**" というメッセージが表示される場合は、「[前回の請求期間の請求書が表示されない理由](#noinvoice)」を参照してください。

    ![Screenshot that shows billing periods, the download option, and total charges for each billing period](./media/download-azure-invoice-daily-usage-date/downloadinvoice.png)

4. また、 **[CSV のダウンロード]** をクリックして、消費量と見積料金の日単位の内訳をダウンロードすることもできます。

    ![請求書と使用状況のダウンロード ページを示すスクリーンショット](./media/download-azure-invoice-daily-usage-date/usageandinvoice.png)

請求書の詳細については、「[Microsoft Azure の課金内容の確認](../understand/review-individual-bill.md)」をご覧ください。 コスト管理に関するヘルプについては、[Azure の課金とコスト管理で想定外の料金を防ぐ](getting-started.md)方法に関するページを参照してください。

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Microsoft 顧客契約の請求書のダウンロード

請求書は、Microsoft の顧客契約の[課金プロファイル](../understand/mca-overview.md#billing-profiles)ごとに生成されます。 Azure portal から請求書をダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。

1. "**コスト管理 + 請求**" を検索します。
2. 課金プロファイルを選択します。
3. **[請求書]** を選択します。
4. 請求書グリッドで、ダウンロードする請求書の行を探します。
5. 行の末尾にある [ダウンロード] ボタンをクリックします。
6. ダウンロードのコンテキスト メニューで、 **[請求書]** を選択します。

最後の請求期間の請求書が表示されない場合は、**追加情報**に関するページを参照してください。 <!-- Fix this -->
### <a name="noinvoice"></a> 前回の請求期間の請求書が表示されない理由

請求書が表示されない理由はいくつか考えられます。

- Azure のサブスクリプションを開始してから 30 日以上経過していない。

- 請求書がまだ生成されていない。 請求期間の終了までお待ちください。

- 請求書を表示するアクセス許可がない。 Microsoft 顧客契約を結んでいる場合、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。 その他のサブスクリプションでは、アカウント管理者でない場合は古い請求書が表示されないことがあります。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](manage-billing-access.md)に関するページをご覧ください。

- 無料試用版を使用している場合や、毎月のクレジット額がまだ残っているサブスクリプションがある場合は、Microsoft 顧客契約を結んでいない限り、請求書を取得できません。

## <a name="get-your-invoice-in-email-pdf"></a>メールで請求書を入手する (.pdf)

オプトインして、Azure の請求書を電子メールで受取る受信者を追加設定することができます。 この機能は、サポート オファー、エンタープライズ契約、Azure イン オープン プランなどの特定のサブスクリプションでは利用できない場合があります。 Microsoft 顧客契約を結んでいる場合は、「サブスクリプションの請求書をメールで受け取る」を参照してください。

### <a name="get-your-subscriptions-invoices-in-email"></a>サブスクリプションの請求書をメールで受け取る

1. [[サブスクリプション] ページ](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)で、自分のサブスクリプションを選択します。 自分が所有するサブスクリプションごとにオプトインします。 **[請求書]** をクリックし、 **[請求書を送信する]** をクリックします。

    ![オプトイン フローを示すスクリーン ショット](./media/download-azure-invoice-daily-usage-date/invoicesdeeplink01.png)

2. **[オプトイン]** をクリックして、条項に同意します。

    ![手順 2 オプトイン フローを示すスクリーン ショット](./media/download-azure-invoice-daily-usage-date/invoicearticlestep02.png)

3. 契約書に同意したら、追加の受信者を構成できます。 受信者を削除すると、その電子メール アドレスも削除されます。 変更する場合には、再度追加する必要があります。

    ![手順 3 オプトイン フローを示すスクリーン ショット](./media/download-azure-invoice-daily-usage-date/invoicearticlestep03.png)

以上の手順を実行してもメールが届かない場合は、[プロファイルの通信設定](https://account.windowsazure.com/profile)のメール アドレスが正しいことを確認してください。

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>サブスクリプションの請求書のメールによる取得をオプトアウトする

上記の手順に従い、 **[電子メールで送信した請求書からオプトアウトする]** をクリックすることで、電子メールの請求書の取得をオプトアウトできます。 このオプションにより、メールで請求書を受信するよう設定されているすべてのメール アドレスが削除されます。 オプトアウトを解除する場合、受信者を再構成できます。

 ![オプトアウト フローを示すスクリーン ショット](./media/download-azure-invoice-daily-usage-date/invoicearticlestep04.png)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Microsoft 顧客契約の請求書をメールで受け取る

Microsoft 顧客契約を結んでいる場合は、メールの請求書の取得をオプトインできます。 すべての課金プロファイルの所有者、共同作成者、閲覧者、および請求書管理者が、メールで請求書を取得します。 閲覧者は、メールの請求書の設定を更新できません。

1. "**コスト管理 + 請求**" を検索します。
1. 課金プロファイルを選択します。
1. **[設定]** で **[プロパティ]** を選択します。
1. **[請求書を電子メールで送信]** で、 **[請求書を電子メールで送信の設定の更新]** を選択します。
1. **[オプトイン]** を選択します。
1. **[Update]** をクリックします。

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>課金プロファイルの請求書のメールでの取得をオプトアウトする

上記の手順に従い、 **[オプト アウト]** をクリックすることで、メールの請求書の取得をオプトアウトできます。すべての所有者、共同作成者、閲覧者、および請求書管理者も、メールの請求書の取得からオプトアウトされます。 閲覧者の場合は、メールの請求書の基本設定を変更できません。

## <a name="download-usage-in-azure-portal"></a>Azure portal で使用状況をダウンロードする

 ほとんどのサブスクリプションでは、次の手順に従って、毎日の使用状況を確認してください。

1. Azure portal の [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ページから、[請求書へのアクセス権を持つユーザー](manage-billing-access.md)として自分のサブスクリプションを選択します。

2. **[請求書]** を選択します。

    ![Screenshot that shows the Billing & usage option](./media/download-azure-invoice-daily-usage-date/billingandusage.png)

3. 確認する請求書期間の [ダウンロード] ボタンをクリックします。

4. **[CSV のダウンロード]** をクリックして、消費量と見積料金の日単位の内訳をダウンロードします。  CSV ファイル準備には数分かかる場合があります。

### <a name="download-usage-for-ea-customers"></a>EA のお客様の使用量のダウンロード

EA のお客様として使用量データを表示およびダウンロードするには、料金表示ポリシーが有効になっているエンタープライズ管理者、アカウント所有者、または部門管理者である必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. **[使用量 + 請求金額]** を選択します。
1. ダウンロードしたい月で、 **[ダウンロード]** を選択します。

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Microsoft 顧客契約に関する使用量のダウンロード

課金プロファイルの使用量データを表示およびダウンロードするには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。

#### <a name="download-usage-for-billed-charges"></a>課金された料金の使用量のダウンロード

1. "**コスト管理 + 請求**" を検索します。
2. 課金プロファイルを選択します。
3. **[請求書]** を選択します。
4. 請求書グリッドで、ダウンロードする使用量に対応する請求書の行を探します。
5. 行の末尾にある省略記号 (`...`) をクリックします。
6. ダウンロードのコンテキスト メニューで **[Azure の利用状況と請求金額]** を選択します。

#### <a name="download-usage-for-open-charges"></a>課金されていない料金の使用量のダウンロード

現在の請求期間の月度累計使用量 (料金がまだ課金されていないことを意味します) もダウンロードできます。

1. "**コスト管理 + 請求**" を検索します。
2. 課金プロファイルを選択します。
3. **[概要]** ブレードで **[Azure の利用状況と請求金額をダウンロードする]** をクリックします。

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

請求書および料金の詳細については、以下を参照してください。

- [Microsoft Azure の課金内容の確認](../understand/review-individual-bill.md)
- [Azure 請求書の用語について](../understand/understand-invoice.md)
- [Microsoft Azure の詳細な使用状況の用語を理解する](../understand/understand-usage.md)
- [組織の Azure の価格を表示する](ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、以下を参照してください。

- [課金プロファイルの請求書の料金を理解する](../understand/review-customer-agreement-bill.md)
- [課金プロファイルの請求書の用語を理解する](../understand/mca-understand-your-invoice.md)
- [課金プロファイルの Azure の使用量と料金のファイルを理解する](../understand/mca-understand-your-usage.md)
- [課金プロファイルの税務書類を表示およびダウンロードする](../understand/mca-download-tax-document.md)
- [組織の Azure の価格を表示する](ea-pricing.md)
