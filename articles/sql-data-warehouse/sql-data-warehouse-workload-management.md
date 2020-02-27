---
title: ワークロードの管理
description: Azure SQL Data Warehouse でのワークロード管理の実装に関するガイダンス。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 01/13/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 287ad5467f9f3aac7eb8c9d7c19ea15c380c6879
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935408"
---
# <a name="what-is-workload-management"></a>ワークロード管理とは

混合ワークロードを実行すると、ビジー状態のシステムでリソースの問題が発生する可能性があります。  ソリューション アーキテクトは、従来のデータ ウェアハウス アクティビティ (データの読み込み、変換、クエリなど) を分離して、SLA の準拠に十分なリソースが確保されるようにするための方法を模索しています。  

物理サーバーの分離によって、インフラストラクチャが未活用、予約超過、またはキャッシュが継続的にハードウェアの開始と停止の準備をしている状態になり、使用率が低下する可能性があります。  良好なワークロード管理スキームでは、リソースが効率よく管理され、リソースが確実に有効活用され、投資収益率 (ROI) が最大化されます。

データ ウェアハウスのワークロードは、データ ウェアハウスに対して発生するすべての操作を指します。 これらのコンポーネントの深度や幅は、データ ウェアハウスの成熟度レベルによって異なります。  データ ウェアハウスのワークロードには次のようなものがあります。 
- ウェアハウスにデータを読み込むプロセス全体 
- データ ウェアハウスの分析とレポートの実行
- データ ウェアハウスのデータの管理 
- データ ウェアハウスからのデータのエクスポート

データ ウェアハウスのパフォーマンス容量は、[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)によって決まります。
- すべてのパフォーマンス プロファイルに割り当てられたリソースを確認するには、「[メモリと同時実行の制限](memory-concurrency-limits.md)」を参照してください。
- 容量を調整するには、[スケールアップまたはスケールダウン](quickstart-scale-compute-portal.md)してください。


## <a name="workload-management-concepts"></a>ワークロード管理の概念
以前は、[リソース クラス](resource-classes-for-workload-management.md)を通じて SQL Data Warehouse のクエリ パフォーマンスを管理していました。  リソース クラスがクエリにメモリを割り当てることができるかどうかは、ロールのメンバーシップに基づきます。  リソース クラスの主な課題は、構成が完了すると、ワークロードを制御するためのガバナンスや機能がなくなることです。  

たとえば、アドホック ユーザー ロールのメンバーシップを smallrc に付与すると、そのユーザーはシステム上のメモリの 100% を消費できました。  リソース クラスでは、リソースを予約して重要なワークロードで使用できるようにするための方法がありません。

SQL Data Warehouse のワークロード管理は、次の 3 つの大まかな概念で構成されています。[ワークロードの分類](sql-data-warehouse-workload-classification.md)、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)、そして[ワークロードの分離](sql-data-warehouse-workload-isolation.md)です。  これらの機能により、ワークロードによるシステム リソースの活用方法をより細かく制御できます。

ワークロードの分類は、ワークロード グループに要求を割り当て、重要度のレベルを設定するという概念です。  従来、この割り当ては、[sp_addrolemember](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management#change-a-users-resource-class) を使用してロールのメンバーシップを介して行われていました。  これを、[CREATE WORKLOAD CLASSIFER](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql) を使用して実行できるようになりました。  分類機能では、ラベル、セッション、要求を分類する時間などの豊富なオプションのセットが提供されます。

ワークロードの重要度は、要求がリソースにアクセスする順序に影響します。  ビジー状態のシステムでは、重要度の高い要求がリソースに最初にアクセスします。  重要度によって、ロックへの順次アクセスも保証されます。 

ワークロードの分離では、ワークロード グループのリソースが予約されます。  ワークロード グループに予約されているリソースは、そのワークロード グループのみで実行されるように保証されます。  ワークロード グループでは、リソース クラスと同様に、要求ごとに割り当てられるリソースの量を定義することもできます。  ワークロード グループを使用すると、一連の要求で消費できるリソースの量を予約したり上限を設定したりできます。  最後に、ワークロード グループは、クエリ タイムアウトなどのルールを要求に適用するためのメカニズムです。  


## <a name="next-steps"></a>次のステップ

- ワークロードの分類の詳細については、[ワークロードの分類](sql-data-warehouse-workload-classification.md)に関するページを参照してください。  
- ワークロードの分離の詳細については、[ワークロードの分離](sql-data-warehouse-workload-isolation.md)に関するページを参照してください。  
- ワークロードの重要度の詳細については、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に関するページを参照してください。  
- ワークロード管理の監視の詳細については、[ワークロード管理ポータルの監視](sql-data-warehouse-workload-management-portal-monitor.md)に関するページをご覧ください。  
