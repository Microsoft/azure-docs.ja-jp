---
title: ポータルで SQL Server Windows VM を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Portal で Windows SQL Server 2017 仮想マシンを作成する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/11/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bbf127f105f0ef22a23f00541396c2ac66b0e5f9
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828358"
---
# <a name="quickstart-create-a-sql-server-2017-windows-virtual-machine-in-the-azure-portal"></a>クイック スタート:Azure ポータルで SQL Server 2017 Windows 仮想マシンを作成する

> [!div class="op_single_selector"]
> * [Windows](quickstart-sql-vm-create-portal.md)
> * [Linux](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

このクイック スタートでは、Azure ポータルで SQL Server 仮想マシンを作成する方法について説明しています。


  > [!TIP]
  > - このクイック スタートでは、SQL VM を迅速にプロビジョニングしてそこに接続する方法について説明します。 その他の SQL VM プロビジョニング オプションについては、[Azure Portal での Windows SQL Server VM のプロビジョニング ガイド](virtual-machines-windows-portal-sql-server-provision.md)を参照してください。
  > - SQL Server の仮想マシンに関するご質問については、[よくあるご質問](virtual-machines-windows-sql-server-iaas-faq.md)に関するページをご覧ください。

## <a id="subscription"></a>Azure サブスクリプションを取得する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a id="select"></a>SQL Server VM イメージを選択する

1. アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal の左側のメニューで **[Azure SQL]** を選択します。 **[Azure SQL]** が一覧にない場合は、 **[すべてのサービス]** を選択してから、検索ボックスに「*Azure SQL*」と入力します。
1. **[+ 追加]** を選択して、 **[Select SQL deployment option]\(SQL デプロイ オプションの選択\)** ページを開きます。 **[SQL 仮想マシン]** タイル上の **[詳細の表示]** を選択することで、追加情報を表示できます。
1. **Free SQL Server License: ドロップダウンから SQL Server 2017 Developer on Windows Server 2016** イメージを選択します。

   ![新規検索ウィンドウ](./media/quickstart-sql-vm-create-portal/select-sql-2017-vm-image.png)

1. **作成** を選択します。

   ![新規検索ウィンドウ](./media/quickstart-sql-vm-create-portal/create-sql-2017-vm-image.png)

## <a id="configure"></a>基本的な詳細を指定する

**[基本]** タブで次の情報を指定します。

1. **[プロジェクトの詳細]** セクションで Azure サブスクリプションを選択し、 **[新規作成]** を選択して新しいリソース グループを作成します。 名前として「_SQLVM-RG_」と入力します。

   ![Subscription](media/quickstart-sql-vm-create-portal/basics-project-details.png)

1. **[インスタンスの詳細]** で、以下の操作を行います。
    1. **[仮想マシン名]** に「_SQLVM_」と入力します。 
    1. **[リージョン]** で場所を選択します。 
    1. このクイック スタートでは、 **[可用性オプション]** の設定を _[インフラストラクチャ冗長は必要ありません]_ のままにしておきます。 可用性オプションの詳細については、[可用性](../../windows/availability.md)に関するページを参照してください。 
    1. **[イメージ]** の一覧で、_Free SQL Server License:SQL Server 2017 Developer on Windows Server 2016_ という名前のイメージを選択します。 
    1. 仮想マシンの **[サイズ]** で **[サイズの変更]** を選択し、 **[A2 Basic]** プランを選択します。 予期しない課金を防ぐために、利用を終了したリソースは必ずクリーンアップしてください。 

   ![インスタンスの詳細](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

1. **[管理者アカウント]** で、ユーザー名 (_azureuser_ など) とパスワードを指定します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。

   ![[Administrator account] (管理者アカウント)](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

1. **[受信ポートの規則]** で **[選択したポートを許可する]** を選択し、ドロップダウンから **[RDP (3389)]** を選択します。 

   ![受信ポートの規則](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)

## <a name="sql-server-settings"></a>SQL Server の設定

**[SQL Server の設定]** タブで、以下のオプションを構成します。

1. **[セキュリティとネットワーク]** で、 **[SQL の接続]** に _[パブリック (インターネット)]_ を選択します。また、パブリック シナリオでよく知られているポート番号が使用されることを避けるために、ポートを `1401` に変更します。 
1. **[SQL 認証]** で **[有効]** を選択します。 VM 用に構成したのと同じユーザー名と パスワードが SQL ログインに設定されます。 [**Azure Key Vault の統合**](virtual-machines-windows-ps-sql-keyvault.md)の既定の設定を使用します。 **ストレージ構成**は基本的な SQL Server VM イメージでは使用できません。ただし、他のイメージに使用可能なオプションに関する詳細情報を[ストレージ構成](virtual-machines-windows-sql-server-storage-configuration.md#new-vms)に関するページで見つけることができます。  

   ![SQL サーバーのセキュリティの設定](media/quickstart-sql-vm-create-portal/sql-server-settings.png)


1. 必要に応じて、他の設定を変更し、 **[確認および作成]** を選択します。 

   ![確認および作成](media/quickstart-sql-vm-create-portal/review-create.png)


## <a name="create-the-sql-server-vm"></a>SQL Server VM を作成する

**[確認および作成]** タブで概要を確認し、 **[作成]** を選択して、この VM に対して指定した SQL Server、リソース グループ、およびリソースを作成します。

Azure Portal でデプロイを監視できます。 画面の上部にある **[通知]** ボタンをクリックすると、デプロイの基本的な状態が表示されます。 デプロイには数分かかることがあります。 

## <a name="connect-to-sql-server"></a>SQL Server への接続

1. ポータルで、仮想マシンのプロパティの **[概要]** セクションにある、SQL Server VM の **[パブック IP アドレス]** を見つけます。

1. インターネットに接続された別のコンピューターで、[SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) を開きます。


1. **[サーバーへの接続]** または **[データベース エンジンへの接続]** ダイアログ ボックスで、 **[サーバー名]** の値を編集します。 VM のパブリック IP アドレスを入力します。 コンマを追加し、新しい VM を構成するときに指定したカスタム ポートの **1401** を追加します。 たとえば、「 `11.22.33.444,1401` 」のように入力します。

1. **[認証]** ボックスで、 **[SQL Server 認証]** を選択します。

1. **[ログイン]** ボックスに、有効な SQL ログインの名前を入力します。

1. **[パスワード]** ボックスに、ログインのパスワードを入力します。

1. **[接続]** を選択します。

    ![SSMS 接続](./media/quickstart-sql-vm-create-portal/ssms-connect.png)

## <a id="remotedesktop"></a>VM にリモート ログインする

リモート デスクトップを使用して SQL Server 仮想マシンに接続するには、次の手順に従います。

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

SQL Server 仮想マシンに接続した後は、SQL Server Management Studio を起動し、ローカル管理者の資格情報を使用して Windows 認証で接続できます。 SQL Server 認証を有効にした場合は、プロビジョニングの間に構成した SQL のログインとパスワードを使用して SQL 認証で接続することもできます。

マシンにアクセスすると、要件に基づいてマシンと SQL Server の設定を直接変更することができます。 たとえば、ファイアウォールの設定を構成したり、SQL Server の構成設定を変更したりできます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

SQL VM を継続的に実行する必要がない場合は、使用中でないときに停止することで、不要な料金の発生を回避できます。 仮想マシンに関連付けらているすべてのリソースは、関連付けられているリソース グループをポータルで削除することで完全に削除することもできます。 これを行うと仮想マシンも完全に削除されるため、このコマンドは注意して使用してください。 詳細については、「 [ポータルを使用した Azure リソースの管理](../../../azure-resource-manager/manage-resource-groups-portal.md)」を参照してください。


## <a name="next-steps"></a>次の手順

このクイック スタートでは、Azure portal で SQL Server 2017 仮想マシンを作成しました。 新しい SQL Server にデータを移行する方法の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [SQL VM にデータベースを移行する](virtual-machines-windows-migrate-sql.md)
