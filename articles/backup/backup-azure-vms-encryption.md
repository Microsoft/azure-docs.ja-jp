---
title: 暗号化された Azure VM をバックアップおよび復元する
description: Azure Backup サービスを使用して、暗号化された Azure VM をどのようにバックアップおよび復元するかについて説明します。
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 3719f26211677a1b726d3c3dc1d217fb58db1fb2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77583176"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>暗号化された Azure VM をバックアップおよび復元する

この記事では、[Azure Backup](backup-overview.md) サービスを使用して、暗号化されたディスクを含む Windows または Linux の Azure 仮想マシン (VM) をバックアップおよび復元する方法について説明します。

開始する前に Azure Backup が Azure VM とやりとりするしくみの詳細について確認する場合は、次のリソースを参照してください。

- Azure VM のバックアップ アーキテクチャを[確認する](backup-architecture.md#architecture-built-in-azure-vm-backup)。
- Azure VM のバックアップと Azure Backup の拡張機能の[詳細を確認](backup-azure-vms-introduction.md)する

## <a name="encryption-support"></a>暗号化のサポート

Azure Backup では、その OS またはデータ ディスクを Azure Disk Encryption (ADE) で暗号化している Azure VM のバックアップをサポートしています。 ADE では、Windows VM の暗号化に BitLocker を使用し、Linux VM には dm-crypt 機能を使用します。 ADE は、ディスク暗号化キーとシークレットを管理できるように、Azure Key Vault と統合されています。 Key Vault のキーの暗号化キー (KEK) は、さらにセキュリティ層を追加するために使用できます。これにより、Key Vault に記述する前に暗号化のシークレットが暗号化されます。

Azure Backup では、次の表にまとめたように、Azure AD アプリの有無に関係なく、ADE を使用して Azure VM をバックアップして復元することができます。

**VM ディスクの種類** | **ADE (BEK/dm-crypt)** | **ADE と KEK**
--- | --- | ---
**アンマネージド** | はい | はい
**マネージド**  | はい | はい

- [ADE](../security/azure-security-disk-encryption-overview.md)、[Key Vault](../key-vault/key-vault-overview.md)、[KEK](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek/) に関する詳細を参照してください。
- Azure VM ディスクの暗号化については、[よくあるご質問](../security/azure-security-disk-encryption-faq.md)に関するページを参照してください。

### <a name="limitations"></a>制限事項

- 同じサブスクリプションとリージョン内で暗号化された VM をバックアップして復元することができます。
- Azure Backup では、スタンドアロン キーを使用して暗号化された VM がサポートされます。 VM を暗号化するために使用された証明書の一部であるキーは、現在サポートされていません。
- Recovery Services のバックアップ コンテナーとして、同じサブスクリプションとリージョン内で暗号化された VM をバックアップして復元することができます。
- 暗号化された VM は、ファイル/フォルダー レベルでは復旧できません。 ファイルとフォルダーを復元するには、VM 全体を復旧する必要があります。
- VM を復元する場合、暗号化された VM に[既存の VM を置き換える](backup-azure-arm-restore-vms.md#restore-options)オプションを使用することはできません。 このオプションは、暗号化されていないマネージド ディスクに対してのみサポートされています。

## <a name="before-you-start"></a>開始する前に

開始する前に、以下を行います。

1. ADE が有効にされた 1 つまたは複数の [Windows](../security/azure-security-disk-encryption-windows.md) または [Linux](../virtual-machines/linux/disk-encryption-overview.md) の VM があることを確認します。
2. Azure VM バックアップの[サポート マトリックスを確認](backup-support-matrix-iaas.md)します。
3. Recovery Services のバックアップ コンテナーをまだお持ちでない場合は、[作成](backup-azure-arm-vms-prepare.md#create-a-vault)します。
4. 既にバックアップが有効になっている VM に対して暗号化を有効にしている場合、中断せずにバックアップを継続できるように、Key Vault へのアクセス許可を Backup に提供する必要があるだけです。 これらのアクセス許可に関する[詳細](#provide-permissions)を参照してください。

さらに、状況によっては、いくつか行う必要があることがあります。

- **VM に VM エージェントをインストールする**: Azure Backup では、マシンで実行されている Azure VM エージェントに拡張機能をインストールすることで、Azure VM がバックアップされます。 VM が Azure Marketplace のイメージから作成されている場合は、エージェントがインストールされ、実行されます。 カスタム VM を作成する場合、またはオンプレミスのマシンを移行する場合は、[手動でのエージェントのインストール](backup-azure-arm-vms-prepare.md#install-the-vm-agent)が必要な場合があります。

## <a name="configure-a-backup-policy"></a>バックアップ ポリシーを構成する

1. Recovery Services のバックアップ コンテナーをまだ作成していない場合は、[こちらの手順](backup-azure-arm-vms-prepare.md#create-a-vault)に従ってください。
2. ポータルでコンテナーを開いて、 **[作業の開始]** セクションの **[バックアップ]** を選択します。

    ![バックアップ ブレード](./media/backup-azure-vms-encryption/select-backup.png)

3. **[バックアップの目標]**  >  **[ワークロードはどこで実行されていますか?]** で **[Azure]** を選択します。
4. **[バックアップの対象]** で、 **[仮想マシン]**  >  **[OK]** を選択します。

      ![シナリオ ブレード](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. **[バックアップ ポリシー]**  >  **[バックアップ ポリシーの選択]** で、コンテナーに関連付けるポリシーを選択します。 次に、 **[OK]** をクリックします
    - バックアップ ポリシーでは、バックアップが取得されるタイミングと、それらが保存される期間を指定します。
    - 既定のポリシーの詳細がドロップダウン メニューの下に一覧表示されます。

    ![Open Scenario blade](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 既定のポリシーを使用する必要がない場合は、 **[新規作成]** を選択して、[カスタム ポリシーを作成します](backup-azure-arm-vms-prepare.md#create-a-custom-policy)。

7. 選択したポリシーを使用してバックアップする暗号化された VM を選び、 **[OK]** を選択します。

      ![暗号化された VM の選択](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Azure Key Vault を使用する場合は、コンテナー ページ上で、Azure Backup に Key Vault 内のキーとシークレットへの読み取り専用のアクセス権が必要なことを示すメッセージが表示されます。

    - このメッセージを受け取った場合は、操作は必要ありません。

        ![アクセス OK](./media/backup-azure-vms-encryption/access-ok.png)

    - このメッセージを受け取った場合は、[以下の手順](#provide-permissions)に示されたように、アクセス許可を設定する必要があります。

        ![アクセスの警告](./media/backup-azure-vms-encryption/access-warning.png)

9. **[バックアップの有効化]** をクリックして、コンテナーにバックアップ ポリシーをデプロイし、選択した VM に対してバックアップを有効にします。

## <a name="trigger-a-backup-job"></a>バックアップ ジョブをトリガーする

初回バックアップはスケジュールに従って実行されますが、次のようにすぐに実行することもできます。

1. コンテナー メニューで **[バックアップ アイテム]** をクリックします。
2. **[バックアップ アイテム]** で、 **[Azure Virtual Machines]** をクリックします。
3. **[バックアップ アイテム]** の一覧で、省略記号 [...] をクリックします。
4. **[今すぐバックアップ]** をクリックします。
5. **[今すぐバックアップ]** で、カレンダー コントロールを使用して復旧ポイントを保持する最終日を選択します。 次に、 **[OK]** をクリックします
6. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 VM のサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

## <a name="provide-permissions"></a>アクセス許可を付与する

Azure VM では、キーとシークレット、および関連付けられた VM がバックアップするために、読み取り専用のアクセス権が必要です。

- ご利用の Key Vault は、Azure サブスクリプションの Azure AD テナントに関連付けられます。 **メンバー ユーザー**の場合は、さらに操作しなくても、Azure Backup によって Key Vault へのアクセス権が取得されます。
- **ゲスト ユーザー**の場合は、キー コンテナーにアクセスするために、Azure Backup にアクセス許可を提供する必要があります。

アクセス許可を設定するには

1. Azure portal で **[すべてのサービス]** を選択して、**キー コンテナー**を検索します。
2. バックアップをしている暗号化された VM と関連付けられたキー コンテナーを選択します。
3. **[アクセス ポリシー]**  > 、 **[新規追加]** の順に選択します。
4. **[プリンシパルの選択]** を選択して、「**バックアップ管理**」と入力します。
5. **[Backup Management Service]\(バックアップ管理サービス\)**  > 、 **[選択]** の順に選択します。

    ![Backup サービスの選択](./media/backup-azure-vms-encryption/select-backup-service.png)

6. **[アクセス ポリシーの追加]**  > 、 **[テンプレートからの構成 (省略可能)]** で、 **[Azure Backup]** を選択します。
    - **[キーのアクセス許可]** と **[シークレットのアクセス許可]** に、必要なアクセス許可が事前入力されます。
    - VM が **BEK のみ**を使用して暗号化される場合、シークレットのみを対象としたアクセス許可が必要なため、 **[キーのアクセス許可]** の選択を解除する必要があります。

    ![Azure Backup の選択](./media/backup-azure-vms-encryption/select-backup-template.png)

7. **[OK]** をクリックします。 **[Backup Management Service]\(バックアップ管理サービス\)** が、 **[アクセス ポリシー]** に追加されます。

    ![アクセス ポリシー](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. **[保存]** をクリックして、Azure Backup にアクセス許可を提供します。

## <a name="restore-an-encrypted-vm"></a>暗号化された VM を復元する

次のように暗号化された VM を復元します。

1. [VM ディスクを復元します](backup-azure-arm-restore-vms.md#restore-disks)。
2. 以下のいずれかを行って、仮想マシン インスタンスを再作成します。
    1. 復元操作の間に生成されるテンプレートを使用して VM の設定をカスタマイズし、VM のデプロイをトリガーします。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)。
    2. PowerShell を使用して、復元されたディスクから新しい VM を作成します。 [詳細については、こちらを参照してください](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)。
3. Linux VM の場合は、データ ディスクが開かれてマウントされるように、ADE 拡張機能を再インストールします。

## <a name="next-steps"></a>次のステップ

問題が発生した場合は、以下の記事を確認します。

- 暗号化された Azure VM をバックアップして復元するときの[一般的なエラー](backup-azure-vms-troubleshoot.md)
- [Azure VM エージェント/バックアップの拡張機能](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)に関する問題
