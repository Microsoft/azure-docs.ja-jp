---
title: サポートされているバージョン - Azure Database for MySQL
description: Azure Database for MySQL サービスでサポートされている MySQL サーバーのバージョンについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 920f6a4fec1ec8a260a98641888268e4955bbf44
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718779"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>サポートされている Azure Database for MySQL サーバー バージョン

Azure Database for MySQL は、InnoDB ストレージ エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) から開発されました。 このサービスは、コミュニティでサポートされている現在のメジャー バージョン (MySQL 5.6、5.7、8.0) をすべてサポートしています。 MySQL では、X.Y.Z の名前付けスキームが使用されています。ここで、X はメジャー バージョン、Y はマイナー バージョン、Z はバグ修正リリースです。 スキームの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)をご覧ください。

> [!NOTE]
> シングルサーバー デプロイ オプションでは、ゲートウェイを使用してサーバー インスタンスに接続をリダイレクトします。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。

Azure Database for MySQL では現在、MySQL の次のメジャーおよびマイナー バージョンがサポートされています。


| Version | シングル サーバー <br/> 現行のマイナー バージョン |フレキシブル サーバー (プレビュー) <br/> 現行のマイナー バージョン  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL バージョン 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) | サポートされていません|
|MySQL バージョン 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL バージョン 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、バグ修正バージョンの更新プログラムの適用が自動管理されます。 たとえば、5.7.20 から 5.7.21 などです。  

現在、メジャー バージョンのアップグレードは、MySQL v5.6 から v5.7 へのアップグレードがサポートされています。 詳細については、[メジャー バージョンのアップグレードの実行方法](how-to-major-version-upgrade.md)に関する記事を参照してください。 5\.7 から 8.0 にアップグレードする場合は、[ダンプを実行し、新しいエンジンのバージョンで作成されたサーバーにそれを復元](./concepts-migrate-dump-restore.md)することをお勧めします。

## <a name="next-steps"></a>次のステップ

- Azure Database for MySQL のバージョン管理ポリシーの詳細については、[このドキュメント](concepts-version-policy.md)を参照してください。
- **サービス レベル** に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください
