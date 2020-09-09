---
title: チュートリアル - Azure Service Fabric Mesh のリソースをクリーンアップする
description: 使わなくなったリソースに対して課金されないように、Azure Service Fabric Mesh のリソースを削除する方法を説明します。
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b8ce3c795bc9ad212331ce1c1f413fe7fd6da909
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246753"
---
# <a name="tutorial-remove-azure-resources"></a>チュートリアル: Azure のリソースを削除する

このチュートリアルはシリーズの第 5 部であり、課金されないようにアプリとそのリソースを削除する方法を示します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * リソースの料金が発生しないように、アプリで使用されているリソースをクリーンアップする。

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Visual Studio で Service Fabric Mesh アプリを作成する](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [ローカル開発クラスター内で実行されている Service Fabric Mesh アプリをデバッグする](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh アプリをデプロイする](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh アプリをアップグレードする](service-fabric-mesh-tutorial-upgrade.md)
> * Service Fabric Mesh リソースをクリーンアップする

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* To Do アプリをデプロイしていない場合は、[Service Fabric Mesh Web アプリケーションの発行](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)に関するページの指示に従ってください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これは、チュートリアルの最後です。 作成したリソースの作業が終わったら、使用しなくなったリソースに課金されないように削除します。 Mesh は 1 秒単位で課金されるサーバーレス サービスのため、これは特に重要です。 Mesh の価格について詳しくは、 https://aka.ms/sfmeshpricing をご覧ください。

Azure が便利な点の 1 つは、作成したリソースを特定のリソース グループに関連付けた場合、リソース グループを削除すると関連付けられているリソースもすべて削除されることです。 1 つずつ削除する必要はありません。

To Do アプリを作成するために新しいリソース グループを作成したので、このリソース グループを安全に削除できます。リソース グループを削除すると、関連するすべてのリソースが削除されます。

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

**sfmeshTutorial1RG** リソース グループは、[ポータルから](../azure-resource-manager/management/manage-resource-groups-portal.md#delete-resource-groups)削除することもできます。 

## <a name="next-steps"></a>次のステップ

Service Fabric Mesh アプリケーションを Azure に発行したら、次のことを行ってみてください。

* サービス間通信の別の例を確認するには、[投票アプリ サンプル](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp)を調べてください
* Service Fabric リソース モデルの詳細については、[Service Fabric Mesh リソース モデル](service-fabric-mesh-service-fabric-resources.md)に関するページを参照してください。
* Service Fabric Mesh の詳細については、[Service Fabric Mesh の概要](service-fabric-mesh-overview.md)に関するページを参照してください。
* [Cloud Shell の概要を読む](../cloud-shell/overview.md)
