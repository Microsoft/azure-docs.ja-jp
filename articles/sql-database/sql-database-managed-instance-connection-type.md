---
title: Azure SQL Database マネージド インスタンスの接続の種類 | Microsoft Docs
description: マネージド インスタンスの接続の種類について学習します
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto
ms.date: 10/07/2019
ms.openlocfilehash: 4ebd81f9c04bd739f1c18f636094040882b5ce36
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001750"
---
# <a name="azure-sql-database-managed-instance-connection-types"></a>Azure SQL Database マネージド インスタンスの接続の種類

この記事では、接続の種類に応じて、クライアントがどのように Azure SQL Database マネージド インスタンスに接続するかについて説明します。 接続の種類を変更するためのスクリプト サンプルと、既定の接続設定の変更に関連する考慮事項を以下に示します。

## <a name="connection-types"></a>接続の種類

Azure SQL Database マネージド インスタンスでは、次の 2 種類の接続をサポートしています。

- **リダイレクト (推奨):** クライアントは、データベースをホストしているノードへの直接接続を確立します。 リダイレクトを使用して接続を有効にするには、ファイアウォールとネットワーク セキュリティ グループ (NSG) を開き、ポート 1433 と 11000 から 11999 でのアクセスを許可する必要があります。 パケットはデータベースに直接送信されるため、プロキシ経由のリダイレクトを使用すると、待機時間とスループットのパフォーマンスが向上します。
- **プロキシ (既定値):** このモードでは、すべての接続でプロキシ ゲートウェイ コンポーネントが使用されます。 接続を有効にするには、プライベート ネットワーク用のポート 1433、パブリック接続用のポート 3342 のみを開く必要があります。 このモードを選択すると、ワークロードの性質によっては待機時間が長くなり、スループットが低下する可能性があります。 最短の待機時間と最高のスループットを実現するために、プロキシ接続ポリシーを使用したリダイレクト接続ポリシーを強くお勧めします。

## <a name="redirect-connection-type"></a>リダイレクトの接続の種類

リダイレクトの接続の種類は、TCP セッションが SQL エンジンに対して確立された後、クライアント セッションがロード バランサーから仮想クラスター ノードの接続先の仮想 IP を取得することを意味します。 後続のパケットは、ゲートウェイを使用せずに、仮想クラスター ノードに直接送信されます。 次の図にこのトラフィックの流れを示します。

![redirect.png](media/sql-database-managed-instance-connection-types/redirect.png)

> [!IMPORTANT]
> 現在、リダイレクトの接続の種類は、プライベート エンドポイントに対してのみ機能します。 接続の種類の設定に関係なく、パブリック エンドポイント経由の接続はプロキシを介して行われます。

## <a name="proxy-connection-type"></a>プロキシの接続の種類

プロキシの接続の種類は、TCP セッションがゲートウェイを使用して確立され、すべての後続パケットがゲートウェイ経由で送信されることを意味します。 次の図にこのトラフィックの流れを示します。

![proxy.png](media/sql-database-managed-instance-connection-types/proxy.png)

## <a name="script-to-change-connection-type-settings-using-powershell"></a>PowerShell を使用して接続の種類の設定を変更するスクリプト

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次の PowerShell スクリプトでは、マネージド インスタンスの接続の種類をリダイレクトに変更する方法を示しています。

```powershell
Install-Module -Name Az
Import-Module Az.Accounts
Import-Module Az.Sql

Connect-AzAccount
# Get your SubscriptionId from the Get-AzSubscription command
Get-AzSubscription
# Use your SubscriptionId in place of {subscription-id} below
Select-AzSubscription -SubscriptionId {subscription-id}
# Replace {rg-name} with the resource group for your managed instance, and replace {mi-name} with the name of your managed instance
$mi = Get-AzSqlInstance -ResourceGroupName {rg-name} -Name {mi-name}
$mi = $mi | Set-AzSqlInstance -ProxyOverride "Redirect" -force
```

## <a name="next-steps"></a>次の手順

- [データベースをマネージド インスタンスに復元する](sql-database-managed-instance-get-started-restore.md)
- [マネージド インスタンスのパブリック エンドポイントの構成](sql-database-managed-instance-public-endpoint-configure.md)方法について学習する
- [マネージド インスタンスの接続アーキテクチャ](sql-database-managed-instance-connectivity-architecture.md)について学習する