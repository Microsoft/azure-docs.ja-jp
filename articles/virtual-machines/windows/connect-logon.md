---
title: Windows Server VM に接続する | Microsoft Docs
description: Azure portal と Resource Manager デプロイ モデルを使用して Windows VM に接続し、サインオンする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ef62b02e-bf35-468d-b4c3-71b63fe7f409
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: cynthn
ms.openlocfilehash: 136b9141ccccfedf8d37fa0832b0673495d82417
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2019
ms.locfileid: "58846559"
---
# <a name="how-to-connect-and-sign-on-to-an-azure-virtual-machine-running-windows"></a>Windows が実行されている Azure 仮想マシンに接続してサインオンする方法
Azure Portal の **[接続]** ボタンを使用して、Windows デスクトップからリモート デスクトップ (RDP) セッションを開始します。 まず、仮想マシンに接続して、サインオンします。

Mac から Windows VM に接続するには、[Microsoft リモート デスクトップ](https://aka.ms/rdmac)など、Mac 用の RDP クライアントをインストールする必要があります。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続
1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のメニューで **[Virtual Machines]** を選択します。
3. 一覧から仮想マシンを選択します。
4. 仮想マシンのページの上部にある **[接続]** を選択します。
2. **[仮想マシンへの接続]** ページで、適切な IP アドレスとポートを選択します。 ほとんどの場合、既定の IP アドレスとポートを使用する必要があります。 **[RDP ファイルのダウンロード]** を選択します。 VM に Just-In-Time ポリシーが設定されている場合は、RDP ファイルをダウンロードする前に、まず **[Request access] (アクセス権の要求)** ボタンをクリックしてアクセス権を要求する必要があります。 Just-In-Time ポリシーの詳細については、[Just in Time ポリシーを使用した仮想マシン アクセスの管理](../../security-center/security-center-just-in-time.md)に関するページを参照してください。
2. ダウンロードした RDP ファイルを開き、プロンプトが表示されたら **[接続]** を選択します。 
2. `.rdp` ファイルの発行元が不明であることを示す警告が表示されます。 これは予期されることです。 **[リモート デスクトップ接続]** ウィンドウで **[接続]** を選択して続行します。
   
    ![Screenshot of a warning about an unknown publisher.](./media/connect-logon/rdp-warn.png)
3. **[Windows セキュリティ]** ウィンドウで、**[その他]**、**[別のアカウントを使用する]** の順に選択します。 仮想マシンのアカウントの資格情報を入力し、**[OK]** を選択します。
   
     **ローカル アカウント**:通常は、仮想マシンの作成時に指定したローカル アカウントのユーザー名とパスワードです。 この場合、ドメインは仮想マシンの名前です。これを *vmname*&#92;*username* の形式で入力します。  
   
    **ドメインに参加している VM**:VM がドメインに属している場合は、*Domain*&#92;*Username* の形式でユーザー名を入力します。 また、アカウントは管理者グループに属しているか、VM へのリモート アクセス特権が付与されている必要があります。
   
    **ドメイン コントローラー**:VM がドメイン コントローラーである場合は、そのドメインのドメイン管理者アカウントのユーザー名とパスワードを入力します。
4. **[はい]** を選択して、目的の仮想マシンであることを確認し、ログオンを完了します。
   
   ![VM の ID の検証に関するメッセージが表示されているスクリーンショット。](./media/connect-logon/cert-warning.png)


   > [!TIP]
   > ポータルの **[接続]** ボタンが淡色表示され、[Express Route](../../expressroute/expressroute-introduction.md) や[サイト間 VPN 接続](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)で Azure に接続されていない場合は、VM を作成してパブリック IP アドレスを割り当ててから RDP を使用する必要があります。 詳細については、[Azure のパブリック IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)に関するページを参照してください。
   > 
   > 

## <a name="connect-to-the-virtual-machine-using-powershell"></a>PowerShell を使用して仮想マシンに接続する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell を使用していて、Aure PowerShell モジュールがインストールされている場合は、`Get-AzRemoteDesktopFile` コマンドレットを使用して接続することもできます。次に例を示します。

この例のコマンドを実行すると、RDP 接続が直ちに起動され、上記のようなプロンプトが表示されます。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -Launch
```

今後の使用のために、RDP ファイルを保存することもできます。

```powershell
Get-AzRemoteDesktopFile -ResourceGroupName "RgName" -Name "VmName" -LocalPath "C:\Path\to\folder"
```

## <a name="next-steps"></a>次の手順
接続に問題がある場合は、[リモート デスクトップ接続に関するトラブルシューティング](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のページを参照してください。 

