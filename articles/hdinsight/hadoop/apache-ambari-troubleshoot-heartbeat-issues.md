---
title: Azure HDInsight での Apache Ambari のハートビートの問題
description: Azure HDInsight での Apache Ambari のハートビートの問題のさまざまな理由のレビュー
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.openlocfilehash: ab88f65d535be2aef5f0b26fa1171c03276466e8
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057075"
---
# <a name="apache-ambari-heartbeat-issues-in-azure-hdinsight"></a>Azure HDInsight での Apache Ambari のハートビートの問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="scenario-high-cpu-utilization"></a>シナリオ:高い CPU 使用率

### <a name="issue"></a>問題

Ambari エージェントの CPU 使用率が高くなっています。これにより、一部のノードで Ambari エージェントのハートビートが失われたことを示すアラートが Ambari UI から出されています。 ハードビートが失われたというアラートは通常、一時的なものです。

### <a name="cause"></a>原因

さまざまな Ambari エージェントのバグが原因で、まれに、Ambari エージェントの CPU 使用率が高く (100 近くに) なる場合があります。

### <a name="resolution"></a>解決策

1. Ambari エージェントのプロセス ID (pid) を識別します。

    ```bash
    ps -ef | grep ambari_agent
    ```

1. 次のコマンドを実行して、CPU の使用率を表示します。

    ```bash
    top -p <ambari-agent-pid>
    ```

1. Ambari エージェントを再起動して、問題を軽減します。

    ```bash
    service ambari-agent restart
    ```

1. 再起動しても効果がない場合は、Ambari エージェント プロセスを強制終了してから開始してください。

    ```bash
    kill -9 <ambari-agent-pid>
    service ambari-agent start
    ```

---

## <a name="scenario-ambari-agent-not-started"></a>シナリオ:Ambari エージェントが開始されていない

### <a name="issue"></a>問題

Ambari エージェントが開始されていません。これにより、一部のノードで Ambari エージェントのハートビートが失われたことを示すアラートが Ambari UI から出ています。

### <a name="cause"></a>原因

アラートは、Ambari エージェントが実行されていないことが原因で発生します。

### <a name="resolution"></a>解決策

1. Ambari エージェントの状態を確認します。

    ```bash
    service ambari-agent status
    ```

1. フェールオーバー コントローラー サービスが実行されているかどうかを確認します。

    ```bash
    ps -ef | grep failover
    ```

    フェールオーバー コントローラー サービスが実行されていない場合は、問題によって、HDInsight エージェントがフェールオーバー コントローラーを起動できないことが原因の可能性があります。 `/var/log/hdinsight-agent/hdinsight-agent.out` ファイルから HDInsight エージェントのログを確認してください。

## <a name="scenario-heartbeat-lost-for-ambari"></a>シナリオ:Ambari のハートビートが失われた

### <a name="issue"></a>問題

Ambari のハートビート エージェントが失われました。

### <a name="cause"></a>原因

OMS ログで CPU 使用率が高くなっています。

### <a name="resolution"></a>解決策

* [Disable-AzHDInsightMonitoring](https://docs.microsoft.com/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) PowerShell コマンドレットを使用して Azure Monitor ログ記録を無効にします。
* `mdsd.warn` ログ ファイルを削除します

---

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
