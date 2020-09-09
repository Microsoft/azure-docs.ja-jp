---
title: Azure Cosmos DB での配列とオブジェクトの操作
description: Azure Cosmos DB で配列とオブジェクトを作成するための SQL 構文について説明します。 またこの記事では、配列オブジェクトに対して操作を実行するいくつかの例も示します
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090775"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Azure Cosmos DB での配列とオブジェクトの操作

Azure Cosmos DB SQL API の重要な機能は、配列とオブジェクトの作成です。

## <a name="arrays"></a>配列

次の例に示されているように配列を作成できます。

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

結果は次のようになります。

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

[ARRAY 式](sql-query-subquery.md#array-expression)を使用して、[サブクエリ](sql-query-subquery.md)の結果から配列を作成することもできます。 このクエリは、配列内の子の個別名をすべて取得します。

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>反復

SQL API では、FROM ソースの [IN キーワード](sql-query-keywords.md#in)で追加される新しいコンストラクトによって、JSON 配列に対する反復がサポートされています。 次の例では

```sql
    SELECT *
    FROM Families.children
```

結果は次のようになります。

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

次のクエリは、`Families`コンテナー内の `children` への反復を実行しています。 出力配列は、前のクエリと異なります。 この例では、 `children` を分割し、結果を 1 つの配列にフラット化しています。  

```sql
    SELECT *
    FROM c IN Families.children
```

結果は次のようになります。

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

以下の例のように、配列の個々のエントリをさらにフィルターすることができます。

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

結果は次のようになります。

```json
    [{
      "givenName": "Lisa"
    }]
```

配列の反復処理の結果に対して集計することもできます。 たとえば、次のクエリは、すべての家族の子どもの数を合計します。

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

結果は次のようになります。

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>次のステップ

- [作業の開始](sql-query-getting-started.md)
- [Azure Cosmos DB .NET のサンプル](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [結合](sql-query-join.md)