---
title: VM の設定から Azure VM をバックアップする
description: この記事では、Azure Backup サービスを使用して単一の Azure VM または複数の Azure VM をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: a8d1c29c894663da76b5882ef7ba249356ba3e6d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87531848"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>VM の設定から Azure VM をバックアップする

この記事では、[Azure Backup](backup-overview.md) サービスを使用して Azure VM をバックアップする方法について説明します。 いくつかの方法を使用して Azure VM をバックアップすることができます。

- 1 つの Azure VM:この記事の手順では、VM の設定から直接 Azure VM をバックアップする方法について説明します。
- 複数の Azure VM:Recovery Services コンテナーを設定して、複数の Azure VM のバックアップを構成することができます。 このシナリオについては、[こちらの記事](backup-azure-arm-vms-prepare.md)の説明に従ってください。

## <a name="before-you-start"></a>開始する前に

1. バックアップのしくみを[学習](backup-architecture.md#how-does-azure-backup-work)し、サポート要件を[確認](backup-support-matrix.md#azure-vm-backup-support)します。
2. Azure VM バックアップの[概要を理解](backup-azure-vms-introduction.md)します。

### <a name="azure-vm-agent-installation"></a>Azure VM エージェントのインストール

Azure VM をバックアップするため、Azure Backup によって、マシンで実行されている VM エージェントに拡張機能がインストールされます。 VM が Azure Marketplace のイメージから作成されている場合は、エージェントが実行されます。 カスタム VM を作成する場合や、オンプレミスからマシンを移行する場合など、時として、エージェントを手動でインストールする必要がある場合があります。

- VM エージェントを手動でインストールする必要がある場合は、[Windows](../virtual-machines/extensions/agent-windows.md) VM または [Linux](../virtual-machines/extensions/agent-linux.md) VM の指示に従ってください。
- エージェントがインストールされた後、バックアップを有効にすると、Azure Backup によってエージェントにバックアップ拡張機能がインストールされます。 ユーザーが何もしなくても、拡張機能に更新プログラムと修正プログラムが適用されます。

## <a name="back-up-from-azure-vm-settings"></a>Azure VM の設定からバックアップする

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]** を選択し、[フィルター] に「**仮想マシン**」と入力してから、 **[仮想マシン]** を選択します。
3. VM の一覧から、バックアップする VM を選択します。
4. VM メニューで、 **[バックアップ]** を選択します。
5. **[Recovery Services コンテナー]** で次のようにします。
   - コンテナーが既にある場合は、 **[既存のものを選択]** を選択し、コンテナーを選択します。
   - コンテナーがない場合は、 **[新規作成]** を選択します。 コンテナーの名前を指定します。 VM と同じリージョンおよびリソース グループに作成されます。 VM の設定からの直接バックアップを有効にするときは、これらの設定を変更できません。

        ![バックアップの有効化ウィザード](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. **[バックアップ ポリシーの選択]** で、次のいずれかを行います。

   - 既定のポリシーをそのままにします。 これにより、1 日に 1 回指定時刻に VM がバックアップされ、30 日間コンテナーに保持されます。
   - 既存のバックアップ ポリシーがある場合は、それを選択します。
   - 新しいポリシーを作成し、ポリシーの設定を定義します。  

       ![Select backup policy](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. **[バックアップの有効化]** を選択します。 これにより、バックアップ ポリシーと VM が関連付けられます。

    ![[バックアップの有効化] ボタン](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. ポータルの通知で構成の進行状況を追跡できます。
9. ジョブが完了したら、VM のメニューで **[バックアップ]** を選択します。 ページに、VM のバックアップの状態、復旧ポイントに関する情報、実行されているジョブ、発行されたアラートが表示されます。

   ![バックアップの状態](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. バックアップを有効にした後は、初回バックアップが実行されます。 初回バックアップは、すぐに開始することも、バックアップ スケジュールに従って開始されるまで待つこともできます。
    - 初回バックアップが完了するまで、 **[前回のバックアップの状態]** には **[警告 (初回のバックアップが保留中)]** と表示されます。
    - 次のスケジュールされたバックアップがいつ実行されるかを確認するには、バックアップ ポリシー名を選択します。

## <a name="run-a-backup-immediately"></a>バックアップをすぐに実行する

1. バックアップをすぐに実行するには、VM のメニューで、 **[バックアップ]**  >  **[今すぐバックアップ]** を選択します。

    ![バックアップを実行する](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. **[今すぐバックアップ]** では、カレンダー コントロールを使用して、復旧ポイントをいつまで保持するかを選択し、 **[OK]** をクリックします。

    ![バックアップ リテンション期間の期限](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. ポータルの通知を使用すると、バックアップ ジョブがトリガーされたことを知ることができます。 バックアップの進行状況を監視するには、 **[すべてのジョブの表示]** を選択します。

## <a name="back-up-from-the-recovery-services-vault"></a>Recovery Services コンテナーからバックアップする

この記事の手順に従って、Azure Backup Recovery Services コンテナーを設定することによって Azure VM のバックアップを有効にし、コンテナーでバックアップを有効にします。

## <a name="next-steps"></a>次のステップ

- この記事のいずれかの手順で問題がある場合は、[トラブルシューティング ガイド](backup-azure-vms-troubleshoot.md)に関する記事をご覧ください。
- バックアップの管理について[学習してください](backup-azure-manage-vms.md)。
