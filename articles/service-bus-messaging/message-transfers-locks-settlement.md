---
title: Azure Service Bus メッセージの転送、ロック、および解決 | Microsoft Docs
description: Service Bus メッセージの転送および解決処理の概要
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2018
ms.author: aschhab
ms.openlocfilehash: 9aaada1ede8912b8b70f37c628ec918eca9be9d2
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676265"
---
# <a name="message-transfers-locks-and-settlement"></a>メッセージの転送、ロック、および解決

Service Bus などのメッセージ ブローカーの重要な機能は、メッセージを受信し、後で取得できるようにキューまたはトピックに保存することです。 *送信*という用語は、一般的に、メッセージ ブローカーにメッセージを転送することを指します。 *受信*という用語は、一般的に、メッセージを取得しているクライアントへメッセージを転送することを指します。

クライアントがメッセージを送信する場合、通常は、メッセージが正常に転送され、ブローカーが受信したか、またはなんらかのエラーが発生したかどうかの確認が求められます。 この肯定または否定応答により、クライアントおよびブローカーは、メッセージの転送状態について理解し、これを*解決*と呼びます。

同様に、ブローカーがメッセージをクライアントに転送する場合、ブローカーとクライアントは、メッセージが正常に送信、処理され、削除できる状態か、またはメッセージの転送や処理が失敗し、メッセージを再送信する必要があるかの確認を求めます。

## <a name="settling-send-operations"></a>送信操作の解決

サポートされている Service Bus API クライアントを使用した Service Bus への送信操作では、通常、明示的に解決されます。API 操作は、Service Bus からの受信結果を受け取るまで待機してから送信操作を完了します。

Service Bus によってメッセージが拒否された場合、拒否には、"追跡 ID" が含まれたテキストとエラーのインジケーターが含まれます。 拒否には、操作が成功する可能性があり、操作を再試行するかどうかに関する情報も含まれています。 この情報はクライアント内で例外に変換され、送信操作の呼び出し元に報告されます。 メッセージが受け取られた場合、操作は自動的に完了します。

.NET 標準クライアントおよび Java クライアント用の排他的なプロトコルであり、[.NET Framework クライアントのオプション](service-bus-amqp-dotnet.md) である AMQP プロトコルを使用する場合、メッセージの転送および解決はパイプライン化され完全に非同期で実行されるため、非同期プログラミング モデル API のバリエーションを使用することをお勧めします。

送信プログラムは、SBMP プロトコルまたは HTTP 1.1 を使用した場合のように、各メッセージの確認を待機する必要がなく、複数のメッセージを短時間に連続的に送信できます。 これらの非同期送信操作は、各メッセージの受け取りや、パーティション分割されたエンティティへのメッセージの格納と同時に、または別のエンティティへの送信と重複して実行できます。 元の送信順序とは異なる順序で完了する場合もあります。

送信操作の結果をどう処理するかを決める戦略は、アプリケーションのパフォーマンスに即時の、かつ重要な影響を与える可能性があります。 このセクションの例は C# で記述されていますが、Java Futures にも同様に適用されます。

これ例では単純なループを使ってアプリケーションによって大量のメッセージが生成され、各送信操作が完了してから次のメッセージを送信しています。同期および非同期 API では、いずれも、解決するまで順次 10 回完全なラウンドトリップを行ってから 10 メッセージのみ送信されています。

あるオンプレミス サイトから Service Bus までの TCP ラウンドトリップ遅延距離を 70 ミリ秒と想定し、Service Bus が各メッセージを受け取り、保存するまで 10 ミリ秒しかかからないとしても、次のループは 8 秒以上かかり、これには、ペイロードの転送時間やルートの輻輳効果の可能性は考慮に入れられていません。

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

アプリケーションが連続的に 10 個の非同期送信操作を開始し、各送信操作の完了を個別に待機した場合、これら 10 個の操作のラウンドトリップ時間は重複します。 10 個のメッセージが連続して転送され、この場合 TCP フレームが同じである可能性もあり、転送時間は主に、ブローカーにメッセージを転送するためのネットワーク関係の時間によって決まります。

前述のループでも同様に推定すると、その次のループの重複実行時間の合計は、1 秒をはるかに下回る可能性があります。

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

すべての非同期プログラミング モデルでは、保留中の操作を保持するため、メモリ ベースの隠された作業キューが使用されることに注意してください。 [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) または **Send** (Java) が返ると、送信タスクは作業キューに入りますが、プロトコル動作は、タスクの実行順序がくるまで開始されません。 信頼性が考慮されるべき状況で、メッセージを連続送信する傾向のあるコードの場合、実際に送信されるまですべての送信メッセージがメモリを使用するため、一度に送信されるメッセージが多くなりすぎないよう注意する必要があります。

次のコード スニペットで示すように、C# のセマフォは、必要な時にアプリケーション レベルを調整できる同期オブジェクトです。 このようにセマフォを使用すると、同時に送信されるメッセージを最大 10 個に制限できます。 使用できる 10 個のセマフォ ロックの 1 つが送信前に取得され、送信が完了すると解放されます。 11 番目のセマフォは、送信前の少なくとも 1 つが完了されるまでループで待機してから、ロックを使用できるようにします。

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

アプリケーションは、操作の結果を取得しない、"送信して放置" 方法で非同期送信する設計には**絶対にしない必要があります**。 そのような設計では、内部および隠れたタスク キューがメモリを消費し、アプリケーションが送信エラーを検知できなくなります。

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

低レベルの AMQP クライアントの場合、Service Bus では "解決済み" 転送を実行できます。 解決済み転送とは、操作の結果が成功でも失敗でもクライアントに報告されず、メッセージは送信時点で解決したとみなされる、"送信して放置" 操作です。 クライアントへのフィードバックがないため、診断に使用できる行動可能なデータがなく、このモードは Azure のサポートを受ける資格がありません。

## <a name="settling-receive-operations"></a>受信操作の解決

受信操作を行う Service Bus API クライアントでは、明示的なモードが 2 つ有効になっています。*受信して削除*と*ピーク ロック*です。

### <a name="receiveanddelete"></a>ReceiveAndDelete

[受信して削除](/dotnet/api/microsoft.servicebus.messaging.receivemode)モードでは、ブローカーは、ブローカーが受信クライアントに送信するすべてのメッセージを、送信時点で解決済みとみなします。 つまり、ブローカーが送信するとただちにメッセージは消費されたとみなされます。 メッセージの転送が失敗した場合、メッセージは失われます。

このモードのメリットは、受信側メッセージでそれ以上のアクションを実行する必要はなく、解決の結果を待機する遅延も発生しないことです。 各メッセージに含まれるデータの価値が低い、またはデータが意味を持つ時間が非常に短時間の場合は、このモードは妥当な選択です。

### <a name="peeklock"></a>PeekLock

[ピーク ロック](/dotnet/api/microsoft.servicebus.messaging.receivemode)モードでは、受信クライアントが、受信したメッセージの明示的な解決を求めることを、ブローカーに指示します。 受信クライアントが処理できるよう、メッセージに排他的なロックがかけられ、他の競合受信クライアントからは認識できなくなります。 ロックの有効期間は、当初キューまたはサブスクリプション レベルで定義され、ロックを所有しているクライアントによる [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) 操作によって延長できます。

メッセージにロックがかけられると、同じキューまたはサブスクリプションから受信するクライアントがロックを受け取り、アクティブなロックがかけられているメッセージではなく、次に使用可能なメッセージを取得できます。 メッセージのロックが明示的に解放された、またはロックの有効期限が切れた場合は、メッセージは取得順序の前または近くに戻され再送信されます。

メッセージが受信クライアントによって繰り返し解放された、または定義されたロック回数 ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)) を過ぎた場合は、メッセージはキューまたはサブスクリプションから自動的に削除され、関連する配信不能キューに配置されます。

受信側のクライアントは、正の確認を受け取ると、API レベルの [Complete](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) を呼び出して、受信メッセージの解決を開始します。 これにより、メッセージが正常に処理されたことがブローカーに通知され、メッセージはキューまたはサブスクリプションから削除されます。 ブローカーは、受信側の解決要求に対し、解決が実行できるかを示す返信を返します。

受信クライアントがメッセージの処理に失敗したが、メッセージの再送を求める場合、[Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) を呼び出してメッセージの解放とロック解除を明示的に要求するか、あるいはロックの有効期限が切れるまで待つことができます。

受信クライアントがメッセージの処理に失敗して、メッセージの再送や操作を再試行しても無駄だと判断した場合は、メッセージを拒否し、[DeadLetter](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter) を呼び出すことによってメッセージを配信不能キューに移動したり、理由コードを含むカスタム プロパティを設定して、配信不能キューからメッセージを取得することもできます。

解決の特殊なケースは、別の記事で説明されている遅延です。

**Complete** または **Deadletter** 操作だけでなく **RenewLock** 操作は、ネットワークの問題や、保持されているロックの有効期限が切れる、または解決を妨げる他のサーバー側の条件が存在すると、失敗することがあります。 後者の場合、サービスは、API クライアントからは例外と認識される否定応答を送信します。 理由がネットワーク接続の切断の場合は、Service Bus は、再接続による接続の AMQP リンクの回復をサポートしていないため、ロックは削除されます。

**Complete** の失敗は、通常は、メッセージ最終の処理で、または処理作業から数分経ってから発生し、その場合、受信アプリケーションは作業の状態を保存するか、同じメッセージが 2 回送信されたときに無視するか、あるいは作業結果を破棄してメッセージが再送された場合と同様に再試行するかを決定できます。

重複するメッセージの配信を識別するための一般的な方法は、送信側によって一意の値に設定され、元の処理からの ID と紐付けられている場合もある、メッセージ ID を確認することです。 ジョブ スケジューラは、特定のワーカーに割り当てようとしているジョブ ID にメッセージ ID を設定することが多く、ワーカーは、ジョブがすでに完了している場合は、2 回目のジョブの割り当てを無視します。

> [!IMPORTANT]
> PeekLock がメッセージに対して取得するロックは揮発性であり、次のような状況では失われる可能性があることに注意してください
>   * サービスの更新
>   * OS の更新
>   * ロックを保持したままエンティティ (キュー、トピック、サブスクリプション) のプロパティを変更。
>
> ロックが失われると、Azure Service Bus によって LockLostException が生成され、クライアント アプリケーション コードに表示されます。 この場合、クライアントの既定の再試行ロジックが自動的に開始され、操作が再試行されます。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)
