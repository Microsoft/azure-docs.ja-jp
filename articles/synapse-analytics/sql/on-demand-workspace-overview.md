---
title: SQL オンデマンド (プレビュー)
description: Azure Synapse Analytics の Synapse SQL オンデマンドについて説明します。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: fc306dbca3191f04a85f2c5cc88d41336c13e09c
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496391"
---
# <a name="sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) 

すべての Azure Synapse Analytics ワークスペース (プレビュー) には、レイク内のデータに対してクエリを実行するために使用できる SQL オンデマンド (プレビュー) エンドポイントがあります。

SQL オンデマンドは、データ レイク内のデータに対するクエリ サービスです。 これを使用すると、次の機能を通じてデータにアクセスできます。
 
- 専用のストアにデータをコピーしたり読み込んだりすることなく、インプレースでデータのクエリを実行できる、使い慣れた T-SQL 構文。 
- さまざまなビジネス インテリジェンスおよびアドホック クエリ実行ツール (最も人気のあるドライバーなど) を提供する、T-SQL インターフェイスを介した統合接続。 

SQL オンデマンドは、大規模なデータとコンピューティングを対象として構築された分散データ処理システムです。 SQL オンデマンドでは、ワークロードに応じて数秒でビッグ データを分析することができます。 クエリ実行のフォールトトレランスが組み込まれているため、大規模なデータ セットを対象とする実行時間の長いクエリの場合でも、高い信頼性と成功率が実現します。

SQL オンデマンドはサーバーレスであるため、インフラストラクチャの設定やクラスターの管理は不要です。 このサービスの既定のエンドポイントはすべての Azure Synapse ワークスペース内で提供されるため、ワークスペースが作成されるとすぐにデータのクエリ実行を開始できます。 予約されているリソースには課金されません。実行するクエリによってスキャンされるデータに対してのみ課金されるため、このモデルはまさに従量課金制モデルになっています。  

データ パイプラインで Apache Spark for Azure Synapse を使用する場合は、データの準備、クレンジング、またはエンリッチメントのために、プロセスで作成した [Spark 外部テーブルに対してクエリ](develop-storage-files-spark-tables.md)を SQL オンデマンドから直接実行できます。 [Private Link](../security/how-to-connect-to-workspace-with-private-links.md) を使用して、SQL オンデマンド エンドポイントを[マネージド ワークスペース VNet](../security/synapse-workspace-managed-vnet.md) に組み込みます。  

## <a name="sql-on-demand-benefits"></a>SQL オンデマンドのベネフィット

データ レイク内のデータを探索したり、そこから分析情報を得たり、既存のデータ変換パイプラインを最適化したりする必要がある場合は、SQL オンデマンドを活用することができます。 これは、次のシナリオに適しています。

- 基本の検出と探索 - データ レイク内のさまざまな形式 (Parquet、CSV、JSON) のデータをすばやく把握できるため、そこから分析情報を抽出する方法を計画することができます。
- 論理データ ウェアハウス - データの再配置や変換を行うことなく、生または異種のデータに基づいてリレーショナル抽象化を実現します。これにより、常に最新のデータを表示できます。
- データ変換 - T-SQL を使用して、効率性が高くシンプルかつスケーラブルな方法でレイク内のデータを変換できるので、それを BI ツールなどにフィードしたり、リレーショナル データ ストア (Synapse SQL データベース、Azure SQL Database など) に読み込んだりできます。

SQL オンデマンドは、さまざまな職務の役に立ちます。

- データ エンジニアは、このサービスを使用してレイクの探索、データの変換、準備を行えるほか、データ変換パイプラインを簡素化できます。 詳細については、こちらの[チュートリアル](tutorial-data-analyst.md)をご覧ください。
- データ サイエンティストは、OPENROWSET や自動スキーマ推論などの機能を利用して、レイク内のデータの内容と構造を迅速に把握することができます。
- データ アナリストは、使い慣れた T-SQL 言語や、SQL オンデマンドに接続できるお気に入りのツールを使用して、データ サイエンティストまたはデータ エンジニアによって作成された[データと Spark 外部テーブル](develop-storage-files-spark-tables.md)を探索できます。
- BI プロフェッショナルは、レイク内のデータおよび Spark テーブルに基づいて [Power BI レポートをすばやく作成](tutorial-connect-power-bi-desktop.md)できます。

## <a name="how-to-start-using-sql-on-demand"></a>SQL オンデマンドを使い始める方法

SQL オンデマンド エンドポイントは、すべての Azure Synapse ワークスペース内で提供されます。 ワークスペースを作成し、使い慣れたツールを使用してすぐにデータのクエリ実行を開始できます。

## <a name="client-tools"></a>クライアント ツール

SQL オンデマンドでは、既存の SQL アドホック クエリ実行およびビジネス インテリジェンス ツールをデータ レイクに利用できます。 使い慣れた T-SQL 構文を使えるため、SQL 製品との TDS 接続を確立できるツールであれば、[Synapse SQL オンデマンドに接続してクエリを実行](connect-overview.md)できます。 Azure Data Studio に接続してアドホック クエリを実行したり、Power BI に接続して数分で分析情報を得たりできます。

## <a name="t-sql-support"></a>T-SQL のサポート

SQL オンデマンドでは T-SQL クエリの実行領域を提供します。これは、半構造化データと非構造化データのクエリ実行に関する機能に対応できるよう、いくつかの点でわずかに強化、拡張されています。 さらに、SQL オンデマンドの設計上の理由から、T-SQL 言語の一部の要素はサポートされていません。たとえば、DML 機能は現在サポートされていません。

- なじみのある概念を使用してワークロードを整理できます。
- データベース - SQL オンデマンド エンドポイントには複数のデータベースを設定できます。
- スキーマ - データベース内には、スキーマと呼ばれるオブジェクト所有者グループが 1 つまたは複数存在します。
- ビュー
- 外部リソース - データ ソース、ファイル形式、テーブル

以下を使用してセキュリティを適用できます。

- ログインとユーザー
- ストレージ アカウントへのアクセスを制御するための資格情報
- オブジェクト レベル単位での権限の許可、拒否、取り消し
- Azure Active Directory の統合

サポートされている T-SQL:

- SQL 関数の大部分を含む、完全な [SELECT](/sql/t-sql/queries/select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 領域がサポートされます
- CETAS - CREATE EXTERNAL TABLE AS SELECT
- ビューとセキュリティのみに関連する DDL ステートメント

SQL オンデマンドにはローカル ストレージがなく、メタデータ オブジェクトのみがデータベースに格納されます。 したがって、次の概念に関連する T-SQL はサポートされていません。

- テーブル
- トリガー
- 具体化されたビュー
- ビューとセキュリティに関連しない DDL ステートメント
- DML ステートメント

### <a name="extensions"></a>拡張機能

SQL オンデマンドでは、データ レイク内のファイルに格納されているデータに対するインプレース クエリをスムーズに実行できるように、次の機能を追加して既存の [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 関数を拡張します。

[複数のファイルまたはフォルダーに対するクエリの実行](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET ファイル形式](query-data-storage.md#query-parquet-files)

[区切りテキストを操作するための追加オプション (フィールド ターミネータ、行ターミネータ、エスケープ文字)](query-data-storage.md#query-csv-files)

[選択した列のサブセットの読み取り](query-data-storage.md#read-a-chosen-subset-of-columns)

[スキーマ推論](query-data-storage.md#schema-inference)

[filename 関数](query-data-storage.md#filename-function)

[filepath 関数](query-data-storage.md#filepath-function)

[複合型と入れ子または繰り返しのデータ構造の操作](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Security

SQL オンデマンドには、データへのアクセスをセキュリティで保護するためのメカニズムが用意されています。

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory との統合と多要素認証

SQL オンデマンドでは、[Azure Active Directory との統合](../../azure-sql/database/authentication-aad-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)によって、データベース ユーザーの ID とその他の Microsoft サービスを一元的に管理できます。 この機能は、アクセス許可の管理を簡略化し、セキュリティを強化します。 Azure Active Directory (Azure AD) では、[多要素認証](../../azure-sql/database/authentication-mfa-ssms-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (MFA) をサポートしています。これにより、シングル サインオン プロセスをサポートすると同時に、データとアプリケーションのセキュリティが強化されます。

#### <a name="authentication"></a>認証

SQL オンデマンド認証とは、エンドポイントへの接続時にユーザーが自分の ID を証明する方法のことです。 2 種類の認証がサポートされています。

- **SQL 認証**

  この認証方法では、ユーザー名とパスワードを使用します。

- **Azure Active Directory 認証**:

  この認証方法では、Azure Active Directory によって管理されている ID を使用します。 Azure AD ユーザーの場合、多要素認証を有効にできます。 [可能であれば](/sql/relational-databases/security/choose-an-authentication-mode?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)、Active Directory 認証 (統合セキュリティ) を使用します。

#### <a name="authorization"></a>承認

承認とは、SQL オンデマンド データベース内でユーザーにどのような操作が許可されるかを示すものであり、ユーザー アカウントのデータベース ロールのメンバーシップとオブジェクトレベルのアクセス許可によって制御されます。

SQL 認証が使用される場合、SQL ユーザーは SQL オンデマンド内にのみ存在し、アクセス許可のスコープは SQL オンデマンド内のオブジェクトに設定されます。 他のサービス (Azure Storage など) のセキュリティ保護可能なオブジェクトへのアクセスは、SQL ユーザーに対して直接許可することができません。これは、そのユーザーが SQL オンデマンドのスコープでのみ存在するためです。 SQL ユーザーは、[サポートされている承認の種類](develop-storage-files-storage-access-control.md#supported-storage-authorization-types)のいずれかを使用して、ファイルにアクセスする必要があります。

Azure AD 認証が使用される場合、ユーザーは SQL オンデマンドやその他のサービス (Azure Storage など) にサインインして、Azure AD ユーザーにアクセス許可を付与できます。

### <a name="access-to-storage-accounts"></a>ストレージ アカウントへのアクセス

SQL オンデマンド サービスにログインしたユーザーは、Azure Storage 内のファイルにアクセスしてクエリを実行する権限を持っている必要があります。 SQL オンデマンドでは、次の種類の承認がサポートされます。

- **Shared Access Signature (SAS)** を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS では、アカウント キーを共有することなく、ストレージ アカウントのリソースへのアクセス権をクライアントに付与できます。 SAS を使用すると、有効期間、付与されるアクセス許可、許容される IP アドレス範囲、許容されるプロトコル (https または http) など、SAS を使用しているクライアントに付与するアクセス権の種類をきめ細かく制御できます。

- **ユーザー ID** ("パススルー" とも呼ばれる) は、SQL オンデマンドにログインしている Azure AD ユーザーの ID がデータへのアクセスの承認に使用される承認の種類です。 データにアクセスする前に、Azure Storage の管理者が、データにアクセスするためのアクセス許可を Azure AD ユーザーに付与する必要があります。 この種類の承認では、SQL オンデマンドにログインした Azure AD ユーザーを使用するため、ユーザーの種類が SQL の場合はサポートされません。

## <a name="next-steps"></a>次のステップ
エンドポイント接続とファイルに対するクエリ実行に関する追加情報については、次の記事をご覧ください。 
- [エンドポイントへの接続](connect-overview.md)
- [ファイルに対するクエリ実行](develop-storage-files-overview.md)
