---
title: マネージド イメージからカスタム プールをプロビジョニングする - Azure Batch | Microsoft Docs
description: マネージド イメージ リソースから Batch プールを作成して、アプリケーション用のソフトウェアとデータを含むコンピューティング ノードをプロビジョニングします。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020150"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>マネージド イメージを使用して仮想マシンのプールを作成する

Batch プールの仮想マシン (VM) のカスタム イメージを作成するには、[Shared Image Gallery](batch-sig-images.md) または *マネージド イメージ* リソースのいずれかを使用できます。

> [!TIP]
> ほとんどの場合、Shared Image Gallery を使用してカスタム イメージを作成する必要があります。 Shared Image Gallery を使用すると、プールを迅速にプロビジョニングしたり、VM の数を増やしたり、VM のプロビジョニング時に信頼性を向上させたりすることができます。 詳細については、「[Shared Image Gallery を使用してカスタム プールを作成する](batch-sig-images.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- **マネージド イメージ リソース**。 カスタム イメージを使用して仮想マシンのプールを作成するには、Batch アカウントと同じ Azure サブスクリプションおよびリージョンに、マネージド イメージ リソースを配置または作成する必要があります。 イメージは、VM の OS ディスクと、それに接続されたデータ ディスク (後者はオプション) のスナップショットから作成する必要があります。 マネージド イメージを準備するための情報と手順については、次のセクションをご覧ください。
  - 作成する各プールには、一意のカスタム イメージを使用します。
  - Batch API を使用してイメージでプールを作成するには、イメージの**リソース ID** を指定します。形式は次のとおりです。`/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage` ポータルを使用するには、イメージの**名前**を使用します。  
  - マネージド イメージ リソースは、スケール アップするプールの有効期間を通じて存在する必要があり、プールを削除した後は削除することができます。

- **Azure Active Directory (AAD) 認証**。 Batch クライアント API では、AAD 認証を使用する必要があります。 AAD の Azure Batch のサポートについては、「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」に記載されています。

## <a name="prepare-a-custom-image"></a>カスタム イメージを準備する

Azure では、次のものからマネージド イメージを準備できます。

- Azure VM の OS とデータ ディスクのスナップショット
- マネージド ディスクで汎用化された Azure VM
- クラウドにアップロードされた、汎用化されたオンプレミス VHD

カスタム イメージを使って Batch プールを安定的にスケーリングするには、最初の方法 (VM のディスクのスナップショットを使用する) *のみ*を使用してマネージド イメージを作成することをお勧めします。 VM を準備し、スナップショットを作成し、スナップショットからイメージを作成する手順については、次の説明をご覧ください。

### <a name="prepare-a-vm"></a>VM を準備する

イメージ用に新しい VM を作成する場合は、Batch によってサポートされているファースト パーティ Azure Marketplace イメージをマネージド イメージのベース イメージとして使用します。 ファースト パーティのイメージのみを、基本イメージとして使用できます。 Azure Batch でサポートされている Azure Marketplace イメージ参照のフルリストを取得するには、[ノード エージェント SKU の一覧表示](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus)操作に関する記事をご覧ください。

> [!NOTE]
> 基本イメージとして追加のライセンスと購入条件のあるサード パーティのイメージを使用することはできません。 このような Marketplace イメージについては、[Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM または [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) VM のガイダンスを参照してください。

- VM がマネージド ディスクを使用して作成されていることを確認してください。 これは VM を作成するときの既定のストレージ設定です。
- VM には、Azure 拡張機能 (カスタム スクリプト拡張機能など) をインストールしないでください。 イメージにプレインストールされた拡張機能が含まれる場合、Azure で Batch プールのデプロイ時に問題が発生する可能性があります。
- 添付データ ディスクを含める場合は、それらを使用する VM 内からディスクを マウントおよびフォーマットする必要があります。
- 提供するベース OS イメージには、必ず既定の一時ドライブを使用するようにしてください。 現在、Batch ノード エージェントでは、既定の一時ドライブを使用する必要があります。
- VM が実行状態になったら、RDP (Windows の場合) または SSH (Linux の場合) を使用して VM に接続します。 必要なソフトウェアをインストールしたり、必要なデータをコピーしてください。  

### <a name="create-a-vm-snapshot"></a>VM スナップショットを作成する

スナップショットは、VHD の完全な読み取り専用コピーです。 VM の OS やデータ ディスクのスナップショットを作成するには、Azure portal またはコマンドライン ツールを使用できます。 スナップショットを作成するための手順とオプションについては、[Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) または [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) VM のガイドをご覧ください。

### <a name="create-an-image-from-one-or-more-snapshots"></a>1 つ以上のスナップショットからイメージを作成する

スナップショットからマネージド イメージを作成するには、[az image create](/cli/azure/image) コマンドなどの Azure コマンドライン ツールを使用します。 イメージを作成する際には、OS ディスクのスナップショットを指定し、必要に応じて 1 つ以上のデータ ディスク スナップショットを指定することができます。

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>ポータルでカスタム イメージからプールを作成する

カスタム イメージを保存し、そのリソース ID または名前を特定したら、そのイメージから Batch プールを作成します。 次の手順は、Azure Portal からプールを作成する方法を示しています。

> [!NOTE]
> Batch API のいずれかを使用してプールを作成する場合は、AAD 認証に使用する ID に、イメージ リソースへのアクセス許可が付与されていることを確認してください。 「[Batch サービスの認証に Active Directory を使用する](batch-aad-auth.md)」を参照してください。
>
> マネージド イメージのリソースは、プールの有効期間にわたって存在する必要があります。 基になるリソースが削除されると、プールはスケールできません。

1. Azure Portal の Batch アカウントに移動します。 このアカウントは、カスタム イメージを含むリソース グループと同じサブスクリプションおよびリージョン内にある必要があります。
2. 左側の **[設定]** ウィンドウで、 **[プール]** メニュー項目を選択します。
3. **[プール]** ウィンドウで、 **[追加]** コマンドを選択します。
4. **[プールの追加]** ウィンドウで、 **[イメージの種類]** ボックスの一覧の **[カスタム イメージ (Linux/Windows)]** を選択します。 **[カスタム VM イメージ]** ドロップダウンで、イメージ名 (リソース ID の短い形式) を選択します。
5. **[Publisher]\(パブリッシャー\)、[Offer]\(プラン\)、[SKU]** で、カスタム イメージに対して適切な値を選択します。
6. **[ノード サイズ]** 、 **[ターゲットの専用ノード数]** 、 **[低優先度ノード]** など、残りの必須の設定ほか、オプションの設定も必要に応じて指定します。

    たとえば、Microsoft Windows Server Datacenter 2016 のカスタム イメージの場合、 **[プールの追加]** ウィンドウは次のように表示されます。

    ![カスタム Windows イメージからプールを追加する](media/batch-custom-images/add-pool-custom-image.png)
  
既存のプールがカスタム イメージに基づいているかどうかを調べるには、 **[プール]** ウィンドウのリソースの概要セクションで **[オペレーティング システム]** プロパティを確認します。 プールがカスタム イメージから作成されている場合、値は **[カスタム VM イメージ]** に設定されます。

プールに関連付けられているすべてのカスタム イメージがプールの **[プロパティ]** ウィンドウに表示されます。

## <a name="considerations-for-large-pools"></a>サイズの大きなプールに関する考慮事項

カスタム イメージを使用して数百個以上の VM を含んだプールを作成する場合は、前述のガイダンスに従い、VM スナップショットから作成されたイメージを使用することが重要です。

次の考慮事項にも注意してください。

- **サイズ制限** - Batch では、カスタム イメージを使用する場合、2500 個の専用コンピューティング ノードか、1000 個の低優先ノードにプール サイズが制限されます。

  同じイメージ (または同じスナップショットに基づく複数のイメージ) を使用して複数のプールを作成する場合は、プール内のコンピューティング ノードの合計数が、上記の制限を超えないようにする必要があります。 1 つのイメージや基盤スナップショットを、複数のプールに使用することは推奨されません。

  制限は、[受信 NAT プール](pool-endpoint-configuration.md)を使ってプールを構成することで緩和される場合があります。

- **サイズ変更のタイムアウト** - プールに固定数のノードが含まれている (自動スケールではない) 場合は、プールの resizeTimeout プロパティの値を 20 ～ 30 分に増やしてください。 タイムアウト時間内にプールがターゲット サイズに達しなかった場合は、別の[サイズ変更操作](/rest/api/batchservice/pool/resize)を実行してください。

  プールに 300 個以上のコンピューティング ノードを含める場合は、ターゲット サイズに達するまでに、プールのサイズを複数回変更しなければならない場合あります。
  
[Shared Image Gallery](batch-sig-images.md) を使用すれば、より多くの共有イメージ レプリカに加えてカスタマイズされたイメージを使用して、より大きなプールを作成できます。 共有イメージを使用すると、プールが安定した状態になるまでにかかる時間は最大 25% 速くなり、VM のアイドル待機時間は最大で 30% 短くなります。

## <a name="considerations-for-using-packer"></a>Packer の使用に関する注意点

ユーザー サブスクリプション モードの Batch アカウントがある場合のみ、マネージド イメージ リソースをPacker で直接作成できます。 Batch サービス モードのアカウントでは、最初に VHD を作成し、次にマネージド イメージ リソースへ VHD をインポートする必要があります。 マネージド イメージ リソースを作成する手順は、プール割り当てモード (ユーザーのサブスクリプションまたは Batch サービス) によって異なります。

カスタム イメージを参照するすべてのプールの有効期間用に、マネージド イメージを作成するために使用するリソースが存在することを確認します。 これに失敗すると、プールの割り当てやサイズ変更に失敗する可能性があります。

イメージまたは基になるリソースが削除された場合は、`There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed` に似たエラーが発生します。 このようなエラーが発生した場合は、基になるリソースが削除されていないことを確認します。

Packer を使用して VM を作成する詳細については、「[Build a Linux image with Packer](../virtual-machines/linux/build-image-with-packer.md)」 (Packer で Linux イメージを作成) または「[Build a Windows image with Packer](../virtual-machines/windows/build-image-with-packer.md)」 (Packer で Windows イメージを作成) を参照してください。

## <a name="next-steps"></a>次のステップ

Batch の詳細な概要については、「[Batch を使って大規模な並列コンピューティング ソリューションを開発する](batch-api-basics.md)」を参照してください。
