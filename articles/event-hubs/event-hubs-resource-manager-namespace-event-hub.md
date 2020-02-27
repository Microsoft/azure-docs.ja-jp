---
title: クイック スタート:コンシューマー グループを含むイベント ハブを作成する - Azure Event Hubs
description: クイック スタート:イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 88cd29af75239f0ad79eb78b5ff8e106c3b2ee56
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163077"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用してイベント ハブを作成する

Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このクイック スタートでは、[Azure Resource Manager テンプレート](../azure-resource-manager/management/overview.md)を使用してイベント ハブを作成します。 Azure Resource Manager テンプレートをデプロイして、イベント ハブを 1 つ含む [Event Hubs](event-hubs-what-is-event-hubs.md) 型の名前空間を作成します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成については、[Azure Resource Manager テンプレートのオーサリング][Authoring Azure Resource Manager templates]に関する記事をご覧ください。 テンプレートで使用する JSON の構文とプロパティについては、「[Microsoft.EventHub resource types (Microsoft.EventHub のリソースの種類)](/azure/templates/microsoft.eventhub/allversions)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-an-event-hub"></a>イベント ハブの作成

このクイック スタートでは、[既存のクイック スタート テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)を使用します。

[!code-json[create-azure-event-hub-namespace](~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json)]

テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular)」をご覧ください。

テンプレートをデプロイするには、次の手順に従います。

1. 次のコード ブロックの **[試してみる]** を選択し、指示に従って Azure Cloud Shell にサインインします。

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   イベント ハブが作成されるまでしばらく時間がかかります。

1. **[コピー]** を選択し、PowerShell スクリプトがコピーされます。
1. シェル コンソールを右クリックし、 **[貼り付け]** を選択します。

## <a name="verify-the-deployment"></a>デプロイを検証する

デプロイを検証するには、[Azure portal](https://portal.azure.com) からリソース グループを開くか、次の Azure PowerShell スクリプトを使用します。  Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 Cloud Shell がまだ開いている場合は、最初の行 (Read-Host) をコピー/実行する必要はありません。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次のステップ

この記事では、Event Hubs 名前空間を作成し、その名前空間にイベント ハブを作成しました。 イベント ハブとの間でイベントを送信または受信するためのステップ バイ ステップの手順については、以下の**イベントの送受信**のチュートリアルを参照してください。

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-java-send-v2.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (送信のみ)](event-hubs-c-getstarted-send.md)
- [Apache Storm (受信のみ)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
