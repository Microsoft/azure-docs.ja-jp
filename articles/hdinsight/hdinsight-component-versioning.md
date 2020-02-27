---
title: Apache Hadoop コンポーネントおよびバージョン - Azure HDInsight
description: Azure HDInsight 内の Apache Hadoop コンポーネントおよびバージョンについて説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/02/2020
ms.openlocfilehash: 13c064701a5ecae2090a99980249f5daaa8d5395
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198958"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは

Microsoft Azure HDInsight の[Apache Hadoop](https://hadoop.apache.org/) エコシステムのコンポーネントおよびバージョンと、Enterprise セキュリティ パッケージについて説明します。 また、HDInsight で Hadoop コンポーネントのバージョンを確認する方法についても説明します。

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>HDInsight の各バージョンで使用できる Apache Hadoop コンポーネント

Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 2017 年 4 月 4 日現在、Azure HDInsight で使用される既定のクラスター バージョンは 3.6 です。

HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。

> [!NOTE]  
> HDInsight サービスの既定のバージョンは、予告なく変更される場合があります。 バージョンの依存関係がある場合は、Azure PowerShell や Azure クラシック CLI を含む .NET SDK を使用してクラスターを作成する際に HDInsight バージョンを指定してください。

| コンポーネント              | HDInsight 4.0 | HDInsight 3.6 (既定値)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop と YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0、1.2.1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1、2.4    | 2.3.0、2.2.0、2.1.0         |
| Apache Livy            | 0.5           | 0.4、0.4、0.3               |
| Apache Kafka           | 1.1.1、2.1    | 1.1、1.0 * (下記の「注」を参照) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> システム パフォーマンスの観点から、Kafka バージョン 0.10 のサポートは 2019 年 3 月に期限切れになりました。

## <a name="check-for-current-hadoop-component-version-information"></a>現在の Hadoop コンポーネントのバージョン情報の確認

HDInsight クラスターのバージョンに関連付けられた Hadoop エコシステム コンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。 Hadoop コンポーネントを調べて、どのバージョンがクラスターに使用されているかを確認するには、Ambari REST API を使用します。 **GetComponentInformation** コマンドによって、サービス コンポーネントに関する情報を取得します。 詳細については、[Apache Ambari のドキュメント](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) を参照してください。

### <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、 [HDInsight リリース ノート](hdinsight-release-notes.md) を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン

次の表は、HDInsight のバージョンの一覧を示しています。 サポート有効期限と提供終了日も記載されます (これらが既知の場合)。

### <a name="available-versions"></a>使用可能なバージョン

次の表は、Azure portal と、PowerShell や .NET SDK などの他のデプロイ方法で使用可能な HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | VM の OS | リリース日 | サポート有効期限 | 提供終了日 | 高可用性 |  Azure portal での可用性 |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |2018 年 9 月 24 日 | | |はい |はい |
| HDInsight 3.6 |Ubuntu 16.0.4 LTS |2017 年 4 月 4 日 | 2020 年 12 月 31 日 |2020 年 12 月 31 日 |はい |はい |

Spark 2.1、2.2 および Kafka 1.0 のサポートは、2020 年 6 月 30 日に有効期限が切れます。

> [!NOTE]  
> バージョンのサポートが期限切れになると、Microsoft Azure Portal で使用できなくなります。 ただし、クラスター バージョンは、Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) コマンドの `Version` パラメーターと .NET SDK を使用することで、バージョンの提供終了日まで利用できます。

### <a name="retired-versions"></a>廃止されたバージョン

次の表は、Azure portal で使用**できない** HDInsight のバージョンをまとめたものです。

| HDInsight のバージョン | HDP のバージョン | VM の OS | リリース日 | サポート有効期限 | 提供終了日 | 高可用性 |  Azure Portal での可用性 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |2016 年 9 月 30 日 |2017 年 9 月 5 日 |2018 年 6 月 28 日 |はい |いいえ |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |2016 年 3 月 29 日 |2016 年 12 月 29 日 |2018 年 1 月 9 日 |はい |いいえ |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2018 年 7 月 31日 |はい |いいえ |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2015 年 12 月 2 日 |2016 年 6 月 27 日 |2017 年 7 月 31 日 |はい |いいえ |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS または Windows Server 2012 R2 |2015 年 2 月 18 日 |2016 年 3 月 1 日 |2017 年 4 月 1 日 |はい |いいえ |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |2014 年 6 月 24 日 |2015 年 5 月 18 日 |2016 年 6 月 30 日 |はい |いいえ |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |2014 年 2 月 11 日 |2014 年 9 月 17 日 |2015 年 6 月 30 日 |はい |いいえ |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |2013 年 10 月 28 日 |2014 年 5 月 12 日 |2015 年 5 月 31 日 |はい |いいえ |
| HDInsight 1.6 |HDP 1.1 | |2013 年 10 月 28 日 |2014 年 4 月 26 日 |2015 年 5 月 31 日 |いいえ |いいえ |

> [!NOTE]  
> ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight バージョン 2.1 以降では既定でデプロイされています。 HDInsight バージョン 1.6 クラスターでは利用できません。

## <a name="enterprise-security-package-for-hdinsight"></a>HDInsight 用の Enterprise セキュリティ パッケージ

Enterprise セキュリティは、クラスターの作成のワークフローの一部として HDInsight クラスターに追加できるオプションのパッケージです。 Enterprise セキュリティ パッケージでは以下の機能がサポートされます。

- 認証のための Active Directory との統合。

    以前は、ローカル管理者ユーザーとローカル SSH ユーザーのみが HDInsight クラスターを作成できました。 ローカル管理者ユーザーは、すべてのファイル、フォルダー、テーブル、列にアクセスできます。  Enterprise セキュリティ パッケージを使用すると、オンプレミスの Active Directory、Azure Active Directory Domain Services、または IaaS 仮想マシン上の Active Directory を含む独自の Active Directory に HDInsight クラスターを統合することで、ロールベースのアクセス制御を有効にすることができます。 クラスターのドメイン管理者は、クラスターにアクセスするための自社の (ドメイン) ユーザー名とパスワードを使用する権限をユーザーに付与することができます。

    詳細については、次を参照してください。

    - [ドメイン参加済み HDInsight クラスターでの Apache Hadoop セキュリティの概要](./domain-joined/hdinsight-security-overview.md)
    - [HDInsight で Azure のドメイン参加済み Apache Hadoop クラスターを計画する](./domain-joined/apache-domain-joined-architecture.md)
    - [ドメイン参加済みサンドボックス環境の構成](./domain-joined/apache-domain-joined-configure.md)
    - [Azure Active Directory Domain Services を使用してドメイン参加済み HDInsight クラスターを構成する](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- データの承認

  - Hive、Spark SQL、Yarn Queues の承認のための Apache Ranger との統合。
  - ファイルとフォルダーのアクセスの制御を設定できます。

    詳細については、次を参照してください。

  - [ドメイン参加済み HDInsight での Apache Hive ポリシーの構成](./domain-joined/apache-domain-joined-run-hive.md)

- アクセスや構成済みポリシーを監視するための監査ログの表示。

### <a name="supported-cluster-types"></a>サポートされているクラスターの種類

現時点では、次のクラスターの種類のみが Enterprise セキュリティ パッケージをサポートしています。

- Hadoop (HDInsight 3.6 のみ)
- Spark
- Kafka
- hbase
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Azure Data Lake Storage のサポート

Enterprise セキュリティ パッケージでは、プライマリ ストレージとアドオン ストレージの両方として Azure Data Lake Storage の使用がサポートされます。

### <a name="pricing-and-service-level-agreement"></a>価格とサービス レベル アグリーメント

Enterprise セキュリティ パッケージの価格と SLA について詳しくは、[HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)に関するページをご覧ください。

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight クラスター バージョンのサービス レベル アグリーメント

サービス レベル アグリーメント (SLA) は、_サポート ウィンドウ_ の条件で定義されます。 サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間です。 バージョンの _サポート有効期限_ が切れている場合、HDInsight クラスターはサポート対象外となります。 所定の HDInsight Version X のサポート有効期限は (新しい X+1 バージョンが利用可能になった後)、次の数式で計算した日付のうち、遅い方とされます。  

- 数式 1:HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
- 数式 2:HDInsight クラスター バージョン X+1 が Azure portal で使用可能になった日付に 90 日を加える。

"_提供終了日_" とは、その日を過ぎると HDInsight でクラスター バージョンを作成できなくなる日付です。 2017 年 7 月 31 日以降は、提供終了日より後に HDInsight クラスターのサイズを変更できません。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>HDInsight バージョンに対応する Hortonworks リリース ノート

このセクションでは、HDInsight で使用される Hortonworks Data Platform ディストリビューションと Apache コンポーネントのリリース ノートへのリンクを提供します。
* HDInsight クラスター Version 4.0 は、[Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) を基盤とする Hadoop ディストリビューションを使用します
* HDInsight クラスター Version 3.6 は、[Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 3.5 は、[Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) を基盤とする Hadoop ディストリビューションを使用します。 HDInsight クラスター バージョン 3.5 は、Azure Portal で作成される _既定の_ Hadoop クラスターです。
* HDInsight クラスター Version 3.4 は、 [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 3.3 は、 [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)を基盤とする Hadoop ディストリビューションを使用します。

  * [Apache Storm のリリース ノート](https://storm.apache.org/2015/11/05/storm0100-released.html)は Apache Web サイトで入手できます。
  * [Apache Hive のリリース ノート](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)は Apache Web サイトで入手できます。
* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。

  * 次のような特定の Apache コンポーネントのリリース ノートを入手できます。[Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、および [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight クラスター Version 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。 2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。
* HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>クラスターの既定のノード構成と仮想マシン サイズ

クラスター用に選択する仮想マシンの SKU の詳細については、[Azure HDInsight のクラスター構成の詳細](hdinsight-supported-node-configuration.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [HDInsight で Apache Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC から HDInsight の Apache Hadoop で作業する](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
