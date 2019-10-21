---
title: Oracle から Azure Database for PostgreSQL-Single Server へのオンライン移行に関する既知の問題と移行の制限事項に関する記事 | Microsoft Docs
description: Oracle から Azure Database for PostgreSQL へのオンライン移行に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: bc37c7cb11b973f89695b5631ec9569ff6b94608
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803912"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Oracle から Azure DB for PostgreSQL-Single Server へのオンライン移行に関する既知の問題と移行の制限事項

以降のセクションで、Oracle から Azure Database for PostgreSQL-Single Server へのオンライン移行に関する既知の問題点と制限事項について説明します。

## <a name="oracle-versions-supported-as-a-source-database"></a>ソース データベースとしてサポートされている Oracle のバージョン

Azure Database Migration Service では、以下への接続がサポートされています。

- Oracle バージョン 10g、11c および 12c。
- Oracle Enterprise、Standard、Express、および Personal Edition。

Azure Database Migration Service では、マルチテナント コンテナー データベース (CDB) への接続はサポートされていません。

## <a name="postgresql-versions-supported-as-a-target-database"></a>ターゲット データベースとしてサポートされている PostgreSQL のバージョン

Azure Database Migration Service では、Azure Database for PostgreSQL-Single Server バージョン 9.5、9.6、10 および 11 への移行がサポートされています。 Azure Database for PostgreSQL-Single Server で現在サポートされているバージョンの情報については、「[サポートされている PostgreSQL Database バージョン](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)」を参照してください。

## <a name="datatype-limitations"></a>データ型に関する制限事項

以下のデータ型は移行**できません**。

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- 入れ子になったテーブル
- ユーザー定義データ型
- メモ
- 仮想列
- ROWID 列に基づく具体化されたビュー

また、空の BLOB/CLOB 列はターゲットの NULL にマップされます。

## <a name="lob-limitations"></a>LOB に関する制限事項

- サイズ制限がある LOB モードが有効になっている場合、Oracle ソース上の空の LOB は NULL 値としてレプリケートされます。
- (30 バイトを超える) 長いオブジェクト名はサポートされていません。
- LONG および LONG RAW 列のデータは 64k を超えることはできません。 64k を超えるデータは切り捨てられます。
- Oracle 12 のみで、LOB 列に対する変更はサポート (移行) されません。
- XMLTYPE 列および LOB 列に対する UPDATE はサポート (移行) されません。

## <a name="known-issues-and-limitations"></a>既知の問題と制限

- 顧客の Oracle への接続には、SYSDB を使用する必要があります。
- パーティション/サブパーティションに対する操作 (ADD、DROP、EXCHANGE、および TRUNCATE) の結果のデータ変更は移行されず、次のエラーが発生する可能性があります。
  - ADD 操作で、追加されたデータを更新および削除した場合、"0 行が処理されました" の警告が返される場合があります。
  - DROP および TRUNCATE 操作で、新しい挿入によって "重複" エラーが発生する場合があります。
  - EXCHANGE 操作で、"0 行が処理されました" 警告と "重複" エラーの両方が発生する場合があります。
- 名前にアポストロフィが含まれるテーブルはレプリケートできません。
