---
title: HDInsight クラスターを削除する方法 - Azure
description: Azure HDInsight クラスターを削除できるさまざまな方法についての情報
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 55c19ee9b23d43b0f9988363405d76fa16949ec9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946038"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する

HDInsight クラスターの課金は、クラスターが作成されると開始し、クラスターが削除されると停止します。 課金は分単位なので、クラスターを使わなくなったら必ず削除してください。 このドキュメントでは、[Azure portal](https://portal.azure.com)、[Azure PowerShell Az モジュール](/powershell/azure/)、および [Azure CLI](/cli/azure/) を使ってクラスターを削除する方法について説明します。

> [!IMPORTANT]  
> HDInsight クラスターを削除しても、そのクラスターに関連付けられている Azure Storage アカウントまたは Data Lake Storage は削除されません。 これらのサービスに格納されているデータは、将来再利用できます。

## <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインする

2. 左側メニューから、 **[すべてのサービス]**  >  **[分析]**  >  **[HDInsight クラスター]** に移動し、クラスターを選択します。

3. 既定のビューから、 **[削除]** アイコンを選択します。 クラスターを削除するプロンプトに従います。

    ![HDInsight クラスター削除ボタン](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

`CLUSTERNAME` を、以下のコード内の HDInsight クラスターの名前に置き換えます。 クラスターを削除するには、PowerShell プロンプトで次のコマンドを実行します。

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME`を HDInsight クラスターの名前、および`RESOURCEGROUP`次のコードでのリソース グループの名前に置き換えます。  クラスターを削除するには、コマンド プロンプトで以下を実行します。

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
