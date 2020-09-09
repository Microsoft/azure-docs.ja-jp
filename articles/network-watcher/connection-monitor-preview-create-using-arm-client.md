---
title: 接続モニター プレビューを作成する - ARMClient
titleSuffix: Azure Network Watcher
description: ARMClient を使用して接続モニター (プレビュー) を作成する方法について説明します。
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567835"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>ARMClient を使用して接続モニター (プレビュー) を作成する

ARMClient を使用して接続モニター (プレビュー) を作成し、リソース間の通信を監視する方法について説明します。 これにより、ハイブリッド デプロイおよび Azure クラウド デプロイがサポートされます。

## <a name="before-you-begin"></a>開始する前に 

接続モニター (プレビュー) で作成する接続モニターでは、オンプレミスのコンピューターと Azure VM の両方をソースとして追加できます。 これらの接続モニターでは、エンドポイントへの接続も監視できます。 エンドポイントは、Azure または他の任意の URL や IP 上のどこにあってもかまいません。

接続モニター (プレビュー) には、次のエンティティが含まれています。

* **接続モニター リソース** – リージョン固有の Azure リソース。 以下のすべてのエンティティが、接続モニター リソースのプロパティです。
* **エンドポイント** –接続チェックに含まれるソースまたはターゲット。 エンドポイントの例としては、Azure VM、オンプレミスのエージェント、URL、IP などがあります。
* **テスト構成** – テスト用のプロトコル固有の構成。 選択したプロトコルに基づいて、ポート、しきい値、テストの頻度、その他のパラメーターを定義できます。
* **テスト グループ** – ソース エンドポイント、ターゲット エンドポイント、テスト構成が含まれるグループ。 接続モニターには、複数のテスト グループを含めることができます。
* **テスト** – ソース エンドポイント、ターゲット エンドポイント、テスト構成の組み合わせ。 テストは、監視データを入手できる最も小さいレベルです。 監視データには、失敗したチェックの割合と、ラウンドトリップ時間 (RTT) が含まれます。

    ![テスト グループとテストの関係が定義されている接続モニターを示す図](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>サンプル ARM テンプレートを使用して作成する手順

ARMClient を使用して接続モニターを作成するには、次のコードを使用します。

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

デプロイ コマンドを次に示します。
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>プロパティの説明

* connectionMonitorName - 接続モニター リソースの名前

* SUB - 接続モニターを作成したいサブスクリプションのサブサブスクリプション ID

* NW - CM が作成される Network Watcher リソース ID 

* location - 接続モニターが作成されるリージョン

* エンドポイント
    * name – 各エンドポイントの一意の名前
    * resourceId – Azure エンドポイントの場合、リソース ID は仮想マシンの Azure Resource Manager (ARM) リソース ID を示します。Azure 以外のエンドポイントの場合、リソース ID は、Azure 以外のエージェントにリンクされている Log Analytics ワークスペースの ARM リソース ID を示します。
    * address – リソース ID が指定されていない場合、またはリソース ID が Log Analytics ワークスペースである場合にのみ適用されます。 Log Analytics リソース ID と共に使用される場合、これは監視に使用できるエージェントの FQDN を示します。 リソース ID なしで使用される場合、これは、任意のパブリック エンドポイントの URL または IP とすることができます。
    * filter – Azure 以外のエンドポイントの場合は、フィルターを使用して、接続モニター リソース内で監視に使用される Log Analytics ワークスペースからエージェントを選択します。 フィルターが設定されていない場合、Log Analytics ワークスペースに属するすべてのエージェントを監視に使用できます。
        * type – type は "エージェント アドレス" として設定します。
        * address – オンプレミスのエージェントの FQDN としてアドレスを設定します。

* テスト グループ
    * name – テスト グループの名前を指定します。
    * testConfigurations - テスト構成であり、これに基づいてソース エンドポイントはターゲット エンドポイントに接続されます。
    * sources - 上記で作成したエンドポイントから選択します。 Azure ベースのソース エンドポイントには Azure Network Watcher 拡張機能がインストールされている必要があり、Azure 以外をベースとしたソース エンドポイントには Azure Log Analytics エージェントがインストールされている必要があります。 ソース用のエージェントをインストールするには、「[監視エージェントをインストールする](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents)」を参照してください。
    * destinations - 前述で作成したエンドポイントから選択します。 Azure VM または任意のエンドポイント (パブリック IP、URL、FQDN) をターゲットとして指定すると、それらへの接続を監視できます。 1 つのテスト グループに、Azure VM、Office 365 URL、Dynamics 365 URL、カスタム エンドポイントを追加できます。
    * disable - このフィールドを使用すると、テスト グループで指定されているすべてのソースとターゲットに対する監視を無効にできます。

* テスト構成
    * name - テスト構成の名前です。
    * testFrequencySec - 指定したプロトコルとポートでソースがターゲットに ping を実行する頻度を指定します。 30 秒、1 分、5 分、15 分、30 分のいずれかを選択できます。 ソースでは、選択した値に基づいて、ターゲットへの接続がテストされます。 たとえば、30 秒を選択すると、ソースにより、少なくとも 30 秒に 1 回、ターゲットへの接続が確認されます。
    * protocol - TCP、ICMP、HTTP、または HTTPS を選択できます。 プロトコルによっては、いくつかのプロトコル固有の構成を行うことができます。
        * preferHTTPS - HTTP 経由で HTTPS を使用するかどうかを指定します。
        * port - 任意の宛先ポートを指定します。
        * disableTraceRoute - これは、プロトコルが TCP または ICMP であるテスト グループに適用されます。 これによって、ソースでのトポロジとホップバイホップ RTT の検出が停止します。
    * successThreshold - 以下のネットワーク パラメーターにしきい値を設定できます。
        * checksFailedPercent - 指定した条件で行われるソースによるターゲットへの接続の確認で、失敗してもかまわないチェックの割合を設定します。 TCP または ICMP プロトコルでは、失敗したチェックの割合はパケット損失の割合と同等と考えられます。 HTTP プロトコルでは、このフィールドは、応答を受け取らなかった HTTP 要求の割合を表します。
        * roundTripTimeMs - テスト構成でソースがターゲットに接続するのにかかるラウンドトリップ時間をミリ秒で設定します。

## <a name="scale-limits"></a>スケールの上限

接続モニターには、次のようなスケール制限があります。

* 1 つのリージョンの 1 つのサブスクリプションあたりの接続モニターの最大数: 100
* 接続モニターあたりのテスト グループの最大数: 20
* 接続モニターあたりのソースとターゲットの最大数: 100
* 接続モニターあたりのテスト構成の最大数: 20 (ARMClient を使用した場合)

## <a name="next-steps"></a>次の手順

* [監視データを分析してアラートを設定する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)を確認する
* [ネットワークの問題を診断する方法](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)を確認する
