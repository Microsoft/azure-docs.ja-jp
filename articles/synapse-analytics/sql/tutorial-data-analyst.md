---
title: SQL オンデマンド (プレビュー) を使用して Azure Synapse Studio (プレビュー) で Azure Open Datasets を分析する
description: このチュートリアルでは、SQL オンデマンド (プレビュー) を使用してさまざまな Azure Open Datasets を組み合わせた探索的データ分析を簡単に実行し、Azure Synapse Studio で結果を視覚化する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2fc98e927fcf9686f0f39dae600f944b485c5a06
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089143"
---
# <a name="use-sql-on-demand-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio"></a>SQL オンデマンドを使用した Azure Open Datasets の分析と Azure Synapse Studio での結果の視覚化

このチュートリアルでは、SQL オンデマンドを使用してさまざまな Azure Open Datasets を組み合わせることで探索的データ分析を実行し、Azure Synapse Studio で結果を視覚化する方法について説明します。

特に、[ニューヨーク市 (NYC) のタクシー データセット](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)を分析します。これには次のものが含まれます。

- 乗車日時と降車日時。
- 乗車地点と降車地点。 
- 移動距離。
- 料金明細。
- 料金の種類。
- 支払いの種類。 
- 運転手から報告された乗客の人数。

## <a name="automatic-schema-inference"></a>自動スキーマ推論

データは Parquet ファイル形式で格納されるため、自動スキーマ推論を使用できます。 ファイル内のすべての列のデータ型を一覧表示することなく、簡単にデータに対するクエリを実行できます。 また、仮想列のメカニズムと filepath 関数を利用して、ファイルの特定のサブセットを除外することもできます。

まず、NYC のタクシー データについて理解するために、次のクエリを実行します。

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

NYC のタクシー データの結果を次のスニペットに示します。

![NYC のタクシー データの結果のスニペット](./media/tutorial-data-analyst/1.png)

同様に、次のクエリを使用して、休日のデータセットに対してクエリを実行できます。

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

休日のデータセットの結果を次のスニペットに示します。

![休日のデータセットの結果のスニペット](./media/tutorial-data-analyst/2.png)

最後に、次のクエリを使用して、気象データセットに対してもクエリを実行できます。

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

気象データセットの結果を次のスニペットに示します。

![気象データセットの結果のスニペット](./media/tutorial-data-analyst/3.png)

個々の列の意味の詳細については、[NYC タクシー](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)、[休日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)、および[気象データ](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)のデータセットに関する説明を参照してください。

## <a name="time-series-seasonality-and-outlier-analysis"></a>時系列、季節性、および外れ値の分析

次のクエリを使用して、毎年のタクシー乗車数を簡単にまとめることができます。

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

毎年のタクシー乗車数の結果を次のスニペットに示します。

![毎年のタクシー乗車数の結果のスニペット](./media/tutorial-data-analyst/4.png)

データは Synapse Studio で**テーブル** ビューから**グラフ** ビューに切り替えることによって視覚化できます。 さまざまな種類のグラフ ( **[面]** 、 **[横棒]** 、 **[縦棒]** 、 **[折れ線]** 、 **[円]** 、 **[散布図]** など) から選択できます。 この例で、 **[Category** column]\(カテゴリ列\) を **current_year** に設定した **[縦棒]** グラフをプロットしてみましょう。

![年間の乗車数を示す縦棒グラフ](./media/tutorial-data-analyst/5.png)

この視覚化から、乗車数が年々減少している傾向がはっきりと見て取れます。 この減少はおそらく、近年のライドシェア企業の人気の高まりが原因であると思われます。

> [!NOTE]
> このチュートリアルの執筆時点では、2019 年のデータは不完全です。 その結果、その年の乗車数が著しく低下しています。

次に、単年度の分析に重点を置いてみましょう。たとえば 2016 年に注目します。 次のクエリでは、その年の毎日の乗車数が返されます。

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

このクエリの結果を次のスニペットに示します。

![2016 年の毎日の乗車数の結果のスニペット](./media/tutorial-data-analyst/6.png)

ここでも、 **[Category** column]\(カテゴリ列\) を **current_day** に設定し、 **[Legend (series)** column]\(凡例 (系列) 列\) を **rides_per_day** に設定した **[縦棒]** グラフをプロットすることで、データを簡単に視覚化できます。

![2016 年の毎日の乗車数を示す縦棒グラフ](./media/tutorial-data-analyst/7.png)

プロット グラフから、土曜日をピーク日とする週単位のパターンがあることが確認できます。 夏期は休暇のため、毎月のタクシー乗車数が減少します。 時期および理由の観点から明確なパターンがないのにタクシー乗車数が大幅に減少することもあります。

次に、NYC タクシー乗車数のデータセットを休日のデータセットと組み合わせることによって、これらの減少が休日と関連しているかどうかを見てみましょう。

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC タクシー乗車数のデータセットと休日データセットの結果の視覚化](./media/tutorial-data-analyst/8.png)

今度は、休日のタクシー乗車数を強調してみましょう。 そのために、 **[Category** column]\(カテゴリ列\) に **none**、 **[Legend (series)** columns]\(凡例 (系列) 列\) に **rides_per_day** および **holiday** を選択します。

![休日のタクシー乗車数のプロット グラフ](./media/tutorial-data-analyst/9.png)

このプロット グラフから、休日はタクシー乗車数が少なくなることがわかります。 1 月 23 日の大幅な減少については、まだ説明がついていません。 では、気象データセットに対してクエリを実行して、その日のニューヨーク市の天気を確認してみましょう。

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![気象データセットの結果の視覚化](./media/tutorial-data-analyst/10.png)

このクエリの結果から、タクシー乗車数の減少は次のことが原因であることが示されます。

- 当日、ニューヨーク市に吹雪が発生して大雪だった (約 30 cm)。
- 寒かった (気温が摂氏 0 度を下回った)。
- 強風だった (約 10 m/秒)。

このチュートリアルでは、データ アナリストが探索的データ分析を迅速に実行し、SQL オンデマンドを使用してさまざまなデータセットを簡単に結合し、Azure Synapse Studio を使用して結果を視覚化する方法について説明しました。

## <a name="next-steps"></a>次のステップ

SQL オンデマンドを Power BI Desktop に接続してレポートを作成する方法については、[SQL オンデマンドの Power BI Desktop への接続とレポートの作成](tutorial-connect-power-bi-desktop.md)に関する記事を参照してください。
 
