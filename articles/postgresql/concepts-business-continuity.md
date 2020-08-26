---
title: ビジネス継続性 - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Database for PostgreSQL を使用する場合のビジネス継続性 (ポイント インタイム リストア、データ センターの停止、geo リストア、レプリカ) について説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/07/2020
ms.openlocfilehash: b14eba63d848b5f583e16b39f3ade6bd7e7ba83f
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031202"
---
# <a name="overview-of-business-continuity-with-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server でのビジネス継続性の概要

この概要では、Azure Database for PostgreSQL に用意されているビジネス継続性とディザスター リカバリーの機能について説明します。 また、データ損失につながる、またはデータベースやアプリケーションを使用不能状態に追い込む破壊的なイベントから復旧するためのオプションについて説明します。 ユーザーまたはアプリケーション エラーがデータ整合性に影響を及ぼすとき、Azure リージョンでシステム停止が発生したとき、あるいはアプリケーションにメンテナンスが必要なときの対処方法について説明します。

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>ビジネス継続性を提供するときに使用できる機能

Azure Database for PostgreSQL で提供されるビジネス継続性機能には、自動バックアップや、ユーザーによる geo リストア開始機能などが含まれます。 機能ごとに、推定復旧時間 (ERT) と潜在的なデータ損失に関する特性が異なります。 これらのオプションについて理解した後は、その中から適切なものを選んで、さまざまなシナリオに対して組み合わせて使うことができます。 ビジネス継続性計画を開発するときは、破壊的なイベントが発生してから、アプリケーションが完全に復旧するまでの最大許容時間について理解する必要があります。これが目標復旧時間 (RTO) です。 さらに、破壊的なイベントの発生後、復旧中にアプリケーションが損失を許容できる最大データ更新 (期間) 量についても理解しなければなりません。これは目標復旧時点 (RPO) です。

次の表は、利用できる機能の ERT と RPO を比較したものです。

| **機能** | **Basic** | **汎用** | **メモリの最適化** |
| :------------: | :-------: | :-----------------: | :------------------: |
| バックアップからのポイントインタイム リストア | リテンション期間内の任意の復元ポイント | リテンション期間内の任意の復元ポイント | リテンション期間内の任意の復元ポイント |
| Geo レプリケーション バックアップからの geo リストア | サポートされていません | ERT < 12 時間<br/>RPO < 1 時間 | ERT < 12 時間<br/>RPO < 1 時間 |

[読み取りレプリカ](concepts-read-replicas.md)の使用も検討してください。

## <a name="recover-a-server-after-a-user-or-application-error"></a>ユーザーまたはアプリケーション エラーの後でサーバーを復旧する

サービスのバックアップを使って、さまざまな破壊的イベントからサーバーを復旧できます。 一部のデータ、重要なテーブル、そしてデータベース全体さえも、うっかり削除してしまう場合があります。 アプリケーションの欠陥などにより、正しいデータが不良データによって誤って上書きされることもあります。

**ポイントインタイム リストア**を実行して、正常であることがわかっている時点のサーバーのコピーを作成できます。 この時点は、サーバーに対して構成されているバックアップ リテンション期間内でなければなりません。 新しいサーバーにデータを復元した後は、元のサーバーを新しく復元したサーバーに置き換えたり、復元したサーバーから元のサーバーに必要なデータをコピーしたりできます。

> [!IMPORTANT]
> 削除したサーバーは、復元**できません**。 サーバーを削除すると、そのサーバーに属するデータベースもすべて削除され、復元できなくなります。 [Azure リソース ロック](../azure-resource-manager/management/lock-resources.md)を使用すると、サーバーが誤って削除されるのを防ぐことができます。

## <a name="recover-from-an-azure-data-center-outage"></a>Azure データ センターの停止から復旧する

まれではありますが、Azure データ センターが停止することもあります。 停止が発生すると、ビジネスが中断します。この中断はわずか数分で解消されることもありますが、数時間に及ぶ場合もあります。

オプションの 1 つは、データ センターの停止が終了し、サーバーがオンラインに戻るのを待つことです。 開発環境など、サーバーがしばらくオフラインになっていてもかまわないアプリケーションの場合はこの方法が使えます。 データ センターが停止したときは、復旧までの時間を予測できないため、このオプションはしばらくサーバーが必要ない場合にのみ有効です。

## <a name="geo-restore"></a>geo リストア

geo リストア機能では、geo 冗長バックアップを使用してサーバーを復元します。 バックアップは、サーバーの[ペアになっているリージョン](../best-practices-availability-paired-regions.md)でホストされます。 これらのバックアップから他の任意のリージョンに復元することができます。 geo リストアでは、バックアップからのデータを使用して新しいサーバーが作成されます。 geo リストアの詳細については、[バックアップと復元の概念に関する記事](concepts-backup.md)を参照してください。

> [!IMPORTANT]
> geo リストアは、geo 冗長バックアップ ストレージでサーバーをプロビジョニングした場合にのみ可能です。 既存のサーバーに対してローカル冗長を geo 冗長バックアップに切り替える場合は、既存のサーバーの pg_dump を使用してダンプを取得し、geo 冗長バックアップで構成された新しく作成したサーバーに復元する必要があります。

## <a name="cross-region-read-replicas"></a>リージョンにまたがる読み取りレプリカ
リージョンにまたがる読み取りレプリカを使用すると、事業継続とディザスター リカバリーの計画を強化できます。 読み取りレプリカは、PostgreSQL の物理的なレプリケーション テクノロジを使用して非同期的に更新されます。 読み取りレプリカ、利用可能なリージョン、フェールオーバーする方法については、[読み取りレプリカの概念に関する記事](concepts-read-replicas.md)を参照してください。 

## <a name="faq"></a>よく寄せられる質問
### <a name="where-does-azure-database-for-postgresql-store-customer-data"></a>Azure Database for PostgreSQL は顧客データをどこに格納しますか?
既定では、Azure Database for PostgreSQL によって、デプロイされているリージョン外に顧客データが移動または格納されることはありません。 ただし、顧客は必要に応じて、[地理冗長バックアップ](concepts-backup.md#backup-redundancy-options)を有効にするか、別のリージョンにデータを格納するための[クロスリージョン読み取りレプリカ](concepts-read-replicas.md#cross-region-replication)を作成することを選択できます。


## <a name="next-steps"></a>次のステップ
- [Azure Database for PostgreSQL での自動バックアップ](concepts-backup.md)の詳細を確認する。 
- [Azure portal](howto-restore-server-portal.md) または [Azure CLI](howto-restore-server-cli.md) を使用して復元する方法を確認する。
- [Azure Database for PostgreSQL の読み取りレプリカ](concepts-read-replicas.md)について確認する。
