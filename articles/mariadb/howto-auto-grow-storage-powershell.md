---
title: ストレージの自動拡張 - Azure PowerShell - Azure Database for MariaDB
description: この記事では、PowerShell を使用して Azure Database for MariaDB のストレージの自動拡張を有効にする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d03a67fc8a8172573598662ad9770b28493e9a2
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497105"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-server-using-powershell"></a>PowerShell を使用した Azure Database for MariaDB サーバーのストレージの自動拡張

この記事では、ワークロードに影響を与えることなく拡張されるように Azure Database for MariaDB サーバーのストレージを構成する方法について説明します。

ストレージの自動拡張により、サーバーの[ストレージ制限に達する](/azure/mariadb/concepts-pricing-tiers#reaching-the-storage-limit)ことで読み取り専用になることを防ぎます。 プロビジョニングされたストレージが 100 GB 以上のサーバーでは、空き領域が10% を下回ると、サイズが 5 GB 増加します。 プロビジョニングされたストレージが 100 GB を超えるサーバーの場合、空き領域が 10 GB を下回ると、サイズが 5% 増加します。 [Azure Database for MariaDB 価格レベル](/azure/mariadb/concepts-pricing-tiers#storage)の [ストレージ] セクションで指定したストレージの最大制限が適用されます。

> [!IMPORTANT]
> ストレージはスケールアップのみ可能で、スケールダウンはできないことに注意してください。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

- ローカルにインストールされた [Az PowerShell モジュール](https://docs.microsoft.com/powershell/azure/install-az-ps)、またはブラウザーの [Azure Cloud Shell](https://shell.azure.com/)
- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Az.MariaDb PowerShell モジュールがプレビュー段階にある間は、次のコマンドを使用して、Az PowerShell モジュールとは別にインストールする必要があります: `Install-Module -Name Az.MariaDb -AllowPrerelease`。
> Az.MariaDb PowerShell モジュールは一般提供されると、将来の Az PowerShell モジュール リリースの一部となり、Azure Cloud Shell 内からネイティブに使用できるようになります。

PowerShell をローカルで使用する場合は、[Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用して Azure アカウントに接続します。

## <a name="enable-mariadb-server-storage-auto-grow"></a>MariaDB サーバーのストレージの自動拡張を有効にする

次のコマンドを使用して、既存のサーバー上でサーバーの自動拡張ストレージを有効にします。

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

次のコマンドを使用して、新しいサーバーの作成時にサーバーの自動拡張ストレージを有効にします。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [PowerShell を使用して Azure Database for MariaDB の読み取りレプリカを作成し、管理する方法](howto-read-replicas-powershell.md)
