---
title: メトリック、アラート、リソース正常性を使用した診断 - Azure Standard Load Balancer
description: ご利用の Azure Standard Load Balancer を診断するために取得可能なメトリック、アラート、およびリソース正常性情報を使用します。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: c362829b1babf954868452a3858da1f319008a9a
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990778"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>メトリック、アラート、およびリソース正常性を使用した Standard Load Balancer の診断

Azure Standard Load Balancer では、次の診断機能が公開されています。

* **多次元メトリックおよびアラート**: Standard Load Balancer 構成用に、[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) を介して多次元診断機能が提供されています。 ご利用の Standard Load Balancer リソースの監視、管理、トラブルシューティングを行うことができます。

* **[リソース正常性]** :Azure portal の [ロード バランサー] ページと ([監視] の) [リソース正常性] ページに、Standard Load Balancer に対する [リソース正常性] セクションが表示されます。 

この記事では、これらの機能の概要、および Standard Load Balancer でこれらの機能を使う方法について説明します。 

## <a name = "MultiDimensionalMetrics"></a>多次元メトリック

Azure Load Balancer では Azure portal の Azure メトリックを介して多次元メトリックが提供されており、Load Balancer リソースにリアルタイム診断分析情報を取り込むために役立ちます。 

Standard Load Balancer をさまざまに構成することで、次のメトリックを取得できます。

| メトリック | リソースの種類 | 説明 | 推奨される集計 |
| --- | --- | --- | --- |
| データ パスの可用性 (VIP 可用性)| パブリックおよび内部ロード バランサー | Standard Load Balancer は、リージョン内からロード バランサー フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従います。 顧客が使用しているデータ パスも検証されます。 測定はアプリケーションには見えないので、他の操作と干渉することはありません。| Average |
| 正常性プローブの状態 (DIP 可用性) | パブリックおよび内部ロード バランサー | Standard Load Balancer では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスが使われます。 このメトリックは、ロード バランサー プールのインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。 正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。 |  Average |
| SYN (同期) パケット | パブリックおよび内部ロード バランサー | Standard Load Balancer は、伝送制御プロトコル (TCP) 接続を終了したり、TCP または UDP のパケット フローと対話したりすることはありません。 フローとハンドシェイクは、常にソースと VM インスタンスの間で発生します。 TCP プロトコルのシナリオのトラブルシューティングを適切に行うために、SYN パケット カウンターを使用して TCP 接続試行の数を把握できます。 このメトリックは、受信済みの TCP SYN パケットの数を報告します。| Average |
| SNAT 接続 | パブリック ロード バランサー |Standard Load Balancer は、パブリック IP アドレス フロントエンドにマスカレードされた送信フローの数を報告します。 送信元ネットワーク アドレス変換 (SNAT) ポートは、有限のリソースです。 このメトリックはアプリケーションが送信フローで SNAT にどれくらい依存しているかを示すことができます。 成功した送信 SNAT フローと失敗した送信 SNAT フローのカウンターがレポートされるので、送信フローの正常性について、トラブルシューティングしたり、理解したりするのに役立てることができます。| Average |
| 割り当てられる SNAT ポート | パブリック ロード バランサー | Standard Load Balancer からは、バックエンド インスタンスごとに割り当てられる SNAT ポートの数が報告されます | Average |
| 使用された SNAT ポート | パブリック ロード バランサー | Standard Load Balancer からは、バックエンド インスタンスごとに活用されている SNAT ポートの数が報告されます | Average | 
| バイト カウンター |  パブリックおよび内部ロード バランサー | Standard Load Balancer は、フロントエンドごとに処理されたデータ量を報告します。 バックエンド インスタンス間でバイト数が均等に配布されていないことがわかります。 Azure の Load Balancer アルゴリズムはフローに基づいているため、これは予期されていることです。 | Average |
| パケット カウンター |  パブリックおよび内部ロード バランサー | Standard Load Balancer は、フロントエンドごとに処理されたパケット数を報告します。| Average |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Azure Portal でロード バランサーのメトリックを表示する

Azure Portal の [メトリック] ページでは、ロード バランサーのメトリックが公開されます。このページは、特定のリソースのロード バランサー リソース ページと、Azure Monitor のページの両方から表示できます。 

Standard Load Balancer リソースのメトリックを表示するには:
1. [メトリック] ページに移動し、次のいずれかを実行します。
   * ロード バランサー リソース ページで、ドロップダウン リストからメトリックの種類を選択します。
   * Azure Monitor のページで、ロード バランサー リソースを選択します。
2. 適切な集約の種類を設定します。
3. 必要に応じて、フィルター処理およびグループ化を構成します。

    ![Standard Load Balancer のメトリック](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

    *図:Standard Load Balancer のデータ パスの可用性メトリック*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API を使用してプログラムで多次元メトリックを取得する

多次元メトリックの定義と値を取得するための API のガイダンスについては、「[Azure 監視 REST API のチュートリアル](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)」をご覧ください。 これらのメトリックは、"すべてのメトリック" オプションのみを使用してストレージ アカウントに書き込むことができます。 

### <a name = "DiagnosticScenarios"></a>一般的な診断シナリオと推奨されるビュー

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>データ パスが稼働していてロード バランサー VIP に使用可能か?

VIP 可用性メトリックでは、VM が存在するコンピューティング ホストまでの、リージョン内のデータ パスの正常性が示されています。 このメトリックは、Azure インフラストラクチャの正常性を反映します。 このメトリックは次の目的に使うことができます。
- サービスの外部可用性を監視します
- サービスが展開されているプラットフォームが正常かどうか、またはゲスト OS やアプリケーション インスタンスが正常かどうかを、詳しく調べて把握します。
- イベントがサービスと基盤のデータ プレーンのどちらに関係するかを切り分けます。 このメトリックを正常性プローブの状態 ("DIP 可用性") と間違えないでください。

Standard Load Balancer リソースのデータ パスの可用性を取得するには:
1. 正しいロード バランサー リソースが選ばれていることを確認します。 
2. **[メトリック]** ドロップダウン リストで **[Data Path Availability]\(データ パスの可用性\)** を選択します。 
3. **[集計]** ドロップダウン リストで **[平均]** を選択します。 
4. さらに、必要なフロントエンド IP アドレスまたはフロントエンド ポートのディメンションとしてフロントエンド IP アドレスまたはフロントエンド ポートに対するフィルターを追加し、選んだディメンションでグループ化します。

![VIP のプローブ](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*図:Load Balancer フロントエンド プローブの詳細*

アクティブな帯域内測定によってメトリックが生成されます。 リージョン内のプローブ サービスから測定のためのトラフィックが発生します。 このサービスは、パブリック フロント エンドでデプロイが作成されるとすぐにアクティブになり、パブリック フロント エンドが削除されるまで継続します。 

デプロイのフロントエンドとルールに一致するパケットが、定期的に生成されます。 パケットは、ソースから、バックエンド プール内の VM が配置されているホストまで、リージョンを移動します。 ロード バランサー インフラストラクチャは、他のすべてのトラフィックの場合と同じ負荷分散操作と変換操作を実行します。 このプローブは負荷分散されるエンドポイントの帯域内です。 バックエンド プール内の正常な VM が存在するコンピューティング ホストにプローブが到達した後に、コンピューティング ホストはプローブ サービスへの応答を生成します。 VM はこのトラフィックを認識しません。

VIP 可用性は次の理由で失敗します。
- デプロイのバックエンド プールに正常な VM が残っていない。 
- インフラストラクチャ障害が発生した。

診断の目的で、[データ パスの可用性メトリックと共に正常性プローブの状態](#vipavailabilityandhealthprobes)を使うことができます。

ほとんどのシナリオでは集計として**平均**を使います。

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>VIP のバックエンド インスタンスはプローブに応答しているか?

正常性プローブ状態メトリックでは、ユーザーがロード バランサーの正常性プローブを構成するときにユーザーによって構成されたアプリケーションのデプロイの正常性が示されます。 ロード バランサーは、正常性プローブの状態を使って、新しいフローの送信先を決めます。 正常性プローブは、Azure インフラストラクチャのアドレスから送信され、VM のゲスト OS 内で認識されます。

Standard Load Balancer リソースの正常性プローブの状態を取得するには:
1. **[正常性プローブの状態]** メトリックと、集計の種類として **[平均]** を選択します。 
2. 必要なフロントエンドの IP アドレスまたはポート (またはその両方) にフィルターを適用します。

正常性プローブは次の理由で失敗します。
- リッスンしていないポート、応答していないポート、または正しくないプロトコルを使用しているポートに対して、正常性プローブを構成している。 サービスが Direct Server Return (DSR、またはフローティング IP) ルールを使っている場合は、サービスが、フロントエンド IP アドレスで構成されたループバックだけでなく、NIC の IP 構成の IP アドレスでリッスンしていることを確認します。
- プローブが、ネットワーク セキュリティ グループ、VM のゲスト OS のファイアウォール、またはアプリケーション レイヤーのフィルターによって許可されていない。

ほとんどのシナリオでは集計として**平均**を使います。

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>送信接続の統計情報を確認する方法 

SNAT 接続メトリックは、([送信フロー](https://aka.ms/lboutbound)の) 接続の成功と失敗の量を示します。

失敗した接続の量が 0 より大きい場合は、SNAT ポートが不足していることを示します。 さらに詳しく調査し、このようなエラーの原因を特定する必要があります。 SNAT ポートの不足は、[送信フロー](https://aka.ms/lboutbound)確立の失敗として示されます。 シナリオおよび動作メカニズムを理解し、SNAT ポートの不足を軽減する方法または回避する設計の方法を学習するには、送信接続に関する記事をご覧ください。 

SNAT 接続の統計情報を取得するには:
1. トリックの種類として **[SNAT Connections]\(SNAT 接続\)** を、集計として **[合計]** を選択します。 
2. 異なる行に表示されている SNAT 接続の成功と失敗をカウントするには、 **[接続の状態]** でグループ化します。 

![SNAT 接続](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*図:Load Balancer の SNAT 接続の数*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>サービスに対する受信/送信接続の試行を確認する方法

SYN パケット メトリックは、特定のフロントエンドに関連付けられている、到着した TCP SYN パケットまたは送信された TCP SYN パケット ([送信フロー](https://aka.ms/lboutbound)の場合) の量を示します。 このメトリックを使用して、サービスへの TCP 接続の試行を把握できます。

ほとんどのシナリオでは、集計として**合計**を使います。

![SYN 接続](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*図:Load Balancer の SYN の数*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>ネットワーク帯域幅の消費量を確認する方法 

バイト カウンターおよびパケット カウンターのメトリックは、フロントエンドごとにサービスによって送信または受信されたバイトおよびパケットの量を示します。

ほとんどのシナリオでは、集計として**合計**を使います。

バイト数またはパケット数の統計情報を取得するには:
1. メトリックの種類として **[Bytes Count]\(バイト数\)** および **[Packet Count]\(パケット数\)** 、またはその両方を、集計として **[平均]** を選択します。 
2. 以下のいずれかを実行します。
   * 特定のフロントエンド IP、フロントエンド ポート、バックエンド IP、またはバックエンド ポートにフィルターを適用します。
   * フィルターを適用せずにロード バランサー リソースの全体的な統計情報を取得します。

![バイト数](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*図:Load Balancer のバイト数*

#### <a name = "vipavailabilityandhealthprobes"></a>ロード バランサーのデプロイを診断する方法

VIP 可用性メトリックと正常性プローブ メトリックを 1 つのグラフに組み合わせて使用すると、問題を探して解決する必要がある場所を特定することができます。 Azure が正常に動作している保証が得られ、この情報を利用して、構成またはアプリケーションが根本原因であることを確定できます。

正常性プローブ メトリックを使うと、ユーザーが指定した構成に従って、Azure が展開の正常性を表示する方法を理解できます。 正常性プローブを確認することは、常に、原因を監視または特定するのに適した第一歩です。

その後さらに、VIP 可用性メトリックを使って、特定のデプロイに関与する基盤データ プレーンの正常性を Azure が表示する方法についての分析情報を得ることができます。 次の例に示すように、両方のメトリックを組み合わせると、障害の可能性がある場所を分離できます。

![データ パスの可用性メトリックと正常性プローブの状態メトリックの組み合わせ](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*図:データ パスの可用性メトリックと正常性プローブの状態メトリックの組み合わせ*

このグラフには次の情報が表示されます。
- グラフの先頭では、VM をホストするインフラストラクチャは使用できず、0% でした。 その後、インフラストラクチャは正常で、VM に到達可能になり、複数の VM がバックエンドに配置されていました。 この情報は、データ パスの可用性 (VIP 可用性) を表す青いトレースによって示され、後で 100% になっています。 
- 紫色のトレースで示されている正常性プローブの状態 (DIP 可用性) は、グラフの先頭では 0% です。 緑色の円で囲んだ領域は正常性プローブ状態 (DIP 可用性) が正常になった場所を示し、この時点で、ユーザーのデプロイは新しいフローを受け付けることができるようになりました。

このグラフを見ることで、ユーザーは、他の問題が発生していたかどうかを推測したりサポートに問い合わせたりすることなく、独力でデプロイのトラブルシューティングを行うことができます。 このサービスは、誤った構成またはアプリケーションの障害によって正常性プローブが失敗したため、利用できませんでした。

## <a name = "ResourceHealth"></a>リソースの正常性状態

Standard Load Balancer リソースの正常性状態は、 **[Monitor]\(監視\) > [サービスの正常性]** の既存の **[リソース正常性]** によって表示されます。

パブリック Standard Load Balancer リソースの正常性を表示するには:
1. **[Monitor]\(監視\)**  >  **[サービス正常性]** の順に選択します。

   ![[Monitor]\(監視\) ページ](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *図:Azure Monitor のサービス正常性のリンク*

2. **[リソース正常性]** を選び、 **[サブスクリプション ID]** が選ばれていることと **[リソースの種類] = [ロード バランサー]** に設定されていることを確認します。

   ![リソースの正常性状態](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *図:正常性ビューを表示するリソースの選択*

3. 一覧で、正常性状態の履歴を表示するロード バランサー リソースを選択します。

    ![Load Balancer の正常性状態](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *図:Load Balancer リソースの正常性ビュー*
 
さまざまなリソースの正常性状態とその説明を次の表に示します。 

| リソースの正常性状態 | 説明 |
| --- | --- |
| 利用可能 | ご利用の Standard Load Balancer リソースは正常であり、使用可能です。 |
| 使用不可 | ご利用の Standard Load Balancer リソースは正常ではありません。 **[Azure Monitor]**  >  **[メトリック]** の順に選択して、正常性を診断します<br>("*使用不可*" 状態は、ご利用の Standard Load Balancer にリソースが接続されていないことを意味している可能性もあります)。 |
| Unknown | ご利用の Standard Load Balancer リソースの正常性状態はまだ更新されていません。<br>("*不明*" 状態は、ご利用の Standard Load Balancer にリソースが接続されていないことを意味している可能性もあります)。  |

## <a name="next-steps"></a>次のステップ

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する。
- [ロード バランサーの送信接続](https://aka.ms/lboutbound)の詳細を確認します。
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) について学習する。
- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) および [REST API を使用してメトリックを取得する方法](/rest/api/monitor/metrics/list)を学習する。
