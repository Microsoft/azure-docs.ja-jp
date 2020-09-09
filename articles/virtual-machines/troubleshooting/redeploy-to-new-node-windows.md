---
title: Azure での Windows 仮想マシンの再デプロイ | Microsoft Docs
description: RDP 接続の問題を軽減するために Azure で Windows 仮想マシンを再デプロイする方法。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074414"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>新しい Azure ノードへの Windows 仮想マシンの再デプロイ
Windows ベースの Azure 仮想マシン (VM) へのリモート デスクトップ (RDP) 接続またはアプリケーション アクセスに関するトラブルシューティングで問題が発生している場合、VM の再デプロイが有効な場合があります。 VM を再デプロイすると、Azure では VM がシャットダウンされ、Azure インフラストラクチャ内の新しいノードに VM が移動されてから、電源が再びオンにされて、すべての構成オプションと関連するリソースが保持されます。 この記事では、Azure PowerShell または Azure ポータルを使用して VM を再デプロイする方法について説明します。

> [!NOTE]
> VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられている動的 IP アドレスが更新されます。 


## <a name="using-azure-powershell"></a>Azure PowerShell の使用
マシンに最新の Azure PowerShell 1.x がインストールされていることを確認します。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/)」を参照してください。

次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM をデプロイします。

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>次のステップ
VM への接続に関する問題が発生した場合は、[RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)または[RDP の詳細なトラブルシューティング手順](detailed-troubleshoot-rdp.md)に関するページをご覧ください。 VM で実行されるアプリケーションにアクセスできない場合は、[アプリケーションの問題のトラブルシューティング](./troubleshoot-app-connection.md)に関するページもご覧ください。
