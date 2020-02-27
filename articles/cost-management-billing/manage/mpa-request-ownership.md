---
title: Microsoft Partner Agreement (MPA) に Azure サブスクリプションの課金所有権を取得する
description: 他のユーザーから Azure サブスクリプションの課金所有権を要求する方法について説明します。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 8e272267ffc8dd2946a390470df78b179ebbdf92
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199655"
---
# <a name="get-billing-ownership-of-azure-subscriptions-to-your-mpa-account"></a>MPA アカウントに Azure サブスクリプションの課金所有権を取得する

マネージド サービスと Azure の使用に対する単一の統合請求書を提供するために、クラウド ソリューション プロバイダー (CSP) は、ダイレクト エンタープライズ契約 (EA) の顧客から Azure サブスクリプションの課金所有権を引き継ぐことができます。

この機能は、[Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) として認定された CSP 直接請求パートナーだけが使用できます。 Microsoft のガバナンスとポリシーが適用され、特定の顧客のレビューと承認が必要になる場合があります。

課金所有権を要求するには、**全体管理者**または**管理エージェント** ロールが必要です。 詳細については、[パートナー センターでのユーザー ロールとアクセス許可の割り当て](https://docs.microsoft.com/partner-center/permissions-overview)に関するページを参照してください。

この記事は、Microsoft Partner Agreement の課金アカウントに適用されます。 これらのアカウントは、新しいコマース エクスペリエンスで顧客の課金を管理するために、クラウド ソリューション プロバイダー (CSP) 向けに作成されています。 新しいエクスペリエンスは、少なくとも 1 人の顧客が Microsoft 顧客契約 (MCA) に同意し、Azure プランを持っているパートナーのみが利用できます。 [Microsoft Partner Agreement にアクセスできるかどうかを確認してください](#check-access-to-a-microsoft-partner-agreement)。

## <a name="prerequisites"></a>前提条件

1. 顧客との[リセラー関係](https://docs.microsoft.com/partner-center/request-a-relationship-with-a-customer)を確立します。 [CSP リージョン承認の概要](https://docs.microsoft.com/partner-center/regional-authorization-overview)を参照して、顧客とパートナーの両方のテナントが同じ承認リージョン内にあることを確認します。  

2. [顧客が Microsoft 顧客契約に同意していることを確認](https://docs.microsoft.com/partner-center/confirm-customer-agreement)します。

3. 顧客に対する [Azure プラン](https://docs.microsoft.com/partner-center/purchase-azure-plan)を設定します。 顧客が複数のリセラーを通じて購入している場合は、顧客とリセラーの組み合わせごとに Azure プランを設定する必要があります。

## <a name="request-billing-ownership"></a>課金所有権を要求する

1. CSP 管理エージェントの資格情報を使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/mpa-request-ownership/search-cmb.png)

3. 左側で **[顧客]** を選択し、一覧から顧客を選択します。

   ![顧客の選択を示すスクリーンショット](./media/mpa-request-ownership/mpa-select-customers.png)        

4. 左下で **[譲渡要求]** を選択し、 **[新しい要求を追加]** を選択します。

   ![[譲渡要求] の選択を示すスクリーンショット](./media/mpa-request-ownership/mpa-select-transfer-requests.png)

5. 譲渡要求を承認する顧客組織内のユーザーのメール アドレスを入力します。 そのユーザーは、Enterprise Agreement のアカウント オーナーである必要があります。 **[譲渡要求を送信します]** を選択します。

   ![譲渡要求の送信を示すスクリーンショット](./media/mpa-request-ownership/mpa-send-transfer-requests.png)

6. ユーザーは、譲渡要求を確認する手順を示す電子メールを受信します。

   ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/mpa-request-ownership/mpa-review-transfer-request-email.png)

7. 譲渡要求を承認するには、ユーザーは、電子メールのリンクを選択し、指示に従います。

    ![譲渡要求電子メールのレビューを示すスクリーンショット](./media/mpa-request-ownership/mpa-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>譲渡要求の状態を確認する

1. [Azure portal](https://portal.azure.com) にサインインします。

2. "**コスト管理 + 請求**" を検索します。

   ![Azure portal でのコストの管理と請求の検索を示すスクリーンショット](./media/mpa-request-ownership/billing-search-cost-management-billing.png)

3. 左側で **[顧客]** を選択します。

   ![顧客の選択を示すスクリーンショット](./media/mpa-request-ownership/mpa-select-customers.png)

4. 一覧から、譲渡要求を送信した顧客を選択します。

5. 左下から **[Transfer requests]** (譲渡要求) を選択します。 譲渡要求ページには、次の情報が表示されます。

    ![譲渡要求の一覧を示すスクリーンショット](./media/mpa-request-ownership/mpa-select-transfer-requests-for-status.png)

   |列|定義|
   |---------|---------|
   |要求日|譲渡要求が送信された日付|
   |Recipient|課金所有権を譲渡する要求の送信先のユーザーの電子メール アドレス|
   |有効期限|要求の有効期限が切れる日付|
   |Status|譲渡要求の状態|

    譲渡要求は、次の状態のいずれかになります。

   |Status|定義|
   |---------|---------|
   |進行中|ユーザーは譲渡要求を承諾していません|
   |処理中|ユーザーは譲渡要求を承諾しました。 ユーザーが選択したサブスクリプションの課金が、自分のアカウントに移されています|
   |完了| ユーザーが選択したサブスクリプションの課金が、自分のアカウントに移されました|
   |エラーで終了|要求が完了しましたが、ユーザーが選択した一部のサブスクリプションの課金を移せませんでした|
   |有効期限切れ|ユーザーは予定どおりには要求を受諾せず、期限切れになりました|
   |Canceled|譲渡要求へのアクセス権を持つユーザーが要求を取り消しました|
   |拒否|ユーザーは譲渡要求を拒否しました|

6. 譲渡要求を選択して、詳細を表示します。 譲渡詳細ページには、次の情報が表示されます。![譲渡済みサブスクリプションの一覧を示すスクリーンショット](./media/mpa-request-ownership/mpa-transfer-completed.png)

   |列  |定義|
   |---------|---------|
   |譲渡要求 ID|譲渡要求の一意の ID。 サポート要求を送信する場合、ID を Azure サポートに知らせると、サポート要求が迅速に処理されます|
   |譲渡要求日|譲渡要求が送信された日付|
   |譲渡要求者|譲渡要求を送信したユーザーの電子メール アドレス|
   |譲渡要求の有効期限| 譲渡要求の有効が期限が切れる日付|
   |受信者のメール アドレス|課金所有権を譲渡する要求の送信先のユーザーの電子メール アドレス|
   |受信者に送信された譲渡リンク|譲渡要求を確認するようにユーザーに送信された URL|

## <a name="supported-subscription-types"></a>サポートされているサブスクリプションの種類

以下に示すサブスクリプションの種類の課金所有権を要求できます。

- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)\*
- [Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/)

\* 最初に、サポート チケットを使用して、Dev/Test サブスクリプションを EA Enterprise プランに変換する必要があります。 譲渡後、Enterprise Dev/Test サブスクリプションは従量課金制料金で課金されます。 顧客の EA を通じて Enterprise Dev/Test プランによって提供される割引は、CSP パートナーには適用されません。

## <a name="additional-information"></a>関連情報

次のセクションでは、譲渡サブスクリプションに関する追加情報が示されます。

### <a name="no-service-downtime"></a>サービスのダウンタイムは発生しない

サブスクリプションの Azure サービスは、中断することなく引き続き実行されます。 ユーザーが譲渡するように選択する Azure サブスクリプションの課金関係だけを移します。

### <a name="disabled-subscriptions"></a>無効なサブスクリプション

無効なサブスクリプションは譲渡できません。 サブスクリプションは、課金所有権を譲渡するにはアクティブな状態になっている必要があります。

### <a name="azure-resources-transfer"></a>Azure リソースの譲渡

VM、ディスク、Web サイトなどのサブスクリプションからすべてのリソースが譲渡されます。

### <a name="azure-marketplace-products-transfer"></a>Azure Marketplace 製品の譲渡

クラウド ソリューション プロバイダー (CSP) によって管理されるサブスクリプションで使用できる Azure Marketplace 製品は、それぞれのサブスクリプションと共に譲渡されます。 CSP に対して有効になっていない Azure Marketplace 製品があるサブスクリプションは、譲渡できません。

### <a name="azure-reservations-transfer"></a>Azure 予約の譲渡

Azure 予約は、自動的にはサブスクリプションとともに移動しません。 予約を移動するように [Azure サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)してください。

### <a name="access-to-azure-services"></a>Azure サービスへのアクセス

譲渡中、[Azure RBAC (ロールベースのアクセス制御)](../../role-based-access-control/overview.md) を使用して割り当てられた既存のユーザー、グループ、またはサービス プリンシパルのアクセスは影響を受けません。 パートナーは、サブスクリプションへの新しい RBAC アクセスを取得しません。  

パートナーは、顧客と協力して、サブスクリプションにアクセスできるようにする必要があります。  パートナーは、[代理管理者 - AOBO](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) または [Azure Lighthouse](https://docs.microsoft.com/azure/lighthouse/concepts/cloud-solution-provider) のオープン サポート チケットにアクセスする必要があります。

### <a name="azure-support-plan"></a>Azure サポート プラン

Azure サポートは、サブスクリプションとともには譲渡されません。 ユーザーは、すべての Azure サブスクリプションを譲渡する場合、サポート プランをキャンセルするように依頼してください。 譲渡後は、CSP パートナーがサポートを担当します。 顧客は、すべてのサポート リクエストで CSP パートナーと連係する必要があります。  

### <a name="charges-for-transferred-subscription"></a>譲渡されたサブスクリプションの料金

サブスクリプションの元の課金所有者は、譲渡が完了する時点までに報告されたすべての料金を負います。 お客様は、譲渡の時点以降にレポートされた料金を負います。 譲渡する前に発生したにもかかわらず、譲渡後に報告される料金もあります。 これらの料金は請求書に表示されます。

### <a name="cancel-a-transfer-request"></a>譲渡要求のキャンセル

要求が承認または拒否されるまで、譲渡要求をキャンセルできます。 譲渡要求をキャンセルするには、[譲渡詳細ページ](#check-the-transfer-request-status)に移動して、ページの下部からキャンセルを選択します。

### <a name="software-as-a-service-saas-transfer"></a>サービスとしてのソフトウェア (SaaS) の譲渡

SaaS 製品は、サブスクリプションとともには渡されません。 ユーザーに、[Azure サポートに連絡](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)して SaaS 製品の課金所有権を譲渡するように依頼します。 課金所有権とともに、ユーザーはリソースの所有権を譲渡することもできます。 リソースの所有権では、製品の詳細の削除や表示など、管理操作を実行できます。 ユーザーは、リソース所有権を譲渡するために、SaaS 製品でリソース所有者である必要があります。

### <a name="additional-approval-for-certain-customers"></a>特定の顧客に対する追加の承認

一部の顧客の譲渡要求では、顧客の現在のエンタープライズ登録構造の性質のために、Microsoft との追加のレビュー プロセスが必要になる場合があります。 パートナーには、顧客に招待状を送信しようとしたときに、このような要件が通知されます。 パートナーは、パートナー開発マネージャーおよび顧客のアカウント チームと協力してこのレビュー プロセスを完了するように要求されます。

### <a name="azure-subscription-directory"></a>Azure サブスクリプション ディレクトリ
譲渡される Azure サブスクリプションのディレクトリは、CSP 関係の確立中に選択された顧客のディレクトリと一致する必要があります。

これら 2 つのディレクトリが一致しない場合は、サブスクリプションを譲渡できません。 Azure サブスクリプションのディレクトリを選択して、顧客との新しい CSP リセラー関係を確立するか、Azure サブスクリプションのディレクトリを変更して、顧客の CSP 関係ディレクトリと一致させる必要があります。 詳細については、「[既存のサブスクリプションを Azure AD ディレクトリに関連付けるには](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory#to-associate-an-existing-subscription-to-your-azure-ad-directory)」を参照してください。


## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft Partner Agreement へのアクセスの確認
[!INCLUDE [billing-check-mpa](../../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください

お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## <a name="next-steps"></a>次のステップ

- Azure サブスクリプションの課金所有権は、お客様に譲渡されます。 [Azure portal](https://portal.azure.com) でこれらのサブスクリプションの料金を追跡します。

- 顧客と協力して、譲渡された Azure サブスクリプションにアクセスします。 [RBAC を使用して、Azure リソースへのアクセスを管理](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)します。
