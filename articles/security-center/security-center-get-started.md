---
title: Standard レベルにアップグレードする - Azure Security Center
description: このクイックスタートでは、セキュリティを強化するために Security Center の Standard 価格レベルにアップグレードする方法を示します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 550c9ff57b9c558f2f175165c7f06ead45991be9
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88226016"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>クイック スタート:Azure サブスクリプションでの Security Center Standard の利用開始
Azure Security Center は、ハイブリッド クラウド ワークロード全体で統合されたセキュリティ管理と脅威保護を実現します。 Free レベルでは Azure リソースのみの制限付きセキュリティが提供されますが、Standard レベルではこれらの機能がオンプレミスと他のクラウドに拡張されます。 Security Center Standard は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Security Center Standard は無料でお試しいただけます。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。

この記事では、セキュリティを強化するために Standard レベルにアップグレードし、仮想マシンに Log Analytics エージェントをインストールしてセキュリティの脆弱性と脅威を監視します。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。

サブスクリプションを Standard レベルにアップグレードするには、サブスクリプションの所有者、サブスクリプションの共同作成者、またはセキュリティ管理者の役割が割り当てられている必要があります。

## <a name="enable-your-azure-subscription"></a>Azure サブスクリプションの有効化

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

1. **[Microsoft Azure]** メニューの **[セキュリティ センター]** を選択します。 **[セキュリティ センター - 概要]** が開きます。

   ![Security Center の概要][2]

**[セキュリティ センター - 概要]** では、ハイブリッド クラウド ワークロードのセキュリティ対策の統合ビューが表示されるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。 Security Center は、ユーザーまたは別のサブスクリプション ユーザーによって以前に Free レベルにオンボードされていない Azure サブスクリプションを自動的に有効にします。

**[サブスクリプション]** メニュー項目をクリックして、サブスクリプションの一覧を表示およびフィルター処理できます。 Security Center は、これらのサブスクリプションのセキュリティの評価を開始して、セキュリティの脆弱性を識別します。 評価の種類をカスタマイズするには、セキュリティ ポリシーを変更します。 セキュリティ ポリシーは、ワークロードの必要な構成を定義し、会社や規制のセキュリティ要件に確実に準拠できるようにします。

Security Center を初めて起動してから数分以内に、以下の項目が表示されます。

- Azure サブスクリプションのセキュリティを向上させる方法についての**推奨事項**。 **[推奨事項]** タイルをクリックすると優先順位が付けられた一覧が起動します。
- Security Center によって現在評価されている **[計算とアプリ]** 、 **[ネットワーク]** 、 **[データのセキュリティ]** 、 **[ID およびアクセス]** の各リソースとそれぞれのセキュリティ対策のインベントリ。

Security Center をフルに活用するには、次の手順に従って Standard レベルにアップグレードし、Log Analytics エージェントをインストールする必要があります。


## <a name="upgrade-to-the-standard-tier"></a>Standard レベルにアップグレードする

Security Center のクイックスタートおよびチュートリアルの目的上、Standard レベルにアップグレードする必要があります。 Security Center Standard には無料試用版があります。 詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/security-center/)を参照してください。 

1. Security Center のサイドバーで、 **[作業の開始]** を選択します。
 
   ![作業の開始](./media/security-center-get-started/get-started-upgrade-tab.png)

    **[アップグレード]** タブに、オンボードの対象となるサブスクリプションとワークスペースが一覧表示されます。

1. **[Select workspaces to enable standard tier on]\(Standard レベルを有効にするワークスペースの選択\)** リストから、アップグレードするワークスペースを選択します。


    > [!TIP]
    > 無料試用版の対象となるワークスペースを選択した場合、次の手順で試用版が開始されます。 ワークスペースが試用版の対象ではない場合、そのワークスペースはアップグレードされて、課金が開始されます。

1. **[アップグレード]** を選択すると、選択したワークスペースが Standard レベルにアップグレードされます。


## <a name="automate-data-collection"></a>自動データ収集
Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure VM と非 Azure コンピューターからデータを収集します。 データは、Log Analytics エージェントを使用して収集されます。このエージェントは、セキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータをワークスペースにコピーします。 既定では、Security Center によって新しいワークスペースが作成されます。

自動プロビジョニングを有効にすると、Security Center は、サポートされているすべての Azure VM と新しく作成される VM に Log Analytics エージェントをインストールします。 自動プロビジョニングを強くお勧めします。

Log Analytics エージェントの自動プロビジョニングを有効にするには、次の手順に従います。

1. Security Center メイン メニューの **[Pricing & settings]\(価格と設定\)** を選択します。
1. サブスクリプションの行で、設定を変更したいサブスクリプションをクリックします。
1. **[データ収集]** タブで、 **[自動プロビジョニング]** を **[オン]** に設定します。
1. **[保存]** を選択します。
---
  ![自動プロビジョニングを有効にする][6]

Azure VM のこの新たな洞察により、Security Center は、システムの更新状態、OS セキュリティ構成、エンドポイント保護に関連する追加の推奨事項を提供し、追加のセキュリティ警告を生成できます。

  ![Recommendations][8]

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 引き続き次のクイックスタートとチュートリアルを行う予定の場合、Standard レベルの実行を継続して、自動プロビジョニングを有効のままにしてください。 続行しないまたは Free レベルに戻したい場合:

1. Security Center のメイン メニューに戻り、 **[Pricing & settings]\(価格と設定\)** を選択します。
2. Free レベルに変更したいサブスクリプションをクリックします。
3. **[価格レベル]** 、 **[Free]** の順に選択して、Standard レベルから Free レベルにサブスクリプションを変更します。
5. **[保存]** を選択します。

自動プロビジョニングを無効にする場合:

1. Security Center のメイン メニューに戻り、 **[Pricing & settings]\(価格と設定\)** を選択します。
2. 自動プロビジョニングを無効にするサブスクリプションをクリーンします。
3. **[データ収集]** タブで、 **[自動プロビジョニング]** を **[オフ]** に設定します。
4. **[保存]** を選択します。

>[!NOTE]
> 自動プロビジョニングを無効にしても、Log Analytics エージェントがプロビジョニングされている Azure VM からそのエージェントは削除されません。 自動プロビジョニングを無効にすると、リソースのセキュリティの監視が制限されます。
>

## <a name="next-steps"></a>次のステップ
このクイックスタートでは、Standard レベルにアップグレードし、ハイブリッド クラウド ワークロード全体での統合セキュリティの管理と脅威保護のために Log Analytics エージェントをプロビジョニングしました。 Security Center の詳しい使用方法を学習するには、オンプレミスおよび他のクラウドの Windows コンピューターのオンボードに関するクイックスタートに進みます。

> [!div class="nextstepaction"]
> [クイック スタート: Windows コンピューターでの Azure Security Center の利用開始](quick-onboard-windows-computer.md)

クラウドの支出を最適化して節約しますか?

> [!div class="nextstepaction"]
> [Cost Management を使用してコスト分析を開始する](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
