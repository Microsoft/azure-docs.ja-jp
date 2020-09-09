---
title: Synapse Studio を使用して Apache Spark アプリケーションを監視する方法
description: Synapse Studio を使用して Apache Spark アプリケーションを監視します。
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: b5ad9e6a448747ac8f33829b1137b1af434e7a9c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87385587"
---
# <a name="use-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Synapse Studio (プレビュー) を使用して Apache Spark アプリケーションを監視する

Azure Synapse Analytics では、Spark を使用して、ワークスペース内の Spark プールでノートブック、ジョブ、その他の種類のアプリケーションを実行できます。

この記事では、Apache Spark アプリケーションを監視する方法を説明し、最新の状態、問題、進行状況を監視できるようにします。

## <a name="access-apache-spark-applications-list"></a>Apache Spark アプリケーション リストにアクセスする

ワークスペースで Apache Spark アプリケーションの一覧を表示するには、まず [Synapse Studio を開き](https://web.azuresynapse.net/)、自分のワークスペースを選択します。

![ワークスペースへのログイン](./media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](./media/common/left-nav.png)

**[Apache Spark applications]\(Apache Spark アプリケーション\)** を選択して、Apache Spark アプリケーションの一覧を表示します。

 ![Spark アプリケーションを選択する](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filter-your-apache-spark-applications"></a>Apache Spark アプリケーションをフィルター処理する

Apache Spark アプリケーションの一覧を関心のあるものだけにフィルター処理することができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、名前に "sales" を含む Apache Spark アプリケーションのみを表示することができます。

![[フィルター] ボタン](./media/common/filter-button.png)

![サンプルのフィルター](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>特定の Apache Spark アプリケーションに関する詳細を表示する

Apache Spark アプリケーションのいずれかの詳細を表示するには、Apache Spark アプリケーションを選択し、詳細を表示します。 Apache Spark アプリケーションがまだ実行中の場合は、進行状況を監視できます。 詳細については、[こちら](apache-spark-applications.md)を参照してください。

## <a name="next-steps"></a>次のステップ

パイプラインの実行の監視に関する詳細については、[Synapse Studio を実行するパイプラインの監視](how-to-monitor-pipeline-runs.md)に関する記事を参照してください。 

Apache Spark アプリケーションのデバッグの詳細については、[Synapse Studio での Apache Spark アプリケーションの監視](apache-spark-applications.md)に関するページを参照してください。