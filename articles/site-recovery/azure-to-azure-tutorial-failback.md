---
title: Azure Site Recovery サービスを使用して Azure VM をプライマリ リージョンにフェールバックします。
description: Azure Site Recovery サービスを使用して Azure VM をプライマリ リージョンにフェールバックする方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 432c92bcfa8a2e0df26adf1516f5bdc9ee73d267
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502377"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Azure リージョン間で Azure VM をフェールバックする

[Azure Site Recovery](site-recovery-overview.md) サービスは、オンプレミスのマシンと Azure 仮想マシン (VM) のレプリケーション、フェールオーバー、およびフェールバックの管理と調整を行うことでディザスター リカバリー戦略に貢献します。

このチュートリアルでは、単一の Azure VM をフェールバックする方法について説明します。 フェールオーバー後、利用できるようになった時点でプライマリ リージョンにフェールバックする必要があります。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> 
> * セカンダリ リージョンの VM をフェールバックする。
> * プライマリ VM をセカンダリ リージョンに再保護する。
> 
> [!NOTE]
> 
> このチュートリアルでは、最小限のカスタマイズで、いくつかの VM をターゲット リージョンにフェールオーバーした後、ソース リージョンにフェールバックする方法を紹介しています。 さらに詳しい手順については、[Azure VM のハウツー ガイド](../virtual-machines/windows/index.yml)を確認してください。

## <a name="before-you-start"></a>開始する前に

* VM の状態が **[フェールオーバーがコミットされました]** になっていることを確認します。
* プライマリ リージョンが使用できることと、そこに新しいリソースを作成してアクセスできることを確認します。
* 再保護が有効になっていることを確認します。

## <a name="fail-back-to-the-primary-region"></a>プライマリ リージョンにフェールバックする

VM が再保護された後で、必要に応じてプライマリ リージョンにフェールバックすることができます。

1. コンテナーで、 **[レプリケートされたアイテム]** を選択してから、再保護された VM を選びます。

    ![Azure portal でのプライマリへのフェールバックを示すスクリーンショット。](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. **[レプリケートされたアイテム]** で、VM を選択してから **[フェールオーバー]** を選びます。
3. **[フェールオーバー]** で、フェールオーバーする復旧ポイントを選択します。
    - **最新 (既定値)** : Site Recovery サービスのすべてのデータを処理し、最短の復旧ポイントの目標 (RPO) を提供します。
    - **最後に処理があった時点**:Site Recovery によって処理された最新の復旧ポイントに VM を戻します。
    - **Custom**:特定の復旧ポイントにフェールオーバーします。 このオプションは、テスト フェールオーバーを実行するときに役立ちます。
4. Site Recovery でフェールオーバーをトリガーする前に DR リージョンの VM のシャットダウンを試行する場合は、 **[フェールオーバーを開始する前にマシンをシャットダウンします]** を選択します。 シャットダウンが失敗した場合でも、フェールオーバーは続行されます。 
5. **[ジョブ]** ページで、フェールオーバーの進行状況を確認します。
6. フェールオーバーが完了した後、VM にログインして検証します。 必要に応じて復旧ポイントを変更することができます。
7. フェールオーバーの検証後、 **[Commit the failover]\(フェールオーバーのコミット\)** を選択します。 コミットにより、利用可能なすべての復旧ポイントが削除されます。 以降、[復旧ポイントの変更] オプションは使用できなくなります。
8. VM がフェールオーバー後、フェールバックされたことを確認できます。

    ![プライマリ リージョンとセカンダリ リージョンの VM を示すスクリーンショット。](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> マネージド ディスクを使用しており、Site Recovery 拡張機能バージョン 9.28.x.x 以降を実行しているコンピューターの場合、[更新プログラム ロールアップ 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) の Site Recovery は、フェールバックが完了して VM が再保護された後にセカンダリ ディザスター リカバリー リージョン内のコンピューターをクリーンアップします。 セカンダリ リージョン内の VM や NIC を手動で削除する必要はありません。 アンマネージド ディスクを含む VM はクリーンアップされないことに注意してください。 フェールバックの後のレプリケーションを完全に無効にした場合、Site Recovery は VM や NIC に加えて、ディザスター リカバリー リージョン内のディスクをクリーンアップします。

## <a name="next-steps"></a>次のステップ

再保護フローの[詳細](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection)について学習します。
