---
title: Azure Service Bus のメッセージング - キュー、トピック、およびサブスクリプション
description: この記事では、Azure Service Bus メッセージング エンティティ (キュー、トピック、サブスクリプション) の概要について説明します。
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 54b6a1fd2d4e8e5ef5bb6522374646257213e4b4
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791602"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus のキュー、トピック、サブスクリプション
Azure Service Bus は、信頼性の高いメッセージ キュー機能や永続的なパブリッシュ/サブスクライブ メッセージング機能など、クラウドベースのメッセージ指向ミドルウェアの一連のテクノロジをサポートしています。 これらのブローカー メッセージング機能は、分離されたメッセージング機能と考えることができ、Service Bus メッセージング ワークロードを使用するパブリッシュ/サブスクライブ、一時的な切り離し、負荷分散のシナリオをサポートしています。 分離型通信には多くの利点があります。 たとえば、クライアントとサーバーを必要に応じて接続し、非同期に操作を実行できます。

Service Bus のメッセージング機能の中核を形成するメッセージング エンティティは、**キュー**、**トピックおよびサブスクリプション**、およびルール/アクションです。

## <a name="queues"></a>キュー
キューでは、コンシューマーが競合している場合のメッセージ配信に **先入れ先出し法 (FIFO)** を使用します。 つまり、受信者は、通常はキューに追加された順序でメッセージを受信して処理します。 そして、1 つのメッセージ コンシューマーだけが、各メッセージを受信して処理します。 キューを使用する主な利点は、**アプリケーション コンポーネントの一時的な切り離し** を達成することです。 言い換えると、プロデューサー (送信者) とコンシューマー (受信者) は、同時にメッセージを送受信する必要がありません。 これは、メッセージが永続的にキューに格納されているためです。 さらに、プロデューサーは、メッセージの処理と送信を続ける場合、コンシューマーからの応答を待つ必要がありません。

関連する利点として **負荷平準化** があります。これにより、プロデューサーとコンシューマーは異なるレートでメッセージを送受信できます。 多くのアプリケーションでは、システム負荷は時間の経過とともに変化します。 ただし、各作業単位に必要な処理時間は通常一定です。 メッセージ プロデューサーとコンシューマーの間をキューで仲介することは、コンシューマー側アプリケーションはピーク時ではなく平均時の負荷を処理できるだけでよいということを意味します。 キューの深さは、受信の負荷の変化に応じて増減します。 この機能は、アプリケーション負荷への対応に必要なインフラストラクチャに関する費用を直接節約できます。 負荷の増大に合わせて、キューからの読み取りのためにワーカー プロセスを追加できます。 各メッセージは、ワーカー プロセスの中の 1 つのプロセスによって処理されます。 さらに、このプルベースの負荷分散では、処理能力を持つ各ワーカー コンピューターがそれら独自の最大レートでメッセージをプルする場合でも、ワーカー コンピューターを最適に使用できます。 このパターンは、しばしば **競合コンシューマー** のパターンと呼ばれます。

キューを使用してメッセージ プロデューサーとメッセージ コンシューマーの間を仲介すると、必然的にコンポーネント間の結び付きは緩くなります。 プロデューサーとコンシューマーは相互に認識しないため、プロデューサーに影響することなく、コンシューマーをアップグレードできます。

### <a name="create-queues"></a>キューの作成
キューは、[Azure portal](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-quickstart-cli.md)、または [Resource Manager テンプレート](service-bus-resource-manager-namespace-queue.md)を使用して作成できます。 次に、[C#](service-bus-dotnet-get-started-with-queues.md)、[Java](service-bus-java-how-to-use-queues.md)、[Python](service-bus-python-how-to-use-queues.md)、[JavaScript](service-bus-nodejs-how-to-use-queues.md)、[PHP](service-bus-php-how-to-use-queues.md)、および [Ruby](service-bus-ruby-how-to-use-queues.md) で記述されたクライアントを使用して、メッセージを送受信します。 

### <a name="receive-modes"></a>受信モード
Service Bus がメッセージを受信する 2 つの異なるモードを指定できます。**ReceiveAndDelete** または **PeekLock** です。 [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでは、Service Bus はコンシューマーからの要求を受信すると、メッセージを読み取り中としてマークしてコンシューマー アプリケーションに返します。 このモードは最もシンプルなモデルです。 これは障害発生時にアプリケーション側でメッセージを処理しないことを許容できるシナリオに最適です。 このシナリオを理解するために、コンシューマーが受信要求を発行した後で、メッセージを処理する前にクラッシュしたというシナリオを考えてみましょう。 Service Bus がメッセージを読み取り中としてマークすると、アプリケーションは再起動時にメッセージの消費を開始します。 クラッシュ前に読み取られたメッセージは見逃してしまいます。

[PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) モードでは、受信処理は 2 段階になります。これにより、メッセージが失われることを許容できないアプリケーションに対応することができます。 Service Bus が要求を受信すると、次の操作が実行されます。

1. 読み取る次のメッセージを検索します。
1. 他のコンシューマーが受信できないようにロックします。
1. 次に、メッセージをアプリケーションに返します。 

アプリケーションは、メッセージの処理を終了するか、後で処理するために確実に保管すると、メッセージに対して [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) を呼び出すことにより、受信プロセスの第 2 ステージを完了します。 Service Bus は、**CompleteAsync** 要求を受信すると、メッセージを読み取り済みとしてマークします。

アプリケーションは、なんらかの理由でメッセージを処理できない場合に、メッセージに対して ([`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) の代わりに) [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) メソッドを呼び出すことができます。 このメソッドが呼び出されると、Service Bus によってメッセージのロックが解除され、同じコンシューマーまたは競合する別のコンシューマーが再度そのメッセージを受信できるようになります。 第 2 に、ロックに関連付けられたタイムアウトがあります。 ロックがタイムアウトになる前にアプリケーションがメッセージの処理に失敗した場合には、Service Bus によってメッセージのロックが解除され、再度受信できる状態になります。

メッセージが処理された後、[`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) を呼び出す前にアプリケーションがクラッシュした場合、アプリケーションが再起動する際に Service Bus によってメッセージが再配信されます。 一般に、この処理は **1 回以上** の処理と呼ばれます。 つまり、各メッセージは 1 回以上処理されます。 特定の状況では、同じメッセージが再配信される可能性があります。 重複した処理を許されないシナリオの場合は、重複を検出する追加ロジックをアプリケーションに追加します。 これはメッセージの [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) プロパティを使用して対処します。このプロパティは配信が試行された後も同じ値を保持します。 この機能は **厳密に 1 回** の処理と呼ばれます。

## <a name="topics-and-subscriptions"></a>トピックとサブスクリプション
キューでは、1 つのコンシューマーが 1 つのメッセージを処理できます。 キューとは対照的に、トピックとサブスクリプションは、"**発行とサブスクライブ**" のパターンで一対多の形式の通信を実現します。 これは、多数の受信者にスケーリングする場合に便利です。 発行された各メッセージは、トピックに登録している各サブスクリプションで使用できるようになります。 パブリッシャーがトピックにメッセージを送信すると、1 つ以上のサブスクライバーが、これらのサブスクリプションに設定されているフィルター規則に応じて、メッセージのコピーを受け取ります。 サブスクリプションでは、追加のフィルターを使用して、受信するメッセージを限定できます。 パブリッシャーは、メッセージをキューに送信するのと同じ方法でトピックにメッセージを送信します。 ただし、コンシューマーはトピックから直接メッセージを受信しません。 代わりに、コンシューマーはトピックのサブスクリプションからメッセージを受信します。 トピック サブスクリプションは、トピックに送信されたメッセージのコピーを受け取る仮想キューのようなものです。 コンシューマーは、キューからメッセージを受信する場合と同じ方法で、サブスクリプションからメッセージを受信します。

キューのメッセージ送信機能はそのままトピックに相当し、メッセージ受信機能はサブスクリプションに相当します。 特に、この機能は、サブスクリプションでは、競合コンシューマー、一時的な切り離し、負荷平滑化、負荷分散など、キューに関してこのセクションで既に説明したのと同じパターンがサポートされることを意味します。

### <a name="create-topics-and-subscriptions"></a>トピックとサブスクリプションを作成する
トピックの作成は、前のセクションで説明したキューの作成と似ています。 トピックとサブスクリプションは、[Azure portal](service-bus-quickstart-topics-subscriptions-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-tutorial-topics-subscriptions-cli.md)、または [Resource Manager テンプレート](service-bus-resource-manager-namespace-topic.md)を使用して作成できます。 次に、[C#](service-bus-dotnet-how-to-use-topics-subscriptions.md)、[Java](service-bus-java-how-to-use-topics-subscriptions.md)、[Python](service-bus-python-how-to-use-topics-subscriptions.md)、[JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)、[PHP](service-bus-php-how-to-use-topics-subscriptions.md)、および [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md) で記述されたクライアントを使用して、メッセージをトピックに送信し、メッセージをサブスクリプションから受信します。 

### <a name="rules-and-actions"></a>ルールとアクション
多くのシナリオでは、特性のあるメッセージは、異なる方法で処理する必要があります。 この処理を実現するために、目的のプロパティを持つメッセージを検索し、該当するプロパティに特定の変更を行うようにサブスクリプションを構成できます。 Service Bus のサブスクリプションには、トピックに送信されたすべてのメッセージが表示されますが、仮想サブスクリプション キューにコピーできるのは、これらのメッセージの一部のみです。 このフィルター処理を行うには、サブスクリプション フィルターを使用します。 このような変更は、"**フィルター アクション**" と呼ばれます。 サブスクリプションの作成時に、メッセージのプロパティを操作するフィルター式を指定できます。 プロパティには、システム プロパティ (**Label** など) とカスタム アプリケーション プロパティ (**StoreName** など) の両方を指定できます。この場合、SQL フィルター式は省略可能です。 SQL フィルター式を指定しない場合は、サブスクリプションで定義されている任意のフィルター アクションが、そのサブスクリプションのすべてのメッセージに対して実行されます。

完全な作業用サンプルについては、GitHub の [TopicSubscriptionWithRuleOperationsSample サンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)を参照してください。

使用可能なフィルター値の詳細については、[SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) クラスと [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) クラスのドキュメントを参照してください。

## <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2.0 のエンティティ (プレビュー)
次のエンティティには、Java Message Service (JMS) 2.0 API を通じてアクセスできます。

  * 一時キュー
  * 一時トピック
  * 共有の永続的サブスクリプション
  * 非共有の永続的サブスクリプション
  * 共有の非永続的サブスクリプション
  * 非共有の非永続的サブスクリプション

[JMS 2.0 エンティティ](java-message-service-20-entities.md)についてと、[それらを使用する](how-to-use-java-message-service-20.md)方法について、さらに学習してください。

## <a name="next-steps"></a>次のステップ

Service Bus のメッセージングの詳細と使用例については、次の詳細トピックをご覧ください。

* [Service Bus メッセージングの概要](service-bus-messaging-overview.md)
* [クイック スタート: Microsoft Azure portal と .NET を使用してメッセージを送受信する](service-bus-quickstart-portal.md)
* [チュートリアル:Microsoft Azure portal とトピック/サブスクリプションを使用して在庫を更新する](service-bus-tutorial-topics-subscriptions-portal.md)


