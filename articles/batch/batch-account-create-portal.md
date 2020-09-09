---
title: Azure portal でアカウントを作成する
description: Azure ポータルで、クラウド内で大規模な並列ワークロードを実行する Azure Batch アカウントを作成する方法について説明します
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711342"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal で Batch アカウントを作成する

このトピックでは、[Azure portal](https://portal.azure.com) で Azure Batch アカウントを作成し、自分のコンピューティング シナリオに適したアカウント プロパティを選ぶ方法について説明します。 また、アクセス キーやアカウント URL のような重要なアカウント プロパティを確認できる場所も紹介します。

Batch アカウントとシナリオの背景については、[Batch サービスのワークフローとリソース](batch-service-workflow-features.md)に関する記事を参照してください。

## <a name="create-a-batch-account"></a>Batch アカウントを作成する

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]** を選択し、 **[コンピューティング]** と **[Batch サービス]** を選択します。

1. **[新しい Batch アカウント]** 設定を入力します。 次の詳細を確認してください。

    ![Batch アカウントを作成する][account_portal]

    a. **サブスクリプション**:Batch アカウントを作成するサブスクリプション。 サブスクリプションが 1 つのみの場合は、既定でそのサブスクリプションが選択されます。

    b. **[リソース グループ]** :新しい Batch アカウント用の既存のリソース グループを選択します。必要に応じて、新しく作成することもできます。

    c. **アカウント名**:選択する名前は、アカウントが作成される Azure リージョン内で一意である必要があります (下の **[場所]** を参照)。 アカウント名に含めることができるのは、英小文字と数字のみで、文字数は 3 ～ 24 文字にする必要があります。

    d. **[場所]** :Batch アカウントを作成する Azure リージョン。 サブスクリプションとリソース グループでサポートされているリージョンのみがオプションとして表示されます。

    e. **ストレージ アカウント**: Batch アカウントに関連付ける省略可能な Azure ストレージ アカウント。 最適なパフォーマンスのために汎用 v2 ストレージ アカウントをお勧めします。 Batch のすべてのストレージ アカウント オプションについては、[Batch 機能の概要](accounts.md#azure-storage-accounts)に関するページを参照してください。 ポータルで、既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。

      ![ストレージ アカウントの作成][storage_account]

    f. **プール割り当てモード**: **[詳細]** 設定タブでは、プール割り当てモードに **[Batch サービス]** または **[ユーザー サブスクリプション]** を指定できます。 ほとんどのシナリオで、既定の **[Batch サービス]** をそのまま使用してください。

      ![Batch のプール割り当てモード][pool_allocation]

1. **[作成]** を選択して、アカウントを作成します。

## <a name="view-batch-account-properties"></a>Batch アカウントのプロパティを表示する

アカウントが作成されたら、そのアカウントを選択して設定とプロパティにアクセスします。 左側のメニューを使用すると、アカウントの設定およびプロパティすべてにアクセスできます。

> [!NOTE]
> Batch アカウントの名前は ID であり、変更することはできません。 Batch アカウントの名前を変更する必要がある場合は、アカウントを削除し、目的の名前で新しいアカウントを作成する必要があります。

![Azure Portal の [Batch アカウント] ブレード][account_blade]

* **Batch アカウント名、URL、およびキー**:[Batch API](batch-apis-tools.md#azure-accounts-for-batch-development) を使用してアプリケーションを開発する場合は、Batch リソースにアクセスするためにアカウントの URL とキーが必要です (Batch では Azure Active Directory 認証もサポートされます)。

    Batch アカウント アクセス情報を表示するには、 **[キー]** を選択します。

    ![Batch account keys in Azure portal][account_keys]

* Batch アカウントに関連付けられているストレージ アカウントの名前とキーを表示するには、 **[ストレージ アカウント]** を選択します。

* Batch アカウントに適用されるリソース クォータを表示するには、 **[クォータ]** を選択します。 詳細については、「[Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## <a name="additional-configuration-for-user-subscription-mode"></a>ユーザー サブスクリプション モードのための追加構成

ユーザー サブスクリプション モードで Batch アカウントを作成することを選択した場合は、アカウントを作成する前に別途、次の手順を実行してください。

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch によるサブスクリプションへのアクセスを許可する (1 回限りの操作)

ユーザー サブスクリプション モードで Batch アカウントを初めて作成する場合は、Batch にサブスクリプションを登録する必要があります (既にこれを行っている場合は、次のセクションに進んでください)。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]**  >  **[サブスクリプション]** の順に選択し、Batch アカウントに使用するサブスクリプションを選択します。

1. **[サブスクリプション]** ページで、 **[リソース プロバイダー]** を選択し、 **[Microsoft.Batch]** を検索します。 **Microsoft.Batch** リソース プロバイダーがサブスクリプションに登録されていることを確認します。 登録されていない場合は、 **[登録]** リンクを選択します。

    ![Microsoft.Batch プロバイダーの登録][register_provider]

1. **[サブスクリプション]** ページで **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]**  >  **[ロール割り当ての追加]** を選択します。

    ![サブスクリプションのアクセスの制御][subscription_access]

1. **[ロール割り当ての追加]** ページで、 **[共同作成者]** または **[所有者]** ロールを選択し、Batch API を探します。 API が見つかるまで、次の各文字列を検索します。
    1. **MicrosoftAzureBatch**。
    1. **Microsoft Azure Batch**。 新しい Azure AD テナントでは、この名前が使用される場合があります。
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** は Batch API の ID です。

1. Batch API を見つけたら、それを選択して **[保存]** を選択します。

    ![Batch のアクセス許可を追加する][add_permission]

### <a name="create-a-key-vault"></a>Key Vault の作成

ユーザー サブスクリプション モードでは、[Azure キー コンテナー](../key-vault/general/overview.md)が必要です。 キー コンテナーは、作成する Batch アカウントと同じサブスクリプションとリージョンにある必要があります。 

1. [Azure portal](https://portal.azure.com) で、 **[新規]**  >  **[セキュリティ]**  >  **[Key Vault]** の順に選択します。

1. **[Key Vault の作成]** ページで、キー コンテナーの名前を入力し、Batch アカウント用のリージョンでリソース グループを作成します。 残りの設定については既定値のままにして、 **[作成]** を選択します。

ユーザー サブスクリプション モードで Batch アカウントを作成するときは、プール割り当てモードとして **[ユーザー サブスクリプション]** を指定し、そのキー コンテナーを選択し、チェック ボックスをオンにして、Azure Batch にキー コンテナーへのアクセス権を付与します。

キー コンテナーへのアクセス権を手動で付与する場合は、キー コンテナーの **[アクセス ポリシー]** セクションに移動して、 **[アクセス ポリシーの追加]** を選択し、 **[Microsoft Azure Batch]** を検索します。 選択したら、ドロップダウン メニューを使用して **[シークレットのアクセス許可]** を設定する必要があります。 Azure Batch には、少なくとも **[取得]** 、 **[一覧]** 、 **[設定]** 、および **[削除]** のアクセス許可を付与する必要があります。

![Azure Batch のシークレットのアクセス許可](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> リンクされている **Key Vault** リソースの **[アクセス ポリシー]** の下で、 **[Azure Virtual Machines (展開用)]** チェック ボックスと **[Azure Resource Manager (テンプレートの展開用)]** チェック ボックスがオンになっていることを確認します。
>
> ![必須の Key Vault アクセス ポリシー](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>サブスクリプション クォータを構成する

ユーザー サブスクリプションの Batch アカウントの場合は、コア クォータを手動で設定する必要があります。 Standard Batch コア クォータは、ユーザー サブスクリプション モードのアカウントには適用されません。

1. [Azure portal](https://portal.azure.com) でユーザー サブスクリプション モードの Batch アカウントを選択し、その設定とプロパティを表示します。
1. 左側のメニューの **[クォータ]** を選択して、Batch アカウントに関連付けられているコア クォータを表示および構成します。

ユーザー サブスクリプション モードのコア クォータの詳細については、「[Batch サービスのクォータと制限](batch-quota-limit.md)」を参照してください。

## <a name="other-batch-account-management-options"></a>その他の Batch アカウント管理オプション

Azure portal を利用する方法に加えて、次のようなツールを使用して Batch アカウントを作成および管理できます。

* [Batch PowerShell コマンドレット](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>次のステップ

* [Batch サービスのワークフローと主要なリソース](batch-service-workflow-features.md) (プール、ノード、ジョブ、タスクなど) について学習します。
* [Batch .NET クライアント ライブラリ](quick-run-dotnet.md)または [Python](quick-run-python.md) を使用した Batch 対応アプリケーションの開発に関する基本事項を確認してください。 これらのクイック スタートでは、Batch サービスを使用して複数のコンピューティング ノードでワークロードを実行するサンプル アプリケーションの開発手順を説明しています。また、Azure Storage を使用してワークロード ファイルのステージングと取得を行う方法についても取り上げています。

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
