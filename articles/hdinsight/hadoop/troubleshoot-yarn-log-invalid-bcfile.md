---
title: Azure HDInsight で Apache Yarn ログを読み取ることができない
description: Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策。
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: 02a79de8aee169f5f702d5fae67194c62363e8c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943049"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>シナリオ: Azure HDInsight で Apache Yarn ログを読み取ることができない

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

ストレージ アカウントから検出された Apache Yarn ログは、人間が読めるものではありません。 ファイル パーサーが動作せず、次のエラー メッセージが生成されます。

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn ログは `IndexFile` 形式に集約されますが、これはファイル パーサーでサポートされていません。

## <a name="resolution"></a>解決方法

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. Ambari UI から **[YARN]**  >  **[Configs]/(構成/)**  >  **[Advanced]/(拡張/)**  >  **[Advanced yarn-site] /(拡張 YARN-サイト/)** に移動します。

1. WASB ストレージの場合: `yarn.log-aggregation.file-formats` の既定値 は `IndexedFormat,TFile` です。 値を `TFile` に変更します。

1. ADLS ストレージの場合: `yarn.nodemanager.log-aggregation.compression-type` の既定値 は `gz` です。 値を `none` に変更します。

1. 変更を保存し、影響を受けるすべてのサービスを再起動します。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](../../azure-portal/supportability/how-to-create-azure-support-request.md)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。