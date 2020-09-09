---
title: 共有メタデータ モデル
description: Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、Spark プール (プレビュー)、SQL オンデマンド エンジン (プレビュー)、および SQL プール間でデータベースとテーブルを共有できます。
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: c11a0ccb08f03775a07716e6c547d849cda347dd
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387338"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Azure Synapse Analytics の共有メタデータ

Azure Synapse Analytics では、さまざまなワークスペース計算エンジンが、Spark プール (プレビュー) と SQL オンデマンド エンジン (プレビュー) の間でデータベースとテーブルを共有できます。

[!INCLUDE [preview](../includes/note-preview.md)]

この共有では、いわゆる最新のデータ ウェアハウス パターンがサポートされており、ワークスペースの SQL エンジンは、Spark で作成されたデータベースとテーブルにアクセスすることができます。 また、SQL エンジンは、他のエンジンと共有されていない独自のオブジェクトを作成することもできます。

## <a name="support-the-modern-data-warehouse"></a>最新のデータ ウェアハウスをサポートする

共有メタデータ モデルでは、次の方法で最新のデータウェアハウス パターンがサポートされます。

1. データ レイクのデータは、準備されたデータを、場合によっては複数のデータベースに含まれる (パーティション分割されている可能性のある)、Parquet でサポートされたテーブルに格納することにより、Spark を使用して効率的に準備され、構造化されます。

2. Spark によって作成されたデータベースとすべてのテーブルは、どの Azure Synapse ワークスペースの Spark プール インスタンスでも可視となり、どの Spark ジョブからも使用できます。 ワークスペース内のすべての Spark プールは、基になる同じカタログ メタ ストアを共有しているため、この機能は[アクセス許可](#security-model-at-a-glance)の対象になります。

3. Spark によって作成されたデータベースと Parquet でサポートされるテーブルは、ワークスペースの SQL オンデマンド エンジンに表示されるようになります。 [データベース](database.md)は、SQL オンデマンド メタデータに自動的に作成されます。また、Spark ジョブによって作成された[外部テーブルとマネージド テーブル](table.md)の両方に、対応するデータベースの `dbo` スキーマの SQL オンデマンド メタデータで外部テーブルとしてアクセスできるようになります。 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

オブジェクトの同期は非同期に行われます。 オブジェクトが SQL コンテキストに表示されるまでには、数秒のわずかな遅延が発生します。 一度表示されると、それに対してクエリを実行できますが、それに対するアクセス権を持つ SQL エンジンによって更新または変更されることはありません。

## <a name="shared-metadata-objects"></a>共有メタデータ オブジェクト

Spark を使用すると、データベース、外部テーブル、マネージド テーブル、およびビューを作成できます。 Spark ビューでは、定義する Spark SQL ステートメントを処理するために Spark エンジンが必要であり、SQL エンジンでは処理できないため、Parquet ストレージ形式を使用するデータベースおよび含まれている外部テーブルとマネージド テーブルのみが、ワークスペースの SQL エンジンと共有されます。 Spark ビューは、Spark プール インスタンス間でのみ共有されます。

## <a name="security-model-at-a-glance"></a>セキュリティ モデルの概要

Spark データベースとテーブルは、SQL エンジンの同期された表現と共に、基になるストレージ レベルで保護されます。 クエリ送信者が使用権限を持つ任意のエンジンによってテーブルに対してクエリが実行されると、クエリ送信者のセキュリティ プリンシパルが、基になるファイルに渡されます。 アクセス許可は、ファイル システム レベルで確認されます。

詳細については、「[Azure Synapse Analytics の共有データベース](database.md)」を参照してください。

## <a name="change-maintenance"></a>メンテナンスを変更する

メタデータ オブジェクトが Spark で削除または変更されると、その変更が取得され、SQL オンデマンド エンジンに反映されます。 同期は非同期であり、変更は少し遅れて SQL エンジンに反映されます。

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics の共有メタデータ データベースの詳細情報](database.md)
- [Azure Synapse Analytics の共有メタデータ テーブルの詳細情報](table.md)

