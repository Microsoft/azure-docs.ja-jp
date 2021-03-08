---
title: Azure Monitor for VMs のゲストの正常性 (プレビュー)
description: 仮想マシンの正常性を表示したり、仮想マシンが異常になったときにアラートを受信したりする方法を含む、Azure Monitor for VMs の正常性機能の概要。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 96bed9f3b04e54e2e9a5234d78f9a2660126742e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100600762"
---
# <a name="azure-monitor-for-vms-guest-health-preview"></a>Azure Monitor for VMs のゲストの正常性 (プレビュー)
Azure Monitor for VMs のゲストの正常性を使用すると、ゲスト オペレーティング システムから一定間隔でサンプリングされる一連のパフォーマンス測定値に基づいて、仮想マシンの正常性を表示できます。 サブスクリプションまたはリソース グループ内のすべての仮想マシンの正常性を迅速に確認したり、特定の仮想マシンの詳細な正常性をドリルダウンしたり、仮想マシンが異常になったときにプロアクティブに通知を受け取ったりすることができます。 

## <a name="enable-virtual-machine-health"></a>仮想マシンの正常性を有効にする
ゲストの正常性機能を有効にし、仮想マシンをオンボードする方法の詳細については、「[Azure Monitor for VMs のゲストの正常性 (プレビュー) を有効にする](vminsights-health-enable.md)」を参照してください。

## <a name="pricing"></a>価格
ゲストの正常性機能には直接のコストは発生しませんが、Log Analytics ワークスペースの正常性状態データのインジェストとストレージにはコストが発生します。 すべてのデータは、*HealthStateChangeEvent* テーブルに格納されます。 価格モデルとコストの詳細については、「[Azure Monitor ログで使用量とコストを管理する](../platform/manage-cost-storage.md)」を参照してください。

## <a name="view-virtual-machine-health"></a>仮想マシンの正常性を表示する
**[作業の開始]** ページの **[ゲスト VM の正常性]** 列では、特定のサブスクリプションまたはリソース グループ内の各仮想マシンの正常性を簡単に確認できます。 各仮想マシンの現在の正常性が表示される同時に、各グループのアイコンによってそのグループ内の現在それぞれの状態にある仮想マシンの数が表示されます。

[![ゲスト VM の正常性を示す [作業の開始] ページ](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>モニター
仮想マシンの正常性状態をクリックすると、そのモニターごとに詳細な状態が表示されます。 各モニターにより、特定のコンポーネントの正常性が測定されます。 仮想マシンの全体的な正常性は、個々のモニターの正常性によって決まります。 

![モニターの例](media/vminsights-health-overview/monitors.png)

次の表に、各仮想マシンで現在利用可能な集計およびユニットのモニターを示します。 

| Monitor | Type | Description |
|:---|:---|:---|
| CPU 使用率 | ユニット | プロセッサの使用率。 |
| ファイル システム | Aggregate | Linux VM 上のすべてのファイル システムの正常性を集計します。 |
| ファイル システム  | Aggregate | Linux VM 上の個々のファイル システムの正常性。 モニターの名前は、ファイル システムの名前です。 |
| 空き領域 | ユニット | ファイ ルシステム上の空き領域の割合。 |
| 論理ディスク | Aggregate | Windows VM 上のすべての論理ディスクの正常性を集計します。 |
| 論理ディスク  | Aggregate | Windows VM 上の個々の論理ディスクの正常性。 モニターの名前は、ディスクの名前です。 |
| メモリ | Aggregate | VM 上のメモリの正常性を集計します。 |
| 使用可能なメモリ | ユニット | VM で使用可能なメガバイト数。 |

## <a name="health-states"></a>正常性状態
各モニターはエージェントで 1 分ごとに値をサンプリングし、サンプリングされた値を各正常性状態の基準と比較します。 特定の状態の基準が当てはまる場合、モニターはその状態に設定されます。 いずれの基準も当てはまらない場合、モニターは正常状態に設定されます。 データは、エージェントから Azure Monitor に 3 分ごとに送信され、状態が変更された場合は即時に送信されます。

各モニターにはルックバック ウィンドウがあり、その時間内に収集されたすべてのサンプルが分析されます。 たとえば、少なくとも 2 つ以上で直近の 3 つ以下のサンプリングされた値の中で最大値を求める 240 秒のルックバック ウィンドウをモニターに設定できます。 値は通常のレートでサンプリングされますが、エージェント操作が中断された場合、特定のウィンドウでサンプリングされる数はわずかに変化する可能性があります。

モニターにはそれぞれ、次の表に示すような取り得る正常性状態がありますが、特定の時点ではただ 1 つの状態になります。 モニターが初期化されると、正常状態で起動します。

| 正常性の状態 | 説明 |
|:---|:---|
| Healthy  | モニターは現在、警告またはクリティカルのしきい値を超えていません。 |
| 警告  | モニターが警告のしきい値を超えました (定義されている場合)。 |
| Critical | モニターがクリティカルのしきい値を超えました (定義されている場合)。 |
| Unknown  | 正常性状態の判断に十分なデータが収集されていません。 |
| 無効 | モニターは現在、無効になっています。 |
| なし     | モニターが開始されたばかりで、まだ評価されていないか、監視対象オブジェクトが存在しません。 |



次の表に示すように、モニターには 2 種類あります。

| モニター | 説明 |
|:---|:---|
| ユニット モニター | リソースまたはアプリケーションの一部の側面を測定します。 パフォーマンス カウンターを調べて、リソースのパフォーマンスまたはその可用性を判断することなどが含まれます。 |
| 集計モニター | 複数のモニターをグループ化して、1 つの集計された正常性状態を示します。 集計モニターには、1 つ以上のユニット モニターとその他の集計モニターを含めることができます。 |


  
### <a name="health-rollup-policy"></a>正常性のロールアップ ポリシー
仮想マシンの正常性状態は、ユニットおよび集計の各モニターからの正常性のロールアップによって決まります。 各集計モニターは、最も悪い状態の正常性のロールアップ ポリシーを使用します。この場合、集計モニターの状態は、正常性状態が最も悪い子モニターの状態と一致します。  

次の例では、 **[空き領域]** モニターがクリティカル状態にあり、これがそのインスタンスの集計、そして **[ファイル システム]** へと順にロール アップします。 **[CPU 使用率]** が警告状態であっても、仮想マシンはクリティカルに設定されます。これがその下で最も悪い状態であるためです。 空き領域が正常状態に戻った場合、CPU 使用率が最も悪い状態のモニターになるため、仮想マシンは警告状態に変わります。

![正常性のロールアップの例](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>モニターの詳細
モニターを選択してその詳細を表示します。これには、次のタブが含まれています。

**[概要]** には、モニターの説明、最後に評価された時刻、現在の正常性状態を判断するためにサンプリングされた値が表示されます。 サンプルの数は、モニターの定義と値の変動性によって異なる場合があります。

[![モニター詳細の [概要]](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

**[履歴]** には、モニターの状態変更の履歴が一覧表示されます。 状態変更のいずれかを展開して、正常性状態を判断するために評価された値を表示できます。 正常性状態が変更された時点でのモニターの構成を表示するには、 **[適用された構成の表示]** をクリックします。



[![モニター詳細の [履歴]](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>構成
選択した VM のモニターの構成を表示および変更します。 詳細については、[Azure Monitor for VMs のゲストの正常性 (プレビュー) での監視の構成](vminsights-health-enable.md)に関する記事を参照してください。

[![モニター詳細の [構成]](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>次のステップ

- [Azure Monitor for VMs のゲストの正常性を有効にして、エージェントをオンボードします。](vminsights-health-enable.md)
- [Azure portal を使用してモニターを構成します。](vminsights-health-configure.md)
- [データ収集ルールを使用してモニターを構成します。](vminsights-health-configure-dcr.md)