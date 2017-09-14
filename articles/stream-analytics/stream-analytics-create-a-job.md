---
title: "Stream Analytics のデータ分析処理ジョブを作成する方法 | Microsoft Docs"
description: "Stream Analytics のデータ分析処理ジョブの作成 | ラーニング パス セグメント。"
keywords: "データ分析処理"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: e825fbcf-69e9-443f-b402-3b7a4568f415
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 05fdf1e20efd129cdfc27e1d37bc9e124edf5dcd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Stream Analytics のデータ分析処理ジョブを作成する方法
Azure Stream Analytics の最上位のリソースは Stream Analytics ジョブです。  ジョブは、1 つ以上の入力データ ソース、データ変換を表すクエリ、結果が書き込まれる 1 つ以上の出力ターゲットで構成されます。 ユーザーはこれらのジョブを使用して、ストリーミング データ シナリオのデータ分析処理を実行できます。

Stream Analytics の使用を開始するには、まず新しい Stream Analytics ジョブを作成します。  ジョブが開始されるまでこのアクションによる課金への影響はないことに注意してください。

1. オンラインの [Azure クラシック ポータル](http://manage.windowsazure.com)または [Azure Portal](https://portal.azure.com/)にサインインします。
2. ポータルで **[新規]** をクリックし、ポータルに応じて **[Data Services]** または **[データ分析]** をクリックし、**[Azure Stream Analytics]** または **[Stream Analytics]** をクリックしてから **[簡易作成]** をクリックします。
   
   ![データ分析処理ジョブ ウィザード](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  
   
   ![データ分析処理ジョブの作成](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  
3. Stream Analytics ジョブに必要な構成を指定します。
   
   * **[ジョブ名]** ボックスに、Stream Analytics ジョブを識別する名前を入力します。 **ジョブ名** が検証されると、緑色のチェック マークが [ジョブ名] ボックスに表示されます。 **[ジョブ名]** で使用できるのは英数字と "-" のみであり、文字数は 3 ～ 63 文字でなければなりません。
   * Azure Portal の **[リージョン]**、または Azure Portal の **[場所]** を使用して、ジョブを実行する地理的な位置を指定します。
   * Azure ポータルを使用している場合は、 **[地域モニタリング ストレージ アカウント]**として使用するストレージ アカウントを選択または作成します。 このストレージ アカウントを使用して、この地域内で実行されるすべての Stream Analytics ジョブの監視データを格納します。
   * Azure ポータルを使用している場合は、アプリケーションに関連するリソースを保持する、新規または既存の **リソース グループ** を指定します。
4. 新しい Stream Analytics ジョブのオプションを構成した後、 **[Stream Analytics ジョブの作成]**をクリックします。 Stream Analytics ジョブが作成されるまで数分かかる場合があります。 Notification Hub で進行状況を監視し、状態を確認できます。
   
   ![データ分析処理ジョブの通知ハブ](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  
   
   ![Azure ポータルのデータ分析処理ジョブのジョブの作成](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  
5. 新しいジョブに **[作成済み]** の状態が表示されます。 **[開始]** ボタンが無効になっていることに注意してください。 ジョブが開始できるようにするには、事前にジョブの入力、クエリ、出力を構成します。
   
   ![データ分析処理ジョブの状態](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  
   
   ![Azure ポータルのデータ分析処理ジョブの状態](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


