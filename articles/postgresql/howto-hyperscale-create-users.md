---
title: ユーザーの作成 - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) と対話する新しいユーザー アカウントを作成する方法について説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 1/8/2019
ms.openlocfilehash: 674fd4372bdf7c3782d18aaf04b48eb0067a9b2e
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484929"
---
# <a name="create-users-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus) でユーザーを作成する

> [!NOTE]
> "ユーザー" という用語は、Hyperscale (Citus) サーバー グループ内のユーザーを指します。 Azure サブスクリプションのユーザーとその特権について確認する場合は、[Azure ロール ベース アクセス制御 (RBAC) に関する記事](../role-based-access-control/built-in-roles.md)を参照するか、または[ロールのカスタマイズ方法](../role-based-access-control/custom-roles.md)について確認してください。

## <a name="the-server-admin-account"></a>サーバー管理者アカウント

PostgreSQL エンジンは[ロール](https://www.postgresql.org/docs/current/sql-createrole.html)を使用してデータベース オブジェクトへのアクセスを制御し、新しく作成された Hyperscale (Citus) サーバー グループにはいくつかのロールが事前に定義されています。

* [既定の PostgreSQL ロール](https://www.postgresql.org/docs/current/default-roles.html)
* `azure_pg_admin`
* `postgres`
* `citus`

Hyperscale はマネージド PaaS サービスであるため、Microsoft だけが `postgres` スーパー ユーザー ロールでサインインできます。 制限付き管理アクセスの場合は、Hyperscale によって `citus` ロールが提供されます。

`citus` ロールのアクセス許可:

* すべての構成変数を読み取ります。これには、通常はスーパーユーザのみが参照できる変数も含まれます。
* すべての pg\_stat\_\* ビューを読み取り、さまざまな統計情報に関連する拡張機能を使用します。これには、通常はスーパーユーザーのみが参照できるビューや拡張機能も含まれます。
* テーブルにアクセス共有ロックを行う可能性のある複数の監視関数を実行します。これは、長時間かかる可能性があります。
* [PostgreSQL 拡張機能を作成します](concepts-hyperscale-extensions.md) (ロールが `azure_pg_admin` のメンバーであるため)。

特に、`citus` ロールにはいくつかの制限があります。

* ロールを作成できない
* データベースを作成できない

## <a name="how-to-create-additional-user-roles"></a>追加のユーザー ロールを作成する方法

前述したように、`citus` 管理者アカウントには、追加のユーザーを作成するアクセス許可がありません。 ユーザーを追加するには、Azure portal インターフェイスを使用します。

1. Hyperscale サーバー グループの **[ロール]** ページに移動して、 **[+ 追加]** をクリックします。

   ![[ロール] ページ](media/howto-hyperscale-create-users/1-role-page.png)

2. ロール名とパスワードを入力します。 **[保存]** をクリックします。

   ![ロールの追加](media/howto-hyperscale-create-users/2-add-user-fields.png)

ユーザーは、サーバー グループのコーディネーター ノード上に作成され、すべてのワーカー ノードに伝達されます。 Azure portal によって作成されたロールには `LOGIN` 属性があります。つまり、これらはデータベースにサインインできる実際のユーザーであることを意味します。

## <a name="how-to-modify-privileges-for-user-role"></a>ユーザー ロールの特権を変更する方法

新しいユーザー ロールは一般に、制限された特権でデータベースへのアクセスを提供するために使用されます。 ユーザーの特権を変更するには、PgAdmin や psql などのツールを使用して、標準的な PostgreSQL コマンドを使用します。 (Hyperscale (Citus) クイックスタートにある [psql を使用した接続](quickstart-create-hyperscale-portal.md#connect-to-the-database-using-psql)に関するセクションを参照してください。)

たとえば、`db_user` に `mytable` の読み取りを許可するには、次のようにアクセス許可を付与します。

```sql
GRANT SELECT ON mytable TO db_user;
```

Hyperscale (Citus) で、単一テーブルの GRANT ステートメントがクラスター全体に伝達され、すべてのワーカー ノードに適用されます。 ただし、システム全体 (スキーマ内のすべてのテーブルなど) の GRANT の場合は、すべての日付ノード上で実行する必要があります。  ヘルパー関数 `run_command_on_workers()` を使用します。

```sql
-- applies to the coordinator node
GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;

-- make it apply to workers as well
SELECT run_command_on_workers(
  'GRANT SELECT ON ALL TABLES IN SCHEMA public TO db_user;'
);
```

## <a name="how-to-delete-a-user-role-or-change-their-password"></a>ユーザー ロールの削除またはパスワードの変更方法

ユーザーを更新するには、Hyperscale サーバー グループの **[ロール]** ページに移動して、ユーザーの横にある省略記号 **[...]** をクリックします。 省略記号をクリックすると、ユーザーの削除やパスワードのリセットのためのメニューが表示されます。

   ![ロールを編集する](media/howto-hyperscale-create-users/edit-role.png)

`citus` ロールには特権が与えられているため、削除できません。

## <a name="next-steps"></a>次のステップ

新しいユーザーのマシンの IP アドレスについてファイアウォールを開き、接続できるようにします。[Azure portal を使用して Hyperscale (Citus) ファイアウォール規則を作成し管理します](howto-hyperscale-manage-firewall-using-portal.md)。

データベース ユーザー アカウントの管理の詳細については、PostgreSQL の製品マニュアルを参照してください。

* [データベース ロールと特権](https://www.postgresql.org/docs/current/static/user-manag.html)
* [GRANT 構文](https://www.postgresql.org/docs/current/static/sql-grant.html)
* [特権](https://www.postgresql.org/docs/current/static/ddl-priv.html)
