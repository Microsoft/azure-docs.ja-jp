---
title: Azure Synapse Analytics でのデータ ウェアハウスの開発に関するリソース
description: SQL Data Warehouse に関する開発コンセプト、設計上の決定、推奨事項、およびコーディング技法。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153317"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Azure Synapse Analytics のデータ ウェアハウスの設計上の決定と コーディング技法 
 この記事では、Azure Synapse Analytics のデータ ウェアハウスに関する主要な設計上の決定、推奨事項、およびコーディング技法をより深く理解するために、追加のリソースについて説明します。

## <a name="key-design-decisions"></a>主要な設計上の決定
次の記事には、Azure Synapse の SQL Analytics 機能を使用して分散データウェア ハウスを開発するための概念と設計に関する決定事項が概説されています。

* [connections](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [トランザクション](sql-data-warehouse-develop-transactions.md)
* [ユーザー定義スキーマ](sql-data-warehouse-develop-user-defined-schemas.md)
* [テーブルのディストリビューション](sql-data-warehouse-tables-distribute.md)
* [テーブルのインデックス](sql-data-warehouse-tables-index.md)
* [テーブルのパーティション](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開発における推奨事項とコーディング技法
次の記事では、SQL Analytics を使用してデータ ウェアハウスを開発するための具体的なコーディング技法、ヒント、および推奨事項について説明しています。

* [ストアド プロシージャ](sql-data-warehouse-develop-stored-procedures.md)
* [ラベル](sql-data-warehouse-develop-label.md)
* [views](sql-data-warehouse-develop-views.md)
* [一時テーブル](sql-data-warehouse-tables-temporary.md)
* [動的 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [ループ](sql-data-warehouse-develop-loops.md)
* [オプションでのグループ化](sql-data-warehouse-develop-group-by-options.md)
* [変数の代入](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>次のステップ
詳細な参照情報については、[T-SQL ステートメント](sql-data-warehouse-reference-tsql-statements.md)に関するページをご覧ください。
