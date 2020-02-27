---
title: Azure HDInsight での Apache Ambari の使用法
description: Azure HDInsight での Apache Ambari の使用方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 466c170985715be52a90d579c19ca23aefefe2e5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066002"
---
# <a name="apache-ambari-usage-in-azure-hdinsight"></a>Azure HDInsight での Apache Ambari の使用法

HDInsight は、クラスターのデプロイと管理に Apache Ambari を使用します。 Ambari エージェントは、ヘッド ノード、ワーカー ノード、Zookeeper、エッジ ノード (存在する場合) といったすべてのノードで実行されます。 Ambari サーバーは、ヘッド ノード (hn0 または hn1) でのみ実行されます。 Ambari サーバーのインスタンスは、一度に 1 つしか実行できません。 これは、HDInsight フェールオーバー コントローラーによって制御されます。 ヘッド ノードの 1 つが再起動またはメンテナンスのために停止している場合は、もう 1 つのヘッド ノードがアクティブになり、2 番目のヘッド ノードの Ambari サーバーが開始されます。

すべてのクラスター構成は、[Ambari UI](./hdinsight-hadoop-manage-ambari.md) を通じて実行する必要があります。ローカルの変更はすべて、ノードの再起動時に上書きされます。

## <a name="failover-controller-services"></a>フェールオーバー コントローラー サービス

HDInsight フェールオーバー コントローラーは、現在のアクティブ ヘッド ノードを指す、ヘッド ノード ホストの IP アドレスを更新する役割も担います。 すべての Ambari エージェントは、その状態とハートビートをヘッド ノード ホストに報告するように構成されています。 フェールオーバー コントローラーは、クラスター内のすべてのノードで実行されている一連のサービスです。それらが実行されていない場合、ヘッド ノードのフェールオーバーが正しく機能しない可能性があり、Ambari サーバーにアクセスしようとすると HTTP 502 が発生します。

どのヘッド ノードがアクティブかを確認するための 1 つの方法としては、クラスター内のいずれかのノードに SSH 接続し、`ping headnodehost` を実行して、IP を 2 つのヘッド ノードのものと比較します。

フェールオーバー コントローラー サービスが実行されていない場合は、ヘッド ノードのフェールオーバーが正しく行われない可能性があり、それにより、Ambari サーバーが実行されない可能性があります。 フェールオーバー コントローラー サービスが実行されているかどうかを確認するには、次を実行します。

```bash
ps -ef | grep failover
```

## <a name="logs"></a>ログ

アクティブなヘッド ノードでは、Ambari サーバーのログを次の場所で確認できます。

```
/var/log/ambari-server/ambari-server.log
/var/log/ambari-server/ambari-server-check-database.log
```

クラスター内のどのノードでも、Ambari エージェントのログは次の場所で確認できます。

```bash
/var/log/ambari-agent/ambari-agent.log
```

## <a name="service-start-sequences"></a>サービス開始シーケンス

起動時のサービス開始の順序は次のとおりです。

1. Hdinsight エージェントが、フェールオーバー コントローラー サービスを開始します。
1. フェールオーバー コントローラー サービスが、アクティブなヘッド ノード上のすべてのノードと Ambari サーバーで Ambari エージェントを開始します。

## <a name="ambari-database"></a>Ambari データベース

HDInsight は、Ambari サーバーのデータベースとして機能する SQL Azure データベースを内部で作成します。 既定の[サービス レベルは S0](../sql-database/sql-database-elastic-pool-scale.md) です。

クラスターを作成するときにワーカー ノードの数が 16 を超えるクラスターの場合は、S2 がデータベースのサービス レベルです。

## <a name="takeaway-points"></a>要点

問題を回避するためにサービスを再起動しようとしている場合を除き、ambari-server または ambari-agent サービスを手動で開始/停止しないでください。 フェールオーバーを強制するには、アクティブなヘッド ノードを再起動します。

クラスター ノード上の構成ファイルはいずれも手動で変更しないでください。その操作は、Ambari UI が自動的に実行します。

## <a name="next-steps"></a>次のステップ

* [Apache Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari-rest-api.md)

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure コミュニティで適切なリソース (回答、サポート、エキスパートなど) につながる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」を参照してください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
