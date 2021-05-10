---
title: Azure Migrate を使用して Azure に移行するために Hyper-V 環境にある多数のサーバーを評価する | Microsoft Docs
description: Azure Migrate サービスを使用して Azure に移行するために、Hyper-V 環境にある多数のサーバーを評価する方法について説明します。
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 495e1bf415146471fcccad34e2879398e12e1769
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780295"
---
# <a name="assess-large-numbers-of-servers-in-hyper-v-environment-for-migration-to-azure"></a>Azure に移行するために Hyper-V 環境にある多数のサーバーを評価する

この記事では、Azure Migrate 検出および評価のツールを使用して、Azure に移行するために Hyper-V 環境にある多数のオンプレミス サーバーを評価する方法について説明します。

[Azure Migrate](migrate-services-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 


この記事では、次のことについて説明します。
> [!div class="checklist"]
> * 大規模な評価を計画します。
> * Azure のアクセス許可を構成し、評価用に Hyper-V を準備します。
> * Azure Migrate プロジェクトを作成し、評価を作成します。
> * 移行を計画するときに評価をレビューします。


> [!NOTE]
> 大規模な評価を行う前に、いくつかのサーバーを評価して概念実証を試す場合は、[チュートリアル シリーズ](./tutorial-discover-hyper-v.md)に従ってください。

## <a name="plan-for-assessment"></a>評価の計画

Hyper-V 環境にある多数のサーバーの評価を計画するときは、以下のようないくつかの考慮点があります。

- **Azure Migrate プロジェクトの計画**: Azure Migrate プロジェクトをデプロイする方法を調べます。 たとえば、データセンターが地理的に異なる場所にある場合や、検出、評価、または移行に関連するメタデータを地理的に異なる場所に格納する必要がある場合は、複数のプロジェクトが必要になることがあります。
- **アプライアンスの計画**: Azure Migrate では、Hyper-V VM として展開されたオンプレミスの Azure Migrate アプライアンスを使用して、評価と移行のためにサーバーが継続的に検出されます。 アプライアンスでは、サーバー、ディスク、ネットワーク アダプターなどの環境の変化が監視されます。 また、これらに関するメタデータとパフォーマンス データが Azure に送信されます。 デプロイするアプライアンスの数を確認する必要があります。


## <a name="planning-limits"></a>計画の制限
 
計画には、この表にまとめた制限を使用します。

**計画** | **制限**
--- | --- 
**Azure Migrate プロジェクト** | 1 つのプロジェクトで最大 35,000 台のサーバーが評価されます。
**Azure Migrate アプライアンス** | 1 つのアプライアンスで最大 5,000 台のサーバーを検出できます。<br/> アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。<br/> 1 つのアプライアンスは、1 つの Azure Migrate リソースにのみ関連付けることができます。<br/> 任意の数のアプライアンスを 1 つの Azure Migrate プロジェクトに関連付けることができます。 <br/><br/> 
**グループ** | 1 つのグループに最大 35,000 台のサーバーを追加できます。
**Azure Migrate の評価** | 1 回の評価で最大 35,000 台のサーバーを評価できます。



## <a name="other-planning-considerations"></a>計画に関するその他の考慮事項

- アプライアンスから検出を開始するには、各 Hyper-V ホストを選択する必要があります。 
- マルチテナント環境を実行している場合は、現在、特定のテナントに属しているサーバーのみを検出することはできません。 

## <a name="prepare-for-assessment"></a>評価の準備

検出および評価ツールのために Azure と Hyper-V を準備します。 

1. [Hyper-V のサポート要件と制限事項](migrate-support-matrix-hyper-v.md)を確認します。
2. Azure Migrate とやり取りするために、自分の Azure アカウントのアクセス許可を設定します
3. Hyper-V ホストとサーバーを準備します

[このチュートリアル](./tutorial-discover-hyper-v.md)の手順に従って、これらの設定を構成します。

## <a name="create-a-project"></a>プロジェクトの作成

計画の要件に従って、以下を実行します。

1. Azure Migrate プロジェクトを作成します。
2. プロジェクトに Azure Migrate 検出および評価のツールを追加します。

[詳細情報](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>評価の作成とレビュー

1. Hyper-V 環境でサーバーの評価を作成します。
1. 移行計画に備えて評価をレビューします。

評価の作成とレビューの[詳細を確認](tutorial-assess-hyper-v.md)します。
    

## <a name="next-steps"></a>次のステップ

この記事では、次の内容について説明します。
 
> [!div class="checklist"] 
> * Hyper-V 環境にあるサーバーの Azure Migrate 評価のスケーリングを計画しました
> * 評価のために Azure と Hyper-V を準備しました
> * Azure Migrate プロジェクトを作成し、評価を実行しました
> * 移行に備えて評価をレビューしました。

次に、[評価の計算](concepts-assessment-calculation.md)方法と、[評価の変更](how-to-modify-assessment.md)方法について確認します