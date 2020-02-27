---
title: Azure Data Factory における継続的インテグレーションとデリバリー
description: 継続的インテグレーションとデリバリーを使用して Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 7c9f22d27351b0f57c5a0158821f347073ae60b4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77187820"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Azure Data Factory における継続的インテグレーションとデリバリー

## <a name="overview"></a>概要

継続的インテグレーションは、コードベースに対して行われた変更を、できるだけ早く自動的にテストするプラクティスです。 継続的デリバリーは、継続的インテグレーションの間に発生したテストに続けて、変更をステージングまたは実稼働システムにプッシュします。

Azure Data Factory では、継続的インテグレーションと継続的デリバリー (CI/CD) とは、Data Factory パイプラインをある環境 (開発、テスト、運用) から別の環境に移動することを意味します。 CI/CD を行うために、Data Factory UX と Azure Resource Manager テンプレートの統合を使用できます。

Data Factory UX では、 **[ARM テンプレート]** ドロップダウン メニューから Resource Manager テンプレートを生成できます。 **[ARM テンプレートのエクスポート]** を選択すると、ポータルで、データ ファクトリ用の Resource Manager テンプレートと、すべての接続文字列とその他のパラメーターを含む構成ファイルが生成されます。 次に、環境 (開発、テスト、運用) ごとに 1 つの構成ファイルを作成します。 すべての環境で使用されるメインの Resource Manager テンプレート ファイルは変更しません。

この機能とデモを 9 分間で紹介する、次のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>CI/CD のライフサイクル

Azure Repos Git で構成された Azure Data Factory での CI/CD のライフサイクルのサンプル概要を以下に示します。 Git リポジトリを構成する方法の詳細については、「[Azure Data Factory のソース管理](source-control.md)」を参照してください。

1.  Azure Repos Git で開発データ ファクトリが作成され、構成されます。 すべての開発者には、パイプラインやデータセットなどの Data Factory リソースを作成するためのアクセス許可が必要です。

1.  開発者は、各自の機能ブランチに変更を加える過程で、各自の最新の変更を使用して各自のパイプライン実行をデバッグします。 パイプライン実行をデバッグする方法の詳細については、「[Azure Data Factory での反復開発とデバッグ](iterative-development-debugging.md)」を参照してください。

1.  開発者は、変更の結果に問題がなければ、各自の機能ブランチからマスターまたはコラボレーション ブランチへの pull request を作成して、同僚が変更をレビューできるようにします。

1.  pull request が承認され、変更がマスター ブランチにマージされたら、変更を開発ファクトリに発行できます。

1.  チームは、変更をテスト ファクトリに、次に運用ファクトリにデプロイする準備ができたら、マスター ブランチから Resource Manager テンプレートをエクスポートします。

1.  エクスポートされた Resource Manager テンプレートは、異なるパラメーター ファイルを使用してテスト ファクトリと運用ファクトリにデプロイされます。

## <a name="create-a-resource-manager-template-for-each-environment"></a>各環境用の Resource Manager テンプレートを作成する

1. **[ARM テンプレート]** 一覧から、 **[ARM テンプレートのエクスポート]** を選択して、データ ファクトリ用の Resource Manager テンプレートを開発環境にエクスポートします。

   ![Resource Manager テンプレートのエクスポート](media/continuous-integration-deployment/continuous-integration-image1.png)

1. テストおよび運用データ ファクトリで、 **[Import ARM template]\(ARM テンプレートのインポート\)** を選択します。 このアクションによって Azure Portal に移動して、エクスポートされたテンプレートをインポートできます。 **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択して、Resource Manager テンプレート エディターを開きます。

   ![独自のテンプレートの作成](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. **[ファイルの読み込み]** を選択し、生成された Resource Manager テンプレートを選択します。

   ![テンプレートの編集](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. 設定セクションで、リンクされたサービスの資格情報などの構成値を入力します。 完了したら、 **[購入]** を選択して Resource Manager テンプレートをデプロイします。

   ![設定セクション](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Connection strings

接続文字列の構成方法については、コネクタの記事を参照してください。 たとえば、Azure SQL Database の場合は、「[Azure Data Factory を使用した Azure SQL Database との間でのデータのコピー](connector-azure-sql-database.md)」をご覧ください。 接続文字列を検証するために、Data Factory UX でリソースのコード ビューを開くことができます。 コード ビューでは、接続文字列のパスワードまたはアカウント キーの部分は削除されます。 コード ビューを開くには、以下で強調表示されているアイコンを選択します。

![コード ビューを開いて接続文字列を確認する](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Azure Pipelines リリースを使用して継続的インテグレーションを自動化する

以下は、Azure Pipelines リリースを設定し、複数の環境へのデータ ファクトリのデプロイを自動化するためのガイドです。

![Azure Pipelines を使用した継続的インテグレーションの図](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>必要条件

-    [Azure Resource Manager サービス エンドポイント](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)を使用して Visual Studio Team Foundation Server または Azure Repos にリンクされた Azure サブスクリプション。

-   Azure Repos Git 統合で構成されたデータ ファクトリ。

-   各環境用のシークレットを格納する  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。

### <a name="set-up-an-azure-pipelines-release"></a>Azure Pipelines リリースをセットアップする

1.  [Azure DevOps](https://dev.azure.com/) で、データ ファクトリで構成したプロジェクトを開きます。

1.  ページの左側で、 **[パイプライン]** を選択して **[リリース]** を選択します。

    ![パイプラインの選択、リリース](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  **[新しいパイプライン]** を選択するか、既存のパイプラインがある場合は **[新規]** 、 **[新しいリリース パイプライン]** の順に選択します。

1.  **[空のジョブ]** テンプレートを選択します。

    ![空のジョブの選択](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  **[ステージ名]** ボックスに、環境の名前を入力します。

1.  **[成果物の追加]** を選択し、データ ファクトリで構成したリポジトリを選択します。 **[既定のブランチ]** で **adf_publish** を選択します。 **[既定のバージョン]** では **[既定のブランチからの最新バージョン]** を選択します。

    ![アーティファクトの追加](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Azure Resource Manager デプロイ タスクを追加します。

    a.  ステージ ビューで、 **[ステージ タスクを表示します]** を選択します。

    ![ステージ ビュー](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  新しいタスクを作成します。 **[Azure リソース グループのデプロイ]** を探して **[追加]** を選択します。

    c.  デプロイ タスクでは、ターゲットのデータ ファクトリのサブスクリプション、リソース グループ、および場所を選択します。 必要であれば資格情報を指定します。

    d.  **[Action]\(アクション\)** 一覧で、 **[Create or update resource group]\(リソース グループの作成または更新\)** を選択します。

    e.  **[テンプレート]** ボックスの横にある省略記号ボタン ( **[…]** ) を選択します。 この記事の「[各環境用の Resource Manager テンプレートを作成する](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment)」セクションで **[Import ARM Template]\(ARM テンプレートのインポート\)** を使用して作成した Azure Resource Manager テンプレートを参照します。 このファイルは adf_publish ブランチの <FactoryName> フォルダーで探します。

    f.  **[…]** を選択します ( **[Template parameters]\(テンプレート パラメーター\)** ボックスの横にあります)。これにより、パラメーター ファイルを選択します。 選択するファイルは、コピーを作成したか既定のファイル ARMTemplateParametersForFactory.json を使用しているかによって異なります。

    g.  **[…]** を選択します ( **[テンプレート パラメーターのオーバーライド]** ボックスの横にあります)。ターゲットのデータ ファクトリの情報を入力します。 Azure Key Vault から取得した資格情報の場合は、シークレットの名前を二重引用符で囲んで入力します。 たとえば、シークレットの名前が cred1 の場合は、この値に「 **"$ (cred1)"** 」と入力します。

    h. **[配置モード]** で **[Incremental]\(増分\)** を選択します。

    > [!WARNING]
    > **[配置モード]** で **[完全]** を選択すると、Resource Manager テンプレートに定義されていないターゲット リソース グループ内のすべてのリソースを含め、既存のリソースが削除される可能性があります。

    ![データ ファクトリの運用環境デプロイ](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  リリース パイプラインを保存します。

1. リリースをトリガーするには、 **[Create release]\(リリースの作成\)** を選択します。

   ![[Create release]\(リリースの作成\) の選択](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> CI/CD のシナリオでは、異なる環境内の統合ランタイム (IR) の種類は同じである必要があります。 たとえば、開発環境にセルフホステッド IR がある場合、テストや運用などの他の環境でも、IR の種類はセルフホステッドである必要があります。 同様に、複数のステージ間で統合ランタイムを共有している場合は、開発、テスト、運用など、すべての環境で、統合ランタイムをリンクされたセルフホステッドとして構成する必要があります。

### <a name="get-secrets-from-azure-key-vault"></a>Azure Key Vault からシークレットを取得する

Azure Resource Manager テンプレートに渡すシークレットがある場合は、Azure Pipelines リリースで Azure Key Vault を使うことをお勧めします。

シークレットを処理する方法は 2 つあります。

1.  シークレットをパラメーター ファイルに追加します。 詳しくは、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)」をご覧ください。

    発行ブランチにアップロードされたパラメーター ファイルのコピーを作成します。 次の形式を使用して、Key Vault から取得するパラメーターの値を設定します。

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    このメソッドを使用すると、シークレットはキー コンテナーから自動的にプルされます。

    パラメーター ファイルも発行ブランチ内に存在する必要があります。

-  前のセクションで説明されている Azure Resource Manager デプロイ タスクの前に、[Azure Key Vault タスク](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)を追加します。

    1.  **[タスク]** タブで、新しいタスクを作成します。 **Azure Key Vault** を検索して追加します。

    1.  Key Vault タスクで、キー コンテナーを作成したサブスクリプションを選択します。 必要に応じて資格情報を入力し、キー コンテナーを選択します。

    ![Key Vault タスクの追加](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Azure Pipelines エージェントにアクセス許可を与える

   適切なアクセス許可が設定されていない場合、Azure Key Vault タスクがアクセス拒否エラーで失敗することがあります。 リリースのログをダウンロードし、Azure Pipelines エージェントにアクセス許可を与えるコマンドを含む .ps1 ファイルを探します。 コマンドは直接実行できます。 または、ファイルからプリンシパル ID をコピーし、Azure portal でアクセス ポリシーを手動で追加できます。 必要な最低限のアクセス許可は、`Get` と `List` です。

### <a name="update-active-triggers"></a>アクティブなトリガーを更新する

アクティブなトリガーを更新しようとした場合、デプロイは失敗します。 アクティブなトリガーを更新するには、手動でそれらを停止し、デプロイ後に再起動する必要があります。 これは、Azure Powershell タスクを使用して実行できます。

1.  リリースの **[タスク]** タブで、 **[Azure PowerShell]** タスクを追加します。 タスク バージョン 4.* を選択します。 

1.  お使いのファクトリがあるサブスクリプションを選択します。

1.  スクリプトの種類として **[スクリプト ファイル パス]** を選択します。 これにより、リポジトリへの PowerShell スクリプトの保存が求められます。 次の PowerShell スクリプトを使用すると、トリガーを停止できます。

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

(`Start-AzDataFactoryV2Trigger` 関数を使用して) 同様の手順を実行することで、デプロイ後にトリガーを再起動できます。

### <a name="sample-pre--and-post-deployment-script"></a>サンプルの配置前と配置後スクリプト

次のサンプル スクリプトは、トリガーをデプロイ前に停止し、デプロイ後に再起動するために使用できます。 このスクリプトには、削除済みのリソースを完全に削除するコードも含まれています。 Azure DevOps の git リポジトリにスクリプトを保存し、バージョン 4.* で Azure PowerShell タスクを介して参照します。

デプロイ前スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


デプロイ後スクリプトを実行する場合は、 **[スクリプトの引数]** フィールドで、次のパラメーターのバリエーションを指定する必要があります。

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

    ![Azure PowerShell task](media/continuous-integration-deployment/continuous-integration-image11.png)

こちらがデプロイ前/後に使用できるスクリプトです。 削除されたリソースとリソース参照を示しています。

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Resource Manager テンプレートでカスタム パラメーターを使用する

GIT モードの場合は、Resource Manager テンプレートの既定のプロパティをオーバーライドして、テンプレート内のパラメーター化されたプロパティと、ハードコーディングされたプロパティを設定することができます。 既定のパラメーター化のテンプレートは、以下のシナリオでオーバーライドすることもできます。

* 自動化された CI/CD を使用していて、Resource Manager のデプロイ中にいくつかのプロパティを変更したいが、プロパティが既定でパラメーター化されていない。
* ファクトリが非常に大きく、既定の Resource Manager テンプレートが許容されるパラメーターの上限 (256) よりも多いために無効である。

このような条件下で既定のパラメーター化テンプレートをオーバーライドするには、データ ファクトリ Git 統合のためのルート フォルダーとして指定されたフォルダー内に arm-template-parameters-definition.json という名前のファイルを作成します。 正確なファイル名を使用する必要があります。 Data Factory は、コラボレーション ブランチからだけでなく、現在 Azure Data Factory ポータルにあるどのブランチからでもこのファイルを読み取ります。 プライベート ブランチからファイルを作成または編集し、UI の **[ARM テンプレートのエクスポート]** を選択して変更内容をテストすることができます。 その後、このファイルをコラボレーション ブランチ内にマージできます。 ファイルが見つからない場合は、既定のテンプレートが使用されます。

### <a name="syntax-of-a-custom-parameters-file"></a>カスタム パラメーター ファイルの構文

カスタム パラメーター ファイルを作成する際のいくつかのガイドラインを次に示します。 ファイルは、(トリガー、パイプライン、リンクされたサービス、データセット、統合ランタイムなどの) エンティティの種類ごとのセクションで構成されています。
* 関連するエンティティ型の下にプロパティ パスを入力します。
* プロパティ名を  `*` に設定すると、その下 (再帰的にではなく、最初のレベルまでのみ) にあるすべてのプロパティをパラメーター化することを指示します。 また、この構成に例外を指定することもできます。
* プロパティの値を文字列として設定すると、プロパティをパラメーター化することを指示します。  `<action>:<name>:<stype>` の形式を使用します。
   *  `<action>`  には、次のいずれかの文字を指定できます。
      * `=`  は、パラメーターの既定値として現在の値を保持することを意味します。
      * `-`  は、パラメーターの既定値を保持しないことを意味します。
      * `|`  は、接続文字列またはキーに対する Azure Key Vault からのシークレットの特殊なケースです。
   * `<name>`  は、パラメーターの名前です。 空白の場合は、プロパティの名前になります。 値が `-` 文字で始まる場合、名前は短縮されます。 たとえば、`AzureStorage1_properties_typeProperties_connectionString` は `AzureStorage1_connectionString` に短縮されます。
   * `<stype>`  は、パラメーターの型です。  `<stype>`  が空白の場合、既定の型は `string` です。 サポートされる値は `string`、`bool`、`number`、`object`、`securestring` です。
* 定義ファイルに配列を指定すると、テンプレート内の一致するプロパティが配列であることを指示します。 Data Factory は、配列の統合ランタイム オブジェクトに指定された定義を使用することで、配列内のすべてのオブジェクトを反復処理します。 2 番目のオブジェクトである文字列は、各反復処理のパラメーターの名前として使用されるプロパティ名です。
* 定義をリソース インスタンスに固有にすることはできません。 定義はその型のすべてのリソースに適用されます。
* 既定では、Key Vault シークレットなどのセキュリティで保護されたすべての文字列と、接続文字列、キー、トークンなどのセキュリティで保護された文字列がパラメーター化されます。
 
### <a name="sample-parameterization-template"></a>サンプルのパラメーター化テンプレート

パラメーター化テンプレートの内容例を次に示します。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
ここでは、前のテンプレートがどのように構築されるかについて、リソースの種類別に説明します。

#### <a name="pipelines"></a>パイプライン
    
* パス `activities/typeProperties/waitTimeInSeconds` 内のすべてのプロパティがパラメーター化されます。 `waitTimeInSeconds` という名前のコードレベルのプロパティを持つパイプライン内のアクティビティ (たとえば、`Wait` アクティビティ) はすべて、既定の名前を使用して数値としてパラメーター化されます。 ただし、Resource Manager テンプレートにその既定値はありません。 これは Resource Manager デプロイ時に必須の入力になります。
* 同様に、`headers` というプロパティ (たとえば、`Web` アクティビティ内の) は、型 `object` (JObject) でパラメーター化されます。 これは、ソース ファクトリの値と同じ値である既定値を保持します。

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* パス `typeProperties` の下にあるすべてのプロパティは、それぞれの既定値を使用してパラメーター化されます。 たとえば、`IntegrationRuntimes` 型のプロパティの下には `computeProperties` と `ssisProperties` という 2 つのプロパティがあります。 両方のプロパティの型は、それぞれの既定の値と型 (Object) で作成されます。

#### <a name="triggers"></a>トリガー

* `typeProperties` の下では、2 つのプロパティがパラメーター化されます。 1 つ目は `maxConcurrency` で、既定値を持つように指定されていて、型は `string` です。 既定のパラメーター名 `<entityName>_properties_typeProperties_maxConcurrency` を持ちます。
* `recurrence` プロパティもパラメーター化されます。 その下にあるそのレベルのすべてのプロパティは、既定の値とパラメーター名を持つ文字列としてパラメーター化されるよう指定されます。 例外は、型 `number` としてパラメーター化される `interval` プロパティです。 パラメーター名の末尾には `<entityName>_properties_typeProperties_recurrence_triggerSuffix` が付きます。 同様に、`freq` プロパティは文字列であり、文字列としてパラメーター化されます。 ただし、`freq` プロパティは既定値なしでパラメーター化されます。 名前は短縮され、サフィックスが付けられます。 たとえば、「 `<entityName>_freq` 」のように入力します。

#### <a name="linkedservices"></a>LinkedServices

* リンクされたサービスは一意です。 リンクされたサービスとデータセットにはさまざまな型があるため、型固有のカスタマイズを行うことができます。 この例では、型 `AzureDataLakeStore` のすべてのリンクされたサービスに対して、特定のテンプレートが適用されます。 他のすべての場合 (`*` を使用)、別のテンプレートが適用されます。
* `connectionString` プロパティは、`securestring` 値としてパラメーター化されます。 既定値はありません。 これには、末尾に `connectionString` が付く、短縮されたパラメーター名が付けられます。
* プロパティ `secretAccessKey` は (たとえば、Amazon S3 のリンクされたサービスでは) `AzureKeyVaultSecret` になることがあります。 Azure Key Vault シークレットとして自動的にパラメーター化され、構成済みのキー コンテナーからフェッチされます。 キー コンテナー自体もパラメーター化できます。

#### <a name="datasets"></a>データセット

* データセットに型固有のカスタマイズを使用できますが、\* レベルの構成を明示的に指定しなくても構成を指定できます。 前の例では、`typeProperties` の下にあるすべてのデータセット プロパティがパラメーター化されます。

### <a name="default-parameterization-template"></a>既定のパラメーター化テンプレート

次に、現在の既定のパラメーター化テンプレートを示します。 いくつかのパラメーターのみを追加する必要がある場合は、このテンプレートを直接編集することをお勧めします。既存のパラメーター化構造が失われないためです。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

次の例は、既定のパラメーター化テンプレートに 1 つの値を追加する方法を示しています。 Databricks のリンクされたサービスの既存の Azure Databricks インタラクティブ クラスター ID をパラメーター ファイルに追加するだけです。 このファイルは、`Microsoft.DataFactory/factories/linkedServices` のプロパティ フィールドの下に `existingClusterId` が追加されていることを除き、前のファイルと同じであることに注意してください。

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>リンクされた Resource Manager テンプレート

データ ファクトリの CI/CD を設定済みの場合、ファクトリが大規模になるにつれて Azure Resource Manager テンプレートの制限を超える可能性があります。 たとえば、1 つの制限として、Resource Manager テンプレート内のリソースの最大数があります。 ファクトリ用の完全な Resource Manager テンプレートを生成しながら、大規模なファクトリに対応するために、Data Factory では、リンクされた Resource Manager テンプレートが生成されるようになりました。 この機能を使用すると、ファクトリのペイロード全体が複数のファイルに分割されるので、制限によって制約されることはありません。

Git が構成されている場合は、リンクされたテンプレートが生成され、完全な Resource Manager テンプレートと共に、 adf_publish ブランチの linkedTemplates という名前の新しいフォルダーに保存されます。

![リンクされた Azure Resource Manager テンプレートのフォルダー](media/continuous-integration-deployment/linked-resource-manager-templates.png)

通常、リンクされた Resource Manager テンプレートは、マスター テンプレートと、マスターにリンクされた一連の子テンプレートで構成されます。 親テンプレートは ArmTemplate_master.json という名前になり、子テンプレートには、ArmTemplate_0. json、ArmTemplate_1.json といったパターンで名前が付けられます。 

完全な Resource Manager テンプレートではなく、リンクされたテンプレートを使用するには、ArmTemplateForFactory.json (完全な Resource Manager テンプレート) ではなく ArmTemplate_master.json をポイントするように CI/CD タスクを更新します。 Resource Manager では、リンクされたテンプレートをストレージ アカウントにアップロードして、デプロイ時に Azure によってアクセスできるようにする必要もあります。 詳細については、[リンクされた Resource Manager テンプレートの VSTS を使用したデプロイ](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)に関する記事を参照してください。

Data Factory のスクリプトは、必ず CI/CD パイプラインのデプロイ タスクの前後に追加してください。

Git が構成されていない場合は、 **[ARM テンプレート]** 一覧の **[ARM テンプレートのエクスポート]** を使用して、リンクされたテンプレートにアクセスできます。

## <a name="hotfix-production-branch"></a>運用ブランチへの修正プログラムの適用

ファクトリを運用環境にデプロイし、すぐに修正が必要なバグがあることが判明したが現在のコラボレーション ブランチをデプロイできない場合、修正プログラムのデプロイが必要なことがあります。 この方法は、クイック修正エンジニアリングまたは QFE と呼ばれています。

1.  Azure DevOps で、運用環境にデプロイされたリリースに移動します。 デプロイされた最後のコミットを見つけます。

2.  コミット メッセージから、コラボレーション ブランチのコミット ID を取得します。

3.  そのコミットから新しい修正プログラム ブランチを作成します。

4.  Azure Data Factory UX に移動し、修正プログラム ブランチに切り替えます。

5.  Azure Data Factory UX を使用して、バグを修正します。 変更をテストします。

6.  修正が検証されたら、 **[ARM テンプレートのエクスポート]** を選択して、修正プログラムの Resource Manager テンプレートを取得します。

7.  このビルドを adf_publish ブランチに手動でチェックインします。

8.  adf_publish のチェックインに基づいて自動的にトリガーするようにリリース パイプラインを構成している場合、新しいリリースは自動的に開始します。 それ以外の場合は、手動でリリースをキューに配置します。

9.  テストと運用ファクトリに修正プログラム リリースをデプロイします。 このリリースには、以前の運用ペイロードに加えて、手順 5 で行った修正が含まれています。

10. 修正プログラムでの変更を開発ブランチに追加して、今後のリリースに同じバグが含まれないようにします。

## <a name="best-practices-for-cicd"></a>CI/CD のベスト プラクティス

データ ファクトリとの Git 統合を使用していて、変更を開発からテストを経て運用環境に移行する CI/CD パイプラインがある場合は、次のベスト プラクティスをお勧めします。

-   **Git 統合**。 Git 統合で構成する必要があるのは開発環境のデータ ファクトリのみです。 テスト環境と運用環境への変更は CI/CD を介してデプロイされるので、Git 統合は必要ありません。

-   **データ ファクトリの CI/CD スクリプト**。 CI/CD の Resource Manager のデプロイ手順の前に、トリガーの停止と再起動やクリーンアップの実行など、特定のタスクを完了する必要があります。 デプロイの前後に PowerShell スクリプトを使用することをお勧めします。 詳細については、「[アクティブなトリガーを更新する](#update-active-triggers)」を参照してください。

-   **統合ランタイムと共有**。 統合ランタイムは頻繁には変更されず、CI/CD のすべてのステージで類似しています。 そのため、Data Factory では、CI/CD のすべてのステージで統合ランタイムの名前と種類を同じにすることが求められます。 すべてのステージで統合ランタイムを共有する場合は、共有の統合ランタイムを含めるためだけに三項ファクトリを使用することを検討してください。 この共有ファクトリは、すべての環境で、リンクされた統合ランタイムの種類として使用できます。

-   **Key Vault**。 Azure Key Vault に基づいてリンクされたサービスを使用する場合は、異なる環境用に個別のキー コンテナーを保持することで、さらに有効に活用できます。 キー コンテナーごとに個別のアクセス許可レベルを構成することもできます。 たとえば、運用環境のシークレットへのアクセス許可をチーム メンバーに持たせたくない場合があります。 このアプローチに従う場合は、すべてのステージで同じシークレット名を保持することをお勧めします。 同じ名前にすると、CI/CD 環境全体で Resource Manager テンプレートを変更する必要がなくなります。なぜなら、変わる項目はキー コンテナーの名前だけであり、これは Resource Manager テンプレートのパラメーターの 1 つです。

## <a name="unsupported-features"></a>サポートされていない機能

- 設計上、Data Factory では、コミットのチェリーピックやリソースの選択的発行は許可されません。 発行には、データ ファクトリで加えられたすべての変更が含まれます

    - データ ファクトリのエンティティは相互に依存しています。 たとえば、トリガーはパイプラインに依存し、パイプラインはデータセットや他のパイプラインに依存しています。 リソースのサブセットを選択的に発行すると、予期しない動作やエラーにつながる可能性があります。
    - まれに、選択的な発行が必要になった場合は、修正プログラムの使用を検討してください。 詳細については、「[運用ブランチへの修正プログラムの適用](#hotfix-production-branch)」を参照してください。

-   非公開のブランチから発行することはできません。

-   現在、Bitbucket でプロジェクトをホストすることはできません。
