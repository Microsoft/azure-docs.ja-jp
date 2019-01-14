---
title: Azure PowerShell サンプル スクリプト - GitHub の継続的デプロイでアプリを作成する | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - GitHub からの継続的なデプロイで Web アプリを作成する
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 14444efd4083d39e8ceb4a47fc1ed7c37ea1c245
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586571"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>GitHub からの継続的なデプロイで Web アプリを作成する

このサンプル スクリプトでは、App Service で Web アプリを関連リソースと合わせて作成し、GitHub リポジトリからの継続的デプロイを設定します。 継続的なデプロイを使用しない GitHub でのデプロイについては、「[Web アプリを作成して GitHub からコードをデプロイする](powershell-deploy-github.md)」を参照してください。

必要に応じて、[Azure PowerShell ガイド](/powershell/azure/overview)の手順に従って Azure PowerShell をインストールし、`Connect-AzureRmAccount` を実行して、Azure との接続を作成します。 また、次のことを確認します。

- Azure との接続が、`az login` コマンドを使用して作成されている。
- アプリケーション コードが、自分が所有するパブリックまたはプライベートの GitHub リポジトリ内にある。
- [GitHub アカウントでアクセス トークンを作成](https://help.github.com/articles/creating-an-access-token-for-command-line-use/)している。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、Web アプリ、およびすべての関連リソースを削除できます。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | App Service プランを作成します。 |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Web アプリを作成します。 |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | リソース グループのリソースを変更します。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の Azure App Service Web Apps 用 Azure PowerShell サンプル スクリプトは、[Azure PowerShell サンプル](../samples-powershell.md)のページにあります。
