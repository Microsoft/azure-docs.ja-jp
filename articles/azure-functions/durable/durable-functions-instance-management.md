---
title: Durable Functions でのインスタンスの管理 - Azure
description: Azure Functions の Durable Functions 拡張機能でインスタンスを管理する方法を説明します。
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1ab2e35c916c6bd6f2d73a328f71710378fac890
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343940"
---
# <a name="manage-instances-in-durable-functions-azure-functions"></a>Durable Functions でのインスタンスの管理 (Azure Functions)

[Durable Functions](durable-functions-overview.md) オーケストレーション インスタンスは、開始、終了、照会したり、通知イベントを送信したりできます。 インスタンス管理はすべて、[オーケストレーション クライアント バインド](durable-functions-bindings.md)を使用して行います。 この記事では、各インスタンスの管理操作について詳しく説明します。

## <a name="starting-instances"></a>インスタンスの開始

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) の [StartNewAsync] (https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) メソッド (.NET) または `DurableOrchestrationClient` の `startNew` (JavaScript) は、オーケストレーター関数の新しいインスタンスを開始します。 このクラスのインスタンスを取得するには、`orchestrationClient` バインドを使用します。 内部的には、このメソッドは、メッセージをコントロール キューにエンキューし、これにより `orchestrationTrigger` トリガー バインドを使用する、指定された名前の関数がトリガーされます。

この非同期操作は、オーケストレーション プロセスが正常にスケジュールされたときに完了します。 オーケストレーション プロセスは30 秒以内に開始する必要があります。 これよりも長くかかると `TimeoutException` がスローされます。

> [!WARNING]
> JavaScript でローカルに開発する場合は、環境変数 `WEBSITE_HOSTNAME` を `localhost:<port>` に設定する必要があります。たとえば、 `DurableOrchestrationClient` のメソッドを使用するには、`localhost:7071` に設定します。 この要件の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-js/issues/28)に関するページをご覧ください。

### <a name="net"></a>.NET

[StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) のパラメーターは次のとおりです。

* **名前**: スケジュールするオーケストレーター関数の名前。
* **入力**:オーケストレーター関数に入力として渡す必要のある JSON でシリアル化できる任意のデータ。
* **InstanceId**: (省略可能) インスタンスの一意の ID。 指定しない場合は、ランダムなインスタンス ID が生成されます。

単純な C# の例を次に示します。

```csharp
[FunctionName("HelloWorldManualStart")]
public static async Task Run(
    [ManualTrigger] string input,
    [OrchestrationClient] DurableOrchestrationClient starter,
    ILogger log)
{
    string instanceId = await starter.StartNewAsync("HelloWorld", input);
    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

`startNew` へのパラメーターは次のとおりです。

* **名前**: スケジュールするオーケストレーター関数の名前。
* **InstanceId**: (省略可能) インスタンスの一意の ID。 指定しない場合は、ランダムなインスタンス ID が生成されます。
* **入力**:(省略可能) オーケストレーター関数に入力として渡す必要のある JSON でシリアル化できる任意のデータ。

単純な JavaScript の例を次に示します。

```javascript
const df = require("durable-functions");

module.exports = async function(context, input) {
    const client = df.getClient(context);

    const instanceId = await client.startNew("HelloWorld", undefined, input);
    context.log(`Started orchestration with ID = ${instanceId}.`);
};
```

> [!NOTE]
> インスタンス ID にはランダムな識別子を使用します。 これにより、複数の VM にわたってオーケストレーター関数をスケールするとき、負荷が均等に分散されるようになります。 ランダムではないインスタンスの ID は、外部ソースから ID を取得するとき、または[シングルトン オーケストレーター](durable-functions-singletons.md) パターンを実装するときに使用します。

### <a name="using-core-tools"></a>Core Tools を使用する

[Azure Functions Core Tools](../functions-run-local.md) の `durable start-new` コマンドを使用してインスタンスを直接開始することもできます。 使用できるパラメーターは次のとおりです。

* **`function-name` (必須)**: 開始する関数の名前
* **`input` (省略可能)**: 関数への入力 (インラインまたは JSON ファイル経由のどちらか)。 ファイルの場合は、`@path/to/file.json` など、`@` を使用してファイルへのパスにプレフィックスを付けます。
* **`id` (省略可能)**: オーケストレーション インスタンスの ID。 指定しない場合は、ランダムな GUID が生成されます。
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

> [!NOTE]
> Core Tools のコマンドでは、これらが関数アプリのルート ディレクトリから実行されると仮定されます。 `connection-string-setting` と `task-hub-name`が明示的に指定されている場合、コマンドは任意のディレクトリから実行できます。 関数アプリのホストを実行しなくてもこれらのコマンドを実行できますが、ホストが実行されていないと、一部の効果が得られないことがあります。 たとえば、`start-new` コマンドではターゲットのタスク ハブに開始メッセージがエンキューされますが、メッセージを処理できる関数アプリのホスト プロセスが実行されていない限り、オーケストレーションは実際に実行されません。

次のコマンドは HelloWorld という名前の関数を開始し、'counter-data.json' ファイルの内容をその関数に渡します。

```bash
func durable start-new --function-name HelloWorld --input @counter-data.json --task-hub-name TestTaskHub
```

## <a name="querying-instances"></a>インスタンスの照会

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_) メソッド (.NET) または `DurableOrchestrationClient` クラスの `getStatus` メソッド (JavaScript) は、オーケストレーション インスタンスの状態のクエリを実行します。

これは、パラメーターとして `instanceId` (必須)、`showHistory` (省略可能)、`showHistoryOutput` (省略可能)、および `showInput` (省略可能、.NET のみ) を取ります。

* **`showHistory`**: `true` に設定されている場合は、応答に実行履歴が含まれます。
* **`showHistoryOutput`**: `true` に設定されている場合は、実行履歴にアクティビティ出力が含まれます。
* **`showInput`**: `false` に設定されている場合は、応答に関数の入力は含まれません。 既定値は `true` です。 (.NET のみ)

このメソッドは次のプロパティを持つ JSON オブジェクトを返します。

* **名前**: オーケストレーター関数の名前。
* **InstanceId**: オーケストレーションのインスタンス ID (`instanceId` 入力と同じにする必要があります)。
* **CreatedTime**: オーケストレーター関数が実行を開始した時刻。
* **LastUpdatedTime**: オーケストレーションが最後にチェックポイントされた時刻。
* **入力**:JSON 値としての関数の入力。 `showInput` が false の場合、このフィールドは設定されません。
* **CustomStatus**: JSON 形式でのカスタム オーケストレーションの状態。
* **出力**:JSON 値としての関数の出力 (関数が完了している場合)。 オーケストレーター関数が失敗した場合、このプロパティには、エラーの詳細が含まれます。 オーケストレーター関数が終了した場合、このプロパティには、提供されている終了の理由が含まれます (存在する場合)。
* **RuntimeStatus**: 次のいずれかの値です。
  * **Pending**: インスタンスはスケジュールされたが、まだ実行を開始していません。
  * **Running**: インスタンスが実行を開始しました。
  * **Completed**: インスタンスが正常に完了しました。
  * **ContinuedAsNew**: インスタンスが新しい履歴で自身を再開しました。 これは遷移状態です。
  * **Failed**: インスタンスがエラーで失敗しました。
  * **Terminated**: インスタンスが突然停止されました。
* **履歴**: オーケストレーションの実行履歴。 このフィールドにデータが設定されるのは、`showHistory` を `true` に設定した場合のみです。

インスタンスが存在しないか、まだ開始されていない場合、このメソッドは `null` を返します。

### <a name="c"></a>C#

```csharp
[FunctionName("GetStatus")]
public static async Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    var status = await client.GetStatusAsync(instanceId);
    // do something based on the current status.
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const status = await client.getStatus(instanceId);
    // do something based on the current status.
}
```

### <a name="using-core-tools"></a>Core Tools を使用する

[Azure Functions Core Tools](../functions-run-local.md) の `durable get-runtime-status` コマンドを使用してオーケストレーション インスタンスの状態を直接取得することもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)**: オーケストレーション インスタンスの ID
* **`show-input` (省略可能)**: `true` に設定されている場合は、応答に関数の入力が含まれます。 既定値は `false` です。
* **`show-output` (省略可能)**: `true` に設定されている場合は、応答に関数の出力が含まれます。 既定値は `false` です。
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

次のコマンドでは、0ab8c55a66644d68a3a8b220b12d209c というオーケストレーション インスタンス ID を持つインスタンスの状態 (入力と出力を含む) を取得します。 `func` コマンドが関数アプリのルート ディレクトリから実行されていることが想定されます。

```bash
func durable get-runtime-status --id 0ab8c55a66644d68a3a8b220b12d209c --show-input true --show-output true
```

`durable get-history` コマンドを使用して、オーケストレーション インスタンスの履歴を取得できます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)**: オーケストレーション インスタンスの ID
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 durableTask:HubName を使用して host.json 内で設定することもできます。

```bash
func durable get-history --id 0ab8c55a66644d68a3a8b220b12d209c
```

## <a name="querying-all-instances"></a>すべてのインスタンスのクエリを実行する

`GetStatusAsync` (.NET) または `getStatusAll` (JavaScript) メソッドを使用して、すべてのオーケストレーション インスタンスの状態のクエリを実行できます。 .NET では、それを取り消したい場合、`CancellationToken` オブジェクトを渡すことができます。 このメソッドは、`GetStatusAsync` メソッドと同じプロパティを含むオブジェクトをパラメーターと共に返します。

### <a name="c"></a>C#

```csharp
[FunctionName("GetAllStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instances = await client.getStatusAll();
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-core-tools"></a>Core Tools を使用する

[Azure Functions Core Tools](../functions-run-local.md) の `durable get-instances` コマンドを使用してインスタンスのクエリを直接実行することもできます。 使用できるパラメーターは次のとおりです。

* **`top` (省略可能)**: このコマンドは、ページングをサポートします。 このパラメーターは、要求ごとに取得されるインスタンス数に対応します。 既定値は 10 です。
* **`continuation-token` (省略可能)**: 取得するインスタンスのページ/セクションを示すトークン。 `get-instances` を実行するたびに、次の一連のインスタンスにトークンが返されます。
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

```bash
func durable get-instances
```

## <a name="querying-instances-with-filters"></a>フィルターを使用したインスタンスのクエリ

`GetStatusAsync` (.NET) または `getStatusBy` (JavaScript) メソッドを使用して、一連の定義済みのフィルターに一致するオーケストレーション インスタンスの一覧を取得することもできます。 使用可能なフィルター オプションには、オーケストレーションの作成時刻と、オーケストレーションのランタイム状態が含まれます。

### <a name="c"></a>C#

```csharp
[FunctionName("QueryStatus")]
public static async Task Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")]HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger log)
{
    IEnumerable<OrchestrationRuntimeStatus> runtimeStatus = new List<OrchestrationRuntimeStatus> {
        OrchestrationRuntimeStatus.Completed,
        OrchestrationRuntimeStatus.Running
    };
    IList<DurableOrchestrationStatus> instances = await starter.GetStatusAsync(
        new DateTime(2018, 3, 10, 10, 1, 0),
        new DateTime(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    ); // You can pass CancellationToken as a parameter.
    foreach (var instance in instances)
    {
        log.LogInformation(JsonConvert.SerializeObject(instance));
    };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const runtimeStatus = [
        df.OrchestrationRuntimeStatus.Completed,
        df.OrchestrationRuntimeStatus.Running,
    ];
    const instances = await client.getStatusBy(
        new Date(2018, 3, 10, 10, 1, 0),
        new Date(2018, 3, 10, 10, 23, 59),
        runtimeStatus
    );
    instances.forEach((instance) => {
        context.log(JSON.stringify(instance));
    });
};
```

### <a name="using-the-functions-core-tools"></a>Functions Core Tools を使用する

`durable get-instances` コマンドは、フィルターと共に使用することもできます。 前述の `top`、`continuation-token`、`connection-string-setting`、`task-hub-name` パラメーターに加えて、3 つのフィルター パラメーター (`created-after`、`created-before`、`runtime-status`) を使用できます。

* **`created-after` (省略可能)**: この日付/時刻 (UTC) の後に作成されたインスタンスを取得します。 ISO 8601 形式の日時が受け入れられます。
* **`created-before` (省略可能)**: この日付/時刻 (UTC) の前に作成されたインスタンスを取得します。 ISO 8601 形式の日時が受け入れられます。
* **`runtime-status` (省略可能)**: 状態がこれら ('実行中'、'完了' など) に一致するインスタンスを取得します。 複数の (スペースで区切られた) 状態を指定できます。
* **`top` (省略可能)**: 要求ごとに取得されるインスタンスの数。 既定値は 10 です。
* **`continuation-token` (省略可能)**: 取得するインスタンスのページ/セクションを示すトークン。 `get-instances` を実行するたびに、次の一連のインスタンスにトークンが返されます。
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

フィルター (`created-after`、`created-before`、`runtime-status` のいずれか) が指定されていない場合、`top` インスタンスは、ランタイムの状態や作成時刻に関係なく取得されます。

```bash
func durable get-instances --created-after 2018-03-10T13:57:31Z --created-before  2018-03-10T23:59Z --top 15
```

## <a name="terminating-instances"></a>インスタンスの終了

実行中のオーケストレーション インスタンスは、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) メソッド (.NET) または `DurableOrchestrationClient` クラスの `terminate` メソッド (JavaScript) を使用して終了できます。 2 つのパラメーター `instanceId` と `reason` 文字列は、ログとインスタンス状態に書き込まれます。 終了されたインスタンスは、次の `await` (.NET) または `yield` (JavaScript) ポイントに到達するとすぐに実行を停止するか、あるいは既に `await` (.NET) または `yield` (JavaScript) 上にある場合は直ちに終了します。

### <a name="c"></a>C#

```csharp
[FunctionName("TerminateInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "It was time to be done.";
    return client.TerminateAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "It was time to be done.";
    return client.terminate(instanceId, reason);
};
```

> [!NOTE]
> インスタンスの終了は、現在、伝達されません。 アクティビティ関数およびサブオーケストレーションは、これらを呼び出したオーケストレーション インスタンスが終了しているかどうかに関係なく、完了するまで実行されます。

### <a name="using-core-tools"></a>Core Tools を使用する

[Core Tools](../functions-run-local.md) の `durable terminate` コマンドを使用してオーケストレーション インスタンスを直接終了することもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)**: 終了するオーケストレーション インスタンスの ID
* **`reason` (省略可能)**: 終了の理由
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

次のコマンドでは、ID が 0ab8c55a66644d68a3a8b220b12d209c のオーケストレーション インスタンスを終了します。

```bash
func durable terminate --id 0ab8c55a66644d68a3a8b220b12d209c --reason "It was time to be done."
```

## <a name="sending-events-to-instances"></a>インスタンスへのイベントの送信

イベント通知は、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) メソッド (.NET) または `DurableOrchestrationClient` クラスの `raiseEvent` メソッド (JavaScript) を使用して、実行中のインスタンスに送信できます。 これらのイベントを処理できるインスタンスは、[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.NET) または `waitForExternalEvent` (JavaScript) への呼び出しを待っているインスタンスです。

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.NET) および `raiseEvent` (JavaScript) へのパラメーターは次のとおりです。

* **InstanceId**: インスタンスの一意の ID。
* **EventName**: 送信するイベントの名前。
* **EventData**: インスタンスに送信する JSON でシリアル化できるペイロード。

### <a name="c"></a>C#

```csharp
[FunctionName("RaiseEvent")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    int[] eventData = new int[] { 1, 2, 3 };
    return client.RaiseEventAsync(instanceId, "MyEvent", eventData);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const eventData = [ 1, 2, 3 ];
    return client.raiseEvent(instanceId, "MyEvent", eventData);
};
```

> [!WARNING]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、または指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージは破棄されます。 この動作の詳細については、[GitHub の問題](https://github.com/Azure/azure-functions-durable-extension/issues/29)に関するトピックをご覧ください。

### <a name="using-core-tools"></a>Core Tools を使用する

[Core Tools](../functions-run-local.md) の `durable raise-event` コマンドを使用してオーケストレーション インスタンスにイベントを直接発生させることもできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)**: オーケストレーション インスタンスの ID
* **`event-name` (省略可能)**: 発生させるイベントの名前。 既定値は `$"Event_{RandomGUID}"` です
* **`event-data` (省略可能)**: オーケストレーション インスタンスに送信するデータ。 これは JSON ファイルへのパスにするか、コマンド ラインでデータを直接指定することができます
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

```bash
func durable raise-event --id 0ab8c55a66644d68a3a8b220b12d209c --event-name MyEvent --event-data @eventdata.json
```

```bash
func durable raise-event --id 1234567 --event-name MyOtherEvent --event-data 3
```

## <a name="wait-for-orchestration-completion"></a>オーケストレーションの完了の待機

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスは、オーケストレーション インスタンスからの実際の出力を同期的に取得するために使用できる .NET の [WaitForCompletionOrCreateCheckStatusResponseAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_WaitForCompletionOrCreateCheckStatusResponseAsync_) API を公開します。 JavaScript では、`DurableOrchestrationClient` クラスが同じ目的のために `waitForCompletionOrCreateCheckStatusResponse` API を公開します。 これらのメソッドは `timeout` に 10 秒、および `retryInterval` に 1 秒の既定値を使用します (これらが設定されていない場合)。  

この API の使用方法を示す HTTP トリガー関数の例を次に示します。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpSyncStart.cs)]

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpSyncStart/index.js)]

この関数は、次のように 2 秒のタイムアウトと 0.5 秒の再試行間隔を使用して呼び出すことができます。

```bash
    http POST http://localhost:7071/orchestrators/E1_HelloSequence/wait?timeout=2&retryInterval=0.5
```

オーケストレーション インスタンスからの応答を取得するために必要な時間に応じて 2 つのケースがあります。

* オーケストレーション インスタンスが、定義されたタイムアウト (このケースでは 2 秒) 内に完了すると、応答は、同期して提供される実際のオーケストレーション インスタンス出力です。

    ```http
        HTTP/1.1 200 OK
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:14:29 GMT
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ]
    ```

* オーケストレーション インスタンスが、定義されたタイムアウト (このケースでは 2 秒) 内に完了できない場合、応答は「**HTTP API URL の検出**」で説明されている既定のものになります。

    ```http
        HTTP/1.1 202 Accepted
        Content-Type: application/json; charset=utf-8
        Date: Thu, 14 Dec 2017 06:13:51 GMT
        Location: http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}
        Retry-After: 10
        Server: Microsoft-HTTPAPI/2.0
        Transfer-Encoding: chunked

        {
            "id": "d3b72dddefce4e758d92f4d411567177",
            "sendEventPostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/raiseEvent/{eventName}?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "statusQueryGetUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177?taskHub={taskHub}&connection={connection}&code={systemKey}",
            "terminatePostUri": "http://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/terminate?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}",
            "rewindPostUri": "https://localhost:7071/admin/extensions/DurableTaskExtension/instances/d3b72dddefce4e758d92f4d411567177/rewind?reason={text}&taskHub={taskHub}&connection={connection}&code={systemKey}"
        }
    ```

> [!NOTE]
> webhook URL の形式は、実行している Azure Functions ホストのバージョンによって異なる場合があります。 前の例は、Azure Functions 2.x ホストに対するものです。

## <a name="retrieving-http-management-webhook-urls"></a>HTTP 管理 webhook URL の取得

外部システムは、[HTTP API URL の検出](durable-functions-http-api.md)に関するページで説明されている既定の応答の一部である webhook URL を介して、Durable Functions と通信できます。 ただし、webhook URL には、[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) クラスの [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) メソッド (.NET) または `DurableOrchestrationClient` クラスの `createHttpManagementPayload` メソッド (JavaScript) を経由して、オーケストレーション クライアントまたはアクティビティ関数でプログラムでアクセスすることもできます。

[CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_) と `createHttpManagementPayload` には、次の 1 つのパラメーターがあります。

* **instanceId**: インスタンスの一意の ID。

これらのメソッドは、[HttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.HttpManagementPayload.html#Microsoft_Azure_WebJobs_Extensions_DurableTask_HttpManagementPayload_) のインスタンス (.NET) またはオブジェクト (JavaScript) を次の文字列プロパティと共に返します。

* **Id**: オーケストレーションのインスタンス ID (`InstanceId` 入力と同じにする必要があります)。
* **StatusQueryGetUri**: オーケストレーション インスタンスの状態の URL。
* **SendEventPostUri**: オーケストレーション インスタンスの "イベント発生" URL。
* **TerminatePostUri**: オーケストレーション インスタンスの "終了" URL。
* **RewindPostUri**: オーケストレーション インスタンスの "rewind" URL。

アクティビティ関数は、オーケストレーションへのイベントを監視したり、発生させたりするために、これらのオブジェクトのインスタンスを外部システムに送信できます。

### <a name="c"></a>C#

```csharp
[FunctionName("SendInstanceInfo")]
public static void SendInstanceInfo(
    [ActivityTrigger] DurableActivityContext ctx,
    [OrchestrationClient] DurableOrchestrationClient client,
    [DocumentDB(
        databaseName: "MonitorDB",
        collectionName: "HttpManagementPayloads",
        ConnectionStringSetting = "CosmosDBConnection")]out dynamic document)
{
    HttpManagementPayload payload = client.CreateHttpManagementPayload(ctx.InstanceId);

    // send the payload to Cosmos DB
    document = new { Payload = payload, id = ctx.InstanceId };
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, ctx) {
    const client = df.getClient(context);

    const payload = client.createHttpManagementPayload(ctx.instanceId);

    // send the payload to Cosmos DB
    context.bindings.document = JSON.stringify({
        id: ctx.instanceId,
        payload,
    });
};
```

## <a name="rewinding-instances-preview"></a>インスタンスの巻き戻し (プレビュー)

失敗したオーケストレーション インスタンスは、[RewindAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RewindAsync_System_String_System_String_) (.NET) または `rewindAsync` (JavaScript) API を使用して、以前の正常な状態に*巻き戻す*ことができます。 これは、オーケストレーションを "*実行中*" 状態に戻し、オーケストレーション エラーの原因となったアクティビティやサブオーケストレーションの実行失敗を再実行することで機能します。

> [!NOTE]
> この API は、適切なエラー処理や再試行ポリシーの代わりとなるものではありません。 予期しない理由でオーケストレーション インスタンスが失敗する場合にのみ使用するものです。 エラー処理と再試行ポリシーについて詳しくは、[エラー処理](durable-functions-error-handling.md)に関するトピックをご覧ください。

"*巻き戻し*" のユース ケースの一例として、一連の[人による承認](durable-functions-overview.md#pattern-5-human-interaction)があります。 承認が必要であることをユーザーに通知し、リアルタイムの応答を待機する一連のアクティビティ関数があるとします。 すべての承認アクティビティが応答を受信するかタイムアウトになった後、アプリケーションの構成ミス (無効なデータベース接続文字列など) により別のアクティビティが失敗します。 結果として、ワークフローの深い部分でオーケストレーションが失敗します。 `RewindAsync` (.NET) または `rewindAsync` (JavaScript) API を使用すると、アプリケーション管理者は構成エラーを修正し、失敗したオーケストレーションを失敗の直前の状態に*巻き戻す*ことができます。 人間の対話手順はいずれも再承認が不要で、オーケストレーションは正常に完了できるようになります。

> [!NOTE]
> "*巻き戻し*" 機能では、永続タイマーを使用したオーケストレーション インスタンスの巻き戻しはサポートされません。

### <a name="c"></a>C#

```csharp
[FunctionName("RewindInstance")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    string reason = "Orchestrator failed and needs to be revived.";
    return client.RewindAsync(instanceId, reason);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (Functions 2.x のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);

    const reason = "Orchestrator failed and needs to be revived.";
    return client.rewind(instanceId, reason);
};
```

### <a name="using-core-tools"></a>Core Tools を使用する

[Core Tools](../functions-run-local.md) の `durable rewind` コマンドを使用してオーケストレーション インスタンスを直接巻き戻すこともできます。 使用できるパラメーターは次のとおりです。

* **`id` (必須)**: オーケストレーション インスタンスの ID
* **`reason` (省略可能)**: オーケストレーション インスタンスを巻き戻す理由
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

```bash
func durable rewind --id 0ab8c55a66644d68a3a8b220b12d209c --reason "Orchestrator failed and needs to be revived."
```

## <a name="purge-instance-history"></a>インスタンスの履歴を消去する

> [!NOTE]
> `PurgeInstanceHistoryAsync` API は現在、C# でのみ使用できます。 これは、今後のリリースで JavaScript に追加される予定です。

オーケストレーションの履歴は、[PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_) を使用して消去できます。 この機能により、オーケストレーションに関連付けられているすべてのデータ (Azure Table の行とサイズの大きいメッセージ BLOB (存在する場合)) が削除されます。 このメソッドには 2 つのオーバーロードがあります。 1 つ目は、オーケストレーション インスタンスの ID によって履歴を消去します。

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [ManualTrigger] string instanceId)
{
    return client.PurgeInstanceHistoryAsync(instanceId);
}
```

2 番目の例では、指定した時間間隔後に完了したすべてのオーケストレーション インスタンスの履歴を消去する、タイマーによってトリガーされる関数を示します。 この場合は、30 日以上前に完了したすべてのインスタンスのデータが削除されます。 これは、1 日 1 回、午前 12 時に実行するようにスケジュールされています。

```csharp
[FunctionName("PurgeInstanceHistory")]
public static Task Run(
    [OrchestrationClient] DurableOrchestrationClient client,
    [TimerTrigger("0 0 12 * * *")]TimerInfo myTimer)
{
    return client.PurgeInstanceHistoryAsync(
                    DateTime.MinValue,
                    DateTime.UtcNow.AddDays(-30),  
                    new List<OrchestrationStatus>
                    {
                        OrchestrationStatus.Completed
                    });
}
```

> [!NOTE]
> パラメーターとして期間を受け入れる *PurgeInstanceHistory* オーバーロードでは、ランタイムの状態が完了、終了、失敗のいずれかであるオーケストレーション インスタンスだけが処理されます。

### <a name="using-core-tools"></a>Core Tools を使用する

[Core Tools](../functions-run-local.md) の `durable purge-history` コマンドを使用して、オーケストレーション インスタンスの履歴を消去することができます。 上記の 2 つ目の C# の例と同様に、指定した時間間隔中に作成されたすべてのオーケストレーション インスタンスの履歴が消去されます。 消去されるインスタンスをランタイムの状態でさらにフィルター処理できます。 コマンドにはいくつかのパラメーターがあります。

* **`created-after` (省略可能)**: この日付/時刻 (UTC) の後に作成されたインスタンスの履歴を消去します。 ISO 8601 形式の日時が受け入れられます。
* **`created-before` (省略可能)**: この日付/時刻 (UTC) の前に作成されたインスタンスの履歴を消去します。 ISO 8601 形式の日時が受け入れられます。
* **`runtime-status` (省略可能)**: 状態がこれら ('実行中'、'完了' など) に一致するインスタンスの履歴を消去します。 複数の (スペースで区切られた) 状態を指定できます。
* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

次のコマンドでは、2018 年 11 月 14 日午後 7 時 35 分 (UTC) より前に作成されたすべての失敗したインスタンスの履歴が削除されます。

```bash
func durable purge-history --created-before 2018-11-14T19:35:00.0000000Z --runtime-status failed
```

## <a name="deleting-a-task-hub"></a>タスク ハブを削除する

[Core Tools](../functions-run-local.md) の `durable delete-task-hub` コマンドを使用して、特定のタスク ハブに関連付けられているすべてのストレージ成果物を削除できます。 これには、Azure ストレージのテーブル、キュー、BLOB が含まれます。 コマンドには 2 つのパラメーターがあります。

* **`connection-string-setting` (省略可能)**: 使用するストレージ接続文字列を含むアプリケーション設定の名前。 既定では、AzureWebJobsStorage です。
* **`task-hub-name` (省略可能)**: 使用する持続的なタスク ハブの名前。 既定では、DurableFunctionsHub です。 これは、durableTask:HubName を使用して [host.json](durable-functions-bindings.md#host-json) 内で設定することもできます。

次のコマンドでは、'UserTest' タスク ハブに関連付けられている Azure ストレージ データがすべて削除されます。

```bash
func durable delete-task-hub --task-hub-name UserTest
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [インスタンス管理に HTTP API を使用する方法を確認する](durable-functions-http-api.md)
