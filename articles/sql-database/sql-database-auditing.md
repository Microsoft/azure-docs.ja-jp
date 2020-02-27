---
title: 監査の使用
description: Azure SQL データベース監査を使用して、データベースイベントを追跡し、監査ログに書き込みます。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/11/2020
ms.openlocfilehash: 686e426ef0b7706eff168e42ffc67417b2c5c743
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212899"
---
# <a name="get-started-with-sql-database-auditing"></a>SQL Database 監査の使用

Azure [SQL Database](sql-database-technical-overview.md) および [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の監査では、データベース イベントが追跡され、Azure ストレージ アカウント、Log Analytics ワークスペース、または Event Hubs の監査ログにイベントが書き込まれます。 また、監査によって以下を行うことができます。

- 規定コンプライアンスの維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

- コンプライアンスを保証するものではありませんが、標準へのコンプライアンスを強化します。 標準コンプライアンスをサポートする Azure プログラムの詳細については、[Azure セキュリティ センター](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)を参照してください。ここから最新の SQL Database コンプライアンス証明書の一覧を入手できます。


> [!NOTE] 
> このトピックは Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

## <a id="subheading-1"></a>Azure SQL データベース監査の概要

SQL Database 監査を使用して、以下を行うことができます。

- **保持** 。 監査するデータベース活動のカテゴリを定義できます。
- **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

> [!IMPORTANT]
> 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。
>
> - すべてのストレージの種類 (v1、v2、BLOB) がサポートされています。
> - すべてのストレージ レプリケーション構成がサポートされています。
> - 仮想ネットワークとファイアウォールの背後にあるストレージがサポートされています。
> - **Premium Storage** は現在**サポートされていません**。
> - **Azure Data Lake Storage Gen2 ストレージ アカウント**用の**階層型名前空間**は現在**サポートされていません**。
> - 一時停止中の **Azure SQL Data Warehouse** で監査を有効にすることはサポートされていません。 監査を有効にするには、Data Warehouse を再開します。
   
## <a id="subheading-8"></a>サーバー レベルおよびデータベース レベルの監査ポリシーを定義する

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。

- サーバー ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

- "*サーバー BLOB 監査が有効*" な場合は、"*常にデータベースに適用*" されます。 データベース監査設定に関係なく、データベースが監査されます。

- サーバーだけでなくデータベースやデータ ウェアハウスで BLOB 監査を有効にしても、サーバーの BLOB 監査の設定がオーバーライドされたり変更されたりすることは "*ありません*"。 どちらの監査も並行して存在します。 つまり、データベースは並行して 2 回監査されることになります (1 回はサーバー ポリシー、もう 1 回はデータベース ポリシーによって監査されます)。

   > [!NOTE]
   > 次の場合を除き、サーバー BLOB 監査とデータベース BLOB 監査の両方を有効にすることは避けてください。
    > - 特定のデータベースに対して異なる "*ストレージ アカウント*" または "*リテンション期間*" を使用する場合。
    > - 特定のデータベースの監査対象とするイベントの種類またはカテゴリが、このサーバー上の他のデータベースの管理対象と異なる場合。 たとえば、特定のデータベースに対してのみ監査が必要なテーブルの挿入がある場合など。
   >
   > これらに該当しない場合は、サーバー レベルの BLOB 監査のみを有効にし、すべてのデータベースに対してデータベース レベルの監査を無効にすることをお勧めします。

## <a id="subheading-2"></a>サーバーの監査を設定する

以下のセクションでは、Azure Portal を使用した監査の構成について説明します。

  > [!NOTE]
   >監査ログを書き込む場所を構成するときに、複数のオプションから選択できるようになりました。 ログは、Azure ストレージ アカウント、Log Analytics ワークスペース (Azure Monitor ログで使用)、イベント ハブ (イベント ハブで使用) に書き込むことができます。 これらのオプションは組み合わせて構成でき、それぞれの場所に監査ログが書き込まれます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. SQL データベース/サーバー ペインの [セキュリティ] 見出しの下にある **[監査]** に移動します。
3. サーバーの監査ポリシーを設定する場合は、データベース監査ページの **[サーバー設定を表示]** リンクを選択できます。 そうすると、サーバー監査設定を表示または修正することができます。 サーバー監視ポリシーは、このサーバー上にある既存のデータベースと新規作成されたデータベースのすべてに適用されます。

    ![ナビゲーション ウィンドウ][2]

4. データベース レベルで監査を有効にする場合は、 **[監査]** を  **[ON]\(オン\)** に切り替えます。 サーバーの監査が有効になっている場合、データベース構成監査とサーバー監査が並行して存在します。

    ![ナビゲーション ウィンドウ][3]

### <a id="audit-storage-destination">ストレージ保存先への監査</a>

ストレージ アカウントへの監査ログの書き込みを構成するには、 **[ストレージ]** を選択し、 **[容量の詳細]** を開きます。 ログを保存する Azure ストレージ アカウントを選択し、リテンション期間を選択します。 次に、 **[OK]** をクリックします 保持期間よりも古いログは削除されます。

   > [!IMPORTANT]
   > - リテンション期間の既定値は 0 (無制限のリテンション期間) です。 この値は、ストレージ アカウントを監査用に構成するときに **[ストレージ設定]** の **[リテンション期間 (日数)]** スライダーを移動して変更できます。
   > - リテンション期間を 0 (無制限のリテンション期間) から他の値に変更した場合、リテンション期間は、リテンション期間の値が変更された後に書き込まれたログにのみ適用されることに注意してください (リテンション期間が無制限に設定されている間に書き込まれたログは、リテンション期間が有効になった後も保持されます)。

   ![ストレージ アカウント](./media/sql-database-auditing-get-started/auditing_select_storage.png)

仮想ネットワークまたはファイアウォールの下にストレージ アカウントを構成するには、サーバー上に [Active Directory 管理者](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#provision-an-azure-active-directory-administrator-for-your-managed-instance)が必要です。このストレージアカウントの **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** を有効にします。 また、選択したストレージ アカウントに対して 'Microsoft.Authorization/roleAssignments/write' アクセス許可を持っている必要があります。

マネージド ID に "ストレージ BLOB データ共同作成者" ロールを付与するために、[ユーザー アクセス管理者](../role-based-access-control/built-in-roles.md#user-access-administrator)になることをお勧めします。 アクセス許可とロールベースのアクセス制御の詳細については、「[Azure リソースのロールベースのアクセス制御 (RBAC) の概要](../role-based-access-control/overview.md)」および「[Azure RBAC と Azure portal を使用してロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-portal.md)」を参照してください。

### <a id="audit-log-analytics-destination">Log Analytics 保存先への監査</a>
  
Log Analytics ワークスペースへの監査ログの書き込みを構成するには、 **[Log Analytics (プレビュー)]** を選択して **[Log Analytics の詳細]** を開きます。 ログが書き込まれる Log Analytics ワークスペースを選択または作成し、 **[OK]** をクリックします。

   ![Log Analytics ワークスペース](./media/sql-database-auditing-get-started/auditing_select_oms.png)
    
  > [!WARNING]
   > Log Analytics に対する監査を有効にすると、インジェストのレートに基づくコストが発生します。 この[オプション](https://azure.microsoft.com/pricing/details/monitor/)を使用した場合のコストを承知のうえで利用するか、または、監査ログを Azure ストレージ アカウントに格納することを検討してください。

### <a id="audit-event-hub-destination">イベント ハブ保存先への監査</a>

イベント ハブへの監査ログの書き込みを構成するには、 **[イベント ハブ (プレビュー)]** を選択し、 **[イベント ハブの詳細]** を開きます。 ログが書き込まれるイベント ハブを選択し、 **[OK]** をクリックします。 イベント ハブがお使いのデータベースおよびサーバーと同じリージョンにあることを確認します。

   ![イベント ハブ](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

## <a id="subheading-3"></a>監査ログとレポートを分析する

監査ログを Azure Monitor ログに書き込む場合:

- [Azure Portal](https://portal.azure.com) を使用します。  関連するデータベースを開きます。 データベースの **[監査]** ページの上部にある **[監査ログの表示]** をクリックします。

    ![監査ログの表示](./media/sql-database-auditing-get-started/auditing-view-audit-logs.png)

- ログを表示するには、2 つの方法があります。
    
    **[監査レコード]** ページの上部にある **[Log Analytics]** をクリックすると、Log Analytics ワークスペースでログ ビューが開きます。このビューで、時間の範囲と検索クエリをカスタマイズできます。
    
    ![Log Analytics ワークスペースで開く](./media/sql-database-auditing-get-started/auditing-log-analytics.png)

    **[監査レコード]** ページの上部にある **[ダッシュボードの表示]** をクリックすると、監査ログ情報を表示するダッシュボードが開きます。ここで、セキュリティ分析情報へのドリルダウンや機微なデータへのアクセスなどを実行できます。 このダッシュボードは、データのセキュリティ分析情報を得るのに役立つように設計されています。
    時間の範囲と検索クエリをカスタマイズすることもできます。 
    ![Log Analytics ダッシュボードの表示](media/sql-database-auditing-get-started/auditing-view-dashboard.png)

    ![Log Analytics ダッシュボード](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard.png)

    ![Log Analytics のセキュリティ分析情報](media/sql-database-auditing-get-started/auditing-log-analytics-dashboard-data.png)
 

- また、Log Analytics ブレードから監査ログにアクセスすることもできます。 ご自身の Log Analytics ワークスペースを開いて、 **[全般]** セクションで **[ログ]** をクリックします。 監査ログを表示するには、*search "SQLSecurityAuditEvents"* などの単純なクエリから始めることができます。
    ここから [Azure Monitor ログ](../log-analytics/log-analytics-log-search.md) を使用して、監査ログのデータに対して詳細検索を実行することもできます。 Azure Monitor ログにより、統合された検索とカスタム ダッシュボードを使用してオペレーション インサイトがリアルタイムで得られるため、ワークロードやサーバー全体に散在する何百万件のレコードもすぐに分析できます。 Azure Monitor ログの検索言語とコマンドに関する有用な追加情報については、[Azure Monitor ログ検索リファレンス](../log-analytics/log-analytics-log-search.md)に関するページをご覧ください。

監査ログをイベント ハブに書き込む場合:

- イベント ハブの監査ログ データを使用するには、イベントを処理し、そのイベントをターゲットに書き込むようにストリームを設定する必要があります。 詳細については、「[Azure Event Hubs のドキュメント](../event-hubs/index.yml)」を参照してください。
- イベント ハブの監査ログは [Apache Avro](https://avro.apache.org/) イベントの本体でキャプチャされ、UTF-8 エンコードの JSON 形式を使用して格納されます。 監査ログを読み取るために、[Avro Tools](../event-hubs/event-hubs-capture-overview.md#use-avro-tools) またはこの形式を処理する同様のツールを使用できます。

監査ログを Azure ストレージ アカウントに書き込むことを選択すると、複数の方法でログを表示できるようになります。

> [!NOTE] 
> [読み取り専用レプリカ](sql-database-read-scale-out.md)での監査は自動的に有効になります。 ストレージ フォルダーの階層、命名規則、ログ形式の詳細については、「[SQL Database 監査ログの形式](sql-database-audit-log-format.md)」を参照してください。 

- 監査ログは、設定時に選択したアカウントで集計されます。 [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを使用して監査ログを調査できます。 Azure Storage では、監査ログは **sqldbauditlogs** という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。 ストレージ フォルダーの階層、命名規則、ログ形式の詳細については、「[SQL Database 監査ログの形式](https://go.microsoft.com/fwlink/?linkid=829599)」を参照してください。

- [Azure Portal](https://portal.azure.com) を使用します。  関連するデータベースを開きます。 データベースの **[監査]** ページの上部にある **[監査ログの表示]** をクリックします。

    ![ナビゲーション ウィンドウ][7]

    **[監査レコード]** が開きます。ここからログを参照できます。

  - **[監査レコード]** ページの上部にある **[フィルター]** をクリックすると、特定の日付を表示できます。
  - **[監査対象]** を切り替えると、"*サーバー監視ポリシー*" で作成された監査レコードと "*データベース監査ポリシー*" で作成された監査レコードを切り替えることができます。
  - **[Show only audit records for SQL injections]\(SQL インジェクションの監査レコードのみを表示する\)** チェックボックスをオンにすると、SQL インジェクション関連の監査レコードのみを表示できます。

       ![ナビゲーション ウィンドウ][8]

- システム関数 **sys.fn_get_audit_file** (T-SQL) を使用して、表形式で監査ログ データを返します。 この関数の使用方法の詳細については、[sys.fn_get_audit_file](/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql) に関するページをご覧ください。

- SQL Server Management Studio (SSMS 17 以降) で **[監査ファイルの統合]** を使用します。
    1. SSMS のメニューから、 **[ファイル]**  >  **[開く]**  >  **[監査ファイルの統合]** を選択します。

        ![ナビゲーション ウィンドウ][9]
    2. **[監査ファイルの追加]** ダイアログ ボックスが表示されます。 **[追加]** オプションのいずれかを選択して、ローカル ディスクから監査ファイルをマージするか、Azure Storage からインポートするかを選択します。 Microsoft Azure Storage の詳細とアカウント キーを提供する必要があります。

    3. 統合するすべてのファイルを追加した後に、 **[OK]** をクリックして統合の操作を完了します。

    4. 統合されたファイルを SSMS で開くと、ファイルを表示および分析し、XEL または CSV ファイルまたはテーブルにエクスポートすることができます。

- Power BI を使用します。 Power BI で監査ログのデータを表示および分析できます。 ダウンロード可能なテンプレートの詳細と、テンプレートへのアクセスについては、[Power BI での監査ログ データの分析](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/)に関するページを参照してください。
- ポータル経由で、あるいは [Azure ストレージ エクスプローラー](https://storageexplorer.com/)などのツールを利用して Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。
  - ログ ファイルをローカルでダウンロードした後に、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析します。
  - また、Azure ストレージ エクスプ ローラーを使用して、同時に複数のファイルをダウンロードすることもできます。 それには、特定のサブフォルダーを右クリックし、 **[名前を付けて保存]** を選択してローカル フォルダーに保存します。

- 他の方法:

  - 複数のファイルまたはログ ファイルが含まれるサブフォルダーをダウンロードした後、前述の SSMS 監査ファイルの統合の指示に従って、ローカルでマージすることができます。
  - BLOB 監査ログをプログラムで表示します。

    - PowerShell を使用して[拡張イベント ファイルにクエリを実行します](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/)。

## <a id="subheading-5"></a>運用方法

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">geo レプリケーション対応データベースの監査</a>

Geo レプリケーション データベースでは、プライマリ データベースの監査を有効にすると、セカンダリ データベースにも同一の監査ポリシーが適用されます。 プライマリ データベースとは別に**セカンダリ サーバー**で監査を有効にすることで、セカンダリ データベースに対する監査を設定することもできます。

- サーバー レベル (**推奨**):**プライマリ サーバー**と**セカンダリ サーバー**の両方で監査を有効にします。プライマリ データベースとセカンダリ データベースは、それぞれのサーバーレベル ポリシーに基づいて個別に監査されます。
- データベースレベル:セカンダリ データベースのデータベースレベルの監査は、プライマリ データベースの監査設定から構成する必要があります。
  - 監査は、サーバーではなく "*プライマリ データベース自体*" で有効にする必要があります。
  - プライマリ データベースで監査を有効にすると、セカンダリ データベースでも有効になります。

    >[!IMPORTANT]
    >データベースレベルの監査では、セカンダリ データベースのストレージ設定はプライマリ データベースと同じになるため、リージョンをまたいだトラフィックが発生します。 サーバー レベルの監査のみを有効にし、すべてのデータベースでデータベース レベルの監査を無効なままにしておくことをお勧めします。

### <a id="subheading-6">ストレージ キーの再生成</a>

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 監査ログを Azure Storage に書き込む場合、ご自身のキーを最新の情報に更新するときに、お使いの監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. **[容量の詳細]** を開きます。 **[ストレージ アクセス キー]** ボックスで **[セカンダリ]** をクリックし、 **[OK]** をクリックします。 次に、監査構成ページの上部にある **[保存]** をクリックします。

    ![ナビゲーション ウィンドウ][5]
2. ストレージ構成ページに移動し、プライマリ アクセス キーを再生成します。

    ![ナビゲーション ウィンドウ][6]
3. 監査構成ページに戻り、[ストレージ アクセス キー] を [セカンダリ] から [プライマリ] に切り替え、 **[OK]** をクリックします。 次に、監査構成ページの上部にある **[保存]** をクリックします。
4. ストレージ構成ページに戻り、セカンダリ アクセス キーを (次のキー更新サイクルの準備として) 再生成します。

## <a name="additional-information"></a>追加情報

- 監査対象イベントをカスタマイズする場合は、[PowerShell コマンドレット](#subheading-7)または [REST API](#subheading-9) を使用して行います。

- 監査設定を構成した後に、新しい脅威の検出機能をオンにし、電子メールを構成してセキュリティの警告を受信します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、[脅威の検出の概要](sql-database-threat-detection-get-started.md)に関するページを参照してください。
- ログの形式、ストレージ フォルダーの階層、および命名規則の詳細については、[BLOB 監査ログ形式のリファレンス](https://go.microsoft.com/fwlink/?linkid=829599)に関するドキュメントを参照してください。

    > [!IMPORTANT]
    > Azure SQL Database Audit では、監査レコードの文字列フィールドに 4,000 文字のデータを格納します。 監査可能なアクションから返された、**statement** または **data_sensitivity_information** 値に 4,000 を超える文字が含まれる場合、最初の 4,000 文字以降のすべてのデータは、**切り捨てられ、監査されません**。

- 監査ログは Azure サブスクリプションの Azure Blob Storage 内にある**追加 BLOB** に書き込まれます。
  - **Premium Storage** は現在、追加 BLOB では**サポートされていません**。

- 既定の監査ポリシーには、すべてのアクションと次のアクション グループのセットが含まれます。これは、データベースに対して実行されたすべてのクエリとストアド プロシージャに加えて、成功および失敗したログインを監査します。

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    「[Azure PowerShell を使用して SQL Database の監査を管理する](#subheading-7)」セクションで説明されているように、PowerShell を使用してさまざまな種類のアクションおよびアクション グループの監査を構成することができます。

- AAD 認証を使用している場合、失敗したログインのレコードは SQL 監査ログに表示 "*されません*"。 失敗したログインの監査レコードを表示するには、これらのイベントの詳細をログに記録している [Azure Active Directory ポータル]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)にアクセスする必要があります。

- Azure SQL Database 監査は、可用性とパフォーマンスのために最適化されています。 非常に負荷の高いアクティビティの実行時には、Azure SQL Database は操作の続行を可能にするために一部の監査イベントを記録しない場合があります。

- ストレージ アカウントで不変の監査を構成するには、「[保護された追加 BLOB の書き込みを許可する](../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes)」を参照してください。 監査のコンテナー名は **sqldbauditlogs** であることに注意してください。

    > [!IMPORTANT]
    > 時間ベースの保持における保護された追加 BLOB の書き込みの許可の設定は、現時点では次のリージョンでのみ使用および表示できます。
    > - 米国東部
    > - 米国中南部
    > - 米国西部 2


## <a id="subheading-7"></a>Azure PowerShell を使用して Azure SQL Server およびデータベースの監査を管理する

**PowerShell コマンドレット (WHERE 句のサポートによってフィルタリングを強化)** :

- [データベース監査ポリシーを作成または更新する (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [サーバー監査ポリシーを作成または更新する (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)
- [データベース監査ポリシーを取得する (Get-AzSqlDatabaseAudit)](/powershell/module/az.sql/get-azsqldatabaseaudit)
- [サーバー監査ポリシーを取得する (Get-AzSqlServerAudit)](/powershell/module/az.sql/get-azsqlserveraudit)
- [データベース監査ポリシーを削除する (Remove-AzSqlDatabaseAudit)](/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [サーバー監査ポリシーを削除する (Remove-AzSqlServerAudit)](/powershell/module/az.sql/remove-azsqlserveraudit)

スクリプトの例については、[PowerShell を使用した監査と脅威検出の構成](scripts/sql-database-auditing-and-threat-detection-powershell.md)に関するページを参照してください。

## <a id="subheading-8"></a>REST API を使用して Azure SQL Server およびデータベースの監査を管理する

**REST API**:

- [データベース監査ポリシーの作成または更新](/rest/api/sql/database%20auditing%20settings/createorupdate)
- [サーバー監査ポリシーの作成または更新](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [データベース監査ポリシーの取得](/rest/api/sql/database%20auditing%20settings/get)
- [サーバー監査ポリシーの取得](/rest/api/sql/server%20auditing%20settings/get)

WHERE 句のサポートによってフィルタリングを強化した拡張ポリシー:

- [データベース "*拡張*" 監査ポリシーの作成または更新](/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [サーバー "*拡張*" 監査ポリシーの作成または更新](/rest/api/sql/server%20auditing%20settings/createorupdate)
- [データベース "*拡張*" 監査ポリシーの取得](/rest/api/sql/database%20extended%20auditing%20settings/get)
- [サーバー "*拡張*" 監査ポリシーの取得](/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-9"></a>Azure Resource Manager テンプレートを使用して Azure SQL Server およびデータベースの監査を管理する

以下の例で確認できるように、[Azure Resource Manager](../azure-resource-manager/management/overview.md) テンプレートを使用して Azure SQL データベース監査を管理できます。

- [Azure BLOB ストレージ アカウントに監査ログを書き込むように監査機能を有効にした Azure SQL Server をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Log Analytics に監査ログを書き込むように監査機能を有効にした Azure SQL Server をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Event Hubs に監査ログを書き込むように監査機能を有効にした Azure SQL Server をデプロイする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> リンクされたサンプルは、外部の公開リポジトリにあり、保証なしに "手を加えず" に提供され、Microsoft サポート プログラム/サービスのサポート対象ではなありません。

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage Azure SQL Server and Database auditing using Azure PowerShell]: #subheading-7
[Manage SQL database auditing using REST API]: #subheading-8
[Manage Azure SQL Server and Database auditing using ARM templates]: #subheading-9

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png 
