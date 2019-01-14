---
title: Azure Kubernetes Service (AKS) と Azure Database for PostgreSQL を接続する
description: Azure Kubernetes Service と Azure Database for PostgreSQL を接続する方法について説明します
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: conceptual
ms.openlocfilehash: ff8508db55b04d2c55158b5846325d0c13665048
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542749"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Azure Kubernetes Service と Azure Database for PostgreSQL を接続する

Azure Kubernetes Service (AKS) からは、Azure で使用できるマネージド Kubernetes クラスターが提供されます。 AKS と Azure Database for PostgreSQL を併用してアプリケーションを作成するときに考慮する選択肢を下にいくつか示します。


## <a name="accelerated-networking"></a>Accelerated Networking
AKS クラスターで高速ネットワーク対応の基礎 VM を使用します。 VM で高速ネットワークが有効になると、待ち時間が少なくなり、ジッターが減り、VM の CPU 使用率が下がります。 高速ネットワークのしくみ、サポートされている OS バージョン、[Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 向けにサポートされている VM インスタンスについて説明します。

2018 年 11 月から、AKS は、これらのサポートされている VM インスタンスでの高速ネットワークに対応します。 これらの VM を使用する新しい AKS クラスターでは、高速ネットワークが既定で有効になっています。

AKS クラスターで高速ネットワークが有効になっているかどうかは次の方法で確認できます。
1. Azure portal に進み、AKS クラスターを選択します。
2. [プロパティ] タブを選択します。
3. **[インフラストラクチャ リソース グループ]** の名前をコピーします。
4. ポータル検索バーを使用し、インフラストラクチャ リソース グループを見つけて開きます。
5. そのリソース グループ内の VM を選択します。
6. VM の **[ネットワーク]** タブに移動します。
7. **[高速ネットワーク]** が "有効" になっていることを確認します。


## <a name="open-service-broker-for-azure"></a>Open Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) を使用すると、Kubernetes または Cloud Foundry から Azure サービスを直接プロビジョニングできます。 これは Azure 用の [Open Service Broker API](https://www.openservicebrokerapi.org/) 実装です。

OSBA を使用すると、Azure Database for PostgreSQL サーバーを作成し、Kubernetes のネイティブ言語で AKS クラスターにそれをバインドできます。 OSBA と Azure Database for PostgreSQL を併用する方法については、[OSBA GitHub ページ](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md)で確認できます。 


## <a name="connection-pooling"></a>接続プール
接続プーラーにより、データベースへの新しい接続の作成と終了に関連するコストと時間が最小限に抑えられます。 プールは、再利用できる接続のコレクションです。 

PostgreSQL では複数の接続プーラーを使用できます。 その 1 つは [PgBouncer](https://pgbouncer.github.io/) です。 Microsoft Container Registry では、提供されている軽量なコンテナー化された PgBouncer をサイドカーで使用して、AKS から Azure Database for PostgreSQL への接続をプールできます。 このイメージにアクセスして使用する方法については、[Docker Hub のページ](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)をご覧ください。 


## <a name="next-steps"></a>次の手順
-  [Azure Kubernetes Service クラスターを作成する](../aks/kubernetes-walkthrough.md)
