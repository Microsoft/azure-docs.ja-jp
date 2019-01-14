---
title: PowerShell の使用 - Azure Batch | Microsoft Docs
description: Batch リソースの管理に使用できる Azure PowerShell コマンドレットの簡単な紹介。
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 10/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bd8e5425a09f0faeaa573cec58def88f352b9a1d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548070"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>PowerShell コマンドレットで Batch リソースを管理する

Batch API、Azure portal、Azure コマンド ライン インターフェイス (CLI) を使用して実行するタスクの多くは、Azure Batch PowerShell コマンドレットで実行したりスクリプト化したりすることができます。 この記事では、Batch アカウントを管理したり、プール、ジョブ、タスクといった Batch リソースを操作したりするときに使用できるコマンドレットについて説明します。

すべての Batch コマンドレットの一覧およびコマンドレットの詳細な構文については、 [Azure Batch コマンドレットのリファレンス](/powershell/module/azurerm.batch/#batch)を参照してください。

この記事は、Azure Batch モジュール 4.1.5 のコマンドレットに基づいています。 最新のサービスや機能強化を活かすためにも、Azure PowerShell モジュールは定期的に更新することをお勧めします。

## <a name="prerequisites"></a>前提条件

* [Azure PowerShell モジュールをインストールして構成します](/powershell/azure/overview)。 特定の Azure Batch モジュール (プレリリース モジュールなど) をインストールする方法については、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/AzureRM.Batch/5.0.0-preview)を参照してください。 

* **Connect-AzureRmAccount** コマンドレットを実行してサブスクリプションに接続します (Azure Batch コマンドレットは、Azure Resource Manager モジュールに付属しています)。

  ```PowerShell
  Connect-AzureRmAccount
  ```

* **Batch プロバイダーの名前空間に登録します**。 この操作は、**サブスクリプションごとに 1 回**実行するだけでかまいません。
  
  ```PowerShell
  Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`
  ```

## <a name="manage-batch-accounts-and-keys"></a>Batch アカウントとキーを管理する

### <a name="create-a-batch-account"></a>Batch アカウントを作成する

**New-AzureRmBatchAccount** は、指定したリソース グループに Batch アカウントを作成します。 リソース グループがまだない場合は、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドレットを実行して作成します。 **location** パラメーターで Azure リージョンのいずれか (例: "Central US") を指定します。 例: 

```PowerShell
New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

次に、リソース グループに Batch アカウントを作成します。<*account_name*> にアカウントの名前を指定し、リソース グループの場所と名前を指定します。 Batch アカウントの作成は、完了までにしばらく時間がかかる場合があります。 例: 

```PowerShell
New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch アカウント名は、リソース グループの Azure リージョン内で重複しないこと、文字数が 3 ～ 24 文字であること、小文字と数字のみで構成されていることが必要になります。
> 

### <a name="get-account-access-keys"></a>アカウントのアクセス キーを取得する

**Get-AzureRmBatchAccountKeys** は、Azure Batch アカウントに関連付けられているアクセス キーを示します。 たとえば、作成したアカウントのプライマリ キーとセカンダリ キーを取得するには、次のように実行します。

 ```PowerShell
$Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>新しいアクセス キーを生成する

**New-AzureRmBatchAccountKey** は、Azure Batch アカウントの新しいプライマリ アカウント キーまたはセカンダリ アカウント キーを生成します。 たとえば、Batch アカウントの新しいプライマリ キーを生成するには、次のように入力します。

```PowerShell
New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> 新しいセカンダリ キーを生成するには、 **KeyType** パラメーターの "Secondary" を指定します。 プライマリ キーとセカンダリ キーは個別に再生成する必要があります。
> 

### <a name="delete-a-batch-account"></a>Batch アカウントを削除する

**Remove-AzureRmBatchAccount** は、Batch アカウントを削除します。 例: 

```PowerShell
Remove-AzureRmBatchAccount -AccountName <account_name>
```

メッセージが表示されたら、削除するアカウントを確認します。 アカウントの削除は完了するまでに時間がかかる場合があります。

## <a name="create-a-batchaccountcontext-object"></a>BatchAccountContext オブジェクトを作成する

Batch リソースを管理するための認証は、共有キー認証または Azure Active Directory 認証を使用して行うことができます。 Batch PowerShell コマンドレットを使用して認証を行うにはまず、自分のアカウントの資格情報または ID を格納するための BatchAccountContext オブジェクトを作成します。 BatchAccountContext オブジェクトは、 **BatchContext** パラメーターを引数として受け取るコマンドレットに渡します。 

### <a name="shared-key-authentication"></a>共有キー認証

```PowerShell
$context = Get-AzureRmBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> 既定では、アカウントのプライマリ キーは認証に使用されますが、BatchAccountContext オブジェクトの **KeyInUse** プロパティを変更することで使用するキーを明示的に選択できます (例: `$context.KeyInUse = "Secondary"`)。
> 

### <a name="azure-active-directory-authentication"></a>Azure Active Directory 認証

```PowerShell
$context = Get-AzureRmBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Batch リソースを作成および変更する
Batch アカウントでリソースを作成するには、**New-AzureBatchPool**、**New-AzureBatchJob**、**New-AzureBatchTask** などのコマンドレットを使用します。 既存のリソースのプロパティを更新するための対応する **Get-** コマンドレットと **Set-** コマンドレットがあり、Batch アカウントのリソースを削除するための **Remove-** コマンドレットがあります。

以下の例を見るとわかるように、これらのコマンドレットの多くは、その使用時に、BatchContext オブジェクトを渡すことに加え、リソースの詳細な設定を含んだオブジェクトを作成するか、引数として渡す必要があります。 その他の例については、各コマンドレットの詳細なヘルプを参照してください。

### <a name="create-a-batch-pool"></a>Create a Batch pool

Batch プールを作成または更新する際は、コンピューティング ノードのオペレーティング システムに関してクラウド サービスの構成または仮想マシンの構成を選択します (「[Batch 機能の概要](batch-api-basics.md#pool)」を参照)。 クラウド サービス構成を指定した場合、コンピューティング ノードはいずれかの [Azure ゲスト OS リリース](../cloud-services/cloud-services-guestos-update-matrix.md#releases)を使用してイメージ化されます。 仮想マシンの構成を指定した場合、[Azure Virtual Machines Marketplace][vm_marketplace] に記載されたサポートされている Linux または Windows VM イメージのいずれかを指定するか、自身で準備したカスタム イメージを指定できます。

オペレーティング システムの設定は、**New-AzureBatchPool** を実行するときに、PSCloudServiceConfiguration オブジェクトまたは PSVirtualMachineConfiguration オブジェクトで渡します。 たとえば、以下のスニペットは、仮想マシンの構成を選び、Ubuntu Server 16.04-LTS のイメージを使用して、Standard_A1 サイズのコンピューティング ノードで新しい Batch プールを作成しています。 ここでは、**VirtualMachineConfiguration** パラメーターに PSVirtualMachineConfiguration オブジェクトとして *$configuration* 変数を指定しています。 **BatchContext** パラメーターには、先ほど定義した *$context* 変数を BatchAccountContext オブジェクトとして指定しています。

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","16.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 16.04")

New-AzureBatchPool -Id "mypool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

新しいプール内のコンピューティング ノードの目標数は、自動スケールの式によって求められます。 この例では、式は **$TargetDedicated=4**という簡単なものであり、プールのコンピューティング ノードの数が最大 4 であることを示します。

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>プール、ジョブ、タスク、およびその他の詳細のクエリ

Batch アカウントで作成されたエンティティを照会するには、**Get-AzureBatchPool**、**Get-AzureBatchJob**、**Get-AzureBatchTask** などのコマンドレットを使用します。

### <a name="query-for-data"></a>データのクエリ

たとえば、 **Get-AzureBatchPools** を使用してプールを検索します。 既定では、これは、既に BatchAccountContext オブジェクトが *$context*に格納されていると仮定して、自分のアカウントのすべてのプールを照会します。

```PowerShell
Get-AzureBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>OData フィルターを使用する

**Filter** パラメーターを使用して OData フィルターを指定すると、関心のあるオブジェクトのみを検索できます。 たとえば、ID が "myPool" で始まるすべてのプールを検索できます。

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzureBatchPool -Filter $filter -BatchContext $context
```

このメソッドは、ローカルのパイプラインで “Where-Object” を使用するほど柔軟ではありません。 ただし、クエリは Batch サービスに直接送信されるため、すべてのフィルター処理がサーバー側で行われ、インターネットの帯域幅を節約できます。

### <a name="use-the-id-parameter"></a>ID パラメーターの使用

OData フィルターに代わる方法として、 **ID** パラメーターを使用します。 "myPool" という ID の特定のプールを照会するには:

```PowerShell
Get-AzureBatchPool -Id "myPool" -BatchContext $context
```

**ID** パラメーターは、完全 ID の検索のみをサポートし、ワイルドカードや OData 形式のフィルターはサポートしません。

### <a name="use-the-maxcount-parameter"></a>MaxCount パラメーターを使用する

既定では、各コマンドレットは最大で 1000 のオブジェクトを返します。 この制限に達した場合は、オブジェクトが少なくなるようにフィルターで絞り込むか、 **MaxCount** パラメーターを使用して明示的に最大値を設定してください。 例: 

```PowerShell
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context
```

上限を削除するには、 **MaxCount** を 0 以下に設定します。

### <a name="use-the-powershell-pipeline"></a>PowerShell パイプラインを使用する

Batch コマンドレットは、コマンドレット間でデータを送信するために PowerShell パイプラインを活用できます。 これは、パラメーターを指定するのと同じ効果がありますが、複数のエンティティを簡単に操作できます。

たとえば、自分のアカウントのすべてのタスクを検索して表示します。

```PowerShell
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

プール内のすべてのコンピューティング ノードを再起動します。

```PowerShell
Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>アプリケーション パッケージの管理

アプリケーション パッケージを利用すると、プール内の計算ノードにアプリケーションを簡単にデプロイできます。 Batch PowerShell コマンドレットを使用して、Batch アカウントのアプリケーション パッケージをアップロードおよび管理し、パッケージ バージョンをコンピューティング ノードにデプロイできます。

**作成** する:

```PowerShell
New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**追加** する:

```PowerShell
New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

アプリケーションの**既定のバージョン**を設定する:

```PowerShell
Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

アプリケーションのパッケージを**一覧表示**する:

```PowerShell
$application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

アプリケーション パッケージを**削除**する:

```PowerShell
Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

アプリケーションを**削除**する:

```PowerShell
Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> アプリケーションを削除する前に、アプリケーションのアプリケーション パッケージのすべてのバージョンを削除する必要があります。 現在アプリケーション パッケージがあるアプリケーションを削除しようとすると、"競合" エラーが発生します。
> 

### <a name="deploy-an-application-package"></a>アプリケーション パッケージをデプロイする

プールの作成時に、デプロイ用の 1 つ以上のアプリケーション パッケージを指定できます。 プールの作成時にパッケージを指定すると、ノードがプールに参加する際にパッケージが各ノードにデプロイされます。 ノードが再起動または再イメージ化されるときにも、パッケージがデプロイされます。

プールのノードがそこに参加するタイミングでアプリケーション パッケージをデプロイするには、`-ApplicationPackageReference` オプションを指定してプールを作成します。 まず、**PSApplicationPackageReference** オブジェクトを作成し、アプリケーション ID とプールのコンピューティング のノードにデプロイするパッケージのバージョンでオブジェクトを構成します。

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

次に、プールを作成し、`ApplicationPackageReferences` オプションの引数としてパッケージ参照オブジェクトを指定します。

```PowerShell
New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

アプリケーション パッケージについて詳しくは、「[Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ](batch-application-packages.md)」をご覧ください。

> [!IMPORTANT]
> アプリケーション パッケージを使用するには、お使いの Batch アカウントに [Azure ストレージ アカウントをリンクする](#linked-storage-account-autostorage) 必要があります。
> 
> 

### <a name="update-a-pools-application-packages"></a>プールに含まれるアプリケーション パッケージの更新

既存のプールに割り当てられているアプリケーションを更新するには、まず、必要なプロパティ (アプリケーション ID とパッケージのバージョン) を指定して PSApplicationPackageReference オブジェクトを作成します。

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

次に、バッチからプールを取得し、既存のパッケージをすべて削除します。新しいパッケージ参照を追加し、新しいプール設定で Batch サービスを更新します。

```PowerShell
$pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzureBatchPool -BatchContext $context -Pool $pool
```

これで、Batch サービスでプールのプロパティが更新されました。 ただし、プールのコンピューティング ノードに新しいアプリケーション パッケージを実際にデプロイするには、それらのノードを再起動するか、再イメージ化する必要があります。 次のコマンドを使用して、プール内のすべてのノードを再起動できます。

```PowerShell
Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context
```

> [!TIP]
> プール内のコンピューティング ノードに複数のアプリケーション パッケージをデプロイできます。 現在デプロイされているパッケージを置き換えるのではなく、アプリケーション パッケージを "*追加*" する場合は、上記の `$pool.ApplicationPackageReferences.Clear()` 行を省略します。
> 
> 

## <a name="next-steps"></a>次の手順

* コマンドレットの詳しい構文と例については、 [Azure Batch コマンドレットのリファレンス](/powershell/module/azurerm.batch/#batch)を参照してください。
* Batch におけるアプリケーションとアプリケーション パッケージについて詳しくは、「[Batch アプリケーション パッケージを使用したコンピューティング ノードへのアプリケーションのデプロイ](batch-application-packages.md)」をご覧ください。

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
