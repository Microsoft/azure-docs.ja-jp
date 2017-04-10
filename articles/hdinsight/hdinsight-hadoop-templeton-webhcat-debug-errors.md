---
title: "HDInsight の WebHCat エラーの説明と解決策"
description: "HDInsight で WebHCat から返される一般的なエラーの原因とその解決策について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1b3d94b1-207d-4550-aece-21dc45485549
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: 2fbbfd3d221ef2f6bce302ed169eddf5a8b0d73e
ms.lasthandoff: 01/18/2017


---
# <a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>HDInsight で WebHCat (Templeton) から受信したエラーの説明と解決策
WebHCat (旧称 Templeton) を使用して HDInsight を使用するときに、エラーが発生することがあります。 このドキュメントでは、一般的なエラーのガイダンスとして、エラーが発生する理由と、そのエラーを解決策として考えられるものを説明します。

## <a name="what-is-webhcat"></a>WebHCat とは
[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) は、[Hadoop](https://cwiki.apache.org/confluence/display/Hive/HCatalog) のテーブルおよびストレージの管理層である HCatalog 用の REST API です。 WebHCat は、HDInsight クラスターで既定で有効になっており、ジョブの送信、ジョブの状態の取得などの操作をクラスターにログインすることなく行うために、さまざまなツールで使用されます。

## <a name="modifying-configuration"></a>構成の変更
> [!IMPORTANT]
> このドキュメントに示すエラーのいくつかは、構成されている最大値が超過したことが原因で発生します。 解決策に値が変更可能であることが示されている場合は、次のどちらかの方法で値を変更する必要があります。


* **Windows** クラスターの場合: スクリプト アクションを使用して、クラスターの作成時に値を構成します。 詳細については、 [スクリプト アクションの開発](hdinsight-hadoop-script-actions.md)に関するページを参照してください。
* **Linux** クラスターの場合: Ambari (Web または REST API) を使用して値を変更します。 詳細については、 [Ambari を使用した HDInsight の管理](hdinsight-hadoop-manage-ambari.md)

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

### <a name="default-configuration"></a>既定の構成
WebHCat のパフォーマンスに影響を与えたり、超過したときにエラーが発生したりする既定の構成値を次に示します。

| Setting | 実行内容 | 既定値 |
| --- | --- | --- |
| [yarn.scheduler.capacity.maximum-applications][maximum-applications] |同時にアクティブにできるジョブの最大数 (保留中または実行中) |10,000 |
| [templeton.exec.max-procs][max-procs] |同時に処理できる要求の最大数 |20 |
| [mapreduce.jobhistory.max-age-ms][max-age-ms] |ジョブの履歴が保持される日数 |7 日 |

## <a name="too-many-requests"></a>要求が多すぎます
**HTTP 状態コード**: 429

| 原因 | 解決策 |
| --- | --- |
| WebHCat によって 1 分間に処理される同時要求の最大数を超過した (既定値は 20)。 |最大同時要求数を超える数の要求を送信しないようにワークロードを減らすか、 `templeton.exec.max-procs`を変更して同時要求の上限をより大きな値にします。 詳細については、「 [構成の変更](#modifying-configuration) 」を参照してください。 |

## <a name="server-unavailable"></a>Server unavailable
**HTTP 状態コード**: 503

| 原因 | 解決策 |
| --- | --- |
| これは、通常、クラスターのプライマリ ヘッドノードとセカンダリ ヘッドノードの間のフェールオーバー中に発生します。 |2 分待ってから操作をやり直してください。 |

## <a name="bad-request-content-could-not-find-job"></a>Bad request Content: Could not find job
**HTTP 状態コード**: 400

| 原因 | 解決策 |
| --- | --- |
| ジョブ履歴クリーナーによってジョブの詳細がクリーンアップされた。 |ジョブ履歴の既定の保有期間は 7 日間です。 これは、 `mapreduce.jobhistory.max-age-ms`を変更することによって変更できます。 詳細については、「 [構成の変更](#modifying-configuration) 」を参照してください。 |
| フェールオーバーによってジョブが強制終了した。 |最大で&2; 分間にわたってジョブの送信を再試行してください。 |
| 無効なジョブ ID が使用された。 |ジョブ ID が正しいことを確認してください。 |

## <a name="bad-gateway"></a>Bad gateway
**HTTP 状態コード**: 502

| 原因 | 解決策 |
| --- | --- |
| WebHCat プロセス内で内部ガベージ コレクションが行われている。 |ガベージ コレクションが終了するまで待つか、または WebHCat サービスを再起動します。 |
| ResourceManager サービスからの応答の待機中にタイムアウトが発生した。 アクティブなアプリケーションの数が構成された最大値 (既定値は 10,000) に達した場合に発生することがあります。 |現在実行中のジョブが完了するまで待つか、または `yarn.scheduler.capacity.maximum-applications`を変更して同時実行ジョブの制限値をより大きな値にします。 詳細については、「 [構成の変更](#modifying-configuration) 」を参照してください。 |
| `Fields` が `*` に設定されているときに、[GET/jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) 呼び出しですべてのジョブを取得しようとした。 |*すべて*のジョブの詳細を取得する操作は実行しないでください。代わりに、`jobid` を使用して、特定のジョブ ID を超えるジョブの詳細を取得してください。 または、`Fields` を使用しないでください。 |
| ヘッドノードのフェールオーバー中に WebHCat サービスがダウンした。 |2 分待ってから操作をやり直してください。 |
| WebHCat を通じて送信された 500 個を超えるジョブが保留中になっている。 |現在保留中のジョブが完了するのを待ってから、次のジョブを送信します。 |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml


