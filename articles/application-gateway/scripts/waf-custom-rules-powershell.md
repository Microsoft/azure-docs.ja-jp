---
title: Azure PowerShell のサンプル スクリプト - WAF カスタム ルールの作成
description: Azure PowerShell のサンプル スクリプト - Web アプリケーション ファイアウォールのカスタム ルールの作成
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8bf19052c11301412a299c31a2cd73ed2f9ffc7d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079899"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Azure PowerShell を使用して Web アプリケーション ファイアウォール (WAF) のカスタム規則を作成する

このスクリプトは、カスタム ルールを使用する Application Gateway の Web アプリケーション ファイアウォールを作成します。 要求ヘッダーに User-Agent *evilbot* が含まれている場合、カスタム規則はトラフィックをブロックします。

## <a name="prerequisites"></a>前提条件

### <a name="azure-powershell-module"></a>Azure PowerShell モジュール

Azure PowerShell をローカルにインストールして使用する場合、このスクリプトでは Azure PowerShell モジュール バージョン 2.1.0 以降が必要になります。

1. バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。
2. Azure との接続を作成するには、`Connect-AzAccount` を実行します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループ、アプリケーション ゲートウェイ、すべての関連リソースを削除します。

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | サブネット構成を作成します。 |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | サブネット構成を使用して仮想ネットワークを作成します。 |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | アプリケーション ゲートウェイのパブリック IP アドレスを作成します。 |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | サブネットをアプリケーション ゲートウェイと関連付ける構成を作成します。 |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | アプリケーション ゲートウェイにパブリック IP アドレスを割り当てる構成を作成します。 |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | アプリケーション ゲートウェイにアクセスするために使用するポートを割り当てます。 |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | アプリケーション ゲートウェイのバックエンド プールを作成します。 |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | バックエンド プールの設定を構成します。 |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | リスナーを作成します。 |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | ルーティング規則を作成します。 |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | アプリケーション ゲートウェイの階層と容量を指定します。 |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | アプリケーション ゲートウェイを作成します。 |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|アプリケーション ゲートウェイに自動スケーリング構成を作成します。|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|ファイアウォールの条件用の一致変数を作成します。|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|カスタム ルール用の一致条件を作成します。|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|アプリケーション ゲートウェイのファイアウォール ポリシー用の新しいカスタム ルールを作成します。|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|アプリケーション ゲートウェイのファイアウォール ポリシーを作成します。|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|アプリケーション ゲートウェイの WAF 構成を作成します。|

## <a name="next-steps"></a>次のステップ

- WAF のカスタム ルールの詳細については、[Web アプリケーション ファイアウォールのカスタム ルール](../custom-waf-rules-overview.md)に関するページを参照してください。
- Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
- 追加のアプリケーション ゲートウェイの PowerShell スクリプトのサンプルは、[Azure Application Gateway のドキュメント](../powershell-samples.md)にあります。
