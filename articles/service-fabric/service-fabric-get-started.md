---
title: Azure マイクロサービスの Windows 開発環境のセットアップ | Microsoft Docs
description: ランタイム、SDK、およびツールをインストールし、ローカル開発クラスターを作成します。 このセットアップを完了すると、Windows でアプリケーションを構築する準備が整います。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: b94e2d2e-435c-474a-ae34-4adecd0e6f8f
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2018
ms.author: ryanwi
ms.openlocfilehash: 968c9f2455269cf41a701de685e4f47255b46847
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53386430"
---
# <a name="prepare-your-development-environment-on-windows"></a>Windows で開発環境を準備する
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
> 
> 

Windows 開発コンピューターで [Azure Service Fabric アプリケーション][1]をビルドして実行するには、Service Fabric ランタイム、SDK、およびツールをインストールしてください。 また、SDK に含まれる [Windows PowerShell スクリプトの実行を有効にする](#enable-powershell-script-execution)必要があります。

## <a name="prerequisites"></a>前提条件
### <a name="supported-operating-system-versions"></a>サポートされるオペレーティング システムのバージョン
開発では、次のオペレーティング システムのバージョンがサポートされます。

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 のサポート:
> - Windows 7 には、既定では Windows PowerShell 2.0 のみが含まれます。 Service Fabric PowerShell のコマンドレットには PowerShell 3.0 以降が必要です。 Microsoft ダウンロード センターから [Windows PowerShell 5.0 をダウンロード][powershell5-download]できます。
> - Service Fabric リバース プロキシは、Windows 7 で使用できません。
>

## <a name="install-the-sdk-and-tools"></a>SDK とツールのインストール
SDK とツールをインストールするには、Web Platform Installer (Web PI) を使用することをお勧めします。 Web PI を使用してランタイム エラーが発生した場合は、特定の Service Fabric リリースのリリース ノートで、インストーラーへの直接リンクを検索することもできます。 リリース ノートは、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)の各種リリースの発表で見つけることができます。

### <a name="to-use-visual-studio-2017"></a>Visual Studio 2017 を使用するには
Service Fabric ツールは、Visual Studio 2017 の Azure 開発ワークロードに含まれています。 このワークロードを Visual Studio のインストールの一環として有効にします。
さらに、Web Platform Installer を使用して Microsoft Azure Service Fabric SDK とランタイムをインストールする必要があります。

* [Microsoft Azure Service Fabric SDK のインストール][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Visual Studio 2015 を使用するには (Visual Studio 2015 Update 2 以降が必要)
Visual Studio 2015 では、Service Fabric ツールは、Web Platform Installer を使用して SDK やランタイムと共にインストールされます。

* [Microsoft Azure Service Fabric SDK とツールのインストール][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>SDK のみのインストール
SDK のみが必要な場合は、次のパッケージをインストールすることができます。
* [Microsoft Azure Service Fabric SDK のインストール][core-sdk]

現在のバージョンは次のとおりです。
* Service Fabric SDK および Tools 3.3.617
* Service Fabric ランタイム 6.4.617
* Service Fabric Tools for Visual Studio 2015 2.4.11116.1
* Visual Studio 2017 15.9 には、Service Fabric Tools for Visual Studio 2.4.11024.1 が含まれています 

サポートされているバージョンの一覧については、[Service Fabric のサポート](service-fabric-support.md)に関するページを参照してください。

> [!NOTE]
> アプリケーションまたはクラスターのアップグレードには、1 台のマシンのクラスター (OneBox) はサポートされていません。クラスターのアップグレードを実行する必要がある場合や、アプリケーションのアップグレードの実行で問題がある場合は、OneBox クラスターを削除し再作成します。 

## <a name="enable-powershell-script-execution"></a>PowerShell スクリプトの実行の有効化
Service Fabric は、ローカル開発クラスターの作成、および Visual Studio からのアプリケーションのデプロイに、Windows PowerShell スクリプトを使用します。 既定では、Windows はこれらのスクリプトの実行をブロックします。 これらを有効にするには、PowerShell 実行ポリシーを変更する必要があります。 管理者として PowerShell を開き、次のコマンドを入力します。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```
## <a name="install-docker-optional"></a>Docker のインストール (省略可能)
Service Fabric は、マシン クラスター全体にマイクロサービスをデプロイする[コンテナー オーケストレーター](service-fabric-containers-overview.md)です。 ローカル開発クラスターで Windows コンテナー アプリケーションを実行するには、まず Docker for Windows をインストールする必要があります。 [Docker CE for Windows (安定版)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description) を入手します。 Docker をインストールして起動したら、トレイ アイコンを右クリックし、**[Switch to Windows containers]\(Windows コンテナーに切り替える\)** を選択します。 この手順は、Windows に基づいて Docker イメージを実行するために必要です。

## <a name="next-steps"></a>次の手順
開発環境のセットアップが完了したので、アプリのビルドと実行を開始してください。

* [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)
* [ローカル クラスター上でアプリケーションをデプロイし管理する方法](service-fabric-get-started-with-a-local-cluster.md)
* [プログラミング モデル Reliable Services と Reliable Actors について学びます](service-fabric-choose-framework.md)
* [GitHub での Service Fabric コード サンプルの確認](https://aka.ms/servicefabricsamples)
* [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Service Fabric キャンペーン ページ"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI のリンク"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI のリンク"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI のリンク"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
