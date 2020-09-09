---
title: データ暗号化のトラブルシューティング - Azure Database for PostgreSQL (単一サーバー)
description: Azure Database for PostgreSQL (単一サーバー) でデータ暗号化のトラブルシューティングを行う方法を学習する
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 02/13/2020
ms.openlocfilehash: ee0a1ebe483dd4719fd1a84fec37906329116eba
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86117901"
---
# <a name="troubleshoot-data-encryption-in-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (単一サーバー) でのデータ暗号化のトラブルシューティング

この記事では、カスタマー マネージド キーを使用してデータの暗号化を構成した場合に、Azure Database for PostgreSQL の単一サーバー展開で発生する可能性のある一般的な問題を特定し、解決する方法について説明します。

## <a name="introduction"></a>はじめに

Azure Key Vault でカスタマー マネージド キーを使用するようにデータ暗号化を構成する場合、サーバーにはキーへの継続的なアクセスが必要となります。 サーバーが Azure Key Vault のカスタマー マネージド キーにアクセスできなくなると、すべての接続が拒否され、該当するエラー メッセージが表示され、Azure portal でその状態が ***[アクセス不可]*** に変わります。

アクセスできない Azure Database for PostgreSQL サーバーが不要になった場合は、それを削除してコストを発生させないようにすることができます。 キー コンテナーへのアクセスが復元され、サーバーが使用できるようになるまで、サーバーに対する他のすべての操作は許可されません。 また、アクセスできないサーバーがカスタマー マネージド キーで暗号化されている場合は、そのサーバーで `Yes` (カスタマー マネージド) から `No` (サービス マネージド) にデータ暗号化オプションを変更することもできません。 サーバーに再度アクセスできるようにするには、キーを手動で再検証する必要があります。 このアクションは、カスタマー マネージド キーへのアクセス許可が取り消されている間、データを不正アクセスから保護するために必要です。

## <a name="common-errors-causing-server-to-become-inaccessible"></a>サーバーにアクセスできなくなる原因となる一般的なエラー

Azure Key Vault キーを使用するデータ暗号化に関するほとんどの問題は、次のような構成ミスにより発生します。

- キー コンテナーが使用できない、または存在しない。
  - キー コンテナーが誤って削除された。
  - 間欠的なネットワーク エラーのために、キー コンテナーを使用できない。

- キー コンテナーへのアクセス許可がない、またはキーが存在しない。
  - キーの有効期限が切れたか、誤って削除または無効化された。
- Azure Database for PostgreSQL インスタンスのマネージド ID が誤って削除された。
  - Azure Database for PostgreSQL インスタンスのマネージド ID の、キーのアクセス許可が不十分である。 たとえば、アクセス許可に取得、ラップ、ラップ解除が含まれていない。
  - Azure Database for PostgreSQL インスタンスに対するマネージド ID のアクセス許可が取り消されたか、削除された。

## <a name="identify-and-resolve-common-errors"></a>一般的なエラーの識別と解決

### <a name="errors-on-the-key-vault"></a>Key Vault でのエラー

#### <a name="disabled-key-vault"></a>無効な Key Vault

- `AzureKeyVaultKeyDisabledMessage`
- **説明**:Azure Key Vault キーが無効になっているため、サーバーで操作を完了できませんでした。

#### <a name="missing-key-vault-permissions"></a>Key Vault アクセス許可がない

- `AzureKeyVaultMissingPermissionsMessage`
- **説明**:サーバーに、Azure Key Vault に対して必要な取得、ラップ、およびラップ解除のアクセス許可がありません。 ID でサービス プリンシパルに欠落しているアクセス許可を付与します。

### <a name="mitigation"></a>対応策

- カスタマー マネージド キーが、キー コンテナーに存在することを確認します。
- キー コンテナーを識別した後、Azure portal でそのキー コンテナーに移動します。
- キー URI により、存在するキーが特定されていることを確認します。

## <a name="next-steps"></a>次のステップ

[Azure portal を使用して、Azure Database for PostgreSQL でカスタマー マネージド キーによるデータ暗号化を設定します](howto-data-encryption-portal.md)
