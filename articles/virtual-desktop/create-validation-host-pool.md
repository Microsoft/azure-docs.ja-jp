---
title: Windows Virtual Desktop ホスト プール サービスの更新プログラム - Azure
description: 運用環境に更新プログラムを展開する前にサービスの更新プログラムを監視する検証ホスト プールを作成する方法。
author: Heidilohr
ms.topic: tutorial
ms.date: 03/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8eac40ad958a10b8c853304ee2be8b2dc27af1a2
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008714"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>チュートリアル:サービスの更新プログラムを検証するためのホスト プールを作成する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[この記事](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md)を参照してください。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 ホスト プールを運用環境にデプロイする前に、検証ホスト プールを作成することを強くお勧めします。 更新プログラムは最初に検証ホスト プールに適用されるので、運用環境に展開する前にサービスの更新プログラムを監視できます。 検証ホスト プールがない場合、運用環境でユーザーにダウンタイムをもたらす可能性のあるエラーを招く変更を検出できないことがあります。

アプリで最新の更新プログラムを確実に処理できるようにするには、検証ホスト プールを運用環境のホスト プールとできるだけ類似したものにする必要があります。 ユーザーは、運用環境のホスト プールに接続する場合と同じくらい頻繁に、検証ホスト プールに接続する必要があります。 ホスト プールでのテストを自動化している場合は、検証ホスト プールでの自動テストも含める必要があります。

[診断機能](diagnostics-role-service.md)または [Windows Virtual Desktop のトラブルシューティングの記事](troubleshoot-set-up-overview.md)を使用して、検証ホスト プールでの問題をデバッグすることができます。

>[!NOTE]
> 将来のすべての更新プログラムをテストするために、検証ホスト プールを所定の場所に置いておくことをお勧めします。

>[!IMPORTANT]
>Azure Resource Management 統合を使用した Windows Virtual Desktop は、現在、検証環境の有効化と無効化に問題があります。 問題が解決された時点でこの記事は更新される予定です。

## <a name="prerequisites"></a>前提条件

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールの設定](powershell-module.md)に関するページの手順に従って、PowerShell モジュールを設定し、Azure にサインインしてください。

## <a name="create-your-host-pool"></a>ホスト プールを作成する

ホスト プールは、これらのいずれかの記事の手順に従って作成できます。
- [チュートリアル:](create-host-pools-azure-marketplace.md)Azure Marketplace を使用してホスト プールを作成する
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>検証ホスト プールとしてホスト プールを定義する

次の PowerShell コマンドレットを実行して、新しいホスト プールを検証ホスト プールとして定義します。 角かっこで囲まれた値を、セッションに関連する値に置き換えます。

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

次の PowerShell コマンドレットを実行して、検証プロパティが設定されていることを確認します。 角かっこで囲まれた値を、セッションに関連する値に置き換えます。

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

コマンドレットの結果は、次の出力のようになります。

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="update-schedule"></a>更新スケジュール

サービスの更新は毎月行われます。 大きな問題がある場合は、より頻繁なペースで重要な更新プログラムが提供されます。

サービスの更新がある場合は、環境を検証するために、多少なりともサインインするユーザーを毎日確保してください。 [TechCommunity サイト](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true)には定期的にアクセスすることをお勧めします。WVDUPdate に関する投稿に注目して、サービスの更新に関する最新情報を把握してください。

## <a name="next-steps"></a>次のステップ

検証ホスト プールを作成したので、Azure Service Health を使用して Windows Virtual Desktop のデプロイを監視する方法を学習できます。

> [!div class="nextstepaction"]
> [サービス アラートを設定する](./set-up-service-alerts.md)
