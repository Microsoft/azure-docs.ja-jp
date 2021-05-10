---
title: Azure Data Factory の Webhook アクティビティ
description: Webhook アクティビティは、ユーザーが指定する特定の条件で接続されたデータセットを検証するまで、パイプラインの実行を継続しません。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: 4c3ff5d7139f4167769f78aa858c7d7a693539a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785939"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory の Webhook アクティビティ

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Webhook アクティビティを使用すると、カスタム コードでパイプラインの実行を制御できます。 Webhook アクティビティを使用すると、顧客のコードでエンドポイントを呼び出して、コールバック URL を渡すことができます。 パイプラインでは、次のアクティビティに進む前に、コールバックが呼び出されるまで実行が待機されます。

> [!IMPORTANT]
> Webhook アクティビティでは、エラー状態とカスタム メッセージをアクティビティとパイプラインに戻すことができるようになりました。 _reportStatusOnCallBack_ を true に設定し、コールバック ペイロードに _StatusCode_ と _Error_ を含めます。 詳細については、「[その他のメモ](#additional-notes)」セクションを参照してください。

## <a name="syntax"></a>構文

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
**name** | Webhook アクティビティの名前。 | String | はい |
**type** | "WebHook" に設定する必要があります。 | String | はい |
**method** | ターゲット エンドポイント用の REST API メソッド。 | 文字列 をオンにします。 サポートされている型は "POST" です。 | はい |
**url** | ターゲット エンドポイントおよびパス。 | 文字列または **resultType** 値の文字列が含まれる式。 | はい |
**headers** | 要求に送信されるヘッダー。 言語と種類を要求に設定する場合の例を次に示します。`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 文字列または **resultType** 値の文字列が含まれる式。 | はい。 `"headers":{ "Content-Type":"application/json"}` のような `Content-Type` ヘッダーが必要です。 |
**body** | エンドポイントに送信されるペイロードを表します。 | 有効な JSON または **resultType** 値の JSON が含まれる式。 要求ペイロードのスキーマについては、「[要求ペイロードのスキーマ](./control-flow-web-activity.md#request-payload-schema)」を参照してください。 | はい |
**認証** | エンドポイントを呼び出すために使用される認証方法。 サポートされる種類は "Basic" および "ClientCertificate" です。 詳細については、[認証](./control-flow-web-activity.md#authentication)に関するページをご覧ください。 認証が必要ない場合は、このプロパティを除外します。 | 文字列または **resultType** 値の文字列が含まれる式。 | いいえ |
**timeout** | **callBackUri** で指定されたコールバックが呼び出されるまでのアクティビティの待機時間。 既定値は 10 分 ("00:10:00") です。 値の TimeSpan 形式は *d*.*hh*:*mm*:*ss* です。 | String | いいえ |
**[Report status on callback] (コールバックで状態を報告する)** | ユーザーが Webhook アクティビティの失敗した状態を報告できるようにします。 | ブール型 | いいえ |

## <a name="authentication"></a>認証

Webhook アクティビティでは、次の認証の種類がサポートされます。

### <a name="none"></a>なし

認証が必要ない場合は、**authentication** プロパティを含めないでください。

### <a name="basic"></a>Basic

基本認証で使用するユーザー名とパスワードを指定します。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>クライアント証明書

PFX ファイルの Base64 でエンコードされたコンテンツとパスワードを指定します。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>マネージド ID

データ ファクトリのマネージド ID を使用してアクセス トークンの要求対象となるリソース URI を指定します。 Azure Resource Management API を呼び出すには、`https://management.azure.com/` を使用します。 マネージド ID が機能する方法について詳しくは、[Azure リソースのマネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> ご利用のデータ ファクトリが Git リポジトリを使用して構成されている場合、基本認証またはクライアント証明書認証を使用するには、ご自分の資格情報を Azure Key Vault に格納する必要があります。 Azure Data Factory では、Git にパスワードは保存されません。

## <a name="additional-notes"></a>その他のメモ

Data Factory では、URL エンドポイントに送信される本文に追加のプロパティ **callBackUri** が渡されます。 Data Factory は、指定されたタイムアウト値の前にこの URI が呼び出されることを想定しています。 この URI が呼び出されない場合、アクティビティは状態 'TimedOut' で失敗します。

Webhook アクティビティが失敗するのは、カスタム エンドポイントへの呼び出しが失敗した場合です。 任意のエラー メッセージをコールバックの本文に追加して、その後のアクティビティで使用できます。

毎回の REST API 呼び出しでは、エンドポイントが 1 分以内に応答しない場合、クライアントがタイムアウトします。 この動作は、HTTP の標準的なベスト プラクティスです。 この問題を解決するには、202 パターンを実装します。 現在のケースでは、エンドポイントで 202 (受理) が返され、クライアントでポーリングが行われます。

要求時の 1 分間のタイムアウトは、アクティビティ タイムアウトとは関係ありません。 後者は、**callbackUri** によって指定されたコールバックを待機するために使用されます。

コールバック URI に戻される本文は、有効な JSON である必要があります。 `Content-Type` ヘッダーを `application/json` に設定します。

**[Report status on callback]\(コールバックで状態を報告する\)** プロパティを使用する場合は、コールバックを作成するときに本文に次のコードを追加する必要があります。

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>次のステップ

Data Factory でサポートされている次の制御フロー アクティビティを参照してください。

- [If Condition アクティビティ](control-flow-if-condition-activity.md)
- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [メタデータの取得アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
- [Web アクティビティ](control-flow-web-activity.md)
- [Until アクティビティ](control-flow-until-activity.md)