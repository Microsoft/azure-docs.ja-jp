---
title: プールとノードのエラーのチェック - Azure Batch
description: この記事では、発生する可能性のあるバックグラウンド操作、およびプールとノードを作成するときにチェックするエラーとそれらを回避する方法について説明します。
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 88382a5b6e0364145d8504b5e25ef1a9bfd0111a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484130"
---
# <a name="check-for-pool-and-node-errors"></a>プールとノードのエラーのチェック

Azure Batch プールを作成して管理するとき、いくつかの操作は直ちに行われます。 ただし、一部の操作は非同期的にバック グラウンドで実行され、完了までに数分かかります。

API、CLI、UI で直ちにエラーが返されるので、すぐに実行される操作のエラーを検出するのは簡単です。

この記事では、プールとプールのノードに対して発生することがあるバック グラウンド操作について説明します。 ここでは、障害を検出して回避する具体的な方法を示します。

## <a name="pool-errors"></a>プールのエラー

### <a name="resize-timeout-or-failure"></a>タイムアウトまたはエラーのサイズ変更

新しいプール作成する場合、または既存のプールのサイズ変更を行う場合、ノードのターゲット数はユーザーが指定します。  作成操作またはサイズ変更操作はすぐに完了しますが、新しいノードを実際に割り当てたり既存のノードを削除したりするには数分かかる場合があります。  サイズ変更タイムアウトを[作成](https://docs.microsoft.com/rest/api/batchservice/pool/add)または[サイズ変更](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API で指定します。 サイズ変更タイムアウトの期間中に、Batch でターゲットとする数のノードを取得できない場合、プールが定常状態に移行し、サイズ変更エラーがレポートされます。

直近の評価についての [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) プロパティに、発生したエラーが列挙されます。

サイズ変更エラーの一般的な原因は次のとおりです。

- サイズ変更タイムアウトが短すぎる
  - ほとんどの状況では、プールのノードを割り当てまたは削除するための時間は、既定のタイムアウトである 15 分あれば十分です。
  - 割り当てるノードの数が多い場合は、サイズ変更タイムアウトを 30 分に設定することをお勧めします。 たとえば、Azure Marketplace イメージから 1,000 を超えるノードにサイズ変更したり、カスタム VM イメージから 300 を超えるノードにサイズ変更したりする場合が該当します。
- 不十分なコア クォータ
  - バッチアカウントでは、すべてのプール全体に割り当て可能なコア数が制限されています。 そのクォータに達すると、バッチはノードの割り当てを停止します。 バッチがさらに多くのノードを割り当てられるようにするために、コア クォータを[増やすことができます](https://docs.microsoft.com/azure/batch/batch-quota-limit)。
- [プールが仮想ネットワーク内にある](https://docs.microsoft.com/azure/batch/batch-virtual-network)場合の不足しているサブネット IP アドレス
  - 仮想ネットワーク サブネットには、要求されたすべてのプールのノードに割り当てるための未割り当ての IP アドレスが十分に存在する必要があります。 そうでない場合、ノードを作成できません。
- [プールが仮想ネットワーク](https://docs.microsoft.com/azure/batch/batch-virtual-network)の時の不十分なリソース
  - ロード バランサー、パブリック IP、ネットワーク セキュリティ グループなどのリソースを、バッチ アカウントと同じサブスクリプションに作成する場合があります。 これらのリソースのためのサブスクリプション クォータが十分であるか確認してください。
- カスタム VM イメージを使用した大規模なプール
  - カスタム VM イメージを使用する大規模なプールは割り当てに時間がかかり、タイムアウトのサイズ変更が発生することがあります。  制限と構成に関する推奨事項については、「[Shared Image Gallery を使用してプールを作成する](batch-sig-images.md)」を参照してください。

### <a name="automatic-scaling-failures"></a>自動スケールの失敗

プール内のノード数を自動的に拡大縮小するように Azure Batch を設定することもできます。 [プール用の自動スケールの数式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)のパラメーターを定義します。 Batch サービスは数式を使用して、プール内のノード数を定期的に評価し、新しいターゲット数を設定します。 次の種類の問題が発生する可能性があります。

- 自動スケール評価が失敗する。
- 結果のサイズ変更操作が失敗してタイムアウトになることがある。
- 自動スケールの数式に問題があるため、ノードのターゲット値が正しくなくなる。 サイズ変更は動作するかタイムアウトになる。

[autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) プロパティを使用して、最後の自動スケール評価に関する情報を取得することができます。 このプロパティは、評価期間、値と結果、およびパフォーマンス エラーを報告します。

[プールのサイズ変更完了イベント](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)により、すべての評価に関する情報がキャプチャされます。

### <a name="delete"></a>削除

ノードを含むプールを削除するとき、Batch はまずノードを削除します。 次にプール オブジェクト自体を削除します。 プールのノードを削除するまでに数分かかります。

Batch は削除プロセス中に[プールの状態](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)を **deleting** に設定します。 プールの削除時間がかかりすぎている場合、呼び出しアプリケーションが **state** と **stateTransitionTime** プロパティを使用して検出します。

## <a name="pool-compute-node-errors"></a>プールのコンピューティング ノードエラー

Batch がプール内のノードを正常に割り当てた場合でも、さまざまな問題が原因で一部のノードが異常な状態になったり、タスクを実行できなくなったりする場合があります。 これらのノードによって引き続き料金が発生してしまうので、使用できないノードに対する支払いを回避するために問題を検出することが重要です。 一般的なノードエラーに加えて、現在の[ジョブの状態](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)を把握しておくと、トラブルシューティングに役立ちます。

### <a name="start-task-failures"></a>開始タスクの失敗

プールについて、省略可能な[タスクの開始](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)を指定する場合もあります。 すべてのタスクと同じように、ストレージからダウンロードするためにコマンドラインとリソース ファイルを指定できます。 タスクの開始は各ノードの開始後にノードごとに実行されます。 **WaitForSuccess** プロパティは、Batch がすべてのタスクをノードにスケジュールする前に、タスクの開始が正常に完了するまで Batch が待機するかどうかを指定します。

タスクの開始が正しく完了するまで待機するようノードを構成したが、タスクの開始が失敗した場合はどうなるでしょうか。 その場合、ノードは使用できませんが、料金は発生します。

タスクの開始の失敗は、最上位レベルの [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) ノードプロパティの [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) と [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) プロパティを使って検出できます。

また、**waitForSuccess** が **true** に設定された場合、開始タスクに失敗すると、Batch はノードの[状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)を **starttaskfailed** に設定します。

すべてのタスクと同様、タスクの開始の失敗には多くの原因があります。  トラブルシューティングするには stdout、stderr、さらにタスク固有のログ ファイルをチェックしてください。

開始タスクは同じノードで複数回実行される可能性があるため、再入可能である必要があります。ノードの再イメージ化時と再起動時に開始タスクが実行されます。 まれに、イベントによってノードの再起動が引き起こされた後、オペレーティング システム ディスクとエフェメラル ディスクの一方が再イメージ化され、もう一方は再イメージ化されていない状態で開始タスクが実行されます。 Batch の開始タスクは (すべての Batch タスクと同様) エフェメラル ディスクから実行されるため、このことが問題になることは通常ありません。しかし、開始タスクでオペレーティング システム ディスクにアプリケーションをインストールし、エフェメラル ディスクには他のデータを維持するような一部のケースでは、同期が失われるために、このことが問題の原因となる可能性があります。両方のディスクを使用している場合は、アプリケーションを適切に保護してください。

### <a name="application-package-download-failure"></a>アプリケーション パッケージのダウンロード エラー

プール用の 1 つ以上のアプリケーション パッケージを指定できます。 Batch は指定されたパッケージ ファイルを各ノードにダウンロードし、ノードが開始した後、タスクがスケジュールされる前にファイルを圧縮解除します。 アプリケーション パッケージと組み合わせてタスクの開始のコマンドラインを使用することが一般的です。 たとえば、別の場所にファイルをコピーしたり、セットアップを実行したりする場合が該当します。

ノード [エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) プロパティは、アプリケーション パッケージのダウンロードや圧縮解除に失敗したノードをレポートします。このとき、ノードの状態は **unusable** に設定されます。

### <a name="container-download-failure"></a>コンテナーのダウンロード エラー

プールには、コンテナーの参照を 1 つまたは複数指定できます。 指定したコンテナーが Batch によって各ノードにダウンロードされます。 ノードの [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) プロパティによって、コンテナーのダウンロード エラーがレポートされ、そのノードの状態が**使用不可**に設定されます。

### <a name="node-in-unusable-state"></a>ノードは使用できない状態

Azure Batch はさまざまな理由で[ノード状態](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)を**使用不可**に設定します。 ノード状態が**使用不可**に設定された場合、ノードにタスクをスケジュールできませんが、料金は発生します。

ノードが **unusable** 状態であるにもかかわらず、[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)を伴っていない場合、Batch が VM と通信できないことを意味します。 このケースでは、Batch によって常に VM の復旧が試みられます。 アプリケーション パッケージまたはコンテナーのインストール エラーが発生した VM については、その状態が **unusable** であっても、Batch が自動的に復旧を試みることはありません。

Batch が原因を特定できる場合、ノードの[エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)プロパティは原因を報告します。

**使用できない** ノードが発生する原因として、次のような例もあります。

- カスタム VM イメージが無効である。 たとえば、イメージが適切に作成されていない場合があります。

- インフラストラクチャの障害または低レベルのアップグレードのため、VM が移動した。 Batch はノードを回復します。

- VM イメージが、それをサポートしないハードウェアにデプロイされている。 [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) の VM で CentOS HPC イメージを実行したりすることが該当します。

- VM が [Azure Virtual Network](batch-virtual-network.md) に存在し、重要なポートへのトラフィックがブロックされている。

- VM は仮想ネットワークに存在するが、Azure Storage へのアウトバウンド トラフィックがブロックされている。

- カスタマーの DNS 構成を使用した仮想ネットワークに VM が存在し、DNS サーバーが Azure Storage を解決できない。

### <a name="node-agent-log-files"></a>ノード エージェント ログ ファイル

各プールのノードで実行される Batch エージェント プロセスはログ ファイルを作成でき、そのログ ファイルは、プールのノード上の問題についてサポートに連絡する必要があるときに役立つ場合があります。 ノードのログ ファイルは、Azure portal、Batch Explorer、または [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 経由でアップロードできます。 ログ ファイルをアップロードして保存することをお勧めします。 実行中のノードのコストを節約するために、ノードまたはプールを後で削除できます。

### <a name="node-disk-full"></a>ノードのディスクがいっぱいである

ジョブ ファイル、タスク ファイル、共有ファイルには、プール ノード VM の一時ドライブが Batch によって使用されます。

- アプリケーション パッケージ ファイル
- タスク リソース ファイル
- Batch のいずれかのフォルダーにダウンロードされたアプリケーション固有のファイル
- タスク アプリケーションの実行ごとの stdout ファイルと stderr ファイル
- アプリケーション固有の出力ファイル

これらのファイルのいくつかは、プール ノードの作成時に 1 回だけ書き込まれます (プールのアプリケーション パッケージやプールの開始タスクのリソース ファイルなど)。 ノードの作成時に 1 回しか書き込まれなかったとしても、ファイルが大きすぎると、それだけで一時ドライブがいっぱいになってしまう可能性はあります。

一方、ノード上で実行されたタスクごとに書き込まれるファイルもあります (stdout や stderr など)。 同じノード上で大量のタスクが実行された場合や、タスクのファイルが大きすぎる場合、一時ドライブがいっぱいになってしまう可能性があります。

一時ドライブのサイズは、VM のサイズによって異なります。 一時ドライブの容量を十分に確保することが、VM のサイズを選ぶ際の 1 つの考慮事項となります。

- Azure portal では、プールを追加する際に VM サイズの全一覧を表示でき、その中に、"リソース ディスク サイズ" という列があります。
- すべての VM サイズについて説明した記事に、"一時ストレージ" 列を含んだ表が掲載されています ([コンピューティング最適化済み VM サイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)に関する記事など)。

それぞれのタスクによって書き込まれるファイルについては、タスクごとに保持期間を指定できます。保持期間はタスク ファイルが保持される期間を決めるもので、その期間を経過したファイルは自動的にクリーンアップされます。 保存期間を短縮することで、ストレージの要件を軽減することができます。

現在、一時ディスク領域がいっぱいになった場合、ノードによるタスクの実行は停止されます。 将来は、[ノード エラー](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)がレポートされるようになります。


## <a name="next-steps"></a>次のステップ

包括的なエラー チェック (特に非同期操作に対するエラー チェック) を実装するようにアプリケーションを設定したことを確認します。 これは問題をすばやく検出して診断するために欠かない場合があります。
