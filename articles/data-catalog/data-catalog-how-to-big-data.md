---
title: "&quot;ビッグ データ&quot; のデータ ソースを操作する方法 | Microsoft Docs"
description: "Azure Blob Storage、Azure Data Lake、Hadoop HDFS などの &quot;ビッグ データ&quot; のデータ ソースと共に Azure Data Catalog を使用するためのパターンを説明する、操作方法に関する記事。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8cf7ea7ca69753c12469dff9dc352d616c276ddd


---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>Azure Data Catalog でビッグ データ ソースを操作する方法
## <a name="introduction"></a>はじめに
**Microsoft Azure Data Catalog** は、完全に管理されたクラウド サービスであり、エンタープライズ データ ソースの登録のシステムと検出のシステムとして機能します。 つまり、 **Azure Data Catalog** とは、ユーザーがデータ ソースを検出、理解、使用するために役立つサービスであり、組織が既存のデータ ソース (ビッグ データなど) から、より多くの価値を引き出すために利用することもできます。

**Azure Data Catalog** では、Azure BLOG ストレージの BLOB とディレクトリに加え、Hadoop HDFS のファイルとディレクトリの登録がサポートされています。 これらのデータ ソースの半構造化という性質によって高い柔軟性は得られますが、これは同時に、 **Azure Data Catalog**へのデータ ソースの登録による効果を最大限に引き出すためには、データ ソースを構造化する方法をユーザーが検討する必要があることを意味します。

## <a name="directories-as-logical-data-sets"></a>論理データ セットとしてのディレクトリ
ビッグ データ ソースを構造化するための一般的なパターンでは、ディレクトリを論理データ セットとして扱います。 最上位レベルのディレクトリはデータ セットの定義に使用されるのに対し、サブフォルダーはパーティションを定義し、それに含まれるファイルはデータそのものを格納しています。

このパターンの例は次のようになります。

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

この例では、vehicle_maintenance_events と location_tracking_events が論理データ セットを表しています。 これらの各フォルダーには、年および月単位でサブフォルダーに分類されたデータ ファイルが含まれています。 各フォルダーに含まれるファイルの数は、数百から数千に及ぶ可能性があります。

このパターンでは、個々のファイルを **Azure Data Catalog** に登録しても、おそらく効果はありません。 代わりに、データを操作するユーザーにとって意味のあるデータ セットを表すディレクトリを登録してください。

## <a name="reference-data-files"></a>参照データ ファイル
補足的なパターンでは、参照データ セットを個々のファイルとして格納します。 このようなデータ セットは、ビッグ データの "小さな" 側面と見なされる可能性があり、多くの場合は、分析データ モデルにおける次元に似ています。 参照データ ファイルにはレコードが含まれていますが、レコードは、ビッグ データ ストアのどこかに格納されているデータ ファイルの大部分についてのコンテキストを提供するために使用されます。

このパターンの例は次のようになります。

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

アナリストやデータ サイエンティストがより大規模なディレクトリ構造に含まれるデータを操作する場合、これらの参照ファイル内のデータは、より大きなデータ セット内で名前または ID のみで参照されるエンティティについてより詳細な情報を提供するために使用できます。

このパターンでは、個々の参照データ ファイルを **Azure Data Catalog**に登録することが理にかなっています。 各ファイルはデータ セットを表し、個別に注釈を付けたり探索したりできます。

## <a name="alternate-patterns"></a>代替パターン
ここまでに説明したパターンは、ビッグ データ ストアを構造化できるただ 2 つの方法ですが、それぞれの実装は異なります。 データ ソースがどのように構造化されているかに関係なく、ビッグ データ ソースを **Azure Data Catalog**に登録する際は、組織内の他のユーザーにとって価値のあるデータ セットを表すファイルまたはディレクトリを登録することに重点を置いてください。 すべてのファイルとディレクトリを登録すると、カタログは煩雑になり、ユーザーが必要なデータを見つけにくくなります。

## <a name="summary"></a>概要
データ ソースを **Azure Data Catalog** に登録すると、そのデータ ソースの探索や理解が簡単になります。 ビッグ データ ファイルと、論理データ セットを表すディレクトリを登録して注釈を付けることで、ユーザーが必要なビッグ データを見つけて使用することを支援できます。



<!--HONumber=Nov16_HO3-->


