---
title: テンプレートを使用して Azure Firewall をデプロイする
description: テンプレートを使用して Azure Firewall をデプロイする
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 12/01/2018
ms.author: victorh
ms.openlocfilehash: 86fdbbacf3e8064afe0aaaaebea1d6ef6c25f9d4
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52865833"
---
# <a name="deploy-azure-firewall-using-a-template"></a>テンプレートを使用して Azure Firewall をデプロイする

[[Create AzureFirewall]\(AzureFirewall の作成\) サンドボックス セットアップ テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)では、ファイアウォールを使用してテスト ネットワーク環境が作成されます。 ネットワークには、次の 3 つのサブネットを含む 1 つの仮想ネットワーク (VNet) があります。*AzureFirewallSubnet*、*ServersSubnet*、*JumpboxSubnet*。 *ServersSubnet* と *JumpboxSubnet* には、それぞれ 1 つの 2 コア Windows Server 仮想マシンがあります。

ファイアウォールは、*AzureFirewallSubnet* サブネット内にあり、*www.microsoft.com* へのアクセスを許可する単一のルールを含むアプリケーション ルール コレクションを備えています。

ユーザー定義のルートは、ファイアウォール規則が適用されるファイアウォールを経由する *ServersSubnet* サブネットからのネットワーク トラフィックを指します。

Azure Firewall の詳細については、[Azure portal を使用した Azure Firewall のデプロイと構成](tutorial-firewall-deploy-portal.md)に関するページを参照してください。

## <a name="use-the-template-to-deploy-azure-firewall"></a>テンプレートを使用して Azure Firewall をデプロイする

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

**テンプレートを使用して Azure Firewall をインストールしてデプロイするには:** 

1. [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox) でテンプレートにアクセスします。
   
1. 概要を読み、デプロイする準備ができたら、**[Azure へのデプロイ]** を選択します。
   
1. 必要に応じて、Azure portal にサインインします。 

1. ポータルの **[Create a sandbox setup of AzureFirewall]\(AzureFirewall のサンド ボックス設定の作成\)** ページで、次の値を入力または選択します。
   
   - **リソース グループ**: **[新規作成]** を選択して、リソース グループの名前を入力し、**[OK]** を選択します。 
   - **仮想ネットワーク名**: 新しい VNet の名前を入力します。 
   - **管理ユーザー名**: 管理者ユーザー アカウントのユーザー名を入力します。
   - **管理パスワード**: 管理者パスワードを入力します。 
   
1. 使用条件を読み、**[上記の使用条件に同意する]** をオンにします。
   
1. **[購入]** を選択します。
   
   リソースの作成には数分かかります。 
   
1. ファイアウォールを使用して作成されたリソースを探索します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソースが必要なくなった場合は、PowerShell コマンド [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループ、ファイアウォール、およびすべての関連リソースを削除できます。 *MyResourceGroup* という名前のリソース グループを削除するには、次を実行します。 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name MyResourceGroup
```
引き続きファイアウォール監視チュートリアルに進む場合は、リソース グループとファイアウォールをまだ削除しないでください。 

## <a name="next-steps"></a>次の手順

次に、Azure Firewall のログを監視することができます。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Firewall のログを監視する](./tutorial-diagnostics.md)
