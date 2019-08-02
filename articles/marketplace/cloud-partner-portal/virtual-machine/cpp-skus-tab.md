---
title: Azure Marketplace 向け Cloud パートナー ポータルの仮想マシンの SKU タブ
description: Azure Marketplace で仮想マシンのオファーを作成する際に使用する SKU タブについて説明します。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: dceb82af73061b91676ffb7061c7495995e76667
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868717"
---
# <a name="virtual-machine-skus-tab"></a>仮想マシンの SKU タブ

**[新しいオファー]** ページの **[SKU]** タブを使用すると、1 つまたは複数の SKU を作成して、それらを新しいオファーに関連付けることができるようになります。  異なる SKU を使用すると、機能セット、VM イメージの種類、スループットやスケーラビリティ、課金モデル、その他のいくつかの特性でソリューションを区別できます。


## <a name="create-a-sku"></a>SKU の作成

最初、新しいオファーには関連付けられている SKU がないため、 **[新しい SKU]** をクリックして SKU を作成します。

![仮想マシンの [新しいオファー] タブの [新しい SKU] ボタン](./media/publishvm_005.png)

<br/>

**[新しい SKU]** ダイアログ ボックスが表示されます。  新しい SKU の識別子を入力し、 **[OK]** をクリックします (識別子の名前付け規則については以下をご覧ください)。すると、 **[SKU]** タブに編集可能なフィールドが表示されます。    フィールド名に付いているアスタリスク (*) は、そのフィールドが必須であることを示します。

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![仮想マシンの [新しいオファー] フォームの [SKU] タブ](./media/publishvm_006.png)

次の表は、これらのフィールドの目的、内容、書式設定について説明します。  必須フィールドはアスタリスク (*) で示されます。

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **フィールド**       |     **説明**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU の設定*   |    |
| **SKU ID\***       | この SKU の識別子。  この名前は、最大 50 文字で、小文字の英数字またはダッシュ (-) を使用しますが、末尾にダッシュを使用することはできません。  これは、オファーの発行後に変更することはできません。  |
|  *SKU の詳細*   |  |
| **タイトル\***        | マーケットプレースに表示されるオファーのフレンドリ名。 最大長は 50 文字です。 |
| **要約\***      | マーケットプレースに表示されるオファーの簡潔な説明。 最大長は 100 文字です。 |
| **説明\***  | オファーについてより詳しい説明を提供する説明テキスト。  <!-- TD: max len/guidance? 3k characters -->  |
| **この SKU を非表示にする\*** | マーケットプレースで顧客に対して SKU を表示するかどうかを示します。  SKU をソリューション テンプレートだけで利用可能にし、個々の購入では利用不可にしたい場合は、SKU を非表示にします。  初期テストまたは一時的なオファーまたは季節のオファーに利用することもできます。 |
| **クラウドの可用性\*** | どのクラウドで SKU が使用できるかを決定します。  既定では Azure のパブリック バージョンです。  Microsoft Azure Government は政府用クラウドであり、米国連邦政府、州政府、地方自治体、部族政府、およびその認定パートナー向けにアクセスが規制されています。  政府用クラウドの詳細については、「[Azure Government とは](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)」を参照してください。 |
| **プライベート SKU か\*** | SKU がプライベートかパブリックかどうかを示します。 既定値は **[いいえ]** です。  詳細については、「[プライベート SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)」を参照してください。 |
| **国/リージョンの可用性\*** | SKU を購入できる国や世界のリージョンを決定します。 少なくとも 1 つのリージョン/国を選択します。 <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *料金*   |  |
| **ライセンス モデル\***| 使用する標準化された課金モデル。  **[使用量ベースで月ごとに課金される SKU]** を選択すると、アコーディオン セクションが開いて、コアごとの価格の詳細および無料使用期間を提供するかどうかを指定できます。  このセクションでは、この価格体系をエクスポートして、Excel にインポートすることもできます。 詳細については、「[Azure Marketplace の課金オプション](../../billing-options-azure-marketplace.md)」を参照してください。 | 
|  *VM イメージ*   |  |
| **オペレーティング システム ファミリ\*** | ソリューション VM が Windows ベースか Linux ベースかどうかを示します。 |
| **オペレーティング システムの種類の選択** | 指定された OS の特定のベンダーまたはリリース。 |
| **OS フレンドリ名\*** | 顧客に表示するオペレーティング システムの名前。  |
| **推奨される VM サイズ\*** | 標準化されたリストから最大 6 個の推奨される VM サイズを選択できるようにします。  この一覧は、Azure portal と Microsoft Marketplace に渡されます。  この一覧にある、(その顧客のサブスクリプション、リージョン、ゾーンなどにとって) 有効な最初の VM サイズは、その潜在顧客の既定値として設定されます。  ユーザーは、このサイズを、ソリューションのイメージと互換性のある任意のサイズに変更できます。 | 
| **ポートを開く**| 開くポートと、SKU でサポートするプロトコル。  これらの構成は、ソリューション VM のネットワークに対して構成した仮想ネットワークと一致する必要があります。 これらの設定は、VM のデプロイ中に有効になります。 ただし、SKU を発行した後で、ポートの設定を変更できます。 詳細については、「[Azure portal を使用して仮想マシンへのポートを開く方法](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal)」を参照してください。 <br/>次の既定のネットワーク マッピングがすべての VM に追加されます。 &emsp; Windows:3389 -> 3389 TCP、5986 -> 5986 TCP、&emsp; Linux:22 -> 22、TCP (SSH)。 |
| **ディスクのバージョン**  | 関連付けられたソリューション VM。ディスク バージョン番号とディスク URL で指定されます。 ディスク バージョンは、[セマンティック バージョン](https://semver.org/)形式 `<major>.<minor>.<patch>` である必要があります。  URL は、オペレーティング システム VHD に対して作成した Shared Access Signature URI です。  SKU あたり最大 8 つのディスク バージョンを追加することができますが、SKU について最も大きな番号のディスク バージョンだけが Azure Marketplace で表示されます。 その他のバージョンは、API を使用してのみ表示されます。  <!--TD: Add more specific link to API --> <br/> **[新しいデータ ディスク]** アコーディオン セクションでは、最大 15 個のデータ ディスクを VM にアタッチすることができます。  SKU を所定の VM バージョンと関連付けられたデータ ディスクで発行すると、この構成は変更できなくなります。  さらに VM バージョンが SKU に追加された場合、そのバージョンでも同じ数のデータ ディスクをサポートする必要があります。 <br/> Azure ベースの VM イメージを作成していない場合、このフィールドを後で追加または更新できます。  関連付けられている VM リソースの作成の詳細については、[VM の技術資産の作成](./cpp-create-technical-assets.md)に関するセクションをご覧ください。  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> **[保存]** をクリックしてここまでの作業を保存します。 次のタブは、オファーで[体験版](./cpp-test-drive-tab.md)をサポートするかどうかを指定します。


## <a name="additional-pricing-considerations"></a>価格に関する追加の考慮事項

上記の価格モデルは、基本的な説明です。  これは変更中ですが、地元または地域の税法および Microsoft の価格ポリシーの影響を受ける可能性があります。 

### <a name="new-core-sizes-added-on-722019"></a>2019 年 7 月 2 日に追加された新しいコア サイズ

2019 年 7 月 2 日に、(コア数に基づく) 新しい Azure 仮想マシン サイズに対する新しい料金の追加が VM の発行元に通知されました。  新しい料金は、コア サイズが 10、44、48、60、120、208、416 に対するものです。  既存の VM の場合、これらのコア サイズの新しい価格は、現在の価格に基づいて自動的に計算されます。  発行元は、2019 年 8 月 1 日までに追加料金を確認し、必要な変更を行う必要があります。  この日を過ぎて、発行元がまだ再パブリッシュを行っていない場合、これらの新しいコア サイズに対して自動的に計算された価格が有効になります。


### <a name="simplified-currency-pricing"></a>簡略化された通貨による価格

2018 年 9 月 1 日以降、 **[Simplified Currency Pricing] (簡略化された通貨による価格)** という新しいセクションがポータルに追加されます。 Microsoft では、世界中でより予測可能な価格とお客様からの回収を実現することにより、Azure Marketplace でのビジネスの合理化を図っています。 この合理化には、お客様に請求する通貨の数を減らすことが含まれます。  詳細については、「[Azure Marketplace の既存の VM オファーを更新する](./cpp-update-existing-offer.md)」を参照してください。


### <a name="additional-information-on-taxes-and-prices"></a>税金と価格に関する追加情報

* Microsoft では、一部の国/地域を「*税送金国*」に分類しています。  それらの国/地域では、Microsoft がお客様から税金を徴収して、政府に納税 (レミット) します。  その他の国/地域では、通常、パートナーが顧客から税金を徴収して、政府に納税する責任を負います。 後者の国/地域で販売することを選択した場合は、現地の税金を計算して支払いを実行できる必要があります。  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* プランの運用開始後は、価格の変更はできなくなります。 ただし、サポートされるリージョンを追加または削除することはできます。 
* Microsoft は、スケジュールされている SKU の料金に加えて、顧客の標準の Azure VM 使用料金を請求します。
* すべてのリージョンの価格は、現地通貨で、価格設定時点の通貨レートで設定されます。  <!-- TD: Meaning? - Offer created, published, other? -->
* 各リージョンの価格を個別に設定するには、価格スプレッドシートをエクスポートして、カスタム価格を適用してからインポートします。 


## <a name="next-steps"></a>次の手順

[テスト ドライブ](./cpp-test-drive-tab.md)機能をサポートしている場合は、必要に応じてその情報を指定します。そうでない場合は、オファーに対する [Marketplace](./cpp-marketplace-tab.md) データを指定します。
