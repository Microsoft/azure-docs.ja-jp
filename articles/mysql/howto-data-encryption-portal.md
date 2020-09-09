---
title: データの暗号化 - Azure portal - Azure Database for MySQL
description: Azure portal を使用して Azure Database for MySQL のデータ暗号化を設定し、管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 097e07f3f39fc647d311af5e4eface4a7c6b3675
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87498482"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure portal を使用した Azure Database for MySQL のデータ暗号化

Azure portal を使用して Azure Database for MySQL のデータ暗号化を設定し、管理する方法について説明します。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* Azure Key Vault で、カスタマー マネージド キーに使用する Key Vault とキーを作成します。
* カスタマー マネージド キーとして使用するには、キー コンテナーに次のプロパティが必要です。
  * [論理的な削除](../key-vault/general/soft-delete-overview.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* カスタマー マネージド キーとして使用するには、キーに次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * 取得、キーのラップ、キーのラップ解除の各操作を実行できる

## <a name="set-the-right-permissions-for-key-operations"></a>キー操作に対する適切なアクセス許可を設定する

1. Key Vault で、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加]** の順に選択します。

   ![[アクセス ポリシー] と [アクセスポリシーの追加] が強調表示されている Key Vault のスクリーンショット](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **[キーのアクセス許可]** を選択して、 **[取得]** 、 **[ラップ]** 、 **[ラップ解除]** 、および MySQL サーバーの名前である **[プリンシパル]** を選択します。 お使いのサーバー プリンシパルが既存のプリンシパルの一覧に見つからない場合は、登録する必要があります。 初めてデータの暗号化を設定しようとして失敗したときに、サーバー プリンシパルを登録するように求められます。

   ![アクセス ポリシーの概要](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **[保存]** を選択します。

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL のデータ暗号化を設定する

1. Azure Database for MySQL で、 **[データの暗号化]** を選択して、カスタマー マネージド キーをセットアップします。

   ![[データの暗号化] が強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. キー コンテナー とキーの組を選択するか、キー識別子を入力することができます。

   ![データの暗号化のオプションが強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **[保存]** を選択します。

4. 確実にすべてのファイル (一時ファイルを含む) が完全に暗号化されるように、サーバーを再起動します。

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>復元サーバーまたはレプリカ サーバーでのデータ暗号化の使用

Key Vault に格納されている顧客のマネージド キーで Azure Database for MySQL が暗号化された後、新しく作成されたサーバーのコピーも暗号化されます。 この新しいコピーは、ローカルまたは geo 復元操作を使用するか、レプリカ (ローカル/リージョン間) 操作を使用して作成できます。 そのため、暗号化された MySQL サーバーの場合は、次の手順を使用して、暗号化済みの復元されたサーバーを作成できます。

1. サーバーで、 **[概要]**  >  **[復元]** の順に選択します。

   ![[概要] と [復元] が強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   または、レプリケーションが有効なサーバーでは、 **[設定]** 見出しの下にある **[レプリケーション]** を選択します。

   ![[レプリケーション] が強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 復元操作が完了すると、作成された新しいサーバーは、プライマリ サーバーのキーで暗号化されます。 ただし、このサーバーの機能とオプションは無効になっており、サーバーにアクセスできません。 新しいサーバーの ID には、キー コンテナーへのアクセス許可がまだ付与されていないため、これによってデータ操作が抑止されます。

   ![[アクセス不可] 状態が強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. サーバーにアクセスできるようにするには、復元されたサーバーでキーを再検証します。 **[データの暗号化]**  >  **[キーの再検証]** の順に選択します。

   > [!NOTE]
   > 新しいサーバーのサービス プリンシパルにキー コンテナーへのアクセス権を付与する必要があるため、最初の再検証の試行は失敗します。 サービス プリンシパルを生成するには、 **[キーの再検証]** を選択します。これにより、エラーが表示されますが、サービス プリンシパルが生成されます。 その後、この記事で前述した[こちらの手順](#set-the-right-permissions-for-key-operations)を参照してください。

   ![再検証の手順が強調表示されている Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   新しいサーバーへのアクセス権をキー コンテナーに付与する必要があります。

4. サービス プリンシパルを登録した後、もう一度キーを再検証すると、サーバーの通常の機能が再開されます。

   ![復元された機能を示している Azure Database for MySQL のスクリーンショット](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、「[カスタマー マネージド キーを使用した Azure Database for MySQL のデータの暗号化](concepts-data-encryption-mysql.md)」を参照してください。
