---
title: 組み込みのインデクサーを使用して大容量のデータ セットのインデックスを作成する
titleSuffix: Azure Cognitive Search
description: 大容量データのインデックス作成またはバッチ モードでの計算負荷の高いインデックス作成の戦略と、リソース管理と、スケジュール設定されたインデックス作成、並列インデックス作成、および分散インデックス作成のためのテクニック。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190954"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Azure Cognitive Search で大容量のデータ セットのインデックスを作成する方法

データ量が増加した場合や処理の変更が必要になった場合、単純な、または既定のインデックス作成戦略では、もはや十分でないことに気付くことがあります。 Azure Cognitive Search の場合、大容量のデータ セットに対応するための方法がいくつかあります。それは、データ アップロード要求を構造化する方法から、スケジュール設定され分散されたワークロードに対してソース固有のインデクサーを使用する方法までさまざまです。

実行時間の長いプロセスにも同じ手法が適用されます。 具体的には、「[並列インデックス作成](#parallel-indexing)」で説明する手順は、[AI エンリッチメント パイプライン](cognitive-search-concept-intro.md)での画像分析や自然言語処理など、計算負荷の高いインデックス作成に役立ちます。

以下のセクションでは、大量のデータのインデックスを作成する 3 つの方法について説明します。

## <a name="option-1-pass-multiple-documents"></a>オプション 1: 複数のドキュメントを渡す

大容量のデータ セットにインデックスを付けるための最も簡単なメカニズムの 1 つは、複数のドキュメントまたはレコードを 1 つの要求で送信するというものです。 ペイロード全体が 16 MB を超えない限り、1 つの要求によって、一括アップロード操作で最大 1000 のドキュメントを処理できます。 .NET SDK で [Add Documents REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) と [Index メソッド](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet)のどちらを使用しているかによって、これらの制限が適用されます。 どちらの API でも、各要求の本文に 1,000 個のドキュメントをパッケージ化します。

バッチ インデックス作成は REST または .NET を使用するか、またはインデクサーを介して、要求ごとに実装されます。 いくつかのインデクサーは、それぞれ異なる制限下で動作します。 具体的には、Azure Blob インデックス作成では、より大きな平均ドキュメント サイズを認識して、バッチ サイズが 10 のドキュメントに設定されます。 [インデクサーの作成 (REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) に基づいたインデクサーの場合は、引数 `BatchSize` を設定することで、ご利用のデータの特性に合わせてこの設定をカスタマイズすることができます。 

> [!NOTE]
> ドキュメントのサイズが大きくならないように、クエリできないデータをインデックスに追加しないようにします。 イメージとその他のバイナリ データは直接検索できないため、インデックスには格納しないでください。 クエリ不可能なデータを検索結果に含めるには、リソースへの URL 参照を格納する検索不可能なフィールドを定義してください。

## <a name="option-2-add-resources"></a>オプション 2:リソースを追加する

[Standard 価格レベル](search-sku-tier.md)のいずれかでプロビジョニングされたサービスには、多くの場合、ストレージとワークロードの両方 (クエリまたはインデックス作成) に対して十分に活用されない容量が含まれます。この場合、[パーティションとレプリカの数を増やす](search-capacity-planning.md)ことが、より容量の大きなデータ セットに対応するための明確な解決策となります。 最良の結果を得るには、ストレージ用のパーティションとデータ インジェスト作業用のレプリカというリソースが両方とも必要です。

レプリカとパーティションを増やすことは、コストを増大させる課金対象のイベントです。しかし、最大の負荷で継続的にインデックスを作成をする場合以外は、インデックス作成プロセスの期間中にスケールを追加し、インデックス作成が終了した後でリソース レベルを下方に調整することができます。

## <a name="option-3-use-indexers"></a>オプション 3:インデクサーを使用する

[インデクサー](search-indexer-overview.md)は、検索可能なコンテンツに対してサポートされている Azure データ ソースをクロールするために使用されます。 大規模なインデックス作成を特に目的としていなくても、いくつかのインデクサー機能は、より大きな容量のデータ セットに対応する上で特に便利です。

+ スケジューラを使用すると、インデックス作成を一定の間隔で配分して行い、時間をかけて徐々に行うことができます。
+ スケジュール済みのインデックス作成は、最後の既知の停止ポイントから再開することができます。 24 時間の範囲内でデータ ソースが完全にクロールされていない場合、中断されたところから 2 日目にインデクサーによってインデックス作成が再開されます。
+ データをより小さな個々のデータ ソースにパーティション分割することにより、並列処理が実現されます。 ソース データは、Azure BLOB ストレージ内の複数のコンテナーなど、より小さなコンポーネントに分割してから、対応する複数の[データ ソース オブジェクト](https://docs.microsoft.com/rest/api/searchservice/create-data-source)を Azure Cognitive Search で作成して、並列でインデックスを作成することができます。

> [!NOTE]
> インデクサーはデータ ソース固有のものです。そのため、インデクサー アプローチの使用は、Azure 上で選択される次のデータ ソースに対してのみ有効です。[SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、[Blob Storage](search-howto-indexing-azure-blob-storage.md)、[Table Storage](search-howto-indexing-azure-tables.md)、[Cosmos DB](search-howto-index-cosmosdb.md)。

### <a name="scheduled-indexing"></a>スケジュール済みのインデックス作成

インデクサーのスケジュール設定は、大容量のデータ セットの処理や、コグニティブ検索パイプラインでの画像分析などの低速実行処理を行う上で重要なメカニズムです。 インデクサーの処理は、24 時間の範囲内で動作します。 24 時間以内に処理が完了しなかった場合、インデクサーのスケジュール機能が効果を発揮します。 

スケジュール機能付きインデックスは、特定の間隔で始動する設計になっており、通常は、ジョブが完了し、次回のスケジュール間隔で再開されます。 ただし、間隔内で処理が完了しない場合、インデクサーは (時間不足のため) 停止します。 処理がどこで停止したかがシステムに記録されており、次の間隔では、前回停止した個所から処理が再開されます。 

実際には、インデックスの読み込みに数日かかる場合、24 時間スケジュールでインデクサーを配置できます。 次の 24 時間サイクルでインデックス作成が再開すると、前回正常だったと認識されたドキュメントから再開されます。 このようにして、インデクサーは、すべての未処理ドキュメントが処理されるまで、ドキュメントのバックログに従って数日にわたり処理を続けます。 この方法の詳細については、[Azure Blob Storage での大容量のデータセットのインデックス作成](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)に関するページを参照してください。 一般的なスケジュール設定の詳細については、[インデクサーの作成 (REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) に関するページ、または [Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>並列インデックス作成

並列インデックス作成戦略は、複数のデータ ソースのインデックスをまとめて作成する方法に基づいています。ここで、各データ ソース定義によってデータのサブセットが指定されます。 

ルーチン以外の計算負荷の高いインデックス作成要件の場合 (コグニティブ検索パイプライン、画像分析、または自然言語処理でスキャンされるドキュメント上での OCR など)、実行時間の長いプロセスを最短の時間で完了させるには、多くの場合、並列インデックス作成戦略が適切なアプローチです。 クエリ要求を排除または削減できる場合、クエリを同時に処理しないサービスでの並列インデックス作成は、処理に時間がかかるサイズの大きなコンテンツを処理するのに最善の戦略オプションとなります。 

並列処理は次の要素で構成されます。

+ 複数のコンテナーまたは同じコンテナー内の複数の仮想フォルダーにソース データを分割します。 
+ 各ミニ データ セットを、独自の[インデクサー](https://docs.microsoft.com/rest/api/searchservice/create-indexer)と対になった、独自の[日付ソース](https://docs.microsoft.com/rest/api/searchservice/create-data-source)にマッピングします。
+ コグニティブ検索の場合、各インデクサー定義で同じ[スキルセット](https://docs.microsoft.com/rest/api/searchservice/create-skillset)を参照します。
+ 同じターゲット検索インデックスに書き込みます。 
+ すべてのインデクサーが同時に実行されるよう、スケジュールを設定します。

> [!NOTE]
> Azure Cognitive Search では、個々のレプリカやパーティションをインデックス作成やクエリ処理に割り当てることはできません。 システムがリソースの使用方法を決定します。 クエリのパフォーマンスへの影響を把握するには、実稼働環境に展開する前に、テスト環境で並列インデックス作成を試してください。  

### <a name="how-to-configure-parallel-indexing"></a>並列インデックス作成の構成方法

インデクサーの処理容量は、検索サービスによって使用される各サービス ユニット (SU) の 1 つのインデクサー サブシステムを基に概算されます。 2 つ以上のレプリカがある Basic または Standard レベルにプロビジョニングされている Azure Cognitive Search サービス上では、複数の並列インデクサーが可能です。 

1. [Azure Portal](https://portal.azure.com) の、検索サービス ダッシュ ボードの **[概要]** ページにある、 **[価格レベル]** で並列インデックスに対応できることを確認してください。 Basic および Standard の両方のレベルで、複数のレプリカが提供されています。

2. **[設定]**  >  **[スケール]** で並列処理の[レプリカを増やし](search-capacity-planning.md)ます。インデクサー ワークロードごとに 1 つ追加します。 既存のクエリ量は十分な数に設定します。 インデックス作成のためのクエリ ワークロードを犠牲にすることは、適切なトレードオフではありません。

3. Azure Cognitive Search インデクサーが到達可能なレベルの複数のコンテナーにデータを分割します。 これには、Azure SQL Database 内の複数のテーブル、Azure BLOB ストレージ内の複数のコンテナー、または複数のコレクションが考えられます。 テーブルまたはコンテナーごとに 1 つのデータ ソース オブジェクトを定義します。

4. 並列実行する複数のインデクサーを作成し、スケジュールを設定します。

   + 1 つのサービスに 6 つのレプリカを作成するとします。 データ セット全体を 6 つに分割するには、6 つのインデクサーを作成し、そのそれぞれを 6 分の 1 のデータ セットを含むデータ ソースに割り当てるよう構成します。 

   + 各インデクサーが同じインデックスを参照するよう設定します。 コグニティブ検索ワークロードの場合は、各インデクサーが同じスキルセットを参照するよう設定します。

   + 各インデクサーの定義で、同じランタイム実行パターンをスケジュールします。 たとえば、`"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` は、8 時間間隔ですべてのインデクサーが 2018 年 5 月 15 日に実行されるスケジュールを作成します。

予定された時刻に、すべてのインデクサーの実行が開始され、データが読み込まれ、エンリッチメント (認知検索パイプラインを構成した場合) が適用され、インデックスに書き込まれます。 Azure Cognitive Search では、更新中のインデックスはロックされません。 同時書き込みは、特定の書き込みが最初の試行に成功しなかった場合、再試行することで管理されます。

> [!Note]
> レプリカを増やす際、インデックスのサイズが大幅に増加することが予想される場合は、パーティション数を増やすことを検討してください。 パーティションにはインデックスのコンテンツのスライスが格納され、パーティション数が多いほど、各パーティションに格納されるスライスも小さくなります。

## <a name="see-also"></a>参照

+ [インデクサーの概要](search-indexer-overview.md)
+ [ポータル内でのインデックス作成](search-import-data-portal.md)
+ [Azure SQL Database インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage インデクサー](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage インデクサー](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search のセキュリティ](search-security-overview.md)
