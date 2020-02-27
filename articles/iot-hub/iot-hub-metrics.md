---
title: メトリックを使用して Azure IoT Hub を監視する | Microsoft Docs
description: Azure IoT Hub メトリックを使用して、IoT Hub の全体的な正常性を評価および監視する方法。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705736"
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub メトリックの理解

IoT Hub メトリックは、Azure サブスクリプション内の Azure IoT リソースの状態に関する有用なデータを提供します。 IoT Hub メトリックにより、ユーザーは IoT Hub サービスとそれに接続されたデバイスの全体的な正常性を評価することができます。 ユーザー向けの統計情報は重要です。この情報は、IoT Hub で起こっていることを確認するのに役立ち、また、Azure サポートに連絡することなく問題の根本原因を特定するための有用な情報となります。

メトリックは既定で有効になっています。 IoT Hub メトリックは Azure Portal で表示できます。

> [!NOTE]
> IoT Hub メトリックを使用して、お使いの IoT ハブに接続されている IoT プラグ アンド プレイ デバイスに関する情報を表示できます。 IoT プラグ アンド プレイ デバイスは、[IoT プラグ アンド プレイ パブリック プレビュー](../iot-pnp/overview-iot-plug-and-play.md)の一部です。

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub メトリックを表示する方法

1. IoT Hub を作成します。 IoT ハブを作成する手順については、[デバイスから IoT Hub へのテレメトリの送信](quickstart-send-telemetry-dotnet.md)に関するガイドを参照してください。

2. IoT Hub のブレードを開きます。 そこで **[メトリック]** をクリックします。
   
    ![メトリック オプションが IoT Hub リソース ページにある場所を示すスクリーンショット](./media/iot-hub-metrics/enable-metrics-1.png)

3. メトリック ブレードから、IoT Hub のメトリックを表示し、メトリックのカスタム ビューを作成できます。 
   
    ![IoT Hub のメトリック ページを示すスクリーンショット](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. メトリック データを Event Hubs エンドポイントまたは Azure ストレージ アカウントに送信するように選択するには、 **[診断設定]** 、 **[診断設定の追加]** の順にクリックします。

   ![診断設定ボタンの位置を示すスクリーンショット](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub メトリックとその使用方法

IoT Hub には、ハブの正常性の概要と、ハブに接続されているデバイスの総数を示すいくつかのメトリックが用意されています。 複数のメトリックから得た情報を組み合わせることで、IoT Hub の状態をより大局的に把握することができます。 次の表では、各 IoT Hub で追跡されるメトリックを示すと共に、各メトリックが IoT Hub の全体的な状態にどのように関連するかを説明します。

|メトリック|メトリックの表示名|ユニット|集計の種類|説明|Dimensions|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|テレメトリ メッセージ送信試行|Count|合計|IoT Hub への送信が試行された Device to Cloud テレメトリ メッセージの数|なし|
|d2c.telemetry.ingress.success|送信済みテレメトリ メッセージ|Count|合計|IoT Hub に正常に送信された Device to Cloud テレメトリ メッセージの数|なし|
|c2d.commands.egress.complete.success|C2D メッセージ配信完了|Count|合計|デバイスで正常に完了した Cloud to Device メッセージ配信の数|なし|
|c2d.commands.egress.abandon.success|破棄した C2D メッセージ|Count|合計|デバイスで中止された Cloud to Device メッセージの数|なし|
|c2d.commands.egress.reject.success|C2D メッセージ拒否数|Count|合計|デバイスで拒否された Cloud to Device メッセージの数|なし|
|C2DMessagesExpired|期限切れ C2D メッセージ (プレビュー)|Count|合計|有効期限が切れた cloud-to-device メッセージの数|なし|
|devices.totalDevices|デバイスの総数 (非推奨)|Count|合計|IoT Hub に登録されたデバイスの数|なし|
|devices.connectedDevices.allProtocol|接続されているデバイス (非推奨) |Count|合計|IoT Hub に接続されているデバイスの数|なし|
|d2c.telemetry.egress.success|ルーティング: 配信されたテレメトリ メッセージ|Count|合計|IoT Hub ルーティングを使用して、メッセージがすべてのエンドポイントに正常に配信された回数。 メッセージが複数のエンドポイントにルーティングされている場合、この値は正常に配信されるたびに 1 つずつ増えます。 メッセージが同じエンドポイントに複数回配信されている場合、この値は正常に配信されるたびに 1 つずつ増えます。|なし|
|d2c.telemetry.egress.dropped|ルーティング: 破棄されたテレメトリ メッセージ |Count|合計|デッド エンドポイントであるため、メッセージが IoT Hub ルーティングで破棄された回数。 この値では、フォールバック ルートに配信されるメッセージはカウントされません。破棄されたメッセージはそこには配信されないためです。|なし|
|d2c.telemetry.egress.orphaned|ルーティング: 孤立したテレメトリ メッセージ |Count|合計|どのルーティング規則 (フォールバック規則を含む) にも一致しなかっため、メッセージが IoT Hub ルーティングによって孤立した回数。 |なし|
|d2c.telemetry.egress.invalid|ルーティング: 互換性のないテレメトリ メッセージ|Count|合計|エンドポイントと互換性がないため、IoT Hub ルーティングでメッセージを配信できなかった回数。 この値に再試行回数は含まれません。|なし|
|d2c.telemetry.egress.fallback|ルーティング: フォールバックのために配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、フォールバック ルートに関連付けられているエンドポイントにメッセージが配信された回数。|なし|
|d2c.endpoints.egress.eventHubs|ルーティング: Event Hub に配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Event Hub エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.eventHubs|ルーティング: Event Hub のメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Event Hub エンドポイントの受信メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.serviceBusQueues|ルーティング: Service Bus キューに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus キュー エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.serviceBusQueues|ルーティング: Service Bus キューのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus キュー エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.serviceBusTopics|ルーティング: Service Bus トピックに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが Service Bus トピック エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.serviceBusTopics|ルーティング: Service Bus トピックのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと Service Bus トピック エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.builtIn.events|ルーティング: メッセージ/イベントに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージが組み込みのエンドポイント (メッセージ/イベント) に正常に配信された回数。|なし|
|d2c.endpoints.latency.builtIn.events|ルーティング: メッセージ/イベントのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージと組み込みエンドポイント (メッセージ/イベント) の受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.storage|ルーティング: ストレージに配信されたメッセージ|Count|合計|IoT Hub ルーティングにより、メッセージがストレージ エンドポイントに正常に配信された回数。|なし|
|d2c.endpoints.latency.storage|ルーティング: ストレージのメッセージの待機時間|ミリ秒|Average|IoT Hub の受信メッセージとストレージ エンドポイントの受信テレメトリ メッセージの間の平均待機時間 (ミリ秒)。|なし|
|d2c.endpoints.egress.storage.bytes|ルーティング: ストレージに配信されたデータ|バイト|合計|IoT Hub ルーティングでストレージ エンドポイントに配信されたデータの量 (バイト)。|なし|
|d2c.endpoints.egress.storage.blobs|ルーティング: ストレージに配信された BLOB|Count|合計|IoT Hub ルーティングで BLOB がストレージ エンドポイントに配信された回数。|なし|
|EventGridDeliveries|Event Grid 配信 (プレビュー)|Count|合計|Event Grid に発行された IoT Hub イベントの数。 成功および失敗した要求の数には、Result ディメンションを使用します。 EventType ディメンションはイベントの種類 (https://aka.ms/ioteventgrid) ) を示します。|ResourceId、<br/>Result、<br/>EventType|
|EventGridLatency|Event Grid の待機時間 (プレビュー)|ミリ秒|Average|oT Hub イベントが生成されてから、そのイベントが Event Grid に発行されるまでの平均待機時間 (ミリ秒)。 この数は、すべてのイベントの種類の間の平均値です。 特定の種類のイベントの待機時間を確認するには、EventType ディメンションを使用します。|ResourceId、<br/>EventType|
|d2c.twin.read.success|成功したデバイスからのツイン読み取り|Count|合計|デバイスが開始して成功したツイン読み取りの数。|なし|
|d2c.twin.read.failure|失敗したデバイスからのツイン読み取り|Count|合計|デバイスが開始したツイン読み取りの失敗数。|なし|
|d2c.twin.read.size|デバイスからのツイン読み取りの応答サイズ|バイト|Average|デバイスが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|なし|
|d2c.twin.update.success|成功したデバイスからのツイン更新|Count|合計|デバイスが開始して成功したツイン更新の数。|なし|
|d2c.twin.update.failure|失敗したデバイスからのツイン更新|Count|合計|デバイスが開始して失敗したツイン更新の数。|なし|
|d2c.twin.update.size|デバイスからのツイン更新のサイズ|バイト|Average|デバイスが開始して成功したツイン更新の平均、最小、および最大サイズ。|なし|
|c2d.methods.success|成功したダイレクト メソッドの呼び出し|Count|合計|成功したダイレクト メソッドの呼び出しの数。|なし|
|c2d.methods.failure|失敗したダイレクト メソッドの呼び出し|Count|合計|失敗したダイレクト メソッドの呼び出しの数。|なし|
|c2d.methods.requestSize|ダイレクト メソッドの呼び出しの要求サイズ|バイト|Average|成功したダイレクト メソッド要求の平均、最小、および最大サイズ。|なし|
|c2d.methods.responseSize|ダイレクト メソッドの呼び出しの応答サイズ|バイト|Average|成功したダイレクト メソッド応答の平均、最小、および最大サイズ。|なし|
|c2d.twin.read.success|成功したバック エンドからのツイン読み取り|Count|合計|バックエンドが開始して成功したツイン読み取りの数。|なし|
|c2d.twin.read.failure|失敗したバックエンドからのツイン読み取り|Count|合計|バックエンドが開始して失敗したツイン読み取りの数。|なし|
|c2d.twin.read.size|バック エンドからのツイン読み取りの応答サイズ|バイト|Average|バックエンドが開始して成功したツイン読み取りの平均、最小、および最大サイズ。|なし|
|c2d.twin.update.success|成功したバックエンドからのツイン更新|Count|合計|バックエンドが開始して成功したツイン更新の数。|なし|
|c2d.twin.update.failure|失敗したバック エンドからのツイン更新|Count|合計|バックエンドが開始して失敗したツイン更新の数。|なし|
|c2d.twin.update.size|バックエンドからのツイン更新のサイズ|バイト|Average|バックエンドが開始して成功したツイン更新の平均、最小、および最大サイズ。|なし|
|twinQueries.success|成功したツイン クエリ|Count|合計|成功したツイン クエリの数。|なし|
|twinQueries.failure|失敗したツイン クエリ|Count|合計|失敗したツイン クエリの数。|なし|
|twinQueries.resultSize|ツイン クエリの結果のサイズ|バイト|Average|成功したツイン クエリの結果の平均、最小、および最大サイズ。|なし|
|jobs.createTwinUpdateJob.success|成功したツイン更新ジョブの作成|Count|合計|正常に作成されたツイン更新ジョブの数。|なし|
|jobs.createTwinUpdateJob.failure|失敗したツイン更新ジョブの作成|Count|合計|作成に失敗したツイン更新ジョブの数。|なし|
|jobs.createDirectMethodJob.success|成功したメソッド呼び出しジョブの作成|Count|合計|正常に作成されたダイレクト メソッド呼び出しジョブの数。|なし|
|jobs.createDirectMethodJob.failure|失敗したメソッド呼び出しジョブの作成|Count|合計|作成に失敗したダイレクト メソッド呼び出しジョブの数。|なし|
|jobs.listJobs.success|成功したジョブ一覧の呼び出し|Count|合計|正常に実行されたジョブ一覧の呼び出しの数。|なし|
|jobs.listJobs.failure|失敗したジョブ一覧の呼び出し|Count|合計|失敗したジョブ一覧の呼び出しの数。|なし|
|jobs.cancelJob.success|成功したジョブの取り消し|Count|合計|正常に実行されたジョブ取り消しの呼び出し。|なし|
|jobs.cancelJob.failure|失敗したジョブの取り消し|Count|合計|失敗したジョブ取り消しの呼び出し。|なし|
|jobs.queryJobs.success|成功したジョブ クエリ|Count|合計|正常に実行されたクエリ ジョブの呼び出しの数。|なし|
|jobs.queryJobs.failure|失敗したジョブ クエリ|Count|合計|失敗したジョブ クエリの呼び出しの数。|なし|
|jobs.completed|完了したジョブ|Count|合計|完了したジョブの数。|なし|
|jobs.failed|失敗したジョブ|Count|合計|失敗したジョブの数。|なし|
|d2c.telemetry.ingress.sendThrottle|調整エラーの数|Count|合計|デバイスのスループット調整による調整エラーの数|なし|
|dailyMessageQuotaUsed|使用されているメッセージの合計数|Count|Average|現在使用されているメッセージの合計数。 これは累積値であり、毎日 00 時 00 分 (UTC) になるとゼロにリセットされます。|なし|
|deviceDataUsage|デバイス データの合計使用量|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|deviceDataUsageV2|デバイス データの合計使用量 (プレビュー)|バイト|合計|Iot Hub に接続されているデバイスとの間で転送されたバイト数|なし|
|totalDeviceCount|デバイスの合計数 (プレビュー)|Count|Average|IoT Hub に登録されたデバイスの数|なし|
|connectedDeviceCount|接続されているデバイス (プレビュー)|Count|Average|IoT Hub に接続されているデバイスの数|なし|
|configuration|構成メトリック|Count|合計|構成操作のメトリック|なし|

## <a name="next-steps"></a>次のステップ

IoT Hub メトリックの概要については以上です。Azure IoT Hub の管理に関する詳細については、次のリンク先にアクセスしてください。

* [診断ログの設定](iot-hub-monitor-resource-health.md)

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)

* [Azure IoT Edge でエッジ デバイスに AI をデプロイする](../iot-edge/tutorial-simulate-device-linux.md)
