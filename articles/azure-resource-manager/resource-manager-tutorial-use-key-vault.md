---
title: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する | Microsoft Docs
description: Azure Key Vault を使用して Resource Manager テンプレートのデプロイ時にセキュリティで保護されたパラメーターの値を渡す方法について説明します
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3a84f9ed35bac7f56d4a6aa2af94d1c28e335b74
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093201"
---
# <a name="tutorial-integrate-azure-key-vault-in-resource-manager-template-deployment"></a>チュートリアル: Resource Manager Template deployment で Azure Key Vault を統合する

Azure Key Vault からシークレット値を取得し、Resource Manager のデプロイ時にシークレット値をパラメーターとして渡す方法を説明します。 値の Key Vault ID のみを参照するため、値が公開されることはありません。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください

[リソースのデプロイ順序の設定](./resource-manager-tutorial-create-templates-with-dependent-resources.md)に関するチュートリアルでは、仮想マシン、仮想ネットワーク、およびその他の依存リソースを作成します。 このチュートリアルでは、Azure Key Vault から仮想マシン管理者パスワードを取得するようテンプレートをカスタマイズします。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Key Vault を準備する
> * クイック スタート テンプレートを開く
> * パラメーター ファイルを編集する
> * テンプレートのデプロイ
> * デプロイの検証
> * リソースのクリーンアップ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* [Visual Studio Code](https://code.visualstudio.com/) と [Resource Manager ツール拡張機能](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)。
* セキュリティを向上させるには、生成されたパスワードを仮想マシンの管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル:Resource Manager Template deployment で Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-the-key-vault"></a>Key Vault を準備する

このセクションでは、Resource Manager テンプレートを使用して、Key Vault とシークレットを作成します。 このテンプレートを使用すると：

* `enabledForTemplateDeployment` プロパティを有効にしてキー コンテナーを作成する。 このプロパティは、テンプレートのデプロイプロセスがこの Key Vault で定義されているシークレットにアクセスできる前に true である必要があります。
* Key Vault にシークレットを追加する。  シークレットは、仮想マシンの管理者パスワードを格納します。

(仮想マシンのテンプレートをデプロイするユーザーとして) 所有者または Key Vault の共同作成者でない場合、Key Vault の所有者または共同作成者によって Microsoft.KeyVault/vaults/deploy/action 許可 へのアクセス権を与えられる必要があります。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](./resource-manager-keyvault-parameter.md)」を参照してください

Azure AD ユーザーオブジェクト ID は、
テンプレートによるアクセス許可の設定で必要です。 次の手順では、オブジェクト ID (GUID) を取得し、管理者のパスワードも生成します。 パスワードのスプレー攻撃を防ぐためには、生成されたパスワードを使用することをお勧めします。

1. 次の Azure PowerShellまたはAzure CLI コマンドを実行します。  

    ```azurecli-interactive
    echo "Enter your email address that is associated with your Azure subscription):" &&
    read upn &&
    az ad user show --upn-or-object-id $upn --query "objectId" &&
    openssl rand -base64 32
    ```
    ```azurepowershell-interactive
    $upn = Read-Host -Prompt "Input your user principal name (email address) used to sign in to Azure"
    (Get-AzureADUser -ObjectId $upn).ObjectId
    openssl rand -base64 32
    ```
2. オブジェクト ID と生成されたパスワードの両方を書き留めます。 この情報は後で必要になります。
3. 生成されたパスワードが、仮想マシンのパスワード要件を満たしていることを確認します。 各Azure サービスには、特定のパスワード要件があります。 VMのパスワード要件については、 [VM を作成するときのパスワードの要件とは?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を参照してください。

Key Vault を作成するには：

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 テンプレートが Key Vault と Key Vault シークレットを作成します。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Farmtutorials.blob.core.windows.net%2Fcreatekeyvault%2FCreateKeyVault.json"><img src="./media/resource-manager-tutorial-use-key-vault/deploy-to-azure.png" alt="deploy to azure"/></a>

2. 次の値を選択または入力します。  値を入力した後に**購入**を選択しないでください。

    ![Resource Manager テンプレートの Key Vault 統合がポータルをデプロイする](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-key-vault-portal.png)

    * **サブスクリプション**: Azure サブスクリプションを選択します。
    * **リソース グループ**: 一意の名前を割り当てます。 同じリソース グループを使用して次のセッションで仮想マシンをデプロイするので、この名前を書き留めます。 Key Vault と仮想マシンの両方を同じリソース グループに配置すると、チュートリアルの最後にリソースをクリーンアップしやすくなります。
    * **場所**: 場所を選択します。  既定の場所は**米国中部**です。
    * **Key Vault 名**: 一意の名前を割り当てます。 
    * **テナントID**: テンプレート関数は、自動的にテナントID を取得します。既定値を変更しないでください
    * **Ad ユーザー ID**: 最後の手順から取得した、 Azure AD ユーザーオブジェクトIDを入力します。
    * **シークレット名**: 既定の名前は **vmAdminPassword** です。 ここでシークレットの名前を変更する場合は、仮想マシンを展開するときに、シークレット名を更新する必要があります。
    * **シークレット値**: シークレットを入力します。  シークレットは、仮想マシンへのサインインに使用されるパスワードです。 最後の手順で作成した生成されるパスワードを使用することをお勧めします。
    * **上記の使用条件に同意する**: 選択。
3. 上から**パラメーターの編集**を選択してテンプレートを確認します。
4. テンプレートの JSON ファイルの28行目を参照します。 これは、Key Vault リソースの定義です。
5. 35行目を参照：

    ```json
    "enabledForTemplateDeployment": true,
    ```
    `enabledForTemplateDeployment`は Key Vault プロパティです。 デプロイ時にこの Key Vault からシークレットを取得する前に、このプロパティが true である必要があります。
6. 89行目を参照。 これは、Key Vault シークレットの定義です。
7. ページの下部にある**破棄する**を選択します。 何も変更を加えませんでした。
8. 前のスクリーン ショットに示されるすべての値を指定したことを確認し、ページの下部にある**購入**をクリックします。
9. ページの上部からベルのアイコン(通知)を選択して、**通知**ウィンドウを開きます。 リソースが正常にデプロイされるまで待機します。
10. **通知**ウィンドウで **リソース グループに移動** を選択します。 
11. Key Vault 名を選択して開きます。
12. 左側のウィンドウで **アクセスポリシー**を選択します。 自分の名前(アクティブディレクトリ)を一覧表示、それ以外の場合 Key Vault にアクセスするアクセス許可がありません。
13. **[クリックして高度なアクセス ポリシーを表示する]** を選択します。 通知**テンプレートの展開のため Azure Resource Manager へのアクセスを有効にする**が選択されています。 これは、Key Vault の統合が機能するための別の条件です。

    ![Resource Manager テンプレートの Key Vault 統合アクセスポリシー](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-key-vault-access-policies.png)
14. 左側のウィンドウで**プロパティ**を選択します。
15. **リソース ID**のコピーを作成します。 仮想マシンをデプロイする時に、この ID が必要です。  リソース ID の形式は:

    ```json
    /subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
    ```

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このチュートリアルで使用するテンプレートは、「[Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)」(単純な Windows VM をデプロイする) と呼ばれます。

1. Visual Studio Code から、**[ファイル]**>**[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。 これは、[チュートリアル: Azure Resource Manager テンプレートを依存リソースで作成する](./resource-manager-tutorial-create-templates-with-dependent-resources.md)で使用されたものと同じシナリオです。
4. テンプレートによって定義されたリソースは、5 つあります。

    * `Microsoft.Storage/storageAccounts` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)をご覧ください。
    * `Microsoft.Network/publicIPAddresses` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)をご覧ください。
    * `Microsoft.Network/virtualNetworks` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)をご覧ください。
    * `Microsoft.Network/networkInterfaces` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)をご覧ください。
    * `Microsoft.Compute/virtualMachines` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

    カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
5. **[ファイル]**>**[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。
6. 次の URL を開くには手順1～4を繰り返して、ファイルを**azuredeploy.parameters.json**として保存します。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>パラメーター ファイルを編集する

テンプレートファイルに変更を加える必要はありません。

1. **azuredeploy.parameters.json**が開いていない場合、Visual Studio Code で開きます。
2. **adminPassword** パラメーターを次のように更新します。

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```
    **id** を最後の手順で作成した Key Vault の リソース ID に置き換えます。  

    ![Key Vault と Resource Manager テンプレートの仮想マシンのデプロイパラメーターファイルを統合します](./media/resource-manager-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)
3. 次のように値を指定します。

    * **adminUsername**: 仮想マシンの管理者アカウントに名前を付けます。
    * **dnsLabelPrefix**: dnsLabelPrefix に名前を付けます。
4. 変更を保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[テンプレートをデプロイする](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)の指示に従って、テンプレートをデプロイします。 テンプレートをデプロイするには**azuredeploy.json**と**azuredeploy.parameters.json**の両方をクラウドシェルにアップロードし、次の PowerShell スクリプトを使用する必要があります:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name of the Key Vault"
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $resourceGroupName `
    -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
```

テンプレートをデプロイするときに、Key Vault と同じリソース グループを使用します。 そうすると、リソースをクリーンアップしやすくなります。 削除する必要があるのは2 つではなく 1 つのリソース グループのみです。

## <a name="valid-the-deployment"></a>デプロイを有効化する

仮想マシンを正常に配置した後は、Key Vault に格納されているパスワードを使用してログインをテストします。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. **リソース grouips**/**YourResourceGroupName >**/**simpleWinVM**を選択します
3. 上部から**接続**を選択します。
4. **Download RDP File**を選択し、Key Vault に格納されているパスワードを使用して仮想マシンにサインインします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Key Vault からシークレットを取得し、そのシークレットをテンプレートのデプロイで使用しました。  リンク済みテンプレートを作成する方法についての説明は、次を参照してください:

> [!div class="nextstepaction"]
> [リンク済みテンプレートを作成する](./resource-manager-tutorial-create-linked-templates.md)
