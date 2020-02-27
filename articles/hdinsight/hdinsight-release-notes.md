---
title: Azure HDInsight のリリース ノート
description: Azure HDInsight の最新のリリース ノート。 Hadoop、Spark、Microsoft R Server、Hive などの開発に関するヒントや詳細を紹介します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 091ca4d632d89405d85c66e264aff9867979fcd4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905234"
---
# <a name="release-notes"></a>リリース ノート

この記事では、**最近**の Azure HDInsight リリース更新に関する情報を提供します。 以前のリリースについて詳しくは、「[HDInsight リリース ノートのアーカイブ](hdinsight-release-notes-archive.md)」を参照してください。

## <a name="summary"></a>まとめ

Azure HDInsight は、Azure 上でオープン ソース分析を行うエンタープライズのお客様の間で最も人気のあるサービスの 1 つです。

## <a name="release-date-01092020"></a>リリース日: 2020 年 1 月 9 日

このリリースは、HDInsight 3.6 と4.0 の両方に適用されます。 HDInsight リリースは、数日以内にすべてのリージョンでご利用になれます。 ここのリリース日は、最初のリージョンのリリース日です。 以下の変更がない場合は、数日以内にリリースがご自分のリージョンでライブになるまでお待ちください。

> [!IMPORTANT]  
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[HDInsight のバージョン管理に関する記事](hdinsight-component-versioning.md)に関するページを参照してください。

## <a name="new-features"></a>新機能
### <a name="tls-12-enforcement"></a>TLS 1.2 の適用
TLS (トランスポート層セキュリティ) と SSL (Secure Sockets Layer) は、コンピューター ネットワーク上の通信にセキュリティを確保する暗号プロトコルです。 [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0) の詳細を参照してください。 HDInsight はパブリック HTTPs エンドポイントで TLS 1.2 を使用しますが、TLS 1.1 も下位互換性のために引き続きサポートされています。 

このリリースでは、お客様はパブリック クラスター エンドポイント経由のすべての接続でのみ TLS 1.2 を選択できます。 これのサポートに、クラスターの作成時に指定できる新しいプロパティ **minSupportedTlsVersion** が導入されました。 このプロパティを設定しない場合、クラスターは TLS 1.0、1.1、1.2 を引き続きサポートします。これは、今日の動作と同じです。 お客様はこのプロパティの値を "1.2" に設定できます。これは、クラスターが TLS 1.2 以降のみをサポートすることを意味します。 詳細については、[仮想ネットワークの計画に関するページの「トランスポート層セキュリティ」](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#transport-layer-security)のセクションを参照してください。

### <a name="bring-your-own-key-for-disk-encryption"></a>Bring Your Own Key でディスクを暗号化する
HDInsight のマネージド ディスクはすべて、Azure Storage Service Encryption (SSE) で保護されます。 これらのディスク上のデータは、既定で Microsoft が管理するキーを使用して暗号化されます。 このリリース以降、ディスクの暗号化に Bring Your Own Key (BYOK) を使用し、Azure Key Vault を使用して管理することができます。 BYOK 暗号化はクラスターの作成時に 1 回のステップで構成でき、追加コストはかかりません。 Azure Key Vault にマネージド ID として HDInsight を登録し、お使いのクラスターの作成時に暗号化キーを追加するだけです。 詳細については、「[お客様が管理するキー ディスクの暗号化](https://docs.microsoft.com/azure/hdinsight/disk-encryption)」を参照してください。

## <a name="deprecation"></a>非推奨
このリリースに非推奨はありません。 今後の非推奨に備えるには、「[今後の変更](#upcoming-changes)」を参照してください。

## <a name="behavior-changes"></a>動作の変更
このリリースに動作変更はありません。 今後の変更に備えるには、「[今後の変更](#upcoming-changes)」を参照してください。

## <a name="upcoming-changes"></a>今後の変更
今後のリリースでは、次の変更が行われます。 

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>ヘッド ノードには最低 4 コアの VM が必要 
HDInsight クラスターの高可用性および信頼性を確保するには、ヘッド ノードに最低 4 コアの VM が必要です。 2020 年 4 月 6 日以降、お客様は新しい HDInsight クラスターのヘッド ノードに、4 コア以上の VM のみを選択することができます。 既存のクラスターは正常に実行されます。 

### <a name="esp-spark-cluster-node-size-change"></a>ESP Spark クラスター ノード サイズの変更 
今後のリリースで、ESP Spark クラスターに許容される最小ノード サイズは Standard_D13_V2 に変更されます。 A シリーズの VM は、CPU とメモリの容量が比較的少ないため、ESP クラスターで問題が発生する可能性があります。 新しい ESP クラスターの作成に A シリーズの VM は非推奨です。

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Azure 仮想マシン スケール セットへの移行
HDInsight では、Azure 仮想マシンを使用してクラスターをプロビジョニングするようになりました。 今後のリリースでは、HDInsight では代わりに Azure 仮想マシン スケール セットを使用します。 Azure 仮想マシン スケール セットの詳細をご確認ください。

### <a name="hbase-20-to-21"></a>HBase 2.0 から 2.1
今後の HDInsight 4.0 リリースでは、HBase のバージョンがバージョン 2.0 から 2.1 にアップグレードされます。

## <a name="bug-fixes"></a>バグの修正
HDInsight は引き続き、クラスターの信頼性とパフォーマンスの向上を実現します。 

## <a name="component-version-change"></a>コンポーネントのバージョンの変更
このリリースでは、コンポーネントのバージョン変更はありません。 HDInsight 4.0 および HDInsight 3.6 の現在のコンポーネント バージョンについては、こちらを参照してください。

## <a name="known-issues"></a>既知の問題

2020 年 1 月 29 日の時点で、アクティブな問題が発生しており、Jupyter ノートブックを使用しようとするとエラーが表示される場合があります。 この問題を修正するには、以下の手順を使用してください。 最新の情報が必要な場合、または追加の質問をする場合は、こちらの [MSDN の投稿](https://social.msdn.microsoft.com/Forums/en-us/8c763fb4-79a9-496f-a75c-44a125e934ac/hdinshight-create-not-create-jupyter-notebook?forum=hdinsight)や、こちらの [StackOverflow の投稿](https://stackoverflow.com/questions/59687614/azure-hdinsight-jupyter-notebook-not-working/59831103)もご覧いただけます。 この問題が修正された場合は、このページも更新されます。

**エラー**

* ValueError:Cannot convert notebook to v5 because that version doesn't exist (ValueError: ノートブックを v5 に変換できません。このバージョンが存在しないためです)
* "Error loading notebook An unknown error occurred while loading this notebook." (ノートブックの読み込み中のエラー。このノートブックの読み込み中に不明なエラーが発生しました。) "This version can load notebook formats v4 or earlier." (このバージョンでは、v4 以前のノートブック形式を読み込むことができます。)

**原因** 

クラスター上の _version.py ファイルが 4.4.x.## ではなく 5.x.x に更新されました。または Ambari を再起動する必要があります。

**ソリューション**

新しい Jupyter ノートブックを作成していて、上記のいずれかのエラーを受け取った場合は、次の手順を実行して問題を修正してください。

1. Web ブラウザーで `https://CLUSTERNAME.azurehdinsight.net` にアクセスして Ambari を開きます。ここで、CLUSTERNAME はクラスターの名前です。
1. Ambari の左側のメニューで **[Jupyter]** をクリックし、 **[Service Actions]\(サービス アクション\)** で **[Stop]\(停止\)** をクリックします。
1. Jupyter サービスが実行されているクラスター ヘッドノードに SSH 接続します。
1. sudo モードでファイル /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py を開きます。
1. version_info の値を確認します
1. version_info の値が以下のように設定されている場合: 

    version_info = (5, 0, 3)

    このエントリを以下のように変更します。 
    
    version_info = (4, 4, 0)

    ファイルを保存します。 

    version_info が既に (4, 4, 0) に設定されている場合は、Ambari のみを再起動する必要があるため、次の手順に進みます。追加の変更は必要ありません。
1. Ambari に戻り、 **[Service Actions]\(サービス アクション\)** で **[Restart All]\(すべて再起動\)** をクリックします。
