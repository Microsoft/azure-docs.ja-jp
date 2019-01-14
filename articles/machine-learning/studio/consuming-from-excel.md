---
title: Excel で Web サービスを使用する - Azure Machine Learning Studio | Microsoft Docs
description: Azure Machine Learning Studio では、Excel から直接 Web サービスを簡単に呼び出すことができ、コードを作成する必要がありません。
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 3f3cdd2f-1816-487e-ab78-530e01e9788f
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: ad7eae16c2933790aefba3cee1551be29ee457be
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276929"
---
# <a name="consuming-an-azure-machine-learning-studio-web-service-from-excel"></a>Excel からの Azure Machine Learning Studio Web サービスの使用

 Azure Machine Learning Studio では、Excel から直接 Web サービスを簡単に呼び出すことができ、コードを作成する必要がありません。

Excel 2013 (またはそれ以降) または Excel Online を使用している場合は、Excel の [Excel アドイン](excel-add-in-for-web-services.md)を使用することをお勧めします。



## <a name="steps"></a>手順
Web サービスを発行します。 [このページ](walkthrough-5-publish-web-service.md) では、その方法を説明しています。 現在、Excel ブック機能は、1 つの出力を持つ要求/応答サービス (つまり 1 つのスコア付けラベル) のみサポートしています。 

Web サービスを作成したら、Studio の左側の **[WEB サービス]** セクションをクリックして、Excel から使用する Web サービスを選択します。

**従来の Web サービス**

1. Web サービスの **[ダッシュボード]** タブに、**要求/応答**サービスの行があります。 このサービスに 1 つの出力がある場合は、その行の **[Excel ブックのダウンロード]** のリンクを確認してください。
   
    ![][1]
2. **[Excel ブックのダウンロード]** をクリックします。

**新しい Web サービス**

1. Azure Machine Learning Web サービス ポータルで **[Consume (使用)]** を選択します。
2. [Consume (使用)] ページの **[Web service consumption options (Web サービスの使用オプション)]** セクションで、[Excel] アイコンをクリックします。

**ブックを使用する**

1. ブックを開きます。
2. セキュリティの警告が表示されます。**[編集を有効にする]** ボタンをクリックします。
   
    ![][2]
3. セキュリティの警告が表示されます。 **[コンテンツの有効化]** ボタンをクリックして、スプレッドシートのマクロを実行します。
   
    ![][3]
4. マクロが有効になると、テーブルが生成されます。 青の列は RRS Web サービス、または **PARAMETERS**への入力として必要です。 RRS サービス **PREDICTED VALUES** の出力は緑色で表示されます。 特定の行のすべての列がいっぱいになると、ブックは自動的に、スコア付け API を呼び出し、スコア付けされた結果を表示します。
   
    ![][4]
5. 複数の行のスコア付けを実行するには、2 番目の行にデータを入力すると、予測値が生成されます。 複数の行を一度に貼り付けることもできます。

これで、Excel 機能 (グラフ、Power Map、条件付き書式など) と予測値を使用して、データの視覚化に役立てることができます。    

## <a name="sharing-your-workbook"></a>ブックを共有する
マクロが機能するには、API キーがスプレッドシートの一部である必要があります。 つまり、信頼するエンティティ/個人とだけ、ブックを共有する必要があります。

## <a name="automatic-updates"></a>自動更新
RRS 呼び出しは、次の 2 つの状況で発生します。

1. 初めて行のすべての **PARAMETERS**
2. すべての **PARAMETERS** に入力されている行の任意の **PARAMETERS** が変更されたとき。

[1]: ./media/consuming-from-excel/excellink.png
[2]: ./media/consuming-from-excel/enableeditting.png
[3]: ./media/consuming-from-excel/enablecontent.png
[4]: ./media/consuming-from-excel/sampletable.png
