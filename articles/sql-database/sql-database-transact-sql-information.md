---
title: Azure SQL Database への移行時に T-SQL の相違点を解決する | Microsoft Docs
description: Azure SQL Database では完全にサポートされない Transact-SQL ステートメント
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/03/2018
ms.openlocfilehash: fbc4628ff3d3d7d90f7ec2c47c87f7afa3e9cd43
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028837"
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>SQL Database への移行時に Transact-SQL の相違点を解決する

SQL Server から Azure SQL サーバーに[データベースを移行](sql-database-single-database-migrate.md)する場合、SQL Server を移行する前にデータベースの再構築が必要だと気づくことがあります。 この記事では、再構築を実行し、再構築が必要な根本的な原因を理解するうえで役立つガイダンスを提供します。 非互換性を検出するには、[データ移行アシスタント (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) を使用します。

## <a name="overview"></a>概要

アプリケーションが使用する Transact-SQL 機能の大半は、Microsoft SQL Server と Azure SQL Database の両方で完全にサポートされます。 たとえば、データ型、演算子、文字列、算術演算子、論理、およびカーソル機能などのコア SQL コンポーネントは、SQL Server および SQL Database で同様に動作します。 ただし、DDL (データ定義言語) と DML (データ操作言語) 要素における T-SQL のいくつかの相違点により、T-SQL ステートメントとクエリは部分的にしかサポートされません (これについてはこの記事で後ほど説明します)。

また、Azure SQL Database はマスター データベースとオペレーティング システムへの依存から機能を分離するように設計されているため、まったくサポートされていない機能と構文がいくつかあります。 そのため、サーバー レベルの大半のアクティビティは SQL Database には不適切です。 T-SQL ステートメントとオプションは、サーバー レベルのオプション、オペレーティング システムのコンポーネントを構成した場合、またはファイル システムの構成を指定した場合は使用できません。 このような機能が必要な場合は、SQL Database や別の Azure 機能またはサービスから代わりの適切な機能を使用できることがあります。

たとえば、高可用性は、[Always On 可用性グループ](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)に似たテクノロジを使用して、Azure SQL Database に組み込まれています。 可用性グループに関連する T-SQL ステートメントは SQL Database でサポートされず、AlwaysOn 可用性グループに関連する動的管理ビューもサポートされていません。

SQL Database でサポートされる機能とサポートされない機能の一覧については、「 [Azure SQL Database の機能の比較](sql-database-features.md)」をご覧ください。 このページの一覧は、その記事のガイドラインと機能を補足するもので、Transact-SQL ステートメントに重点を置いています。

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>部分的に異なる Transact-SQL 構文のステートメント

コア DDL (データ定義言語) ステートメントは使用できますが、一部の DDL ステートメントにはディスクの配置に関連した拡張機能やサポートされていない機能があります。

- CREATE ステートメントと ALTER DATABASE ステートメントには 30 以上のオプションがあります。 ステートメントには SQL Server にのみ適用されるファイルの配置、FILESTREAM、および Service Broker のオプションが含まれます。 移行前にデータベースを作成する場合は、これは問題になりませんが、データベースを作成する T-SQL コードを移行する場合は、[CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) を [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) の SQL Server 構文と比較して、使用するすべてのオプションがサポートされているかを確認します。 また Azure SQL Database 用の CREATE DATABASE には、SQL Database にのみ適用されるサービス目標と柔軟なスケールのオプションがあります。
- FILESTREAM はサポートされていないため、CREATE ステートメントと ALTER TABLE ステートメントには、SQL Database では使用できない FileTable のオプションがあります。
- CREATE ステートメントと ALTER LOGIN ステートメントはサポートされますが、SQL Database ではすべてのオプションは提供されません。 データベースの移植性を高めるために、SQL Database は可能な限りログインの代わりに包含データベース ユーザーを使用することを推奨しています。 詳細については、「[ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx)」と「[データベース アクセスの制御と許可](sql-database-manage-logins.md)」を参照してください。

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Azure SQL Database でサポートされない Transact-SQL 構文

「 [Azure SQL Database の機能の比較](sql-database-features.md)」に記載されている、サポートされていない機能に関連する Transact-SQL ステートメントの他に、次のステートメントとステートメントのグループはサポートされていません。 そのため、移行するデータベースが次の機能のいずれかを使用している場合は、T-SQL を再構築してこれらの T-SQL の機能とステートメントを取り除きます。

- システム オブジェクトの照合順序
- 接続関連:エンドポイント ステートメント。 SQL データベースは Windows 認証をサポートしませんが、同様の Azure Active Directory 認証をサポートします。 いくつかの認証の種類には、最新バージョン の SSMS が必要です。 詳細については、「[Azure Active Directory 認証を使用して SQL Database または SQL Data Warehouse に接続する](sql-database-aad-authentication.md)」を参照してください。
- 3 部構成または 4 部構成の名前を使用したデータベース間クエリ (読み取り専用のデータベース間クエリは、[エラスティック データベース クエリ](sql-database-elastic-query-overview.md)を使用してサポートされます)。
- データベース間での所有権の継承、`TRUSTWORTHY` 設定
- `EXECUTE AS LOGIN` 代わりに 'EXECUTE AS USER' を使用してください。
- 拡張キー管理以外で暗号化がサポートされています。
- イベント:イベント、イベント通知、クエリ通知
- ファイルの配置:Microsoft Azure により自動的に管理されるデータベース ファイル配置、サイズ、データベース ファイルに関連する構文。
- 高可用性:Microsoft Azure アカウントを通じて管理される高可用性に関連する構文。 これには、バックアップのための構文、復元、AlwaysOn、データベース ミラーリング、ログ配布、復旧モードが含まれます。
- ログ リーダー:SQL Database で使用できないログ リーダーに依存する構文:プッシュ レプリケーション、変更データ キャプチャ。 SQL Database は、プッシュ レプリケーションの記事のサブスクライバーである可能性があります。
- 関数: `fn_get_sql`、`fn_virtualfilestats`、`fn_virtualservernodes`
- ハードウェア:ハードウェア関連のサーバー設定に関連する構文: メモリ、ワーカー スレッド、CPU アフィニティ、トレース フラグなど。 代わりにサービス レベルとコンピューティング サイズを使用します。
- `KILL STATS JOB`
- `OPENQUERY`、`OPENROWSET`、`OPENDATASOURCE`、および 4 部構成の名前
- .NET framework:CLR と SQL Server の統合
- セマンティック検索
- サーバー資格情報:代わりに[データベース スコープの資格情報](https://msdn.microsoft.com/library/mt270260.aspx)を使用してください。
- サーバー レベルの項目:サーバーの役割、`sys.login_token`。 サーバー レベルのアクセス許可の `GRANT`、`REVOKE`、`DENY` は使用できませんが、一部はデータベース レベルのアクセス許可に置き換えられます。 いくつかの便利なサーバー レベルの DMV には、同等のデータベース レベルの DMV があります。
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- `sp_configure` オプションと `RECONFIGURE`。 一部のオプションは、 [ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx)で使用できます。
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server エージェント:SQL Server エージェントまたは MSDB データベースに依存する構文: アラート、演算子、中央管理サーバー。 Azure PowerShell などのスクリプトを代わりに使用します。
- SQL Server 監査:代わりに SQL Database 監査を使用してください。
- SQL Server トレース
- トレース フラグ:トレース フラグのいくつかの項目は、互換性モードに移動されました。
- Transact-SQL デバッグ
- トリガー:サーバー スコープ トリガーまたはログオン トリガー
- `USE` ステートメント:データベース コンテキストを別のデータベースに変更するには、新しいデータベースへの接続を新たに確立する必要があります。

## <a name="full-transact-sql-reference"></a>完全 Transact-SQL リファレンス

Transact-SQL の文法、使用方法、例の詳細については、Microsoft SQL Server オンライン ブックの「 [Transact-SQL リファレンス (データベース エンジン)](https://msdn.microsoft.com/library/bb510741.aspx)」を参照してください。 

### <a name="about-the-applies-to-tags"></a>「適用先」タグについて

Transact-SQL リファレンスには、SQL Server 2008 以降のバージョンに関連する記事が含まれています。 記事タイトルの下に、4 つの SQL Server プラットフォームと適用性を示すアイコン バーがあります。 たとえば、可用性グループは SQL Server 2012 で導入されました。  [CREATE AVAILABILITY GROUP](https://msdn.microsoft.com/library/ff878399.aspx)  の記事では、そのステートメントが **SQL Server (2012 以降)** に適用されることが示されています。 ステートメントは、SQL Server 2008、SQL Server 2008 R2、Azure SQL Database、Azure SQL Data Warehouse、または並列データ ウェアハウスには適用されません。

場合によっては、記事にある一般的な項目を製品で使用できますが、製品間で若干の違いがあることがあります。 相違点は、必要に応じて記事の中で示されます。 場合によっては、記事にある一般的な項目を製品で使用できますが、製品間で若干の違いがあることがあります。 相違点は、必要に応じて記事の中で示されます。 たとえば CREATE TRIGGER の記事は、SQL Database で使用できます。 ただし、サーバー レベルのトリガーの **ALL SERVER** オプションでは、サーバー レベルのトリガーは SQL Database では使用できないと指示されます。 代わりにデータベース レベルのトリガーを使用します。

## <a name="next-steps"></a>次の手順

SQL Database でサポートされる機能とサポートされない機能の一覧については、「 [Azure SQL Database の機能の比較](sql-database-features.md)」をご覧ください。 このページの一覧は、その記事のガイドラインと機能を補足するもので、Transact-SQL ステートメントに重点を置いています。
