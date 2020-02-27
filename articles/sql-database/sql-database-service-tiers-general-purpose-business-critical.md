---
title: General Purpose と Business Critical
description: 記事では、仮想コア ベースの購入モデルにおける General Purpose と Business Critical のサービス レベルについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937836"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database サービス レベル

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99 パーセントの可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database では 3 つのサービス レベルが使用されており、それぞれアーキテクチャ モデルが異なります。 これらのサービス レベルは次のとおりです。

- [General Purpose](sql-database-service-tier-general-purpose.md)。予算重視のワークロード向けに設計されています。
- [Hyperscale](sql-database-service-tier-hyperscale.md)。ほとんどのビジネス ワークロード向けに設計されており、拡張性の高いストレージ、読み取りスケールアウト、およびデータベースの高速復元機能を提供します。
- [Business critical](sql-database-service-tier-business-critical.md)。これは障害に対する高い回復性と高速フェールオーバーを使用する、低待機時間のワークロード向けに設計されています。

この記事では、サービス レベル間の違いと、仮想コア ベースの購入モデルでの General Purpose と Business Critical のサービス レベルにおけるストレージとバックアップに関する考慮事項について説明します。

## <a name="service-tier-comparison"></a>サービス レベルの比較

次の表は、最新世代 (Gen5) のサービス サービス間の主な違いをまとめたものです。 サービス レベルの特徴は単一データベースとマネージド インスタンスで異なる場合があることにご留意ください。

| | リソースの種類 | General Purpose |  ハイパースケール | Business Critical |
|:---:|:---:|:---:|:---:|:---:|
| **最適な用途** | |  予算重視のバランスの取れたコンピューティングおよびストレージ オプションを提供します。 | ほとんどのビジネス ワークロード。 最大 100 TB までのストレージ サイズの自動スケーリング、垂直および水平方向へのなめらかなコンピューティング スケーリング、データベースの高速復元。 | トランザクション レートが高く IO 待ち時間が低い OLTP アプリケーション。 同期的に更新された複数のレプリカを使用して、最高の耐障害性と高速フェールオーバーを提供します。|
|  **リソースの種類で使用可能:** ||単一データベース/エラスティック プール/マネージド インスタンス | 単一データベース | 単一データベース/エラスティック プール/マネージド インスタンス |
| **コンピューティング サイズ**|単一データベース/エラスティック プール | 1 - 80 の仮想コア | 1 - 80 の仮想コア | 1 - 80 の仮想コア |
| | マネージド インスタンス | 4、8、16、24、32、40、64、80 の仮想コア | 該当なし | 4、8、16、24、32、40、64、80 の仮想コア |
| | マネージド インスタンスのプール | 2、4、8、16、24、32、40、64、80 の仮想コア | 該当なし | 該当なし |
| **ストレージの種類** | All | Premium リモート ストレージ (インスタンスあたり) | ローカル SSD キャッシュを使用して切り離したストレージ (インスタンスあたり) | 超高速ローカル SSD ストレージ (インスタンスあたり) |
| **データベースのサイズ** | 単一データベース/エラスティック プール | 5 GB – 4 TB | 最大 100 TB | 5 GB – 4 TB |
| | マネージド インスタンス  | 32 GB – 8 TB | 該当なし | 32 GB – 4 TB |
| **ストレージ サイズ** | 単一データベース/エラスティック プール | 5 GB – 4 TB | 最大 100 TB | 5 GB – 4 TB |
| | マネージド インスタンス  | 32 GB – 8 TB | 該当なし | 32 GB – 4 TB |
| **TempDB のサイズ** | 単一データベース/エラスティック プール | [仮想コアあたり 32 GB](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [仮想コアあたり 32 GB](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [仮想コアあたり 32 GB](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | マネージド インスタンス  | [仮想コアあたり 24 GB](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 該当なし | 最大 4 TB - [ストレージ サイズによる制限](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **ログ書き込みスループット** | 単一データベース | [仮想コアあたり 1875 MB/秒 (最大 30 MB/秒)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/秒 | [仮想コアあたり 6 MB/秒 (最大 96 MB/秒)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | マネージド インスタンス | [仮想コアあたり 3 MB/秒 (最大 22 MB/秒)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | 該当なし | [仮想コアあたり 4 MB/秒 (最大 48 MB/秒)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**可用性**|All| 99.99% |  [セカンダリ レプリカが 1 つで 99.95%、それ以上のレプリカで 99.99%](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99.99% <br/> [ゾーン冗長単一データベースで 99.995%](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**バックアップ**|All|RA-GRS、7 ～ 35 日 (既定では 7 日)| RA-GRS、7 日、一定時間で特定の時点に復旧 (PITR) | RA-GRS、7 ～ 35 日 (既定では 7 日) |
|**インメモリ OLTP** | | 該当なし | 該当なし | 利用可能 |
|**読み取り専用レプリカ**| | 0 組み込み <br> 0 - 4 [geo レプリケーション](sql-database-active-geo-replication.md)を使用 | 0 - 4 組み込み | 1 組み込み、価格に含まれます <br> 0 - 4 [geo レプリケーション](sql-database-active-geo-replication.md)を使用 |
|**価格/課金** | 単一データベース | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対しては請求されません。 | [レプリカごとの仮想コアと使用されたストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対してはまだ請求されません。 | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/single/)に対して請求されます。 <br/>IOPS に対しては請求されません。 |
|| マネージド インスタンス | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/managed/)に対して請求されます。 <br/>IOPS に対しては請求されません。| 該当なし | [仮想コア、予約ストレージ、バックアップ ストレージ](https://azure.microsoft.com/pricing/details/sql-database/managed/)に対して請求されます。 <br/>IOPS に対しては請求されません。| 
|**割引モデル**| | [予約インスタンス](sql-database-reserved-capacity.md)<br/>[Azure ハイブリッド特典](sql-database-azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション| [Azure ハイブリッド特典](sql-database-azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション| [予約インスタンス](sql-database-reserved-capacity.md)<br/>[Azure ハイブリッド特典](sql-database-azure-hybrid-benefit.md) (開発テスト サブスクリプションでは利用不可)<br/>[Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) および [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0023p/) (従量課金制) Dev/Test (開発テスト) サブスクリプション|

詳細については、[単一データベース (仮想コア)](sql-database-vcore-resource-limits-single-databases.md)、[単一データベース プール (仮想コア)](sql-database-dtu-resource-limits-single-databases.md)、[単一データベース (DTU)](sql-database-dtu-resource-limits-single-databases.md)、[単一データベース プール (DTU)](sql-database-dtu-resource-limits-single-databases.md)、[マネージド インスタンス](sql-database-managed-instance-resource-limits.md)に関するページでサービス レベル間の細かな違いを確認してください。

> [!NOTE]
> 仮想コア ベースの購入モデルにおける Hyperscale サービス レベルの詳細については、[Hyperscale サービス レベル](sql-database-service-tier-hyperscale.md)に関するページを参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-purchase-models.md)に関する記事をご覧ください。

## <a name="data-and-log-storage"></a>データとログのストレージ

次の要因は、データ ファイルとログ ファイルに使用されるストレージの量に影響し、General Purpose と Business Critical に適用されます。 Hyperscale のデータ ストレージとログ ストレージの詳細については、「[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)」を参照してください。

- 割り当てられたストレージは、データ ファイル (MDF) およびログ ファイル (LDF) によって使用されます。
- 各単一データベースのコンピューティング サイズでは、最大データベース サイズがサポートされ、既定の最大サイズは 32 GB です。
- 必要な単一データベース サイズ (MDF ファイルのサイズ) を構成するとき、LDF ファイルをサポートするために、30% 以上の追加ストレージが自動的に追加されます
- SQL Database マネージド インスタンスでのストレージ サイズは、32 GB の倍数で指定する必要があります。
- 10 GB とサポートされている最大値の間で、任意の単一データベース サイズを選択できます。
  - Standard または General Purpose サービス レベルでのストレージの場合は、10 GB 単位でサイズを増減します。
  - Premium または Business Critical サービス レベルでのストレージの場合は、250 GB 単位でサイズを増減します。
- General Purpose サービス レベルでは、`tempdb` は接続されている SSD を使用します。このストレージ コストは、仮想コアの価格に含まれます。
- Business Critical サービス レベルでは、`tempdb` は、MDF ファイルおよび LDF ファイルと接続されている SSD を共有します。`tempdb` ストレージ コストは、仮想コアの価格に含まれます。

> [!IMPORTANT]
> MDF および LDF ファイルに割り当てられている合計ストレージに対して課金されます。

MDF および LDF ファイルの現在の合計サイズを監視するには、[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) を使用します。 個々の MDF ファイルおよび LDF ファイルの現在のサイズを監視するには、[sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) を使用します。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="backups-and-storage"></a>バックアップとストレージ

データベース バックアップ用のストレージは、SQL Database のポイントインタイム リストア (PITR) および[長期リテンション期間 (LTR)](sql-database-long-term-retention.md) 機能をサポートするために割り当てられます。 このストレージはデータベースごとに別個に割り当てられ、データベース料金ごとに 2 つが個々に課金されます。

- **PITR**:個々のデータベース バックアップは、[読み取りアクセス geo 冗長ストレージ (RA-GRS) ストレージ](../storage/common/storage-designing-ha-apps-with-ragrs.md)に自動的にコピーされます。 ストレージ サイズは、新しいバックアップが作成されるにつれて、動的に増大します。 ストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 ストレージの使用量は、データベースの変化率とバックアップのリテンション期間に応じて異なります。 リテンション期間は、データベースごとに 7 ～ 35 日の範囲内で別々に構成できます。 データベース サイズの 100% (1 倍) に等しい最小ストレージ量は、追加料金なしで提供されます。 ほとんどのデータベースでは、この容量で十分に 7 日間のバックアップを格納できます。
- **LTR**:SQL Database には、最大 10 年の完全バックアップの長期保有を構成するオプションが用意されています。 LTR ポリシーを設定した場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なるリテンション期間を選択することができます。 選択した構成によって、LTR バックアップに使用されるストレージ容量が決まります。 LTR ストレージのコストを見積もるには、LTR 料金計算ツールを使用できます。 詳細については、[SQL Database の長期保存](sql-database-long-term-retention.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

- General Purpose と Business Critical サービス レベルでの単一データベースにおいて利用できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md)に関するページを参照してください。
- General Purpose と Business Critical サービス レベルでのエラスティック プールにおいて利用できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database 仮想コア ベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md)に関するページを参照してください。
