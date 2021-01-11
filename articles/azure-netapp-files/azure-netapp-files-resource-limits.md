---
title: Azure NetApp Files のリソース制限 | Microsoft Docs
description: Azure NetApp Files リソースの制限と、リソース制限の引き上げを要求する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: b-juche
ms.openlocfilehash: 9facbc1629b8e1330c6bbafb4444d5bfc237d16f
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752295"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files のリソース制限

Azure NetApp Files のリソース制限を理解すると、ボリュームの管理に役立ちます。

## <a name="resource-limits"></a>リソース制限

次の表は、Azure NetApp Files のリソース制限について説明しています。

|  リソース  |  既定の制限  |  サポート要求による調整の可否  |
|----------------|---------------------|--------------------------------------|
|  Azure リージョンあたりの NetApp アカウントの数   |  10    |  はい   |
|  NetApp アカウントあたりの容量プールの数   |    25     |   はい   |
|  サブスクリプションあたりのボリュームの数   |    500     |   はい   |
|  容量プールあたりのボリュームの数     |    500   |    はい     |
|  ボリュームあたりのスナップショット数       |    255     |    いいえ        |
|  Azure Virtual Network あたりの Azure NetApp Files (Microsoft.NetApp/volumes) に委任されたサブネットの数    |   1   |    いいえ    |
|  Azure NetApp Files を使用した VNet (直ちにピアリングされた VNet を含む) で使用されている IP の数   |    1000   |    いいえ   |
|  単一の容量プールの最小サイズ   |  4 TiB     |    いいえ  |
|  単一の容量プールの最大サイズ    |  500 TiB   |   いいえ   |
|  単一のボリュームの最小サイズ    |    100 GiB    |    いいえ    |
|  単一のボリュームの最大サイズ     |    100 TiB    |    いいえ    |
|  1 つのファイルの最大サイズ     |    16 TiB    |    いいえ    |    
|  1 つのディレクトリ内のディレクトリ メタデータの最大サイズ      |    320 MB    |    いいえ    |    
|  ボリュームあたりのファイルの最大数 ([maxfiles](#maxfiles))     |    1 億    |    はい    |    

詳細については、「[容量管理に関する FAQ](azure-netapp-files-faqs.md#capacity-management-faqs)」を参照してください。

## <a name="maxfiles-limits"></a>maxfiles の制限 <a name="maxfiles"></a> 

Azure NetApp Files ボリュームには、*maxfiles* という制限があります。 maxfiles の制限は、ボリュームに格納できるファイルの数です。 Azure NetApp Files ボリュームの maxfiles の制限には、ボリュームのサイズ (クォータ) に基づいてインデックスが作成されます。 ボリュームの maxfiles の制限は、プロビジョニングされたボリュームサイズの TiB ごとに 2000 万ファイルの割合で増減されます。 

サービスでは、プロビジョニングされたサイズに基づいて、ボリュームの maxfiles 制限が動的に調整されます。 たとえば、1 TiB のサイズで初期構成されたボリュームの maxfiles 制限は 2000 万です。 その後ボリュームのサイズを変更すると、以下の規則に基づいて、maxfiles の制限が自動的に再調整されます。 

|    ボリューム サイズ (クォータ)     |  maxfiles 制限の自動再調整    |
|----------------------------|-------------------|
|    1 TiB 未満                 |    2,000 万     |
|    1 TiB 以上、2 TiB 未満    |    4,000 万     |
|    2 TiB 以上、3 TiB 未満    |    6,000 万     |
|    3 TiB 以上、4 TiB 未満    |    8,000 万     |
|    4 TiB 以上                |    1 億    |

ボリュームに対して 4 TiB 以上のクォータを既に割り当てている場合は、[サポート リクエスト](#limit_increase)を開始して、maxfiles の制限を 1 億より大きい値に増やすことができます。

## <a name="request-limit-increase"></a>上限の引き上げを要求する<a name="limit_increase"></a> 

上記の表から調整可能な上限を引き上げるように、Azure サポート要求を作成できます。 

Azure portal ナビゲーション プレーンから: 

1. **[ヘルプとサポート]** をクリックします。
2. **[+ New support request]** (新しいサポート要求) をクリックします。
3. [基本] タブで次の情報を指定します。 
    1. 問題の種類: **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。
    2. [サブスクリプション]\:クォータの増加が必要なリソースのサブスクリプションを選択します。
    3. [クォータの種類]\: **[Storage:Azure NetApp Files limits]\(ストレージ: Azure NetApp Files の制限\)** を選択します。
    4. **次へ: 「解決方法」** を参照してください。
4. [詳細] タブで次の操作を実行します。
    1. [説明] ボックスで、対応するリソースの種類に対して、次の情報を指定します。

        |  リソース  |    親リソース      |    要求された新しい制限     |    クォータの引き上げの理由       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *サブスクリプション ID*   |  *要求された新しい最大**アカウント**数*    |  *どのシナリオやユース ケースが要求を求めたか?*  |
        |  プール    |  *サブスクリプション ID、アカウント URI*  |  *要求された新しい最大**プール**数*   |  *どのシナリオやユース ケースが要求を求めたか?*  |
        |  ボリューム  |  *サブスクリプション ID、アカウント URI、プール URI*   |  *要求された新しい最大**ボリューム**数*     |  *どのシナリオやユース ケースが要求を求めたか?*  |
        |  maxfiles  |  *サブスクリプション ID、アカウント URI、プール URI、ボリューム URI*   |  *要求された新しい最大 **maxfiles** 数*     |  *どのシナリオやユース ケースが要求を求めたか?*  |    

    2. 適切なサポート方法を指定し、契約情報を指定します。

    3. **[次へ: 確認および作成]** をクリックして要求を作成します。 


## <a name="next-steps"></a>次のステップ  

- [Azure NetApp Files のストレージ階層を理解する](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md)
