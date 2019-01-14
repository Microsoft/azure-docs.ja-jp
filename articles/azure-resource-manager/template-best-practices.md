---
title: Azure Resource Manager テンプレートのベスト プラクティス
description: Azure Resource Manager テンプレートを作成するための推奨されるアプローチについて説明します。 テンプレートを使用する場合の一般的な問題を回避するための推奨事項を示します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: tomfitz
ms.openlocfilehash: bd54ae2c82d3baf716784c39951c5cad7ec364b3
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730721"
---
# <a name="azure-resource-manager-template-best-practices"></a>Azure Resource Manager テンプレートのベスト プラクティス

この記事では、ご自身の Resource Manager テンプレートを作成する方法に関する推奨事項について説明します。 これらの推奨事項は、テンプレートを使用したソリューションのデプロイに関する一般的な問題を回避するうえで役立ちます。

Azure サブスクリプションを管理する方法に関する推奨事項については、「[Azure エンタープライズ スキャフォールディング:規範的なサブスクリプション ガバナンス](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)」を参照してください。

すべての Azure クラウド環境で動作するテンプレートを作成する方法に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」を参照してください。

## <a name="parameters"></a>parameters
このセクションの情報は、[パラメーター](resource-manager-templates-parameters.md)を使用するときに役に立ちます。

### <a name="general-recommendations-for-parameters"></a>パラメーターに関する一般的な推奨事項

* パラメーターの使用を最小限に抑えます。 代わりに、デプロイ時に指定する必要のないプロパティの変数またはリテラル値を使用してください。

* パラメーター名にキャメル ケースを使用します。

* SKU、サイズ、容量など、環境に応じて異なる設定のパラメーターを使用します。

* 特定しやすいように指定するリソース名のパラメーターを使用します。

* メタデータですべてのパラメーターを説明します。

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* 重要ではないパラメーターの既定値を定義します。 既定値を指定することで、テンプレートをデプロイしやすくなり、ご自身のテンプレートのユーザーに適切な値の例が表示されます。 パラメーターの既定値は、既定のデプロイ構成のすべてのユーザーに対して有効である必要があります。 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* オプションのパラメーターを指定するために、空の文字列を既定値として使用しないでください。 代わりに、リテラル値または言語式を使用して値を構成します。

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* リソースの種類の API バージョンにパラメーターを使用しないでください。 リソースのプロパティおよび値は、バージョン番号ごとに異なる可能性があります。 パラメーターに API バージョンが設定されると、コード エディターの IntelliSense が適切なスキーマを決定できなくなります。 代わりに、テンプレートの API バージョンをハードコーディングしてください。

* `allowedValues` は慎重に使用してください。 許可されているオプションに、ある値が含まれていないことを確認する必要がある場合にのみ使用します。 `allowedValues` を盛大に使用しすぎると、ご自身の一覧が最新の状態に保たれなくなり、有効なデプロイをブロックしてしまう可能性があります。

* お使いのテンプレートのパラメーター名と PowerShell デプロイ コマンドのパラメーターが同じ場合は、Resource Manager によって、ポストフィックス **FromTemplate** がテンプレート パラメーターに追加され、この名前の競合が解決されます。 たとえば、**ResourceGroupName** という名前のパラメーターをテンプレートに追加した場合、このパラメーターは、[New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) コマンドレットの **ResourceGroupName** パラメーターと競合するため、 デプロイ中、**ResourceGroupNameFromTemplate** に値を指定するように求められます。

### <a name="security-recommendations-for-parameters"></a>パラメーターに関するセキュリティの推奨事項

* ユーザー名とパスワード (またはシークレット) に対して必ずパラメーターを使用します。

* すべてのパスワードおよびシークレットに `securestring` を使用します。 JSON オブジェクトに機密データを渡す場合は、`secureObject` 型を使用します。 secure string 型または secure object 型を含むテンプレート パラメーターをリソースのデプロイ後に読み取ることはできません。 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* ユーザー名、パスワード、または `secureString` 型を必要とする任意の値には既定値を指定しないでください。

* アプリケーションの攻撃対象となる領域が増えるプロパティには既定値を指定しないでください。

### <a name="location-recommendations-for-parameters"></a>パラメーターに関する場所の推奨事項

* パラメーターを使用して、リソースの場所を指定し、既定値を `resourceGroup().location` に設定します。 場所パラメーターを指定することにより、テンプレートのユーザーが、自身がアクセス許可を持つ場所をデプロイ先として指定できます。

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* 場所パラメーターに `allowedValues` を指定しないでください。 指定する場所がすべてのクラウドで使用できるとは限りません。

* 場所パラメーター値は、同じ場所に所属する可能性が高いリソースに対して使用します。 この方法により、ユーザーが場所情報の入力を求められる回数を最小限に抑えることができます。

* 一部の場所で使用できないリソースについては、個別のパラメーターを使用するか、場所のリテラル値を指定します。

## <a name="variables"></a>variables

[変数](resource-manager-templates-variables.md)を使用する場合は、次の情報を活用してください。

* テンプレート内で複数回使用する必要のある値には、変数を使用してください。 1 回しか使用しない値は、ハードコーディングすることでテンプレートが読みやすくなります。

* テンプレート関数を複雑に組み合わせて作成する値に対して変数を使用してください。 複雑な式が変数内にのみ表示されていると、ご自身のテンプレートが読みやすくなります。

* リソースで `apiVersion` に対しては変数を使用しないでください。 リソースのスキーマは、API バージョンに応じて決まります。 多くの場合、バージョンを変更すると、リソースのプロパティも変更されます。

* テンプレートの **variables** セクションでは [reference](resource-group-template-functions-resource.md#reference) 関数は使用できません。 **reference** 関数は、リソースのランタイム状態からその値を取得します。 ただし、変数が解決されるのは、テンプレートの初期解析時です。 **reference** 関数を必要とする値は、テンプレートの **resources** セクションまたは **outputs** セクションに直接作成してください。

* リソース名を表す変数は、一意である必要があります。

* [変数のコピー ループ](resource-group-create-multiple.md#variable-iteration)を使用して、JSON オブジェクトの繰り返しパターンを作成します。

* 未使用の変数を削除します。

## <a name="resource-dependencies"></a>リソースの依存関係

どのような[依存関係](resource-group-define-dependencies.md)を設定するかを決めるときは、次のガイドラインを使用してください。

* プロパティを共有する必要があるリソース間に暗黙的な依存関係を設定するには、**reference** 関数を使用して、リソース名を渡します。 暗黙的な依存関係を既に定義してある場合は、明示的な `dependsOn` 要素を追加しないでください。 これにより、不要な依存関係が設定されるリスクを減らすことができます。

* 子リソースは、その親リソースに依存するように設定します。

* [condition 要素](resource-manager-templates-resources.md#condition)が false に設定されているリソースは、依存関係の順序から自動的に削除されます。 依存関係は、リソースが常にデプロイされているかのように設定してください。

* 明示的に設定しなくても連鎖的な依存関係になるようにします。 たとえば、仮想マシンが仮想ネットワーク インターフェイスに依存し、その仮想ネットワーク インターフェイスは仮想ネットワークとパブリック IP アドレスに依存しているとします。 この場合、仮想マシンは、この 3 つのリソースすべての後にデプロイされますが、この仮想マシンを 3 つのリソースすべてに依存するものとして明示的に設定しないでください。 これにより依存関係の順序が明確になり、後でテンプレートを変更するのも簡単になります。

* デプロイの前に値を指定できる場合は、依存関係なしでリソースをデプロイしてみます。 たとえば、構成値に他のリソースの名前を必要とする場合は、依存関係が不要なことがあります。 ただし、これは必ずしも有効であるとは限りません。リソースによっては、他のリソースが存在するかどうかを確認することがあるためです。 エラーが発生したら、依存関係を追加してください。

## <a name="resources"></a>リソース

[リソース](resource-manager-templates-resources.md)を使用する場合は、次の情報を活用してください。

* 他の共同作業者にリソースの用途がわかるように、テンプレート内の各リソースに **comments** を指定してください。
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* テンプレートで "*パブリック エンドポイント*" (Azure Blob Storage のパブリック エンドポイントなど) を使用する場合、名前空間は "*ハードコーディングしない*" でください。 名前空間を動的に取得するには、**reference** 関数を使用します。 そうすることで、テンプレートのエンドポイントを手作業で変更することなく、別のパブリック名前空間環境にテンプレートをデプロイできます。 API バージョンは、テンプレートのストレージ アカウントで使用するものと同じバージョンに設定します。
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   作成している同じテンプレートにストレージ アカウントがデプロイされている場合、リソースの参照でプロバイダーの名前空間を指定する必要はありません。 簡単な構文の例を次に示します。
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   テンプレート内にパブリック名前空間を使用するために構成されている他の値がある場合は、同じ **reference** 関数を反映するようにこれらの値を変更してください。 たとえば、仮想マシンの診断プロファイルの **storageUri** プロパティは次のように設定できます。
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   また、別のリソース グループにある既存のストレージ アカウントを参照することもできます。

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* 仮想マシンにパブリック IP アドレスを割り当てるのは、アプリケーションで必要な場合のみにしてください。 デバッグや各種管理のために仮想マシン (VM) に接続するには、受信 NAT 規則、仮想ネットワーク ゲートウェイ、またはジャンプボックスを使用してください。
   
     仮想マシンへの接続の詳細については、以下の記事を参照してください。
   
   * [Azure で N 層アーキテクチャの VM を実行する](../guidance/guidance-compute-n-tier-vm.md)
   * [Azure Resource Manager の VM の WinRM アクセスを設定する](../virtual-machines/windows/winrm.md)
   * [Azure Portal を使用して VM への外部アクセスを許可する](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell を使用して VM への外部アクセスを許可する](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI を使用して Linux VM への外部アクセスを許可する](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* パブリック IP アドレスの **domainNameLabel** プロパティは一意である必要があります。 **domainNameLabel** の値は、3 文字以上 63 文字以下で、正規表現 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` で指定された規則に従う必要があります。 **uniqueString** 関数は 13 文字の文字列を生成するため、**dnsPrefixString** パラメーターは 50 文字に制限されます。

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* カスタム スクリプト拡張機能にパスワードを追加する場合は、**protectedSettings** プロパティで **commandToExecute** プロパティを使用してください。
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > シークレット情報を VM と拡張機能にパラメーターとして渡すときに暗号化されるように、関連する拡張機能の **protectedSettings** プロパティを使用する必要があります。
   > 
   > 

## <a name="outputs"></a>出力

テンプレートを使用してパブリック IP アドレスを作成する場合は、IP アドレスの詳細と完全修飾ドメイン名 (FQDN) を返す [outputs セクション](resource-manager-templates-outputs.md)を組み込みます。 出力値を使用して、デプロイ後に簡単にパブリック IP アドレスと FQDN についての詳細を取得できます。

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>次の手順

* Resource Manager テンプレート ファイルの構造の詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* すべての Azure クラウド環境で動作するテンプレートを作成する方法に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」を参照してください。
