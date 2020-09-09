---
title: Azure Monitor ブックのデータソース | Microsoft docs
description: 複数のデータ ソースから作成された作成済みおよびパラメーター化されたカスタム Azure Monitor ブックを使用して、複雑なレポート作成を簡素化します
services: azure-monitor
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mbullwin
ms.openlocfilehash: f27771291d95770a693fa56041f7dce3de459d13
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87081425"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor ブックのデータ ソース

ブックは、多数のデータ ソースと互換性があります。 この記事では、Azure Monitor ブックで現在使用できるデータ ソースについて説明します。

## <a name="logs"></a>ログ

ブックでは、次のソースのログを照会できます。

* Azure Monitor ログ (Application Insights リソースと Log Analytics ワークスペース)。
* リソース中心のデータ (アクティビティ ログ)

ブックの作成者は、基になるリソース データを変換する KQL クエリを使用して、テキスト、グラフ、またはグリッドとして視覚化できる結果セットを選択できます。

![ブックのログ レポート インターフェイスのスクリーンショット](./media/workbooks-overview/logs.png)

ブックの作成者は、複数のリソースを簡単に照会でき、完全に統合された優れたレポート エクスペリエンスを作成できます。

## <a name="metrics"></a>メトリック

Azure リソースは、ブックを介してアクセスできる[メトリック](data-platform-metrics.md)を生成します。 ターゲット リソース、目的のメトリック、およびそれらの集計を指定できる特別なコントロールを通じて、ブック内でメトリックにアクセスできます。 このデータは、グラフまたはグリッドにプロットできます。

![CPU 使用率のブック メトリック グラフのスクリーンショット](./media/workbooks-overview/metrics-graph.png)

![ブック メトリック インターフェイスのスクリーンショット](./media/workbooks-overview/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

ブックでは、Azure Resource Graph (ARG) を使用したリソースとそのメタデータのクエリをサポートしています。 この機能は主に、レポートのカスタム クエリ スコープを構築するために使用されます。 リソース スコープは、ARG がサポートする KQL サブセットを介して表現されます。多くの場合、一般的なユース ケースではこれで十分です。

クエリ コントロールでこのデータ ソースを使用するには、[クエリの種類] ドロップダウンを使用して [Azure Resource Graph] を選択し、ターゲットとなるサブスクリプションを選択します。 クエリ コントロールを使用して、興味深いリソース サブセットを選択する ARG KQL サブセットを追加します。

![Azure Resource Graph KQL クエリのスクリーンショット](./media/workbooks-overview/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

ブックは、Azure Resource Manager REST 操作をサポートします。 これにより、独自の Authorization ヘッダー トークンを指定する必要なく、management.azure.com エンドポイントにクエリを実行できます。

クエリ コントロールでこのデータ ソースを使用するには、[データ ソース] ドロップダウンを使用して [Azure Resource Manager] を選択します。 Http メソッド、url パス、ヘッダー、url パラメーター、本文などの適切なパラメーターを指定します。

> [!NOTE]
> 現在、`GET` 操作、`POST` 操作、`HEAD` 操作のみがサポートされています。

## <a name="azure-data-explorer"></a>Azure Data Explorer

強力な [Kusto](/azure/kusto/query/index) クエリ言語を使用できる [Azure Data Explorer](/azure/data-explorer/) クラスターからのクエリがブックでサポートされるようになりました。   

![Kusto クエリ ウィンドウのスクリーンショット](./media/workbooks-overview/data-explorer.png)

## <a name="workload-health"></a>ワークロードの正常性

Azure Monitor には、Windows または Linux ゲスト オペレーティング システムの可用性とパフォーマンスを予防的に監視する機能があります。 Azure Monitor では、主要なコンポーネントとそのリレーションシップ、それらのコンポーネントの正常性を測定する方法の基準、および異常な状態が検出されたときにアラートを通知するコンポーネントがモデル化されます。 ブックでは、ユーザーがこの情報を使用して高度な対話型レポートを作成できます。

クエリ コントロールでこのデータ ソースを使用するには、 **[クエリの種類]** ドロップダウンを使用して [ワークロードの正常性] を選択し、ターゲットとなるサブスクリプション、リソース グループ、または VM リソースを選択します。 正常性フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深い正常性インシデントのサブセットを選択します。

![アラート クエリのスクリーンショット](./media/workbooks-overview/workload-health.png)

## <a name="azure-resource-health"></a>Azure リソースの正常性

ブックでは、Azure リソースの正常性を取得し、それを他のデータ ソースと組み合わせて、優れた対話型の正常性レポートを作成できます

クエリ コントロールでこのデータ ソースを使用するには、 **[クエリの種類]** ドロップダウンを使用して [Azure の正常性] を選択し、ターゲットとなるリソースを選択します。 正常性フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深いリソースの問題のサブセットを選択します。

![アラート クエリのスクリーンショット](./media/workbooks-overview/resource-health.png)

## <a name="json"></a>JSON

JSON プロバイダーを使用すると、静的な JSON コンテンツからクエリ結果を作成できます。 これは、静的な値のドロップダウン パラメーターを作成するために、パラメーターで最もよく使用されます。 単純な JSON 配列または JSON オブジェクトは、自動的にグリッドの行と列に変換されます。  具体的な動作については、[結果] タブと JSONPath の設定を使用して、列を構成できます。

## <a name="alerts-preview"></a>アラート (プレビュー)

> [!NOTE]
> Azure アラート情報のクエリを実行する方法としては、`AlertsManagementResources` テーブルにクエリを実行して、[Azure Resource Graph](#azure-resource-graph) データ ソースを使用することをお勧めします。
>
> 例については、[Azure Resource Graph のテーブルのリファレンス](../../governance/resource-graph/reference/supported-tables-resources.md)、または[アラート テンプレート](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook)を参照してください。
>
> アラート データ ソースは、作成者が ARG の使用に移行している間は、一定期間引き続き使用できます。 テンプレートでこのデータ ソースを使用することはお勧めしません。 

ブックでは、リソースに関連するアクティブなアラートをユーザーが視覚化できます。 制限事項: アラート データ ソースでは、リソースにクエリを実行するために、サブスクリプションに対する読み取りアクセス権が必要です。また、新しい種類のアラートは表示されない場合があります。 

クエリ コントロールでこのデータ ソースを使用するには、 _[データ ソース]_ ドロップダウンを使用して _[アラート (プレビュー)]_ を選択し、ターゲットとなるサブスクリプション、リソース グループ、またはリソースを選択します。 アラート フィルターのドロップダウンを使用して、ご自分の分析ニーズに合わせて興味深いアラートのサブセットを選択します。

## <a name="custom-endpoint"></a>カスタム エンドポイント

ブックは、任意の外部ソースからのデータの取得をサポートします。 データが Azure の外部に存在する場合は、このデータ ソースの種類を使用してブックに取り込むことができます。

クエリ コントロールでこのデータ ソースを使用するには、 _[データ ソース]_ ドロップダウンを使用して、 _[カスタム エンドポイント]_ を選択します。 `Http method`、`url`、`headers`、`url parameters`、`body` などの適切なパラメーターを指定します。 データ ソースが [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) をサポートしていることを確認してください。サポートしていない場合、要求は失敗します。

テンプレートを使用するときに信頼されていないホストを自動的に呼び出すのを避けるには、ユーザーは使用しているホストを信頼済みとしてマークする必要があります。 これを行うには、 _[信頼済みとして追加]_ ボタンをクリックするか、ブックの設定で信頼されたホストとして追加します。 これらの設定は、Web ワーカーを使用して IndexDb をサポートするブラウザーに保存されます。詳細については、[こちら](https://caniuse.com/#feat=indexeddb)を参照してください。

> [!NOTE]
> すべてのブック ユーザーに表示されるため、フィールド (`headers`、`parameters`、`body`、`url`) にはシークレットを書き込まないでください。

## <a name="next-steps"></a>次のステップ

* ブックの豊富な視覚化オプションの学習を[開始](workbooks-visualizations.md)します。
* ブック リソースへのアクセスを[制御](workbooks-access-control.md)し、共有します。
* [Log Analytics のクエリ最適化のヒント](../log-query/query-optimization.md)
* 
