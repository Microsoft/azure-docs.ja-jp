---
title: Azure Cloud Shell の概要 | Microsoft Docs
description: Azure Cloud Shell について概説します。
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "75832465"
---
# <a name="overview-of-azure-cloud-shell"></a>Azure Cloud Shell の概要
Azure Cloud Shell は、Azure リソースを管理するための、ブラウザーでアクセスできる対話形式の認証されたシェルです。
Bash または PowerShell どちらかのシェル エクスペリエンスを作業方法に合わせて柔軟に選択できます。

下のボタンをクリックし、shell.azure.com からお試しください。

[![埋め込みの起動](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell を起動する")](https://shell.azure.com)

Azure Portal から Cloud Shell アイコンを使用して試してみる。

![ポータルの起動](media/overview/portal-launch-icon.png)

## <a name="features"></a>[機能]

### <a name="browser-based-shell-experience"></a>ブラウザーベースのシェル環境
Cloud Shell は、Azure の管理タスクを視野に入れて開発されたブラウザーベースのコマンド ライン環境です。
Cloud Shell を利用することで、ローカル コンピューターに拘束されることなくクラウドならではの作業が可能となります。

### <a name="choice-of-preferred-shell-experience"></a>最適なシェル エクスペリエンスの選択
ユーザーは、Bash または PowerShell のいずれかを選択できます。
1. **[Cloud Shell]** を選択します。

    ![[Cloud Shell] アイコン](media/overview/overview-cloudshell-icon.png)

2. **[Bash]** または **[PowerShell]** を選択します。

    ![[Bash] または [PowerShell] を選択する](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>認証済みかつ構成済みの Azure ワークステーション
Cloud Shell は Microsoft によって管理されており、広く使われているコマンド ライン ツールと言語サポートを提供しています。 さらに、Azure CLI または Azure PowerShell コマンドレットを介してすばやくリソースにアクセスできるよう、Cloud Shell では安全に自動認証が行われます。

Cloud Shell にインストールされているツールの詳細な一覧は[こちら](features.md#tools)からご覧いただけます。

### <a name="integrated-cloud-shell-editor"></a>統合 Cloud Shell エディター
Cloud Shell は、オープンソース Monaco Editor を基盤とする統合グラフィカル テキスト エディターを提供します。 `code .` を実行して構成ファイルを作成または編集するだけで、Azure CLI または Azure PowerShell を利用したシームレスなデプロイが可能になります。

[Cloud Shell エディターの詳細についてはこちらをご覧ください](using-cloud-shell-editor.md)。

### <a name="integrated-with-docsmicrosoftcom"></a>docs.microsoft.com のツールとの統合

Cloud Shell は、[docs.microsoft.com](https://docs.microsoft.com) でホストされているドキュメントから直接使用できます。 Cloud Shell は [Microsoft 学習ツール](https://docs.microsoft.com/learn/)、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)、[Azure CLI ドキュメント](https://docs.microsoft.com/cli/azure)で統合されています - コード スニペットで [使ってみる] ボタンをクリックすると、没入型のシェル エクスペリエンスが開きます。 

### <a name="multiple-access-points"></a>複数のアクセス ポイント
Cloud Shell は、以下から使用できる柔軟なツールです。
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI ドキュメント](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell ドキュメント](https://docs.microsoft.com/powershell/azure/overview)
* [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code の Azure Account 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Microsoft Azure Files ストレージの接続
Cloud Shell マシンは一時的ですが、ファイルは、ディスク イメージを使用する方法と、`clouddrive` という名前のマウントされたファイル共有を使用する方法の 2 つの方法で保持されます。  Cloud Shell の初回起動時に、リソース グループ、ストレージ アカウント、Azure Files 共有を作成するように求められます。 これは 1 回限りの作業であり、それ以降はすべてのセッションで自動的に接続されます。 1 つのファイル共有をマップすると、Cloud Shell の Bash と PowerShell の両方で使われます。

[新規または既存のストレージ アカウント](persisting-shell-storage.md)をマウントする方法または [Cloud Shell で使用される永続化メカニズム](persisting-shell-storage.md#how-cloud-shell-storage-works)に関する記事で詳細をご確認ください。

> [!NOTE]
> Cloud Shell ストレージ アカウントでは Azure Storage ファイアウォールはサポートされません。

## <a name="concepts"></a>概念
* Cloud Shell は、ユーザーごとにセッション単位で一時的に提供されるホスト上で実行されます。
* Cloud Shell は、無操作状態で 20 分経過するとタイムアウトとなります。
* Cloud Shell では、Azure ファイル共有がマウントされている必要があります
* Cloud Shell では、Bash と PowerShell に対して同じ Azure ファイル共有が使用されます
* Cloud Shell には、ユーザー アカウントごとに 1 台のマシンが割り当てられます。
* Cloud Shell はファイル共有に保持されている 5 GB のイメージを使用して $HOME を永続化します
* Bash では、標準の Linux ユーザーとしてアクセス許可が設定されます。

[Cloud Shell の Bash](features.md) と [Cloud Shell の PowerShell](features-powershell.md) の機能に関する詳細をご覧ください。

## <a name="pricing"></a>価格
Cloud Shell のホストとなるマシンは無料です。ただし、前提条件として Azure Files 共有をマウントする必要があります。 ストレージのコストは通常どおりに適用されます。

## <a name="next-steps"></a>次のステップ
[Cloud Shell の Bash のクイックスタート](quickstart.md) <br>
[Cloud Shell の PowerShell のクイックスタート](quickstart-powershell.md)
