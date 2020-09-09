---
title: Azure Stream Analytics ジョブをコピーまたはバックアップする
description: この記事では、Stream Analytics ジョブをコピーまたはバックアップする方法について説明します。
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: 2c6b6af46ae89f794e05c3aa80716250c566257e
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037224"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブをコピーまたはバックアップする

Visual Studio Code または Visual Studio を使用して、デプロイされた Azure Stream Analytics ジョブをコピーまたはバックアップできます。 別のリージョンにジョブをコピーしても、最後の出力時刻はコピーされません。 そのため、コピーされたジョブを開始するときに、[**最終停止時刻**](https://docs.microsoft.com/azure/stream-analytics/start-job#start-options)を使用することはできません。

## <a name="before-you-begin"></a>開始する前に
* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

* [Azure portal](https://portal.azure.com/) にサインインします。

* [Visual Studio Code の Azure Stream Analytics 拡張機能](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)または [Visual Studio の Azure Stream Analytics ツール](quick-create-vs-code.md#install-the-azure-stream-analytics-tools-extension)をインストールします。  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Visual Studio Code のアクティビティ バーの **[Azure]** アイコンをクリックし、**Stream Analytics ノード**を展開します。 ジョブは、サブスクリプションの下に表示されます。

   ![Stream Analytics Explorer を開く](./media/vscode-explore-jobs/open-explorer.png)

2. ジョブをローカル プロジェクトにエクスポートするには、Visual Studio Code の **Stream Analytics Explorer** で、エクスポートするジョブを特定します。 次に、プロジェクトのフォルダーを選択します。

    ![Visual Studio Code での ASA ジョブのエクスポート](./media/vscode-explore-jobs/export-job.png)

    プロジェクトは選択したフォルダーにエクスポートされ、現在のワークスペースに追加されます。

    ![Visual Studio Code での ASA ジョブのエクスポート](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. ジョブを別のリージョンに発行するか、または別の名前を使用してバックアップするには、クエリエディター (\*.asaql) で **[Select from your subscriptions to publish]\(発行先をサブスクリプションから選択\)** を選択し、指示に従います。

    ![Visual Studio Code で Azure に発行する](./media/quick-create-vs-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. [デプロイされた Azure Stream Analytics ジョブをプロジェクトにエクスポートする指示](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-vs-tools#export-jobs-to-a-project)に従います。

2. クエリ エディターで \*.asaql ファイルを開き、スクリプト エディターで **[Azure に送信]** を選択し、指示に従ってジョブを別のリージョンに発行するか、新しい名前を使用してバックアップします。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Visual Studio Code を使用して Stream Analytics ジョブを作成する](quick-create-vs-code.md)
* [クイック スタート: Visual Studio を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [Azure Pipelines を使用して CI/CD で Azure Stream Analytics ジョブをデプロイする](stream-analytics-tools-visual-studio-cicd-vsts.md)
