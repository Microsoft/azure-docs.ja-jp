---
title: Azure Virtual Machines を新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs
description: Azure Resource Manager を使用して、新しいリソース グループまたはサブスクリプションに仮想マシンを移動します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: tomfitz
ms.openlocfilehash: 7b9cce7ac367f42329e3198c75a7640a205d01fe
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035540"
---
# <a name="move-guidance-for-virtual-machines"></a>仮想マシンの移動に関するガイダンス

この記事では、現在サポートされていないシナリオと、バックアップを利用した仮想マシンを移行する手順について説明します。

## <a name="scenarios-not-supported"></a>サポートされていないシナリオ

次のシナリオはまだサポートされていません。

* 可用性ゾーン内のマネージド ディスクを別のサブスクリプションに移動することはできません。
* Standard SKU Load Balancer または Standard SKU パブリック IP を使用した仮想マシン スケール セットを移動することはできません。
* プランが添付された Marketplace リソースから作成された仮想マシンは、リソース グループまたはサブスクリプションの間で移動できません。 現在のサブスクリプションでの仮想マシンをプロビジョニング解除し、新しいサブスクリプションに再デプロイしてください。
* 既存の仮想ネットワーク内に仮想マシンがあるが、その仮想ネットワーク内のすべてのリソースを移動するつもりはない。
* 低優先度の仮想マシンと低優先度の仮想マシン スケール セットは、リソース グループまたはサブスクリプション間で移動することはできません。

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup を利用した仮想マシン

Azure Backup で構成された仮想マシンを移動するには、次の対処法を使用します。

* 仮想マシンの場所を探します。
* 名前付けパターン `AzureBackupRG_<location of your VM>_1` (たとえば、AzureBackupRG_westus2_1) を持つリソース グループを探します
* Azure portal の場合、「非表示の型」を確認します
* PowerShell の場合、`Get-AzResource -ResourceGroupName AzureBackupRG_<location of your VM>_1`コマンドレットを使用します
* CLI の場合、`az resource list -g AzureBackupRG_<location of your VM>_1`を使用します
* 名前付けパターン `AzureBackup_<name of your VM that you're trying to move>_###########` を持つタイプ `Microsoft.Compute/restorePointCollections` のリソースを探します
* このリソースを削除します。 この操作では、インスタント復旧ポイントのみが削除され、コンテナー内のバックアップされたデータは削除されません。
* 削除が完了した後に、コンテナーと仮想マシンをターゲット サブスクリプションに移動できます。 移動した後は、データの損失なしでバックアップを続行できます。
* バックアップのための Recovery Service コンテナーの移動については、「[Recovery Services の制限事項](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)」を参照してください。

## <a name="next-steps"></a>次の手順

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](../resource-group-move-resources.md)」を参照してください。
