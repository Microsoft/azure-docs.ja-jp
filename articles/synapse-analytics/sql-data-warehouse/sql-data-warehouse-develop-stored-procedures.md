---
title: ストアド プロシージャの使用
description: Synapse SQL プールでのストアド プロシージャの実装によるソリューション開発に関するヒント。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213399"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Synapse SQL プールでのストアド プロシージャの使用

この記事では、ストアド プロシージャの実装によって SQL プールのソリューションを開発するためのヒントを紹介します。

## <a name="what-to-expect"></a>ウィザードの内容

SQL プールでは、SQL Server で使用される多数の T-SQL 機能がサポートされています。 さらに重要なのは、ソリューションのパフォーマンスを最大限にするために使用できる、スケールアウト専用の機能が用意されていることです。

さらに、SQL プールのスケールとパフォーマンスの維持に役立つように、動作の異なるその他の機能が追加されています。

## <a name="introducing-stored-procedures"></a>ストアド プロシージャの概要

ストアド プロシージャは、SQL コードをカプセル化するための優れた方法であり、SQL プール データの近くに格納されます。 ストアド プロシージャは、開発者がコードを管理しやすい単位にカプセル化して、ソリューションをモジュール化することにも役立ち、コードの再利用性が大幅に促進されます。 ストアド プロシージャの柔軟性をさらに高めるために、各ストアド プロシージャはパラメーターを受け入れることもできます。

SQL プールでは、簡素化され、合理化されたストアド プロシージャの実装が提供されます。 SQL Server との最大の違いは、ストアド プロシージャがプリコンパイル済みコードではないことです。

通常、データ ウェアハウスでは、大量のデータに対してクエリを実行するのにかかる時間に比べて、短いコンパイル時間で済みます。 より重要なことは、ストアド プロシージャのコードが大量のクエリに対して正しく最適化されることを確認することです。

> [!TIP]
> 目標は、数時間、数分、(数ミリ秒ではなく) 数秒節約することです。 したがって、ストアド プロシージャを SQL ロジックのコンテナーと考えると参考になります。

SQL プールでストアド プロシージャが実行されると、実行時に SQL ステートメントが解析され、変換され、最適化されます。 このプロセスの中で、各ステートメントが分散クエリに変換されます。 データに対して実行される SQL コードは、送信されるクエリとは異なります。

## <a name="nesting-stored-procedures"></a>ストアド プロシージャの入れ子

ストアド プロシージャが他のストアド プロシージャを呼び出すか、動的 SQL を実行する場合、内側のストアド プロシージャまたはコードの呼び出しは "入れ子になっている" といわれます。

SQL プールでは、最大 8 レベルの入れ子がサポートされます。 対照的に、SQL Server の入れ子レベルは 32 です。

最上位のストアド プロシージャの呼び出しは、入れ子レベル 1 になります。

```sql
EXEC prc_nesting
```

ストアド プロシージャから別の EXEC の呼び出しも行う場合、入れ子レベルは 2 に増えます。

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

次に 2 つ目の手順で何らかの動的 SQL を実行すると、入れ子レベルは 3 に増えます。

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL プールでは、現時点では [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) はサポートされていません。 このため、入れ子レベルを追跡する必要があります。 入れ子レベルの制限である 8 を超えることはほとんどありません。 ただし、超える場合は、入れ子レベルをこの制限内に収めるようにコードを修正する必要があります。

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL プールでは、INSERT ステートメントでストアド プロシージャの結果セットを使用することはできません。 ただし、使用できる別の方法があります。 例については、[一時テーブル](sql-data-warehouse-tables-temporary.md)の記事を参照してください。

## <a name="limitations"></a>制限事項

SQL プールで実装されない Transact-SQL ストアド プロシージャの側面がいくつかあります。

* 一時ストアド プロシージャ
* 番号付きストアド プロシージャ
* 拡張ストアド プロシージャ
* CLR ストアド プロシージャ
* 暗号化オプション
* レプリケーション オプション
* テーブル値パラメーター
* 読み取り専用パラメーター
* 既定のパラメーター
* 実行コンテキスト
* return ステートメント

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
