---
title: Team Data Science Process のデータの取得と理解
description: データ サイエンス プロジェクトのデータの取得と理解のステージの目標、タスク、成果物
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 85d7159edb1bc8608165264578d77519bf09efc2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195062"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Team Data Science Process のデータの取得と理解のステージ

この記事では、Team Data Science Process (TDSP) のデータの取得と理解のステージに関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

   1. **ビジネスの把握**
   2. **データの取得と理解**
   3. **モデリング**
   4. **デプロイ**
   5. **顧客による受け入れ**

TDSP ライフサイクルを視覚化すると、次のようになります。 

![TDSP ライフサイクル](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* ターゲット変数との関係が理解されている、クリーンで高品質のデータ セットを作成します。 データ セットを適切な分析環境に配置して、モデリングに使用できるようにします。
* データの定期的な更新とスコア付けを行うデータ パイプラインのソリューション アーキテクチャを開発します。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 3 つの主な課題に取り組みます。

   * ターゲットの分析環境に**データを取り込む**。
   * データの品質が質問に回答するのに十分かどうかを判断するために、**データを調べる**。 
   * 新しいデータや定期的に更新されるデータをスコア付けするための**データ パイプラインを設定する**。

### <a name="ingest-the-data"></a>データを取り込む
ソースの場所から、トレーニングや予測などの分析操作を実行するターゲットの場所にデータを移動するプロセスを設定します。 データの移動を各種 Azure データ サービスで行う方法についての技術的な詳細とオプションについては、「[分析用のストレージ環境にデータを読み込む](ingest-data.md)」を参照してください。 

### <a name="explore-the-data"></a>データを検索する
モデルのトレーニングを行う前に、データを正しく理解する必要があります。 実際のデータ セットは、ノイズが多かったり、値が欠落していたり、その他の不一致が数多く見つかったりすることがよくあります。 データをモデリングに使用する前に、データの要約と視覚化を使用して、データの品質を監査し、データを処理するために必要な情報を得ることができます。 このプロセスは、通常は繰り返し実行されます。

TDSP には、データの視覚化と概要レポートの準備に役立つ、[IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) と呼ばれる自動化されたユーティリティが用意されています。 IDEAR でデータを調べることから始めて、コーディングを行わずに対話形式でデータへの理解を深めることをお勧めします。 その後、データの調査と視覚化用のカスタム コードを記述できます。 データのクリーニングに関するガイダンスについては、「[機械学習を強化するためのデータを準備するタスク](prepare-data.md)」を参照してください。  

クレンジング済みデータの品質に満足したら、次の手順は、データに内在するパターンの理解を深めることです。 このデータ分析により、ターゲットに適した予測モデルを選択して開発することができます。 ターゲットに対するデータの関連性を表す証拠を探します。 その後、次のモデリング手順に進むための十分なデータがあるかどうかを確認します。 このプロセスも、通常は繰り返し実行されます。 場合によっては、前のステージで最初に特定したデータ セットを強化するために、より正確なデータや関連性の高いデータを持つ新しいデータ ソースを探すことが必要です。 

### <a name="set-up-a-data-pipeline"></a>データ パイプラインを設定する
最初に行うデータの取り込みとクリーニングに加え、通常は、新しいデータをスコア付けするプロセスやデータを定期的に更新するプロセスを、継続的な学習プロセスの一部として設定する必要があります。 スコア付けは、データ パイプラインまたはワークフローを使用して行うことができます。 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) を使用してパイプラインを設定する方法の例については、[Azure Data Factory を使用した SQL Server インスタンスから Azure SQL Database へのデータの移動](move-sql-azure-adf.md)に関するトピックを参照してください。 

このステージでは、データ パイプラインのソリューション アーキテクチャを開発します。 データ サイエンス プロジェクトの次のステージで、パイプラインを並行して開発します。 パイプラインは、ビジネス ニーズとこのソリューションを統合する既存システムの制約に応じて、次のオプションのいずれかにすることができます。 

   * バッチ ベース
   * ストリーミングまたはリアル タイム 
   * ハイブリッド 

## <a name="artifacts"></a>アーティファクト
このステージでの成果物を以下に示します。

   * [データ品質レポート](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): このレポートには、データの概要、各属性とターゲットの関係、変数のランクなどが含まれます。 TDSP の一部として提供される [IDEAR](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/DataReport-Utils) ツールを使用すると、CSV ファイルやリレーショナル テーブルなどの任意の表形式のデータ セットで、このレポートをすばやく生成できます。 
   * **ソリューション アーキテクチャ**: ソリューション アーキテクチャは、モデルの構築後に、新しいデータでのスコア付けや予測の実行に使用されるデータ パイプラインのダイアグラムまたは説明です。 また、新しいデータに基づきモデルを再トレーニングするためのパイプラインも、これに含まれます。 TDSP ディレクトリ構造テンプレートを使用する場合は、[プロジェクト](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) ディレクトリにドキュメントを格納します。
   * **チェックポイント判定**:特徴エンジニアリングとモデルの構築を本格的に開始する前に、プロジェクトを再評価し、期待される値がそのプロジェクトを続行するために十分であるかどうかを判断できます。 たとえば、先に進む準備ができている、さらに多くのデータを収集する必要がある、質問に回答するためのデータが存在しないためプロジェクトを破棄するといった判断ができます。

## <a name="next-steps"></a>次のステップ

TDSP のライフサイクルの各ステップへのリンクを次に示します。

   1. [ビジネスの把握](lifecycle-business-understanding.md)
   2. [データの取得と理解](lifecycle-data.md)
   3. [モデリング](lifecycle-modeling.md)
   4. [デプロイ](lifecycle-deployment.md)
   5. [顧客による受け入れ](lifecycle-acceptance.md)

特定のシナリオのプロセスでのすべての手順を示す完全なチュートリアルが用意されています。 [例を含むチュートリアル](walkthroughs.md)の記事に、各シナリオが、簡潔な説明とリンク付きで示されています。 これらのチュートリアルは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明しています。 

Azure Machine Learning Studio を使用して TDSP の手順を実行する方法の例については、「[Azure Machine Learning での Team Data Science Process の使用](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle-data)」を参照してください。
