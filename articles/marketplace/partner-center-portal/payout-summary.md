---
title: 商用マーケットプレースの支払いの概要 | Azure Marketplace
description: '[支払いの概要] には、プランで獲得した金額の詳細が表示されます。 また、支払いを受け取るタイミングと支払い額を確認することもできます。'
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.topic: guide
ms.date: 12/10/2019
ms.openlocfilehash: 6ee6b6f325ba58ecaa3c3acb5d5ded173262bafb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715432"
---
# <a name="payout-reporting"></a>支払いレポート

[ **[支払いの概要]** ](https://docs.microsoft.com/windows/uwp/publish/payout-summary) には、Microsoft で獲得した金額の詳細が表示されます。 また、支払いを受け取るタイミングと支払い額を確認することもできます。

Azure Marketplace でオファリングを販売する場合は、 **[支払いの概要]** に成功した支払いに関する情報も表示されます。 Azure Marketplace の支払いの詳細については、[Microsoft Azure Marketplace 参加ポリシー](https://go.microsoft.com/fwlink/p/?LinkId=722436)と [Microsoft Azure Marketplace 発行者契約](https://go.microsoft.com/fwlink/p/?LinkID=699560)を参照してください。

> [!NOTE]
> 支払いの対象となるには、収益が $50 の[支払いしきい値](payment-thresholds-methods-timeframes.md)に達している必要があります。 支払いしきい値の詳細については、このページを参照し、[Microsoft Azure Marketplace 発行者契約](https://go.microsoft.com/fwlink/p/?LinkID=699560)を確認してください。

- [支払いレポートにアクセスするためのロールとアクセス許可](#roles-and-permission-to-access-the-payout-report)
- [支払いレポート: Cloud パートナー ポータルとパートナー センターの違い](#payout-report-difference-between-cloud-partner-portal-and-partner-center)
- [顧客の種類](#customer-types)
- [支払いと使用の相関関係](#corelation-between-payout-and-usage)
- [取引履歴のダウンロード](#transaction-history-download-export)
- [課金に関する質問とサポート](#billing-questions-and-support)

## <a name="roles-and-permission-to-access-the-payout-report"></a>支払いレポートにアクセスするためのロールとアクセス許可

| レポート/ページ    | アカウント所有者    | Manager  | Developer | 経営担当者 |  財務担当者 | マーケター |
|------------------|------------------|----------|-----------|----|----|-----|
| 取得レポート (ほぼリアルタイムのデータを含む) | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし | 表示可能 | アクセス権なし |
| フィードバック レポート/応答 | フィードバックの表示および送信可能 | フィードバックの表示および送信可能 | フィードバックの表示および送信可能 | アクセス権なし | アクセス権なし | フィードバックの表示および送信可能 |
| 正常性レポート (ほぼリアルタイムのデータを含む) | 表示可能 | 表示可能 | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし |
| 利用状況レポート | 表示可能 | 表示可能 | 表示可能 | 表示可能 | アクセス権なし | アクセス権なし |
| 支払い受取口座 | 更新可能 | アクセス権なし | アクセス権なし | アクセス権なし | 更新可能 | アクセス権なし |
| 税プロファイル | 更新可能 | アクセス権なし | アクセス権なし | アクセス権なし | 更新可能 | アクセス権なし |
| 支払いの概要 | 表示可能 | アクセス権なし | アクセス権なし | アクセス権なし | 表示可能 | アクセス権なし |

## <a name="payout-report-difference-between-cloud-partner-portal-and-partner-center"></a>支払いレポート: Cloud パートナー ポータルとパートナー センターの違い

| | クラウド パートナー ポータル | パートナー センター |
|---------|---------|---------|
| リンク | [https://cloudpartner.azure.com/](https://cloudpartner.azure.com) | [https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory](https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory) および [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
| 「ナビゲーション」 | 分析情報の支払いで提供される支払いレポート | パートナー センターで提供される支払いレポート – [支払い] アイコン |
| Scope | <ul> <li>進行中の回収、回収済み、および支払い済みを対象に、品目ごとの取引が表示されます </li> <li>レポート – 発注書が作成された後のすべての品目が表示されます。これには、進行中の回収と進行中の課金、および回収の状態とまだ支払いの対象となっていない品目が含まれます。 </li> </ul> | <ul> <li>対象となる収益と見なされると品目が表示されます。</li> <li>顧客が最初に Microsoft に支払いをすると、ISV に支払いレポートの開始が表示されます。</li> <li>支払いレポートには、進行中の回収と進行中の課金は表示されません。  </li> </ul>  |
| 支払いの準備ができていない取引 | 進行中の課金 | 次の見積支払: 支払いの状態は未処理の状態です。  |
| 支払いの状態 |  | 未処理: <br> 収益は支払いの対象となります。 インセンティブ プログラムのプログラム ガイドで定義されているように、冷却期間中はこの状態のままになります。 <br> <br> 近日中: <br> 支払いの前に、支払指図によって生成された保留中の内部レビューが処理されます。 <br> <br> 送信済み: <br> 支払いが銀行に送信されました。 |

## <a name="customer-types"></a>顧客の種類

### <a name="enterprise-agreement"></a>エンタープライズ契約

エンタープライズ契約を結んでいない顧客には、Marketplace でのソフトウェア ライセンス料が毎月請求されます。 エンタープライズ契約を結んでいる顧客は、四半期ごとに提示される請求書を通して月単位で課金されます。

### <a name="credit-cards-and-monthly-invoice"></a>クレジット カードと月次請求書

顧客はクレジット カードと月次請求書を使用して支払うこともできます。 この場合、ソフトウェア ライセンス料金は月単位で課金されます。

### <a name="csp-and-direct-pay-users"></a>CSP と直接支払いユーザー

たとえば、顧客がクレジット カードを使用して購入した場合です。

## <a name="corelation-between-payout-and-usage"></a>支払いと使用の相関関係

|説明    |    Date  | 注文/使用量  | 支払い |
|----------|----------|-----------|-------------|
|注文期間   | 2019 年 8 月 15 日 - 2019 年 8 月 30 日 | **相関属性の注文** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **使用方法** <br> <ul> <li>CustomerId </li> <li>Customer Name</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> 見積拡張価格 <br> 見積支払額 (PC) </li> </ul> |  |
|期間の終了 (月)   | 2019 年 8 月 30 日 | | |
|請求日 | 2019 年 9 月 1 日 | | |
|顧客の支払日 | 2019 年 9 月 1 日 | | |
|エスクロー期間 (クレジット カードのみ、30 日間) | 2019 年 9 月 1 日 - 2019 年 9 月 30 日 | | **相関属性の注文:** <br> <ul><li>AssetId</li> <li>Customer ID</li> <li> Customer Name</li> </ul> <br> **使用方法** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支払いの状態:** 未処理 |
|回収機関の開始 | 2019 年 9 月 1 日 | | |
|回収期間の終了 (最大、30 日) | 2019 年 9 月 30 日 | | |
|支払い計算日 (毎月 15 日) | 2019 年 10 月 1 日 | | **相関属性** <br> <ul><li>AssetId</li> <li>Customer ID</li> <li>Customer Name</li> </ul> <br> **使用方法** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支払いの状態:** 近日中 |
|支払日 | 2019 年 10 月 15 日 | | **相関属性** <br> <ul><li>AssetId</li> <li>Customer ID</li> <li> Customer Name</li> </ul> <br> **使用方法** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支払いの状態:** 支払い送信済み |

### <a name="enterprise-agreement-quarterlymonthly-customers"></a>エンタープライズ契約 (四半期単位/月単位の顧客)

| 説明 |    Date  | 使用法 | 支払い |
|----------|----------|---------|-----------|
|注文期間 | 2019 年 8 月 15 日 - 2019 年 8 月 30 日 | **相関属性の注文** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **利用状況レポート** <br> <ul> <li>CustomerId </li> <li>Customer Name</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> 見積拡張価格 <br> 見積支払額 (PC) </li> </ul> | |
|期間の終了 (四半期) | 2019 年 9 月 30 日 | | |
|請求日 | 2019 年 10 月 15 日 | | |
|エスクロー期間 (クレジット カードのみ、30 日間) | 該当なし | | |
|回収機関の開始 | 2019 年 10 月 15 日 | | |
|クレジット カードのみ、30 日間 | 2019 年 11 月 1 日 - 2019 年 11 月 30 日 | | |
|回収期間の終了 (最大、90 日) | 2020 年 1 月 15 日 | | |
|顧客の支払日 | 2019 年 12 月 30 日 | | |
|支払計算 | 2020 年 1 月 15 日 | | |
|支払日 | 2020 年 2 月 15 日 | | **四半期ベースの顧客向け** <br> <br> **注文レポート** <br> <ul><li>AssetId</li> <li>Customer ID</li> <li> Customer Name</li> </ul> <br> **使用方法** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> </ul> <br> **支払いの状態:** 送信済み |

## <a name="transaction-history-download-export"></a>取引履歴のダウンロード エクスポート

このオプションでは、[取引履歴] ページに表示される各収益品目、収益の種類、日付、関連付けられた取引金額、顧客、製品、およびインセンティブ プログラムに適用されるその他の取引詳細のダウンロードが提供されます。

| 列名     | 説明    |
|-------------|-------------------------------|
| earningId                      | 各収益の一意識別子                                                                                                       |
| participantId                  | プログラムによるパートナー収益のプライマリ ID                                                                            |
| participantIdType              | 主に、インセンティブ プログラムのプログラム ID と、ストア プログラムおよび Azure Marketplace の販売者 IF                                          |
| participantName                | 収益パートナーの名前                                                                                                              |
| partnerCountryCode             | 収益パートナーの場所/国                                                                                                  |
| programName                    | インセンティブ/ストア プログラム名                                                                                                             |
| transactionId                  | 取引の一意識別子                                                                                                    |
| transactionCurrency            | 元の顧客取引が発生した通貨 (パートナーの場所の通貨ではありません)                                     |
| transactionDate                | 取引の日付。 多数の取引が 1 つの収益に寄与するプログラムに便利です                                           |
| transactionExchangeRate        | 対応する取引の米国ドル金額を示すために使用される為替レート                                                                 |
| transactionAmount              | 生成された収益に基づく元の取引通貨の取引金額                                              |
| transactionAmountUSD           | 米国ドルでの取引金額                                                                                                                |
| lever                          | 収益のビジネス ルールを示します                                                                                                  |
| earningRate                    | 収益を生成するために取引金額に適用されたインセンティブ率                                                                      |
| 数量                       | プログラムによって異なります。 取引プログラムの課金数量を示します                                                            |
| quantityType                   | 数量の種類を示します。例: 課金数量、MAU                                                                                     |
| earningType                    | 料金、リベート、協同組合、販売などであるかどうかを示します。                                                                                          |
| earningAmount                  | 元の取引通貨での収益金額                                                                                      |
| earningAmountUSD               | 米国ドルでの収益金額                                                                                                                    |
| earningDate                    | 収益の日付                                                                                                                      |
| calculationDate                | 収益がシステムで計算された日付                                                                                            |
| earningExchangeRate            | 対応する米国ドル金額を示すために使用される為替レート                                                                                  |
| exchangeRateDate               | earningAmountUSD の計算に使用される為替レートの日付                                                                                   |
| paymentAmountWOTax             | "送信済み" の支払いのみを対象とする支払先通貨での収益金額 (税別)                                                                 |
| paymentCurrency                | 支払いプロファイルでパートナーによって選択された支払先通貨。 送信済みの支払いにのみ表示されます                                                   |
| paymentExchangeRate            | ExchangeRateDate を使用して支払通貨での paymentAmountWOTax を計算するために使用される為替レート                                            |
| paymentId            | 支払いの一意識別子。 この番号は、口座取引明細書に表示されます                                            |
| paymentStatus            | 支払いの状態                                            |
| paymentStatusDescription            | 支払い状態のわかりやすい説明                                            |
| customerId                     | 常に空白になります                                                                                                                     |
| customerName                   | 常に空白になります                                                                                                                     |
| partNumber                     | 常に空白になります                                                                                                                     |
| productName                    | 取引にリンクされた製品名                                                                                                       |
| productId                      | 一意の製品識別子                                                                                                                |
| parentProductId                | 一意の親製品識別子。 注: 取引の親製品がない場合は、親製品 ID = 製品 ID です。 |
| parentProductName              | 親製品の名前。 注: 取引の親製品がない場合は、親製品名 = 製品名です。   |
| productType                    | 製品の種類 (アプリ、アドオン、ゲームなど)                                                                                        |
| invoiceNumber                  | 請求書番号 (EA のみに適用)                                                                                                  |
| resellerId                     | リセラー識別子                                                                                                                      |
| resellerName                   | リセラー名                                                                                                                            |
| transactionType                | 取引の種類 (購入、払戻、取消、配賦など)                                                               |
| localProviderSeller            | レコードのローカル プロバイダー/販売者                                                                                                          |
| taxRemitted                    | 送金された税金額 (消費税、使用税、または VAT/GST 税)。                                                                                   |
| taxRemitModel                  | 税の送金を担当する当事者 (消費税、使用税、または VAT/GST 税)。                                                                    |
| storeFee                       | ストアでアプリまたはアドオンを利用できるようにするための料金として、Microsoft によって保持される金額。                                            |
| transactionPaymentMethod       | 取引に使用される顧客の支払い方法 (カード、携帯電話会社の請求、PayPal など)                                |
| tpan                           | サードパーティの広告ネットワークを示します                                                                                                     |
| customerCountry                | 顧客の国                                                                                                                         |
| customerCity                   | 顧客の市区町村                                                                                                                            |
| customerState                  | 顧客の都道府県                                                                                                                           |
| customerZip                    | 顧客の郵便番号                                                                                                                 |
| TenantId                       |                                                                                                                                          |
| externalReferenceId            | プログラムの一意識別子                                                                                                        |
| externalReferenceIdLabel       | 一意識別子のラベル                                                                                                                  |
| transactionCountryCode       | 取引が発生した国別コード                                                                                                                  |
| taxCountry       | 販売先顧客の国                                                                                                                  |
| taxState       | 販売先顧客の都道府県                                                                                                                  |
| taxCity       | 販売先顧客の市区町村                                                                                                                  |
| taxZipCode       | 販売先顧客の郵便番号                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Program Code       | プログラム名と共にマップする文字列                                                                                                                   |
| earningAmountInLastPaymentCurrency       | 前回の支払い通貨での収益金額 (前の支払いが行われていない場合、フィールドは空になります)                                                                                                                   |
| lastPaymentCurrency       | 前回の支払い通貨 (前の支払いが行われていない場合、フィールドは空になります)                                                                                                                   |
| AssetId       | Marketplace サービスの顧客注文の一意識別子。  これは取引された購入品目を表します。 複数の資産が存在することもあります。                                                                                                                   |
| OrderId       | 顧客の請求書に関連付けられます                                                                                                                   |
| LineItemId       | 顧客の請求書の個別行                                                                                                                   |
| 顧客の国       | 顧客が指定した国名。  これは、顧客の Azure サブスクリプションの国とは異なる場合があります。                                                                                                                   |
| Customer EmailAddress       | 顧客が指定したメール アドレス。  これは、顧客の Azure サブスクリプションのメール アドレスとは異なる場合があります。                                                                                                                   |
| SkuId       | 発行中に定義される SKU ID。 1 つのプランに多数の SKU を関連付けることは可能ですが、1 つの SKU に関連付けることのできるプランは 1 つのみです。                                                                                                                   |

>[!Note]
>取引の公開オプションに関するすべてのレポートと分析情報は、Cloud パートナー ポータルの [分析情報] セクションまたはパートナー センターの [分析] セクションから入手できます。

## <a name="billing-questions-and-support"></a>課金に関する質問とサポート

課金に関する質問のサポートを受けるには、[商用マーケットプレースの公開元のサポート](https://aka.ms/marketplacepublishersupport)に問い合わせてください。
