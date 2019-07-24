---
title: Azure の課金内容を確認する | Microsoft Docs
description: Azure サブスクリプションの使用状況と課金内容を確認して理解する方法について説明します
services: ''
documentationcenter: ''
author: tonguyen10
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 9ba52951565c910138a5a49ecc97ad5748411e6d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57842313"
---
# <a name="understand-your-microsoft-azure-bill"></a>Microsoft Azure の課金内容を確認する
Azure の課金内容を確認するには、詳細な毎日の使用状況ファイルおよび Azure ポータルのコスト管理レポートと、請求書を比較します。

この記事は、Enterprise Agreement (EA) をお持ちの Azure カスタマーには適用されません。 EA カスタマーの場合、「[Understand your bill for Azure customers with an Enterprise Agreement](billing-understand-your-bill-ea.md)」 (Enterprise Agreement をお持ちの Azure カスタマーに対する請求について) を参照してください。

この記事は、この記事は、Microsoft 顧客契約をされている Azure カスタマーには適用されません。 Microsoft 顧客契約の課金アカウントをお持ちであれば、「[Understand the Azure charges on your Microsoft Customer Agreement invoice (Microsoft 顧客契約の請求書での Azure 料金を理解する)](billing-mca-understand-your-bill.md)」を参照してください。

請求サイクル、価格、使用量など、Azure Cloud Solution Provider (Azure CSP) プログラムで請求がどのように行われるかの説明については、「[Azure CSP の請求の概要](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/)」を参照してください。

## <a name="charges"></a>請求金額を確認する

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

請求書に記載されている金額について他の情報が必要であれば、使用量や費用を使用状況ファイルや Azure portal と比較できます。

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>オプション 1:使用量や費用を使用状況ファイルと比較する

使用状況の詳細の CSV ファイルは、請求期間と毎日の使用状況ごとの料金を示します。 ファイルの入手方法については、[Azure の請求書と毎日の使用状況データを取得する](billing-download-azure-invoice-daily-usage-date.md)方法に関するページを参照してください。

使用料金は、メーター レベルで表示されます。 以下の用語は、請求書と使用状況の詳細ファイルの両方で同じ内容を意味します。 たとえば、請求書の請求サイクルは、使用状況の詳細ファイルに示された請求期間と同じです。

 | 請求書 (PDF) | 使用状況の詳細 (CSV)|
 | --- | --- |
|請求サイクル | 請求期間 |
 |Name |測定カテゴリ |
 |type |測定サブカテゴリ |
 |リソース |測定名 |
 |リージョン |測定リージョン |
 |消費量 |消費量 |
 |あり |含まれる量 |
 |課金対象 |超過量 |

請求書の **[使用料金]** セクションには、請求期間中に使用された各メーターの合計値が含まれています。 たとえば、次のスクリーン ショットでは、Azure Scheduler サービスの使用料金が表示されます。

![請求書の使用料金](./media/billing-understand-your-bill/1.png)

使用状況の詳細の CSV の **[明細書]** セクションに同じ料金が表示されます。 "*[消費量]*" と "*[料金]*" は請求書と一致します。

![CSV の使用料金](./media/billing-understand-your-bill/2.png)

この料金の日々の明細を確認するには、CSV の **[毎日の使用状況]** セクションに移動します。 *[測定カテゴリ]* の下でフィルターを適用し、*Scheduler* を探します。 メーターが使用された日数と消費量を確認できます。 "*[リソース]*" と "*[リソース グループ]*" の情報も、比較用に一覧表示されます。 "*[消費量]*" の合計値が請求書に表示される値になります。

![CSV の [毎日の使用状況] セクション](./media/billing-understand-your-bill/3.png)

1 日あたりのコストを取得するには、**[明細書]** セクションの "*[消費量]*" を "*[レート]*" で乗算します。

詳細については、次を参照してください。

- [Azure 請求書を理解する](billing-understand-your-invoice.md)
- [Azure の詳細な使用状況を理解する](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>オプション 2:使用量と費用を Azure portal で比較する

Azure ポータルで料金を確認することもできます。 請求の使用状況と料金の概要を取得するには、コスト管理グラフを表示します。

1. Azure portal で [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。
1. 自分のサブスクリプションを選択し、**[コスト分析]** を選択します。
1. **[期間]** でフィルター処理します
1. 引き続き先の例を見ると、Azure Scheduler サービスの使用に対する請求を確認できます。

   ![Azure ポータルでのコスト分析ビュー](./media/billing-understand-your-bill/4.png)

1. その行を選択すると、日単位の費用明細が表示されます。

   ![Azure ポータルでのコスト履歴ビュー](./media/billing-understand-your-bill/5.png)

詳細については、「[Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md#costs)」をご覧ください。

## <a name="external"></a>個別に請求される外部サービス

外部サービス (マーケットプレース) 料金は、サード パーティ ソフトウェア ベンダーによって作成されたリソースに対する金額です。 このようなリソースは Azure Marketplace から入手し、利用できます。 たとえば、Barracuda Firewall はサード パーティによって提供される Azure Marketplace リソースです。 このファイアウォールと対応する測定に関するすべての料金は、外部サービス料金として表示されます。

外部サービスの料金は個別に請求されます。 これらの料金は、Azure の請求書には表示されません。 詳細については、「[Azure 外部サービスの課金について](billing-understand-your-azure-marketplace-charges.md)」を参照してください。

## <a name="resources-billed-by-usage-meters"></a>使用量メーターによって請求されるリソース

Azure では、リソース コストに基づいて直接請求されることはありません。 リソースに対する料金は、1 つまたは複数のメーターを利用して計算されます。 メーターは、リソースの有効期間を通してその使用量を追跡する目的で使用されます。 これらの測定が課金の計算に使用されます。

たとえば、仮想マシンなど、Azure リソースを 1 つ作成すると、1 つまたは複数のメーター インスタンスが作成されます。 メーターは、時間経過に伴ってリソースの使用状況を追跡するために使用されます。 各メーターから送られる使用状況レコードが Azure で利用され、請求金額が計算されます。

たとえば、Azure に作成された 1 つの仮想マシン (VM) に対して、使用状況を追跡するために次の測定が作成される場合があります。

- コンピューティング時間
- IP アドレス時間
- データ転送 (受信)
- データ転送 (送信)
- Standard マネージド ディスク
- Standard マネージド ディスク操作
- Standard IO - ディスク
- Standard IO - ブロック BLOB (読み取り)
- Standard IO - ブロック BLOB (書き込み)
- Standard IO - ブロック BLOB (削除)

VM が作成されると、各メーターが使用状況レコードの出力を開始します。 この使用状況とメーター料金が Azure の測定システムで追跡されます。

## <a name="payment"></a>請求金額を支払う

支払い方法としてクレジット カードまたはデビット カードを設定している場合、支払いは、請求期間の終了後、10 日以内に自動的に行われます。 クレジット カードの明細には、**MSFT Azure** と表記されます。

請求されるクレジットまたはデビット カードを変更する方法については、「[Azure のクレジット カードまたはデビット カードを追加、更新、または削除する](billing-how-to-change-credit-card.md)」を参照してください。

[請求書による支払い](billing-how-to-pay-by-invoice.md)を利用している場合は、請求書の下部に記載されている場所に送金します。

支払い状況を確認するには、[サポート チケットを作成](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) してください。


## <a name="tips-for-cost-management"></a>コスト管理に関するヒント

- 次を利用してコストを見積もる:
  - [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
  - [総保有コスト計算ツール](https://aka.ms/azure-tco-calculator)
  - [各サービスの詳細な価格情報](https://azure.microsoft.com/pricing/)
- [Azure ポータルで使用状況とコストを定期的に確認します](billing-getting-started.md#costs)。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458) してください。

## <a name="learn-more"></a>詳細情報

- [Azure の請求書と毎日の使用状況データを取得する](billing-download-azure-invoice-daily-usage-date.md)
- [Microsoft Azure の請求書の用語を理解する](billing-understand-your-invoice.md)
- [Microsoft Azure の詳細な使用状況の用語を理解する](billing-understand-your-usage.md)
- [Azure portal コスト管理](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Azure の課金とコスト管理で予想外のコストを防ぐ](billing-getting-started.md#costs)
