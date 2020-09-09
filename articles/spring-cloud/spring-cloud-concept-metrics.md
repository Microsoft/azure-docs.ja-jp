---
title: Azure Spring Cloud のメトリックについて
description: Azure Spring Cloud でメトリックを確認する方法について説明します。
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1fcec8b591192bba862bf91040e5d6ca1ef9bcd8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037731"
---
# <a name="understand-metrics-for-azure-spring-cloud"></a>Azure Spring Cloud のメトリックについて

Azure メトリックス エクスプローラーは、グラフの描画、傾向の視覚的な相関付け、およびメトリックの値の急上昇と急降下の調査を行うことができる、Microsoft Azure portal のコンポーネントです。 メトリックス エクスプローラーを使用して、ご利用のソースの正常性と使用率を調べます。 

Azure Spring Cloud では、メトリックに 2 つのビューポイントがあります。
* 各アプリケーションの概要ページのグラフ
* 一般メトリック ページ

 ![メトリック グラフ](media/metrics/metrics-1.png)

アプリケーションの**概要**のグラフは、各アプリケーションの簡単な状態チェックを提供します。 一般**メトリック** ページには、参照のために使用できるすべてのメトリックが含まれます。 一般メトリック ページで独自のグラフを作成しし、それらをダッシュボードにピン留めできます。

## <a name="application-overview-page"></a>アプリケーションの概要ページ
**[アプリ]** でアプリを選択すると、概要ページにグラフが表示されます。  

 ![アプリケーションのメトリックの管理](media/metrics/metrics-2.png)

各アプリケーションの **[アプリケーションの概要]** ページには、アプリケーションの状態をすばやく確認できるようにするメトリック グラフが表示されます。  

 ![アプリケーションのメトリックの概要](media/metrics/metrics-3.png)

Azure Spring Cloud は、1 分ごとに更新されるメトリックを使用して、次の 5 つのグラフを提供します。

* **HTTP サーバー エラー**: アプリに対する HTTP 要求のエラー数
* **受信データ**: アプリが受信したバイト数
* **送信データ**: アプリが送信したバイト数
* **要求**: アプリが受信した要求数
* **平均応答時間**: アプリからの平均応答時間

グラフの場合、1 時間から 7 日までの時間範囲を選択できます。

## <a name="common-metrics-page"></a>一般メトリック ページ

左側のナビゲーション ウィンドウにある **[メトリック]** は、一般メトリック ページにリンクしています。

最初に、表示するメトリックを選択します。

![メトリック選択ビュー](media/metrics/metrics-4.png)

すべてのメトリック オプションの詳細は、以下の[セクション](#user-metrics-options)に記載されています。

次に、各メトリックの集計の種類を選択します。

![メトリックの集計](media/metrics/metrics-5.png)

集計の種類は、グラフ内のメトリック ポイントを時間で集計する方法を示します。 1 分ごとに 1 つの未加工のメトリック ポイントがあり、1 分以内の事前集計の種類がメトリックの種類によって事前に定義されています。
* 合計:ターゲット出力としてすべての値を合計します。
* 平均:ターゲット出力として期間中の平均値を使用します。
* 最大および最小: ターゲット出力として期間中の最大および最小値を使用します。

時間範囲はまた、過去 30 分から過去 30 日間までで調整するか、カスタムの時間範囲で調整できます。

![メトリックの変更](media/metrics/metrics-6.png)

既定のビューには、Azure Spring Cloud サービスのアプリケーションのメトリックがすべて一緒に含まれています。 1 つのアプリまたはインスタンスのメトリックを表示でフィルター処理できます。  **[フィルタの追加]** をクリックし、プロパティを **[アプリ]** に設定し、 **[値]** テキスト ボックスで監視するターゲット アプリケーションを選択します。 

次の 2 種類のフィルター (プロパティ) を使用できます。
* アプリ: アプリ名でフィルター処理
* インスタンス: アプリ インスタンスでフィルター処理

![メトリック フィルター](media/metrics/metrics-7.png)

また、 **[Apply splitting]\(分割の適用\)** オプションも使用できます。これにより、1 つのアプリに対して複数の線が描画されます。

![メトリックの分割](media/metrics/metrics-8.png)

>[!TIP]
> メトリック ページに独自のグラフを構築し、**ダッシュボード**にピン留めできます。 まず、グラフに名前を付けます。  次に、右上隅の **[ダッシュボードにピン留めする]** を選択します。 これで、ポータルの**ダッシュボード**でアプリケーションを確認できるようになりました。

## <a name="user-metrics-options"></a>ユーザー メトリックのオプション

次の表に、使用可能なメトリックとその詳細を示します。

### <a name="error"></a>エラー
>[!div class="mx-tdCol2BreakAll"]
>| 名前 | Spring Actuator メトリック名 | ユニット | 詳細 |
>|----|----|----|------------|
>| tomcat.global.error | tomcat.global.error | Count | 処理された要求で発生したエラーの数 |

### <a name="performance"></a>パフォーマンス
>[!div class="mx-tdCol2BreakAll"]
>| 名前 | Spring Actuator メトリック名 | ユニット | 詳細 |
>|----|----|----|------------|
>| system.cpu.usage | system.cpu.usage | Percent | システム全体の最近の CPU 使用率。 この値は、0.0 から 1.0 の範囲の倍精度浮動小数点数です。 値 0.0 は、最近の観測期間中にすべての CPU がアイドル状態であったことを意味します。一方、値 1.0 は、最近の観測期間中にすべての CPU が 100% の稼働状態であったことを意味します。|
>| process.cpu.usage | アプリの CPU 使用率 (%) | Percent | Java 仮想マシン プロセスの最近の CPU 使用率。 この値は、0.0 から 1.0 の範囲の倍精度浮動小数点数です。 値 0.0 は、最近の観測期間中に、どの CPU でも JVM プロセスからスレッドが実行されなかったことを意味します。一方、値 1.0 は、最近の観測期間中に、すべての CPU で JVM からスレッドが 100% 実行されたことを意味します。 JVM からのスレッドには、アプリケーション スレッドだけでなく、JVM 内部スレッドも含まれます。|
>| jvm.memory.committed | jvm.memory.committed | バイト | JVM で使用できることが保証されているメモリの量を表します。 JVM がシステムに対してメモリを解放する場合があり、保証されているメモリが初期値よりも少なくなる可能性があります。 保証されているメモリは、常に使用量以上になります。 |
>| jvm.memory.used | jvm.memory.used | バイト | 現在使用されているメモリの量を表します (バイト単位)。 |
>| jvm.memory.max | jvm.memory.max | バイト | メモリ管理に使用できるメモリの最大量を表します。 最大量が定義されている場合、使用されるメモリおよび保証されるメモリの量は常にそれ以下になります。 使用されているメモリ量が最大メモリ量以下であっても、保証されているメモリ量よりも使用されるメモリ量が大きくなるように使用されるメモリ量を増やそうとすると、メモリ割り当てが失敗する可能性があります (たとえば、システムの仮想メモリが不足している場合)。 |
>| jvm.gc.max.data.size | jvm.gc.max.data.size | バイト | Java 仮想マシンが起動されてからの、古い世代のメモリ プールのメモリ使用量のピーク。 |
>| jvm.gc.live.data.size | jvm.gc.live.data.size | バイト | 完全な GC 後の古い世代のメモリ プールのサイズ。 |
>| jvm.gc.memory.promoted | jvm.gc.memory.promoted | バイト | GC 前から後にかけた古い世代のメモリ プールのサイズ増加数。 |
>| jvm.gc.memory.allocated | jvm.gc.memory.allocated | バイト | 1つの GC の後から次の GC の前までの若い世代のメモリ プールのサイズ増加に合わせて増加。 |
>| jvm.gc.pause.total.count | jvm.gc.pause (合計数) | Count | この JMV が起動した後の GC の合計数 (若い GC と古い GC を含む)。 |
>| jvm.gc.pause.total.time | jvm.gc.pause (合計時間) | ミリ秒 | この JMV が起動した後に消費された GC 時間の合計 (若い GC と古い GC を含む)。 |

### <a name="request"></a>Request
>[!div class="mx-tdCol2BreakAll"]
>| 名前 | Spring Actuator メトリック名 | ユニット | 詳細 |
>|----|----|----|------------|
>| tomcat.global.sent | tomcat.global.sent | バイト | Tomcat Web サーバーが送信したデータの量 |
>| tomcat.global.received | tomcat.global.received | バイト | Tomcat Web サーバーが受信したデータの量 |
>| tomcat.global.request.total.count | tomcat.global.request (合計数) | Count | Tomcat Web サーバーが処理した要求の合計数 |
>| tomcat.global.request.max | tomcat.global.request.max | ミリ秒 | 要求を処理するための Tomcat Web サーバーの最大時間 |

### <a name="session"></a>Session
>[!div class="mx-tdCol2BreakAll"]
>| 名前 | Spring Actuator メトリック名 | ユニット | 詳細 |
>|----|----|----|------------|
>| tomcat.sessions.active.max | tomcat.sessions.active.max | Count | 同時にアクティブになったセッションの最大数 |
>| tomcat.sessions.alive.max | tomcat.sessions.alive.max | ミリ秒 | 期限切れのセッションがアライブ状態だった最長時間 (秒単位) |
>| tomcat.sessions.created | tomcat.sessions.created | Count | 作成されたセッションの数 |
>| tomcat.sessions.expired | tomcat.sessions.expired | Count | 期限切れになったセッションの数 |
>| tomcat.sessions.rejected | tomcat.sessions.rejected | Count | アクティブなセッションの最大数に達したために作成されなかったセッションの数。 |
>| tomcat.sessions.active.current | tomcat.sessions.active.current | Count | Tomcat セッションのアクティブ数 |

## <a name="see-also"></a>関連項目
* [Azure メトリックス エクスプローラーの概要](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started)

* [診断設定でログとメトリックを分析する](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)

## <a name="next-steps"></a>次のステップ
* [チュートリアル:アラートとアクション グループを使用して Spring Cloud のリソースを監視する](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-alerts-action-groups)

* [Azure Spring Cloud のクォータとサービス プラン](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quotas)

