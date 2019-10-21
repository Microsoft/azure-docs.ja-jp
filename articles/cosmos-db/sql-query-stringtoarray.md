---
title: Azure Cosmos DB クエリ言語での StringToArray
description: Azure Cosmos DB の SQL システム関数 StringToArray について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349281"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 配列に変換された式を返します。 式を変換できない場合は、undefined を返します。  
  
## <a name="syntax"></a>構文
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   JSON 配列式として解析される文字列式です。 
  
## <a name="return-types"></a>戻り値の型
  
  配列式または undefined を返します。 
  
## <a name="remarks"></a>解説
  入れ子になった文字列値を有効な JSON にするには二重引用符で囲む必要があります。 JSON 形式の詳細については、「[json.org](https://json.org/)」をご覧ください。
  
## <a name="examples"></a>例
  
  次の例は、異なる型間で `StringToArray` がどのように動作するかを示します。 
  
 有効な入力を使用した例を次に示します。

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

結果セットは次のようになります。

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

無効な入力を使用した例を次に示します。 
   
 配列内に一重引用符を使用した場合は、有効な JSON ではありません。
クエリ内で有効であっても、有効な配列として解析されません。 配列文字列内の文字列は、"[\\"\\"]" のようにエスケープするか、'[""]' のように一重引用符で囲む必要があります。

```sql
SELECT
    StringToArray("['5','6','7']")
```

結果セットは次のようになります。

```json
[{}]
```

無効な入力の例を次に示します。
   
 渡された式は JSON 配列として解析されます。次の場合は、配列型として評価されないため、undefined が返されます。
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

結果セットは次のようになります。

```json
[{}]
```

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
