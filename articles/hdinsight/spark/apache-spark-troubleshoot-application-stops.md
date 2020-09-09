---
title: Apache Spark Streaming アプリケーションが Azure HDInsight で 24 日後に停止する
description: Apache Spark Streaming アプリケーションは、24 日間の実行後に停止し、ログ ファイルにエラーはありません。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894432"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>シナリオ:Apache Spark Streaming アプリケーションが、Azure HDInsight で 24 日間実行された後に停止する

この記事では、Azure HDInsight クラスターで Apache Spark コンポーネントを使用するときのトラブルシューティングの手順と考えられる解決策について説明します。

## <a name="issue"></a>問題

Apache Spark Streaming アプリケーションは、24 日間の実行後に停止し、ログ ファイルにエラーはありません。

## <a name="cause"></a>原因

`livy.server.session.timeout` 値は、Apache Livy がセッションの完了を待機する時間を制御します。 セッションの長さが `session.timeout` 値に達すると、Livy セッションとアプリケーションが自動的に強制終了されます。

## <a name="resolution"></a>解決策

長時間実行されるジョブの場合は、Ambari UI を使用して `livy.server.session.timeout` の値を大きくします。 URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` を使用して、Ambari UI から Livy 構成にアクセスできます。

`<yourclustername>` を、ポータルに表示されている HDInsight クラスターの名前に置き換えます。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
