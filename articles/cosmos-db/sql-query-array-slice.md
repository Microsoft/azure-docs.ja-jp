---
title: Azure Cosmos DB クエリ言語の ARRAY_SLICE
description: Azure Cosmos DB での SQL システム関数 ARRAY_SLICE について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1df4177bb8b56bc98977af0f5180e8df5affb257
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348550"
---
# <a name="array_slice-azure-cosmos-db"></a>ARRAY_SLICE (Azure Cosmos DB)
 配列式の一部を返します。
  
## <a name="syntax"></a>構文
  
```sql
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   任意の配列式です。  
  
*num_expr*  
   配列を開始する位置を示す 0 から始まる数値インデックス。 配列内の最後の要素を基準とした開始インデックスを指定するために負の値が使用されることがあり、つまり、-1 は配列の最後の要素を参照します。  

*num_expr* 結果の配列に要素の最大数を設定するオプションの数値式。    

## <a name="return-types"></a>戻り値の型
  
  配列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ARRAY_SLICE` を使用して、配列の別のスライスを取得する方法を示します。  
  
```sql
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 結果セットは次のようになります。  
  
```json
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
