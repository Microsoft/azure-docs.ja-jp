---
title: Azure Change Tracking の問題を解決する
description: この記事では、Change Tracking のトラブルシューティングに関する情報を提供します
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 10/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 686a2c0ef0766176658d42d5ca53ea1ce6d3241c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52722940"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Change Tracking とインベントリのトラブルシューティング

## <a name="windows"></a> Windows

### <a name="records-not-showing-windows"></a>シナリオ:Change Tracking レコードが Azure portal に表示されません

#### <a name="issue"></a>問題

Change Tracking のためにオンボーディングされているコンピューターに対してインベントリまたは Change Tracking が表示されません。

#### <a name="cause"></a>原因

このエラーは次の理由で発生する可能性があります。

1. **Microsoft Monitoring Agent** が動作していません。
2. Automation アカウントに戻る通信がブロックされています。
3. Management Packs for Change Tracking がダウンロードされていません。
4. オンボードしている VM の複製元が、Microsoft Monitoring Agent がインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

#### <a name="resolution"></a>解決策

1. **Microsoft Monitoring Agent** (HealthService.exe) がコンピューター上で実行されていることを確認します。
2. [ネットワーク計画](../automation-hybrid-runbook-worker.md#network-planning)に関するセクションで、Change Tracking を動作させるために許可する必要があるアドレスとポートを確認します。
3. Change Tracking とインベントリの次の管理パックがローカルに存在することを確認します。
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
4. 複製イメージを使用する場合は、そのイメージを先に sysprep してから、Microsoft Monitoring Agent エージェントをインストールします。

以上の解決策で問題が解決されず、サポートに連絡する場合、次のコマンドを実行し、エージェントで診断データを収集します。

エージェント コンピューター上で `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` に移動し、次のコマンドを実行します。

```cmd
set stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 既定では、エラー トレースは有効になっています。前の例のような詳しいエラー メッセージを有効にするには、`VER` パラメーターを使用します。 情報トレースが必要であれば、`StartTracing.cmd` を呼び出すときに `INF` を使用します。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。