---
title: Web サービス用の Excel アドイン - Azure Machine Learning Studio | Microsoft Docs
description: コードを作成することなく、Excel から直接 Azure Machine Learning Web サービスを使用する方法。
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: seodec18
ms.author: amlstudiodocs
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2018
ms.openlocfilehash: e836323ad452b86d89eee62936b574b2cc9d0337
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269500"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Azure Machine Learning Studio Web サービスの Excel アドイン
Excel を使用すると、コードを作成することなく、直接 Web サービスを簡単に呼び出すことができます。

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>ブックで既存の Web サービスを使用する手順

1. [サンプルの Excel ファイル](https://aka.ms/amlexcel-sample-2)を開きます。このファイルには、Excel アドインと、タイタニック号の乗客に関するデータが含まれます。 
 
> [!NOTE]
> ファイルに関係する Web サービスの一覧が表示され、下部に [Auto-predict]\(自動予測\) のチェックボックスが表示されます。 自動予測を有効にすると、入力が変更されるたびに**すべての**サービスの予測が更新されます。 オフにした場合は、[Predict All]\(すべて予測\) をクリックして更新する必要があります。 サービス レベルで自動予測を有効にするには、手順 6 に移動します。

2. 対象の Web サービスをクリックして選択します。この例では、「Titanic Survivor Predictor (Excel Add-in Sample) [Score]」を選択します。
   
    ![Web サービスの選択][01]
3. この操作により、**[予測]** セクションが表示されます。  このブックにはサンプル データが既に含まれていますが、空のブックで Excel のセルを選択して、 **[サンプル データを使用]** をクリックすることもできます。
4. ヘッダーを含めてデータを選択し、入力データ範囲のアイコンをクリックします。  [先頭行をデータの見出しとして使用する] ボックスを必ずオンにします。
5. **[出力]** で、データを出力するセル番号を入力します。この場合、「H1」などと入力します。
6. **[予測]** をクリックします。 [Auto-predict]\(自動予測\) チェックボックスを選択すると、予測ボタンを押さなくても、選択した領域 (入力として指定した領域) での任意の変更によって、出力セルの要求と更新がトリガーされます。
   
    ![[予測] セクション][02]

Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイの詳細については、「[チュートリアル手順 5:Azure Machine Learning Web サービスをデプロイする](walkthrough-5-publish-web-service.md)」をご覧ください。

Web サービスの API キーを取得します。 取得元は、クラシック Machine Learning Web サービスを発行したか、新しい Machine Learning Web サービスを発行したかによって異なります。

**クラシック Web サービスを使用する** 

1. Machine Learning Studio の左側の **[Web サービス]** セクションをクリックして、Web サービスを選択します。
   
    ![Studio での Web サービスの選択][04]
2. Web サービスの API キーをコピーします。
   
    ![Studio での API キー][05]
3. Web サービスの **[ダッシュボード]** タブで、**[要求/応答]** をクリックします。
4. **[Request URI (要求 URI)]** セクションを探します。  URL をコピーして保存します。

> [!NOTE]
> これで、[Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインしてクラシック Machine Learning Web サービスの API キーを取得できます。
> 
> 

**新しい Web サービスを使用する**

1. [Azure Machine Learning Web サービス](https://services.azureml.net) ポータルで、**[WEB サービス]** をクリックし、Web サービスを選択します。 
2. **[Consume (使用)]** をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー**と**要求応答** URL をコピーして保存します。

## <a name="steps-to-add-a-new-web-service"></a>新しい Web サービスを追加する手順

1. Web サービスをデプロイするか、既存の Web サービスを使用します。 Web サービスのデプロイの詳細については、「[チュートリアル手順 5:Azure Machine Learning Web サービスをデプロイする](walkthrough-5-publish-web-service.md)」をご覧ください。
2. **[Consume (使用)]** をクリックします。
3. **[Basic consumption info (基本的な実行情報)]** を探します。 **主キー**と**要求応答** URL をコピーして保存します。
4. Excel で、**[Web サービス]** セクションに移動します (**[予測]** セクションにいる場合は、戻る矢印をクリックして Web サービスの一覧に移動します)。
   
    ![Web サービスの選択への移動][03]
5. **[Web サービスの追加]** をクリックします。
6. **URL**というラベルが付いた、Excell アドインのテキスト ボックスに URL を貼り付けます。
7. **API キー**というラベルが付いたテキスト ボックスに API/主キーを貼り付けます。
8. **[追加]** をクリックします。
   
    ![クラシック Web サービスの URL と API キー。][06]
9. Web サービスを使用するには、前述の「既存の Web サービスを使用する手順」に従います。

## <a name="sharing-your-workbook"></a>ブックを共有する
ブックを保存すると、追加した Web サービス用の API/主キーも保存されます。 このため、信頼できる個人とのみブックを共有することをお勧めします。

ご質問は、次のコメント セクション、または[フォーラム](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409)までお寄せください。

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
