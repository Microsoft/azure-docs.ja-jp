---
title: テンプレートのデプロイ スクリプトを使用する | Microsoft Docs
description: Azure Resource Manager テンプレートでデプロイ スクリプトを使用する方法を説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5454d2f80d1febccb0c57ecf2e80d930bb5cb761
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988806"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate-preview"></a>チュートリアル:デプロイ スクリプトを使用して自己署名証明書を作成する (プレビュー)

Azure Resource Manager テンプレートでデプロイ スクリプトを使用する方法を説明します。 デプロイ スクリプトを使用すると、Resource Manager テンプレートでは実行できないカスタム ステップを実行できます。 たとえば、自己署名証明書を作成できます。  このチュートリアルでは、Azure キー コンテナーをデプロイするためのテンプレートを作成した後、同じテンプレート内で `Microsoft.Resources/deploymentScripts` リソースを使用して証明書を作成してからその証明書をそのキー コンテナーに追加します。 デプロイ スクリプトの詳細については、[Azure Resource Manager テンプレートでのデプロイ スクリプトの使用](./deployment-script-template.md)に関する記事を参照してください。

> [!NOTE]
> デプロイ スクリプトは現在プレビュー段階です。 使用するには、[プレビューにサインアップする](https://aka.ms/armtemplatepreviews)必要があります。

> [!IMPORTANT]
> スクリプトの実行とトラブルシューティングのため、同じリソース グループ内に 2 つのデプロイ スクリプト リソース (ストレージ アカウントとコンテナー インスタンス) が作成されます。 これらのリソースは、通常、スクリプトの実行が最終状態になるとスクリプト サービスによって削除されます。 リソースが削除されるまでは、リソースに対して請求が行われます。 詳細については、「[デプロイ スクリプト リソースのクリーンアップ](./deployment-script-template.md#clean-up-deployment-script-resources)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * 失敗したスクリプトをデバッグする
> * リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* **Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)** 。 「[Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./use-vs-code-to-create-template.md)」を参照してください。

* **サブスクリプション レベルで共同作成者のロールが付与された、ユーザー割り当て済みマネージド ID**。 この ID は、デプロイ スクリプトを実行するために使用されます。 作成するには、「[ユーザー割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity)」を参照してください。 この識別 ID は、テンプレートをデプロイするときに必要です。 ID の形式は次のとおりです。

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  次の PowerShell スクリプトを使用して、リソース グループ名と ID 名を指定することで ID を取得します。

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  $id = (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name idName).Id
  ```

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

ゼロからテンプレートを作成するのではなく、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)からテンプレートを開きます。 Azure クイック スタート テンプレートは、Resource Manager テンプレートのリポジトリです。

このクイックスタートで使用するテンプレートは、[Create an Azure Key Vault and a secret](https://azure.microsoft.com/resources/templates/101-key-vault-create/) と呼ばれます。 このテンプレートにより、キー コンテナーが作成され、そのキー コンテナーにシークレットが追加されます。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. **[開く]** を選択して、ファイルを開きます。
4. **[ファイル]** > **[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

テンプレートに次の変更を加えます。

### <a name="clean-up-the-template-optional"></a>テンプレートをクリーンアップする (省略可能)

元のテンプレートでは、キー コンテナーにシークレットが追加されます。  チュートリアルを簡略化するために、次のリソースを削除します。

* **Microsoft.KeyVault/vaults/secrets**

次の 2 つのパラメーター定義を削除します。

* **secretName**
* **secretValue**

これらの定義を削除しない場合は、デプロイ中にパラメーター値を指定する必要があります。

### <a name="configure-the-key-vault-access-policies"></a>キー コンテナーのアクセス ポリシーを構成する

デプロイ スクリプトにより、キー コンテナーに証明書が追加されます。 キー コンテナーのアクセス ポリシーを構成して、マネージド ID にアクセス許可を付与します。

1. パラメーターを追加して、マネージド ID を取得します。

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Visual Studio Code の Resource Manager テンプレート拡張機能では、まだデプロイ スクリプトの形式を設定できません。 次のように、Shift + Alt + F キーを使用して deploymentScripts のリソースの形式を設定しないでください。

1. マネージド ID がキー コンテナーに証明書を追加できるように、キー コンテナーのアクセス ポリシーを構成するためのパラメーターを追加します。

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. 既存のキー コンテナーのアクセス ポリシーを次のように更新します。

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    定義されているポリシーは 2 つあり、1 つはサインイン ユーザー用、もう 1 つはマネージド ID 用です。  サインイン ユーザーに必要なのは、デプロイを検証するための *list* 権限のみです。  チュートリアルを簡略化するために、マネージド ID とサインイン ユーザーの両方に同じ証明書が割り当てられています。

### <a name="add-the-deployment-script"></a>デプロイ スクリプトを追加する

1. デプロイ スクリプトで使用される 3 つのパラメーターを追加します。

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. deploymentScripts リソースを追加します。

    > [!NOTE]
    > インライン デプロイ スクリプトは二重引用符で囲まれているため、デプロイ スクリプト内の文字列は、代わりに単一引用符で囲む必要があります。 PowerShell のエスケープ文字は **&#92;** です。

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2019-10-01-preview",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts` リソースは、キー コンテナー リソースとロールの割り当てリソースに依存します。  これには、次のプロパティがあります。

    * **identity**: デプロイ スクリプトでは、ユーザー割り当てのマネージド ID を使用してスクリプトが実行されます。
    * **kind**: スクリプトの種類を指定します。 現在は、PowerShell スクリプトのみがサポートされています。
    * **forceUpdateTag**: スクリプト ソースが変更されていない場合でもデプロイ スクリプトを実行するかどうかを決定します。 現在のタイム スタンプまたは GUID を指定できます。 詳細については、「[スクリプトを複数回実行する](./deployment-script-template.md#run-script-more-than-once)」を参照してください。
    * **azPowerShellVersion**: 使用する Azure PowerShell モジュールのバージョンを指定します。 現在、デプロイ スクリプトでは、バージョン 2.7.0、2.8.0、3.0.0 がサポートされています。
    * **timeout**: [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601)で指定される、スクリプトの許容最長実行時間を指定します。 既定値は **P1D** です。
    * **arguments**: パラメーター値を指定します。 値はスペースで区切ります。
    * **scriptContent**:スクリプトの内容を指定します。 外部スクリプトを実行するには、代わりに **primaryScriptURI** を使用します。 詳細については、「[外部関数を使用する](./deployment-script-template.md#use-external-scripts)」を参照してください。
        **$DeploymentScriptOutputs** の宣言は、ローカル コンピューターでスクリプトをテストする場合にのみ必要です。 変数を宣言すると、変更を加えなくても、ローカル コンピューターと deploymentScript リソースでスクリプトを実行できます。 $DeploymentScriptOutputs に割り当てられた値は、デプロイの出力として使用できます。 詳細については、「[デプロイ スクリプトからの出力を操作する](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)」を参照してください。
    * **cleanupPreference**: デプロイ スクリプト リソースをいつ削除するかに関する設定を指定します。  既定値は **Always** です。これは、最終状態 (Succeeded、Failed、canceled) にかかわらず、デプロイ スクリプト リソースが削除されることを意味します。 このチュートリアルでは、スクリプトの実行結果が表示されるように **OnSuccess** を使用します。
    * **retentionInterval**: 最終状態に達した後にサービスがスクリプト リソースを保持する期間を指定します。 この期間が経過すると、リソースは削除されます。 期間は ISO 8601 のパターンに基づきます。 このチュートリアルでは、1 日を意味する P1D を使用します。  このプロパティは、**cleanupPreference** が **OnExpiration** に設定されている場合に使用されます。 このプロパティは現在有効ではありません。

    デプロイ スクリプトは、3 つのパラメーター (キー コンテナー名、証明書名、サブジェクト名) を受け取ります。  それにより、証明書が作成された後、キー コンテナーにその証明書が追加されます。

    **$DeploymentScriptOutputs** は、出力値を格納するために使用されます。  詳細については、「[デプロイ スクリプトからの出力を操作する](./deployment-script-template.md#work-with-outputs-from-deployment-scripts)」を参照してください。

    完成したテンプレートは、[こちら](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)で確認できます。

1. デバッグ プロセスを確認するには、デプロイ スクリプトに次の行を追加してしてコード内にエラーを配置します。

    ```powershell
    Write-Output1 $keyVaultName
    ```

    正しいコマンドは **Write-Output1** ではなく **Write-Output** です。

1. **[ファイル]** > **[保存]** を選択して、ファイルを保存します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

Cloud Shell を開いてテンプレート ファイルをそのシェルにアップロードするには、Visual Studio Code のクイックスタートの「[テンプレートのデプロイ](./quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)」セクションを参照してください。 その後、次の PowerShell スクリプトを実行します。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
$identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$resourceGroupName = "${projectName}rg"
$keyVaultName = "${projectName}kv"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

Write-Host "Press [ENTER] to continue ..."
```

デプロイ スクリプト サービスでは、スクリプトの実行用に追加のデプロイ スクリプト リソースを作成する必要があります。 実際のスクリプトの実行時間に加えて、準備とクリーンアップ プロセスが完了するまでに最大で 1 分かかることがあります。

スクリプトで無効なコマンド **Write-Output1** が使用されているため、デプロイが失敗します。 次のようなエラーが表示されます。

```error
The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
program.\nCheck the spelling of the name, or if a path was included, verify that the path is correct and try again.\n
```

デプロイ スクリプトの実行結果は、トラブルシューティングの目的でデプロイ スクリプト リソースに格納されます。

## <a name="debug-the-failed-script"></a>失敗したスクリプトをデバッグする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. リソース グループを開きます。 これは、**rg** が付加されたプロジェクト名です。 リソース グループには 2 つの追加リソースが表示されます。 これらのリソースは、"*デプロイ スクリプト リソース*" と呼ばれます。

    ![Resource Manager テンプレートのデプロイ スクリプト リソース](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    どちらのファイルにも **azscripts** というサフィックスが付いています。 1 つはストレージ アカウントで、もう 1 つはコンテナー インスタンスです。

    **[非表示の型の表示]** を選択して、deploymentScripts リソースの一覧を表示します。

1. **azscripts** サフィックスの付いたストレージ アカウントを選択します。
1. **[ファイル共有]** タイルを選択します。 **azscripts** フォルダーが表示されます。  そのフォルダーには、デプロイ スクリプトの実行ファイルが含まれています。
1. **azscripts** を選択します。 **azscriptinput** および **azscriptoutput** という 2 つのフォルダーが表示されます。  入力フォルダーには、システム用 PowerShell スクリプト ファイルとユーザー用デプロイ スクリプト ファイルが含まれています。 出力フォルダーには、**executionresult.json** とスクリプトの出力ファイルが含まれています。 エラー メッセージは、**executionresult.json** で確認できます。 この出力ファイルは、実行が失敗したため、ここにはありません。

**Write-Output1** の行を削除して、テンプレートを再デプロイします。

2 回目のデプロイが正常に実行されると、**cleanupPreference** プロパティが **OnSuccess** に設定されているため、デプロイ スクリプト リソースはスクリプト サービスによって削除されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Resource Manager テンプレートでデプロイ スクリプトを使用する方法について学習しました。 条件に基づいて Azure リソースをデプロイする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [使用条件](./template-tutorial-use-conditions.md)
