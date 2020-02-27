---
title: 既知の問題:Azure Database for MySQL への オンライン移行
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用するときの、Azure Database for MySQL へのオンライン移行に関する既知の問題と移行の制限事項について説明します。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: afbff1c0b001d00f2791a869850729171782701c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650250"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Azure Database Migration Service を使用した Azure DB for MySQL へのオンライン移行の問題と制限事項

MySQL から Azure Database for MySQL へのオンライン移行に関する既知の問題点と制限事項について、後続のセクションで説明します。

## <a name="online-migration-configuration"></a>オンライン移行の構成


- ソースの MySQL サーバーのバージョンが 5.6.35、5.7.18、またはそれ以降のバージョンである必要があります
- Azure Database for MySQL では、次の項目をサポートしています。
  - MySQL コミュニティ エディション
  - InnoDB エンジン
- 同じバージョン間の移行。 MySQL 5.6 から Azure Database for MySQL 5.7 への移行はサポートされません。
- my.ini (Windows) または my.cnf (Unix) のバイナリ ログを有効にします
  - Server_id=1 のように、Server_id を 1 以上に設定します (MySQL 5.6 の場合のみ)
  - log-bin = \<パス> を設定します (MySQL 5.6 の場合のみ)
  - binlog_format = row を設定します
  - Expire_logs_days = 5 (推奨 - MySQL 5.6 の場合のみ)
- ユーザーが ReplicationAdmin ロールを持つ必要があります。
- ソース MySQL データベースに定義された照合順序は、ターゲット Azure Database for MySQL で定義された照合順序と同じです。
- スキーマは、Azure Database for MySQL のソース MySQL データベースとターゲット データベースの間で一致する必要があります。
- ターゲット Azure Database for MySQL のスキーマに外部キーを含めることはできません。 外部キーを削除するには、次のクエリを使用します。
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    クエリの結果内の外部キー削除 (2 列目) を実行します。
- ターゲット Azure Database for MySQL のスキーマにトリガーを含めることはできません。 ターゲット データベース内のトリガーを削除するには、次のように指定します。
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>データ型に関する制限事項

- **制限事項**:ソースの MySQL データベースに JSON データ型がある場合、移行は、継続的同期中に失敗します。

    **回避策**:ソース MySQL データベース内の JSON データ型を medium text または longtext に変更します。

- **制限事項**:テーブルに主キーがない場合、継続的同期は失敗します。

    **回避策**:移行を続行するには、テーブルの主キーを一時的に設定します。 データの移行が完了した後は、主キーを削除できます。

## <a name="lob-limitations"></a>LOB に関する制限事項

ラージ オブジェクト (LOB) 列は、サイズが大きくなる可能性のある列です。 MySQL の場合、LOB のデータ型には、Medium text、Longtext、BLOB、Mediumblob、Longblob などがあります。

- **制限事項**:LOB のデータ型を主キーとして使用すると、移行は失敗します。

    **回避策**:主キーを、LOB ではない他のデータ型または列に置き換えます。

- **制限事項**:ラージ オブジェクト (LOB) 列の長さが 32 KB を超える場合、ターゲットにおいてデータが切り捨てられることがあります。 次のクエリを使用して、LOB 列の長さを確認できます。
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **回避策**:32 KB を超える LOB 列がある場合は、[Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com) でエンジニアリング チームに相談してください。

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>AWS RDS MySQL からオンラインで移行するときの制限事項

AWS RDS MySQL から Azure Database for MySQL へのオンライン移行を実行しようとすると、次のエラーが発生する場合があります。

- **エラー:** データベース '{0}' はターゲットに外部キーを持っています。 ターゲットを修正し、新しいデータ移行アクティビティを開始します。 ターゲットで次のスクリプトを実行して、外部キーを表示します。

  **制限事項**:スキーマに外部キーが含まれている場合、移行の初回の読み込みと継続的同期は失敗します。
  **回避策**:MySQL Workbench で次のスクリプトを実行して、外部キー削除スクリプトと外部キー追加スクリプトを抽出します。

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **エラー:** Database '{0}' does not exists on server.\(データベース '{0}' はサーバーに存在しません。\) Provided MySQL source server is case sensitive.\(指定された MySQL ソース サーバーでは大文字と小文字が区別されます。\) Please check the database name.\(データベース名を確認してください。\)

  **制限事項**:コマンド ライン インターフェイス (CLI) を使用して MySQL データベースを Azure に移行するときに、このエラーが発生する場合があります。 サービスはソース サーバーでデータベースを見つけることができませんでした。データベース名が正しく指定されていないか、一覧表示されたサーバーにデータベースが存在しない可能性があります。 データベース名では大文字と小文字が区別されることに注意してください。

  **回避策**:正確なデータベース名を指定して、再試行してください。

- **エラー:** There are tables with the same name in the database '{database}'.\(データベース '{database}' に同じ名前のテーブルがあります。\) Azure Database for MySQL does not support case sensitive tables.\(Azure Database for MySQL では、大文字と小文字を区別するテーブルはサポートされていません。\)

  **制限事項**:このエラーは、ソース データベースに同じ名前の 2 つのテーブルがある場合に発生します。 Azure Database for MySQL では、大文字と小文字を区別するテーブルはサポートされていません。

  **回避策**:テーブル名を一意になるように更新して、再試行してください。

- **エラー:** ターゲット データベース {database} が空です。 スキーマを移行してください。

  **制限事項**:このエラーは、ターゲットとなる Azure Database for MySQL データベースに必要なスキーマがない場合に発生します。 データをターゲットに移行できるようにするには、スキーマの移行が必要です。

  **回避策**:ソース データベースからターゲット データベースに[スキーマを移行](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema)してください。

## <a name="other-limitations"></a>その他の制限事項

- 先頭と末尾に中かっこ {} を含むパスワード文字列はサポートされていません。 この制限は、ソース MySQL とターゲット Azure Database for MySQL の両方への接続に適用されます。
- 次の DDL はサポートされていません。
  - すべてのパーティション DDL
  - テーブルの削除
  - テーブルの名前変更
- *alter table <テーブル名> add column <列名>* ステートメントを使用してテーブルの先頭または中間に列を追加することはサポートされていません。 *alter table <テーブル名> add column <列名>* を実行すると、テーブルの末尾に列が追加されます。
- 列データの一部のみに対して作成されたインデックスはサポートされていません。 次のステートメントは、列データの一部のみを使用してインデックスを作成する一例です。

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Azure Database Migration Service では、1 回の移行アクティビティで移行できるデータベースは最大で 4 個です。

- **エラー:** 行サイズが大きすぎます (> 8126)。 一部の列をテキストまたは BLOB に変更すると役立つ場合があります。 現在の行形式では、0 バイトの BLOB プレフィックスがインラインで格納されます。

  **制限事項**:このエラーは、InnoDB ストレージ エンジンを使用して Azure Database for MySQL に移行しているときに、テーブルの行サイズが大きすぎる (> 8126 バイト) 場合に発生します。

  **回避策**:行のサイズが 8126 バイトを超えるテーブルのスキーマを更新します。 データが切り捨てられるため、厳格モードを変更することはお勧めしません。 page_size の変更はサポートされていません。
