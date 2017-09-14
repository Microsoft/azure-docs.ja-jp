---
title: "Azure Service Fabric CLI スクリプトのデプロイのサンプル"
description: "Azure Service Fabric CLI を使用した Azure Service Fabric クラスターへのアプリケーションのデプロイ"
services: service-fabric
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 08/22/2017
ms.author: adegeo
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 74d54b314e81dc260e800e32d6c3edd5215c82b4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---

# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Service Fabric クラスターへのアプリケーションのデプロイ

このサンプル スクリプトは、アプリケーション パッケージをクラスター イメージ ストアにコピーし、クラスターにそのアプリケーションの種類を登録し、そのアプリケーションの種類からアプリケーション インスタンスを作成します。 また、この時点で既定のサービスも作成されます。

必要に応じて、[Service Fabric CLI](../service-fabric-cli.md) をインストールします。

## <a name="sample-script"></a>サンプル スクリプト

[!code-sh[main](../../../cli_scripts/service-fabric/deploy-application/deploy-application.sh "クラスターへのアプリケーションのデプロイ")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

終了したら、[remove](cli-remove-application.md) スクリプトを使用してアプリケーションを削除することができます。 remove スクリプトは、アプリケーション インスタンスを削除し、アプリケーションの種類の登録を解除し、イメージ ストアからアプリケーション パッケージを削除します。

## <a name="next-steps"></a>次のステップ

詳細については、[Service Fabric CLI ドキュメント](../service-fabric-cli.md)をご覧ください。

その他の Azure Service Fabric 用 Service Fabric CLI サンプルは、[Service Fabric CLI のサンプル](../samples-cli.md)のページにあります。

