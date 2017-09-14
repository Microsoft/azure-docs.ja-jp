---
title: "Azure Analysis Services チュートリアルの補足のレッスン: 詳細行 | Microsoft Docs"
description: "この Azure Analysis Services チュートリアルでは、詳細行の式を作成する方法について説明します。"
services: analysis-services
documentationcenter: 
author: Minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: fde5cd9a9efc3a13e731a91962ced5c086a72355
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---
# <a name="supplemental-lesson---detail-rows"></a>補足のレッスン - 詳細行

[!INCLUDE[analysis-services-appliesto-aas-sql2017-later](../../../includes/analysis-services-appliesto-aas-sql2017-later.md)]

この補足のレッスンでは、DAX エディターを使用して、カスタムの詳細行の式を定義します。 詳細行の式はメジャーに関するプロパティです。エンドユーザーに、メジャーの集計結果についてより詳細な情報を提供します。 
  
このレッスンの推定所要時間: **10 分**  
  
## <a name="prerequisites"></a>前提条件  
この補足のレッスンのトピックは、表形式モデルのチュートリアルの一部です。 この補足のレッスンの作業を実行する前に、前のレッスンをすべて完了しているか、または Adventure Works Internet Sales のサンプル モデル プロジェクトを完了している必要があります。  
  
## <a name="what-do-we-need-to-solve"></a>解決するために必要なもの
詳細行の式を追加する前に、InternetTotalSales メジャーの詳細を見てみましょう。

1.  SSDT で、**[モデル]** メニュー > **[Excel で分析]** をクリックして Excel を開き、空のピボットテーブルを作成します。
  
2.  **[ピボットテーブルのフィールド]** で、FactInternetSales テーブルの **InternetTotalSales** メジャーを **[値]** に、DimDate テーブルの **CalendarYear** を **[列]** に、**EnglishCountryRegionName** を **[行]** に追加します。 これで、ピボットテーブルに InternetTotalSales メジャーからの集計結果が、リージョンと年ごとに表示されます。 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. ピボットテーブルで、年およびリージョン名の集計値をダブルクリックします。 ここでは、Australia と 2014 年の値をダブルクリックしています。 データが含まれた新しいシートが開きますが、このデータは実際には役に立ちません。

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
ここで表示したいのは、InternetTotalSales メジャーの集計結果に寄与するデータの列と行が含まれたテーブルです。 そのために、メジャーのプロパティとして詳細行の式を追加します。

## <a name="add-a-detail-rows-expression"></a>詳細行の式を追加する

#### <a name="to-create-a-detail-rows-expression"></a>詳細行の式を作成するには 
  
1. SSDT で、FactInternetSales テーブルのメジャー グリッドの **InternetTotalSales** メジャーをクリックします。 

2. **[プロパティ]** > **[詳細行の式]** で、エディター ボタンをクリックして DAX エディターを開きます。

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. DAX エディターで、次の式を入力します。

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    この式は、FactInternetSales テーブルの名前、列、およびメジャー結果を指定し、ユーザーがピボットテーブルまたはレポート内の集計結果をダブルクリックしたときに、関連テーブルが返されます。

4. Excel に戻って、手順 3 で作成したシートを削除し、集計値をダブルクリックします。 今回は、メジャーに対して詳細行の式プロパティが定義されているので、前回よりも役に立つデータが格納された新しいシートが開きます。

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. モデルを再デプロイします。

  
## <a name="see-also"></a>関連項目  
[SELECTCOLUMNS 関数 (DAX)](https://msdn.microsoft.com/library/mt761759.aspx)   
[補足のレッスン - 動的なセキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)  
[補足のレッスン - 不規則階層](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)  

