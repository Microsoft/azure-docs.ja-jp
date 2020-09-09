---
title: 照合順序のサポート
description: Azure Synapse SQL でサポートされる照合順序の種類
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 1099c4e4dd69a8dc8caee96ec5dda633ce8b9d12
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496204"
---
# <a name="database-collation-support-for-synapse-sql"></a>Synapse SQL でのデータベースの照合順序のサポート

照合順序では、文字ベースのデータ型に対するロケール、コード ページ、並べ替え順序、および大文字と小文字の区別の規則を指定します。 選択されると、照合順序情報を必要とするすべての列と式が、選択された照合順序をデータベース設定から継承します。 既定の継承は、文字ベースのデータ型に対する別の照合順序を明示的に宣言することによって上書きできます。

新しい SQL プール データベースを作成するときに、Azure portal から既定のデータベースの照合順序を変更できます。 この機能により、サポートされる 3800 個のデータベースの照合順序のいずれかを使用して、新しいデータベースをさらに簡単に作成できるようになりました。

CREATE DATABASE ステートメントを使用した作成時には、既定の Synapse SQL オンデマンドデータベースの照合順序を指定できます。

## <a name="change-collation"></a>照合順序を変更する
SQL プール データベースの既定の照合順序を変更するには、単純にプロビジョニング エクスペリエンスで [Collation] フィールドを更新します。 たとえば、既定の照合順序を大文字と小文字が区別されるように変更する場合は、単純に [Collation] の名前を SQL_Latin1_General_CP1_CI_AS から SQL_Latin1_General_CP1_CS_AS に変更します。 

SQL オンデマンド データベースの既定の照合順序を変更するには、ALTER DATABASE ステートメントを使用できます。

## <a name="list-of-unsupported-collation-types"></a>サポートされていない照合順序の種類の一覧
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

また、SQL プールでは、次の照合順序の種類はサポートされていません。

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>現在の照合順序を確認する
データベースの現在の照合順序を確認するには、次の T-SQL スニペットを実行できます。
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
プロパティのパラメーターとして "Collation" が渡された場合、DatabasePropertyEx 関数から現在データベースで指定されている照合順序が返されます。 DatabasePropertyEx 関数の詳細については、MSDN で学習できます。

## <a name="next-steps"></a>次のステップ

SQL プールと SQL オンデマンドのベスト プラクティスに関する追加情報については、次の記事を参照してください。

- [SQL プールのベスト プラクティス](best-practices-sql-pool.md)
- [SQL オンデマンドのベスト プラクティス](best-practices-sql-on-demand.md)


