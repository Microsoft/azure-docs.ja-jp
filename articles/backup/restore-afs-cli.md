---
title: Azure CLI を使用して Azure ファイル共有を復元する
description: Recovery Services コンテナー内のバックアップされた Azure ファイル共有を、Azure CLI を使用して復元する方法について説明します
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 63b2be2fe24c1274ed1581b7b849de578c978842
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931051"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Azure CLI を使用して Azure ファイル共有を復元する

Azure CLI では、Azure リソースを管理するための、Azure のコマンド ライン エクスペリエンスが提供されます。 これは、Azure リソースを使用するためのカスタム オートメーションを構築するための優れたツールです。 この記事では、Azure CLI を使用して、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) によって作成された復元ポイントからファイル共有全体または特定のファイルを復元する方法について説明します。 これらの手順は、[Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) または [Azure Portal](backup-afs.md) を介して実行することもできます。

この記事を最後まで読むと、Azure CLI を使用して以下の操作を実行する方法がわかります。

* バックアップされた Azure ファイル共有の復元ポイントを表示する。
* 完全な Azure ファイル共有を復元する。
* 個々のファイルまたはフォルダーを復元する。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.18 以降を使用する必要があります。 CLI のバージョンを調べるには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

この記事では、Azure Backup によってバックアップされた Azure ファイル共有が既にあることを前提としています。 ない場合は、「[CLI を使用して Azure ファイル共有をバックアップする](backup-afs-cli.md)」を参照して、ファイル共有のバックアップを構成してください。 この記事では、次のリソースを使用します。

| ファイル共有  | ストレージ アカウント | リージョン | 詳細                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *azurefiles*  | *afsaccount*      | EastUS | Azure Backup を使用してバックアップされた元のソース                 |
| *azurefiles1* | *afaccount1*      | EastUS | 別の場所への復旧に使用される復元先のソース |

似た構造のファイル共有を使用して、この記事で説明されているさまざまな種類の復元を試すことができます。

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Azure ファイル共有の復旧ポイントをフェッチする

[az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) コマンドレットを使用して、バックアップされたファイル共有のすべての復旧ポイントの一覧を表示します。

次の例では、*afsaccount* ストレージ アカウント内の *azurefiles* ファイル共有の復旧ポイントの一覧をフェッチします。

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --backup-management-type azurestorage --item-name “AzureFileShare;azurefiles” --workload-type azurefileshare --out table
```

また、次の 2 つの追加パラメーターを指定することにより、コンテナーと項目のフレンドリ名を使用して、前述のコマンドレットを実行することもできます。

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

結果セットは、各復元ポイントの日時と整合性の詳細が含まれる復旧ポイントの一覧です。

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

出力の **Name** 属性は、復旧操作で **--rp-name** パラメーターの値として使用できる復旧ポイント名に対応しています。

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Azure CLI を使用した完全な共有の復旧

この復元オプションを使用すると、元または別の場所に完全なファイル共有を復元できます。

復元操作を実行するには、次のパラメーターを定義します。

* **--container-name**:バックアップされた元のファイル共有がホストされていたストレージ アカウントの名前です。 コンテナーの名前またはフレンドリ名を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--item-name**:復元操作に使用するバックアップされた元のファイル共有の名前です。 バックアップ項目の名前またはフレンドリ名を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。

### <a name="restore-a-full-share-to-the-original-location"></a>完全な共有を元の場所に復元する

元の場所に復元する場合、ターゲット関連のパラメーターを指定する必要はありません。 **競合の解決**のみ指定する必要があります。

次の例では、復元モードを *originallocation* に設定して [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) コマンドレットを使用し、*azurefiles* ファイル共有を元の場所に復元します。 「[Azure ファイル共有の復旧ポイントをフェッチする](#fetch-recovery-points-for-the-azure-file-share)」で取得した復旧ポイント: 932883129628959823 を使用します。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

出力の **Name** 属性は、復元操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

### <a name="restore-a-full-share-to-an-alternate-location"></a>完全な共有を別の場所に復元する

このオプションを使用すると、ファイル共有を別の場所に復元し、元のファイル共有をそのままにすることができます。 別の場所への復旧には、次のパラメーターを指定します。

* **--target-storage-account**: バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **--target-file-share**: バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **--target-folder**: データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。
* **--resolve-conflict**: 復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

次の例では、復元モードを *alternatelocation* に設定して [az backup restore restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) を使用し、*afsaccount* ストレージ アカウントの *azurefiles* ファイル共有を、*afaccount1* ストレージ アカウントの *azurefiles1”* ファイル共有に復元します。

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

出力の **Name** 属性は、復元操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="item-level-recovery"></a>項目レベルの回復

この復元オプションを使用すると、個々のファイルまたはフォルダーを元または別の場所に復元できます。

復元操作を実行するには、次のパラメーターを定義します。

* **--container-name**:バックアップされた元のファイル共有がホストされていたストレージ アカウントの名前です。 コンテナーの名前またはフレンドリ名を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--item-name**:復元操作に使用するバックアップされた元のファイル共有の名前です。 バックアップ項目の名前またはフレンドリ名を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。

復旧する項目について、次のパラメーターを指定します。

* **SourceFilePath**:文字列で表わされた、ファイル共有内の復元するファイルの絶対パス。 このパスは、[az storage file download](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) または [az storage file show](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) CLI コマンドで使用するパスと同じです。
* **SourceFileType**:ディレクトリまたはファイルのどちらを選択するかを指定します。 **Directory** または **File** を指定できます。
* **ResolveConflict**:復元されたデータとの競合が発生した場合の指示。 **Overwrite** または **Skip** を指定できます。

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>個別のファイルまたはフォルダーを元の場所に復元する

特定のファイルまたはフォルダーを元の場所に復元するには、復元モードを *originallocation* に設定して [az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) コマンドレットを使用します。

次の例では、*RestoreTest.txt* ファイルを元の場所である *azurefiles* ファイル共有に復元します。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

出力の **Name** 属性は、復元操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>個別のファイルまたはフォルダーを別の場所に復元する

特定のファイルまたはフォルダーを別の場所に復元するには、復元モードを *alternatelocation* に設定して [az backup restore restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) コマンドレットを使用し、次のターゲット関連パラメーターを指定します。

* **--target-storage-account**: バックアップされたコンテンツの復元先となるストレージ アカウント。 このストレージ アカウントは、コンテナーと同じ場所にある必要があります。
* **--target-file-share**: バックアップされたコンテンツの復元先となるターゲット ストレージ アカウント内のファイル共有。
* **--target-folder**: データの復元先となるファイル共有の下のフォルダー。 バックアップされたコンテンツをルート フォルダーに復元する必要がある場合は、ターゲット フォルダーの値として空の文字列を指定します。

次の例では、元は *azurefiles* ファイル共有にあった *RestoreTest.txt* ファイルを、別の場所、つまり *afaccount1* ストレージ アカウントでホストされている *azurefiles1* ファイル共有内の *restoredata* フォルダーに復元します。

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

出力の **Name** 属性は、復元操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="next-steps"></a>次のステップ

[Azure CLI を使用して Azure ファイル共有のバックアップを管理する](manage-afs-backup-cli.md)方法を学習します。
