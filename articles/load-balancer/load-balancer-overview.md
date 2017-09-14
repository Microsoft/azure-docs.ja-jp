---
title: "Azure Load Balancer の概要 | Microsoft Docs"
description: "Azure Load Balancer の機能の概要、アーキテクチャ、実装。 ロード バランサーの動作とクラウドでの活用について説明します。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 0f313dc0-f007-4cee-b2b9-f542357925a3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 617da1cf41db08d319d6fe9fa7bc96b794a0001e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="azure-load-balancer-overview"></a>Azure Load Balancer の概要

Azure Load Balancer は、アプリケーションに高可用性と優れたネットワーク パフォーマンスを提供します。 Azure Load Balancer は、負荷分散セットで定義されているサービスの正常なインスタンス間で着信トラフィックを分散する、レイヤー 4 (TCP、UDP) ロード バランサーです。

Azure Load Balancer は次のように構成できます。

* 仮想マシンへの着信インターネット トラフィックを負荷分散します。 この構成は、 [インターネットに接続する負荷分散](load-balancer-internet-overview.md)と呼ばれます。
* 仮想ネットワーク内の仮想マシン間、クラウド サービス内の仮想マシン間、クロスプレミスの仮想ネットワーク内のオンプレミスのコンピューターと仮想マシン間で、トラフィックを負荷分散します。 この構成は、 [内部負荷分散](load-balancer-internal-overview.md)と呼ばれます。
* 外部トラフィックを特定の仮想マシンに転送します。

インターネットから到達できるようにするには、クラウド内のすべてのリソースにパブリック IP アドレスが必要です。 Azure のクラウド インフラストラクチャでは、ルーティング不可能な IP アドレスをリソースに使用します。 インターネットとの通信には、ネットワーク アドレス変換 (NAT) とパブリック IP アドレスが使用されます。

## <a name="azure-deployment-models"></a>Azure のデプロイメント モデル

Azure クラシック [デプロイメント モデル](../azure-resource-manager/resource-manager-deployment-model.md)と Resource Manager デプロイメント モデルの違いを理解することが重要です。 各モデルで Azure Load Balancer の構成方法が異なります。

### <a name="azure-classic-deployment-model"></a>Azure クラシック デプロイ モデル

クラウド サービス境界内にデプロイされた仮想マシンは、ロード バランサーを使用するためにグループ化できます。 このモデルでは、パブリック IP アドレスと完全修飾ドメイン名 (FQDN) がクラウド サービスに割り当てられます。 ロード バランサーはポート変換を実行し、クラウド サービスのパブリック IP アドレスを使用して、ネットワーク トラフィックの負荷を分散します。

負荷分散されるトラフィックは、エンドポイントで定義されています。 ポート変換エンドポイントでは、パブリック IP アドレスに割り当てられたパブリック ポートと、特定の仮想マシンでサービスに割り当てられたローカル ポート間の 1 対 1 の関係を持ちます。 負荷分散エンドポイントでは、パブリック IP アドレスと、クラウド サービス内の仮想マシンでサービスに割り当てられたローカル ポート間の 1 対多の関係を持ちます。

![クラシック デプロイ モデルの Azure Load Balancer](./media/load-balancer-overview/asm-lb.png)

図 1 - クラシック デプロイ モデルの Azure Load Balancer

このデプロイ モデルでロード バランサーが使用するパブリック IP アドレスのドメイン ラベルは、\<クラウド サービス名\>.cloudapp.net です。 次の図は、このモデルの Azure Load Balancer を示しています。

### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager デプロイ モデル

Resource Manager デプロイメント モデルでは、クラウド サービスを作成する必要はありません。 複数の仮想マシン間でトラフィックを明示的にルーティングするためにロード バランサーが作成されます。

パブリック IP アドレスは、ドメイン ラベル (DNS 名) を持つ個別のリソースです。 パブリック IP アドレスは、ロード バランサー リソースに関連付けられます。 ロード バランサー規則と着信 NAT 規則では、負荷分散されたネットワーク トラフィックを受信するリソースのインターネット エンドポイントとしてパブリック IP アドレスを使用します。

プライベートまたはパブリック IP アドレスは、仮想マシンに接続されたネットワーク インターフェイス リソースに割り当てられます。 ネットワーク インターフェイスがロード バランサーのバックエンド IP アドレス プールに追加されると、ロード バランサーは作成された負荷分散規則に基づいて、負荷分散されたネットワーク トラフィックを送信できます。

次の図は、このモデルの Azure Load Balancer を示しています。

![リソース マネージャーの Azure Load Balancer](./media/load-balancer-overview/arm-lb.png)

図 2 - Resource Manager の Azure Load Balancer

ロード バランサーは、Resource Manager ベースのテンプレート、API、ツールを使用して管理できます。 Resource Manager の詳細については、[Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)に関する記事をご覧ください。

## <a name="load-balancer-features"></a>Load Balancer の機能

* ハッシュベースの分散

    Azure Load Balancer は、ハッシュベースの分散アルゴリズムを使用します。 既定では、ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類から成る 5 タプル ハッシュを使用して、使用可能なサーバーにトラフィックをマップします。 これは、トランスポート セッション *内* でのみ持続性を提供します。 同じ TCP セッションまたは UDP セッション内のパケットは、負荷分散されたエンドポイントの背後にある同じインスタンスに送信されます。 クライアントが接続を閉じてから開き直すか、同じソース IP から新しいセッションを開始すると、ソース ポートが変更されます。 そのため、トラフィックは別のデータセンターの別のエンドポイントに送信される可能性があります。

    詳細については、 [ロード バランサーの分散モード](load-balancer-distribution-mode.md)に関するページをご覧ください。 次の図は、ハッシュベースの分散を示しています。

    ![ハッシュベースの分散](./media/load-balancer-overview/load-balancer-distribution.png)

    図 3 - ハッシュベースの分散

* ポート フォワーディング

    Azure Load Balancer では、受信通信の管理方法を制御できます。 この通信には、インターネット ホスト、他のクラウド サービス内の仮想マシン、または仮想ネットワークから開始されたトラフィックが含まれます。 この制御は、エンドポイントで表されます (入力エンドポイントとも呼ばれます)。

    入力エンドポイントはパブリック ポートでリッスンし、内部ポートにトラフィックを転送します。 内部エンドポイントと外部エンドポイントには同じポートをマップしたり、別のポートを使用することもできます。 たとえば、パブリック エンドポイントをポート 80 にマップしているときに、Web サーバーがポート 81 をリッスンするように構成できます。 パブリック エンドポイントを作成すると、ロード バランサー インスタンスの作成がトリガーされます。

    Azure ポータルを使用して作成した場合、リモート デスクトップ プロトコル (RDP) およびリモート Windows PowerShell セッションのトラフィック用に、仮想マシンのエンドポイントが自動的に作成されます。 これらのエンドポイントを使用して、インターネット経由で仮想マシンをリモートで管理することができます。

* 自動再構成

    Azure Load Balancer は、インスタンスがスケール アップまたはスケール ダウンされると、すぐに自身を再構成します。 たとえば、クラウド サービス内の Web/worker ロールのインスタンス数を増やしたときや、同じ負荷分散セットに仮想マシンを追加したときに、この再構成が行われます。

* サービスの監視

    Azure Load Balancer では、さまざまなサーバー インスタンスの正常性をプローブできます。 プローブで応答できない場合、ロード バランサーは異常なインスタンスへの新しい接続の送信を停止します。 既存の接続は影響を受けません。

    次の 3 種類のプローブがサポートされています。

    + **ゲスト エージェント プローブ (サービスとしてのプラットフォーム仮想マシンの場合のみ):** ロード バランサーは、仮想マシン内のゲスト エージェントを使用します。 ゲスト エージェントがリッスンし、インスタンスが準備完了状態の場合 (インスタンスがビジー、リサイクル中、停止中などの状態でない場合) にのみ、HTTP 200 OK 応答を返します。 エージェントが HTTP 200 OK で応答できない場合、ロード バランサーは、そのインスタンスを応答不能と見なし、インスタンスへのトラフィックの送信を停止します。 Load Balancer はインスタンスへの ping を続けます。 ゲスト エージェントが、HTTP 200 で応答する場合は、ロード バランサーはそのインスタンスへのトラフィックの送信を再開します。 Web ロールを使用する場合、Web サイト コードは通常、Azure ファブリックやゲスト エージェントでは監視されない w3wp.exe で実行されます。 つまり、w3wp.exe が失敗 (HTTP 500 応答など) してもゲスト エージェントにレポートされず、ロード バランサーはそのインスタンスがローテーションから除外されたことを認識しません。
    + **HTTP カスタム プローブ:** このプローブは、既定の (ゲスト エージェント) プローブを上書きします。 ユーザーはこれを使用して独自のカスタム ロジックを作成し、ロール インスタンスの正常性を判断できます。 ロード バランサーは、エンドポイントを定期的に調査します (既定では 15 秒ごと)。 タイムアウト期間内 (既定値は 31 秒) にインスタンスが TCP ACK または HTTP 200 で応答した場合は、ローテーション内にあると見なされます。 これは、ロード バランサーのローテーションからインスタンスを削除する、ユーザー独自のロジックを実装する場合に役立ちます。 たとえば、インスタンスが CPU の 90% を超えた場合に 200 以外の状態を返すように、インスタンスを構成できます。 w3wp.exe を使用する Web ロールの場合も、Web サイト コードが失敗するとプローブに 200 以外の状態が返されるため、Web サイトの自動監視が行われます。
    + **TCP カスタム プローブ:** このプローブは、定義済みプローブ ポートへの正常な TCP セッションの確立に依存します。

    詳細については、「 [LoadBalancerProbe schema (LoadBalancerProbe スキーマ)](https://msdn.microsoft.com/library/azure/jj151530.aspx)」を参照してください。

* Source NAT

    サービスからインターネットへのすべての送信トラフィックは、着信トラフィックと同じ VIP アドレスを使用してソース NAT (SNAT) 変換されます。 SNAT には重要な利点があります。

    + VIP を別のサービス インスタンスに動的にマップできるため、サービスのアップグレードや障害復旧が簡単にできます。
    + アクセス制御リスト (ACL) の管理が容易になります。 VIP で表現される ACL は、サービスをスケールアップ、スケールダウン、または再デプロイしても変更されません。

    ロード バランサーの構成では、UDP の Full cone NAT がサポートされます。 Full cone NAT は、ポートが (送信要求に応答して) 任意の外部ホストからの着信接続を許可する NAT の一種です。

    仮想マシンによって開始された新しい送信接続ごとに、送信ポートもロード バランサーによって割り当てられます。 外部ホストは、仮想 IP (VIP) の割り当てポートでトラフィックを認識します。 大量の送信接続が必要なシナリオでは、VM が SNAT 専用の送信 IP アドレスを使用できるように、 [インスタンスレベル パブリック IP](../virtual-network/virtual-networks-instance-level-public-ip.md) アドレスを使用することをお勧めします。 これにより、ポートの枯渇のリスクが軽減されます。

    このトピックの詳細については、[送信接続](load-balancer-outbound-connections.md)に関する記事をご覧ください。

### <a name="support-for-multiple-load-balanced-ip-addresses-for-virtual-machines"></a>仮想マシンの複数の負荷分散された IP アドレスのサポート
負荷分散された複数のパブリック IP アドレスを、一連の仮想マシンに割り当てることができます。 この機能を使用して、複数の SSL Web サイトや複数の SQL Server AlwaysOn 可用性グループ リスナーを同じ仮想マシン セット上にホストすることができます。 詳細については、[クラウド サービスごとの複数の VIP](load-balancer-multivip.md) に関する記事をご覧ください。

[!INCLUDE [load-balancer-compare-tm-ag-lb-include.md](../../includes/load-balancer-compare-tm-ag-lb-include.md)]

## <a name="limitations"></a>制限事項

Load Balancer のバックエンド プールには、Basic レベルを除く任意の VM SKU を含めることができます。

## <a name="next-steps"></a>次のステップ

- [インターネットに接続するロード バランサー](load-balancer-internet-overview.md)の詳細を確認する

- [内部ロード バランサーの概要](load-balancer-internal-overview.md)の詳細を確認する

- [インターネットに接続するロード バランサー](load-balancer-get-started-internet-portal.md)を作成する

- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)にについて確認する。


