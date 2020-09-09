---
title: Apache Hive ビューにエラー メッセージが表示されない - Azure HDInsight
description: Azure HDInsight クラスターの Apache Hive ビューで詳細情報が表示されずに、クエリが失敗します。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 5aa03ac3537783daef87c9e7cb7d4ec58988ea9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895214"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>シナリオ:Azure HDInsight の Apache Hive ビューにクエリのエラー メッセージが表示されない

この記事では、Azure HDInsight クラスターで Interactive Query コンポーネントを使用するときのトラブルシューティングの手順と問題の可能な解決策について説明します。

## <a name="issue"></a>問題

Apache Hive ビューのクエリのエラー メッセージが次のように表示され、詳細情報がありません。

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>原因

クエリ エラーのエラー メッセージが大きすぎて、Hive ビューのメイン ページに表示できないことがあります。

## <a name="resolution"></a>解決策

Hive_view の右上にある [通知] タブで、完全なスタックトレースとエラー メッセージを確認します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
