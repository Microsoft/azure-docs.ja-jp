---
title: Windows Virtual Desktop で Windows 7 仮想マシンをデプロイする - Azure
description: Windows Virtual Desktop で Windows 7 仮想マシンを構成してデプロイする方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
ms.openlocfilehash: f8aebee72105e4f3218e7ae5f867962d0ed8e23d
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367371"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Windows Virtual Desktop で Windows 7 仮想マシンをデプロイする

Windows Virtual Desktop で Windows 7 仮想マシン (VM) をデプロイするプロセスは、新しいバージョンの Windows を実行している VM の場合とは若干異なります。 このガイドでは、Windows 7 をデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、「[Create a host pool with PowerShell](create-host-pools-powershell.md)」 (PowerShell を使用してホスト プールを作成する) の手順に従って、ホスト プールを作成します。 その後、[Azure Marketplace でのホスト プールの作成](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group)に関する記述の手順に従って、1 人または複数のユーザーをデスクトップ アプリケーション グループに割り当てます。

## <a name="configure-a-windows-7-virtual-machine"></a>Windows 7 仮想マシンを構成する

前提条件を完了したら、Windows Virtual Desktop でデプロイするために Windows 7 VM を構成することができます。

Windows Virtual Desktop で Windows 7 VM を設定するには、次のようにします。

1. Azure portal にサインインし、Windows 7 Enterprise イメージを検索するか、独自のカスタマイズした Windows 7 Enterprise (x64) イメージをアップロードします。  
2. Windows 7 Enterprise をホスト オペレーティング システムとして使用する 1 つまたは複数の仮想マシンをデプロイします。 仮想マシンでリモート デスクトップ プロトコル (RDP) (TCP/3389 ポート) が許可されていることを確認します。
3. RDP を使用して Windows 7 Enterprise ホストに接続し、デプロイの構成時に定義した資格情報で認証します。 
4. RDP でのホストへの接続時に使用したアカウントを、"リモート デスクトップ ユーザー" グループに追加します。 これを行わないと、VM を Active Directory ドメインに参加させた後に接続できなくなる可能性があります。
5. VM の Windows Update に移動します。
6. 重要なカテゴリのすべての Windows Update をインストールします。
7. 省略可能なカテゴリのすべての Windows Update (言語パックを除く) をインストールします。 これにより、これらの手順を完了するために必要なリモート デスクトップ プロトコル 8.0 更新プログラム ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35393)) がインストールされます。
8. ローカル グループ ポリシー エディターを開き、 **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[リモート デスクトップ サービス]**  >  **[リモート デスクトップ セッション ホスト]**  >  **[リモート セッション環境]** の順に移動します。
9. リモート デスクトップ プロトコル 8.0 ポリシーを有効にします。
10. Active Directory ドメインにこの VM を参加させます。
11. 次のコマンドを実行して、仮想マシンを再起動します。
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. [こちら](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/)の指示に従って、登録トークンを取得します。
13. [Windows 7 用 Windows Virtual Desktop エージェントをダウンロードします](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm)。
14. [Windows 7 用 Windows Virtual Desktop エージェント マネージャーをダウンロードします](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3)。
15. Windows Virtual Desktop エージェント インストーラーを開き、指示に従います。 メッセージが表示されたら、手順 12 で作成した登録キーを指定します。
16. Windows Virtual Desktop インストーラーを開き、指示に従います。
17. 必要に応じて、TCP/3389 ポートをブロックし、リモート デスクトップ プロトコルで VM に直接アクセスできないようにします。

## <a name="next-steps"></a>次のステップ

これで、Windows Virtual Desktop のデプロイは使用できる状態になりました。 [最新バージョンの Windows Virtual Desktop クライアントをダウンロード](https://aka.ms/wvd/clients/windows)して、使い始めてください。

Windows Virtual Desktop での Windows 7 に関する既知の問題のリストとトラブルシューティングの手順については、「[Troubleshoot Windows 7 virtual machines in Windows Virtual Desktop](troubleshoot-windows-7-vm.md)」 (Windows Virtual Desktop で Windows 7 仮想マシンのトラブルシューティングを行う) のトラブルシューティングに関する記事を参照してください。
