---
title: Azure Service Fabric CLI (sfctl) スクリプトのデプロイのサンプル
description: Azure Service Fabric CLI を使用した Azure Service Fabric クラスターへのアプリケーションのデプロイ
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 04/16/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: ff40dc62b4dcd622156a78518bbdcb6b9b430644
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "75526602"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster-using-the-service-fabric-cli"></a>Service Fabric CLI を使用して Service Fabric クラスターにアプリケーションをデプロイする

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。 また、この時点で既定のサービスも作成されます。

必要に応じて、[Service Fabric CLI](../service-fabric-cli.md) をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

終了したら、[remove](cli-remove-application.md) スクリプトを使用してアプリケーションを削除することができます。 remove スクリプトは、アプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除し、イメージ ストアからアプリケーション パッケージを削除します。

## <a name="next-steps"></a>次のステップ

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。
