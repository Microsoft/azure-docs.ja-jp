---
title: Azure SignalR Service のアップストリーム設定
description: アップストリーム メッセージのアップストリーム設定とプロトコルの概要について説明します。
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: be7736d0c90d1c384e15e8c7dee29d016b052dbd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85559428"
---
# <a name="upstream-settings"></a>アップストリームの設定

アップストリームは、Azure SignalR Service からサーバーレス モードで一連のエンドポイントにメッセージと接続イベントを送信できるようになる機能です。 アップストリームを使用すると、サーバーレス モードのクライアントからハブ メソッドを呼び出し、クライアント接続が確立または切断されたときにエンドポイントに通知を送信することができます。

> [!NOTE]
> アップストリーム設定を構成できるのは、サーバーレス モードのみです。

## <a name="details-of-upstream-settings"></a>アップストリーム設定の詳細

アップストリーム設定は、順序が区別される項目の一覧で構成されています。 各項目は次の要素で構成されます。

* URL テンプレート。メッセージの送信先を指定します。
* 一連のルール。
* 認証の構成。 

指定されたイベントが発生すると、項目のルールが 1 つずつ順番にチェックされます。 メッセージは、最初に一致した項目のアップストリームの URL に送信されます。

### <a name="url-template-settings"></a>URL テンプレートの設定

URL をパラメーター化すると、さまざまなパターンをサポートできます。 次の 3 つの定義済みパラメーターがあります。

|定義済みパラメーター|説明|
|---------|---------|
|{hub}| ハブは Azure SignalR Service の概念です。 ハブは分離の単位です。 ユーザーとメッセージ配信の範囲はハブに制限されています。|
|{category}| カテゴリには次のいずれかの値を指定することができます。 <ul><li>**connections**:接続の有効期間イベント。 このイベントは、クライアント接続が確立または切断されたときに発生します。 これには connected イベントと disconnected イベントがあります。</li><li>**messages**:クライアントからハブ メソッドが呼び出されたときに発生します。 これには、**connections** カテゴリを除き、他のすべてのイベントが含まれます。</li></ul>|
|{event}| **messages** カテゴリの場合、イベントはクライアントから送信される[呼び出しメッセージ](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)のターゲットです。 **connections** カテゴリの場合、*connected* と *disconnected* のみが使用されます。|

これらの定義済みパラメーターは、URL パターン内で使用できます。 アップストリーム URL の評価時に、パラメーターは指定された値に置き換えられます。 次に例を示します。 
```
http://host.com/{hub}/api/{category}/{event}
```
"チャット" ハブでクライアント接続が確立すると、次の URL にメッセージが送信されます。
```
http://host.com/chat/api/connections/connected
```
"チャット" ハブのクライアントからハブ メソッド `broadcast` が呼び出されると、次の URL にメッセージが送信されます。
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>ルールの設定

"*ハブ ルール*"、"*カテゴリ ルール*"、および "*イベント ルール*" のルールを個別に設定できます。 照合ルールは 3 つの形式をサポートしています。 例としてイベント ルールを取り上げます。
- すべてのイベントに一致させるには、アスタリスク (*) を使用します。
- 複数のイベントを結合するには、コンマ (,) を使用します。 たとえば、`connected, disconnected` は connected イベントと disconnected イベントと一致します。
- イベントと一致させるには、完全なイベント名を使用します。 たとえば、`connected` は connected イベントと一致します。

### <a name="authentication-settings"></a>[認証設定]

アップストリームの設定項目ごとに個別に認証を設定することができます。 認証を構成すると、アップストリームのメッセージの `Authentication` ヘッダーにトークンが設定されます。 現在、Azure SignalR Service は次の認証の種類をサポートしています。
- `None`
- `ManagedIdentity`

`ManagedIdentity` を選択する場合は、事前に Azure SignalR Service でマネージド ID を有効にする必要があります。また、必要に応じてリソースを指定します。 詳細については、「[Azure SignalR Service のマネージド ID](howto-use-managed-identity.md)」を参照してください。

## <a name="create-upstream-settings-via-the-azure-portal"></a>Azure portal を使用してアップストリーム設定を作成する

1. Azure SignalR Service に移動します。
2. **[設定]** を選択し、 **[サービス モード]** を **[サーバーレス]** に切り替えます。 アップストリームの設定が表示されます。

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="アップストリームの設定":::

3. **[Upstream URL Pattern]\(アップストリームの URL パターン\)** に URL を追加します。 次に **[Hub Rules]\(ハブ ルール\)** などの設定に既定値が表示されます。
4. **[Hub Rules]\(ハブ ルール\)** 、 **[Event Rules]\(イベント ルール\)** 、 **[Category Rules]\(カテゴリ ルール\)** 、 **[Upstream Authentication]\(アップストリーム認証\)** の設定を行うには、 **[Hub Rules]\(ハブ ルール\)** の値を選択します。 設定を編集できるページが表示されます。

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="アップストリームの設定":::

5. **[Upstream Authentication]\(アップストリーム認証\)** を設定するには、まずマネージド ID を有効にしていることを確認します。 次に、 **[Use Managed Identity]\(マネージド ID の使用\)** を選択します。 必要に応じて、 **[Auth Resource ID]\(認証リソース ID\)** で任意のオプションを選択することができます。 詳細については、「[Azure SignalR Service のマネージド ID](howto-use-managed-identity.md)」を参照してください。

## <a name="create-upstream-settings-via-resource-manager-template"></a>Resource Manager テンプレートを使用してアップストリームの設定を作成する

[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)を使用してアップストリームの設定を作成するには、`properties` プロパティで `upstream` プロパティを設定します。 次のスニペットは、アップストリームの設定を作成および更新するために `upstream` プロパティを設定する方法を示しています。

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>サーバーレス プロトコル

Azure SignalR Service からは、次のプロトコルに従うメッセージがエンドポイントに送信されます。

### <a name="method"></a>Method

POST

### <a name="request-header"></a>要求ヘッダー

|名前 |説明|
|---------|---------|
|X-ASRS-Connection-Id |クライアント接続の接続 ID。|
|X-ASRS-Hub |クライアント接続が属するハブ。|
|X-ASRS-Category |メッセージが属するカテゴリ。|
|X-ASRS-Event |メッセージが属するイベント。|
|X-ASRS-Signature |検証に使用されるハッシュ ベースのメッセージ認証コード (HMAC)。 詳細については「[署名](#signature)」を参照してください。|
|X-ASRS-User-Claims |クライアント接続の要求のグループ。|
|X-ASRS-User-Id |メッセージを送信するクライアントのユーザー ID。|
|X-ASRS-Client-Query |クライアントがサービスに接続するときの要求のクエリ。|
|認証 |`ManagedIdentity` を使用している場合の省略可能なトークン。 |

### <a name="request-body"></a>要求本文

#### <a name="connected"></a>接続中

Content-Type, application/json

#### <a name="disconnected"></a>[Disconnected]\(切断済み\)

Content-Type: `application/json`

|名前  |Type  |説明  |
|---------|---------|---------|
|エラー |string |閉じられた接続のエラー メッセージ。 エラーなしで接続が閉じた場合は空。|

#### <a name="invocation-message"></a>呼び出しメッセージ

Content-Type: `application/json` または `application/x-msgpack`

|名前  |Type  |説明  |
|---------|---------|---------|
|InvocationId |string | 呼び出しメッセージを表す省略可能な文字列。 詳細については、「[Invocations](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations)」 (呼び出し) を参照してください。|
|移行先 |string | イベントと同じであり、[呼び出しメッセージ](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)のターゲットと同じです。 |
|引数 |オブジェクトの配列 |`Target` で参照されるメソッドに適用する引数を含む配列。 |

### <a name="signature"></a>署名

サービスでは、プライマリ アクセス キーとセカンダリ アクセス キーの両方が `HMAC` キーとして使用され、`X-ASRS-Connection-Id` 値の SHA256 コードが計算されます。 アップストリームへの HTTP 要求を行うときに、サービスによってそれが `X-ASRS-Signature` ヘッダーに設定されます。
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>次のステップ

- [Azure SignalR Service のマネージド ID](howto-use-managed-identity.md)
- [Azure SignalR Service を使用した Azure Functions の開発と構成](signalr-concept-serverless-development-config.md)
