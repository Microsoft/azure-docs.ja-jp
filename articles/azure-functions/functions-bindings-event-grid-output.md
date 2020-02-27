---
title: Azure Functions の Azure Event Grid 出力バインディング
description: Azure Functions で Event Grid イベントを送信する方法について説明します。
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368928"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure Functions の Azure Event Grid 出力バインディング

イベントをカスタム トピックに書き込むには、Event Grid 出力バインディングを使用します。 有効な[カスタム トピックのアクセス キー](../event-grid/security-authentication.md#custom-topic-publishing)が必要です。

セットアップと構成の詳細については、[概要](./functions-bindings-event-grid.md)を参照してください。

> [!NOTE]
> Event Grid 出力バインディングでは、Shared Access Signature (SAS トークン) はサポートされていません。 トピックのアクセス キーを使用する必要があります。

> [!IMPORTANT]
> Event Grid 出力バインディングは、Functions 2.x 以降でのみ使用することができます。

## <a name="example"></a>例

# <a name="c"></a>[C#](#tab/csharp)

次の例は、メソッドの戻り値を出力として使用してメッセージを Event Grid のカスタム トピックに書き込む [C# 関数](functions-dotnet-class-library.md)を示しています。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

次の例では、`IAsyncCollector` インターフェイスを使用してメッセージのバッチを送信する方法を示します。

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

1 つのイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

複数のイベントを作成する C# スクリプト コードを次に示します。

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次の例は、*function.json* ファイル内の Event Grid 出力バインディング データを示しています。

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

単一のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

複数のイベントを作成する JavaScript コードを次に示します。

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

Event Grid 出力バインディングは、Python では使用できません。

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

---

## <a name="attributes-and-annotations"></a>属性と注釈

# <a name="c"></a>[C#](#tab/csharp)

[C# クラス ライブラリ](functions-dotnet-class-library.md)では、[EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 属性を使用します。

属性のコンストラクターは、カスタム トピックの名前を含むアプリ設定の名前と、トピック キーを含むアプリ設定の名前を受け取ります。 これらの設定の詳細については、「[出力 - 構成](#configuration)」を参照してください。 `EventGrid` 属性の例を次に示します。

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

完全な例については、「[例](#example)」を参照してください。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

属性は、C# スクリプトではサポートされていません。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

属性は、JavaScript ではサポートされていません。

# <a name="python"></a>[Python](#tab/python)

Event Grid 出力バインディングは、Python では使用できません。

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

---

## <a name="configuration"></a>構成

次の表は、*function.json* ファイルと `EventGrid` 属性で設定したバインド構成のプロパティを説明しています。

|function.json のプロパティ | 属性のプロパティ |説明|
|---------|---------|----------------------|
|**type** | 300 | "eventGrid" に設定する必要があります。 |
|**direction** | 300 | "out" に設定する必要があります。 このパラメーターは、Azure Portal でバインドを作成するときに自動で設定されます。 |
|**name** | 300 | イベントを表す関数コードに使用される変数の名前。 |
|**topicEndpointUri** |**TopicEndpointUri** | カスタム トピックの URI を含むアプリ設定の名前 (例: `MyTopicEndpointUri`)。 |
|**topicKeySetting** |**TopicKeySetting** | カスタム トピックのアクセス キーを含むアプリ設定の名前。 |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 構成プロパティの値は、確実に、カスタム トピックの URI を含むアプリ設定の名前に設定してください。 このプロパティにカスタム トピックの URI を直接指定しないでください。

## <a name="usage"></a>使用法

# <a name="c"></a>[C#](#tab/csharp)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

# <a name="c-script"></a>[C# スクリプト](#tab/csharp-script)

`out EventGridEvent paramName` などのメソッド パラメーターを使用してメッセージを送信します。 C# スクリプトでは、`paramName` は *function.json* の `name` プロパティで指定された値です。 複数のメッセージを書き込むには、`out EventGridEvent` の代わりに `ICollector<EventGridEvent>` または `IAsyncCollector<EventGridEvent>` を使用します。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>` を使用して出力イベントにアクセスします。`<name>` は *function.json* の `name` プロパティで指定された値です。

# <a name="python"></a>[Python](#tab/python)

Event Grid 出力バインディングは、Python では使用できません。

# <a name="java"></a>[Java](#tab/java)

Event Grid 出力バインディングは、Java では使用できません。

---

## <a name="next-steps"></a>次のステップ

* [Event Grid イベントをディスパッチする](./functions-bindings-event-grid-trigger.md)
