---
title: データイン レプリケーション - Azure Database for MySQL
description: データイン レプリケーションを使用して、外部のサーバーから Azure Database for MySQL サービスに同期する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 18c53a53a57b3ddca1168fc1075ae09bcd86f000
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462498"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>データを Azure Database for MySQL にレプリケートする

データイン レプリケーションでは、外部の MySQL サーバーから Azure Database for MySQL サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。 データイン レプリケーションは、MySQL のネイティブなバイナリ ログ (binlog) ファイルの位置ベースのレプリケーションに基づいています。 binlog レプリケーションの詳細については、[MySQL binlog レプリケーションの概要](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)に関する記事を参照してください。 

## <a name="when-to-use-data-in-replication"></a>いつデータイン レプリケーションを使用するか
データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。

- **ハイブリッド データ同期:** データイン レプリケーションを使用して、オンプレミス サーバーと Azure Database for MySQL の間でデータの同期を維持できます。 この同期は、ハイブリッド アプリケーションを作成するのに役立ちます。 この方法は、既存のローカル データベース サーバーがあるが、エンドユーザーに近いリージョンにデータを移動する場合に魅力的です。
- **複数のクラウドの同期:** 複雑なクラウド ソリューションでは、データイン レプリケーションを使用して、Azure Database for MySQL と、別のクラウド プロバイダー 間でデータを同期します (これらのクラウドでホストされている仮想マシンとデータベース サービスが含まれます)。
 
移行シナリオについては、[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) (DMS) を使用してください。

## <a name="limitations-and-considerations"></a>制限と考慮事項

### <a name="data-not-replicated"></a>レプリケートされないデータ
マスター サーバー上の [*mysql システム データベース*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)はレプリケートされません。 マスター サーバーでのアカウントとアクセス許可の変更はレプリケートされません。 マスター サーバーでアカウントを作成し、そのアカウントでレプリカ サーバーにアクセスする必要がある場合は、レプリカ サーバー側で同じアカウントを手動で作成します。 システム データベースに含まれているテーブルの詳細については、[MySQL のマニュアル](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html)を参照してください。

### <a name="requirements"></a>必要条件
- マスター サーバーのバージョンは、MySQL バージョン 5.6 以上である必要があります。 
- マスター サーバーとレプリカ サーバーのバージョンは同じである必要があります。 たとえば、両方が MySQL 5.6 バージョンであるか、両方が MySQL バージョン 5.7 である必要があります。
- 各テーブルには主キーが必要です。
- マスター サーバーでは、MySQL InnoDB エンジンを使用する必要があります。
- ユーザーは、バイナリ ログの構成とマスター サーバーでの新しいユーザーの作成を実行できるアクセス許可を持っている必要があります。
- マスター サーバーで SSL が有効になっている場合は、ドメインに対して指定されている SSL CA 証明書が `mysql.az_replication_change_master` ストアド プロシージャに含まれていることを確認します。 次の[例](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication)と `master_ssl_ca` パラメーターを参照してください。
- マスター サーバーの IP アドレスが Azure Database for MySQL レプリカ サーバーのファイアウォール規則に追加されていることを確認します。 [Azure portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) または [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) を使用してファイアウォール規則を更新します。
- マスター サーバーをホストしているコンピューターで、ポート 3306 の受信トラフィックと送信トラフィックの両方が確実に許可されているようにします。
- マスター サーバーに**パブリック IP アドレス**があるか、DNS がパブリックにアクセスできるか、または完全修飾ドメイン名 (FQDN) を持っているようにしてください。

### <a name="other"></a>その他
- データイン レプリケーションは、General Purpose 価格レベルとメモリ最適化価格レベルでのみサポートされます。
- グローバル トランザクション ID (GTID) はサポートされていません。

## <a name="next-steps"></a>次のステップ
- [データイン レプリケーションを設定する](howto-data-in-replication.md)方法を確認する
- [読み取りレプリカを使用した Azure でのレプリケート](concepts-read-replicas.md)について確認する
- [DMS を使用して、最小限のダウンタイムでデータを移行する](howto-migrate-online.md)方法を確認する