---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-javascript
ms.openlocfilehash: dfca8bb6f897f532081df7d141112ddbb190d109
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87406548"
---
[!INCLUDE [Prerequisites](prerequisites-nodejs.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しいプロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `detect.js` という名前のファイルにコピーします。

```javascript
const request = require('request');
const uuidv4 = require('uuid/v4');
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`npm install request uuidv4` を実行します。

これらのモジュールは、HTTP 要求を作成したり、`'X-ClientTraceId'` ヘッダーの一意識別子を作成したりする際に必要となります。

## <a name="set-the-subscription-key-and-endpoint"></a>サブスクリプション キーとエンドポイントの設定

このサンプルは、Translator のサブスクリプション キーとエンドポイントを環境変数 `TRANSLATOR_TEXT_SUBSCRIPTION_KEY` および `TRANSLATOR_TEXT_ENDPOINT` から読み取ることを試みます。 環境変数を使い慣れていない場合は、`subscriptionKey` と `endpoint` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```javascript
var key_var = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY';
if (!process.env[key_var]) {
    throw new Error('Please set/export the following environment variable: ' + key_var);
}
var subscriptionKey = process.env[key_var];
var endpoint_var = 'TRANSLATOR_TEXT_ENDPOINT';
if (!process.env[endpoint_var]) {
    throw new Error('Please set/export the following environment variable: ' + endpoint_var);
}
var endpoint = process.env[endpoint_var];
```

## <a name="configure-the-request"></a>要求の構成

要求モジュールに用意されている `request()` メソッドには、HTTP メソッド、URL、要求パラメーター、ヘッダー、JSON 本文を `options` オブジェクトとして渡すことができます。 このコード スニペットで、実際の要求を構成してみましょう。

```javascript
let options = {
    method: 'POST',
    baseUrl: endpoint,
    url: 'detect',
    qs: {
      'api-version': '3.0',
    },
    headers: {
      'Ocp-Apim-Subscription-Key': subscriptionKey,
      'Content-type': 'application/json',
      'X-ClientTraceId': uuidv4().toString()
    },
    body: [{
          'text': 'Salve, mondo!'
    }],
    json: true,
};
```
要求を認証する最も簡単な方法は、このサンプルで使用している `Ocp-Apim-Subscription-Key` ヘッダーとしてサブスクリプション キーを渡すことです。 または、アクセス トークンのサブスクリプション キーを交換し、アクセス トークンを一緒に `Authorization` ヘッダーとして渡して要求を検証することもできます。

Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のヘッダーに `Ocp-Apim-Subscription-Region` も含める必要があります。

詳細については、[認証](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)に関するページをご覧ください。

## <a name="make-the-request-and-print-the-response"></a>要求の実行と応答の出力

次に、`request()` メソッドを使用して要求を作成します。 前のセクションで作成した `options` オブジェクトを第 1 引数として渡すと、修飾された JSON 応答が出力されます。

```javascript
request(options, function(err, res, body){
    console.log(JSON.stringify(body, null, 4));
});
```

>[!NOTE]
> このサンプルでは、`options` オブジェクト内で HTTP 要求を定義しています。 一方、要求モジュールでは、`.post` や `.get` などの便利なメソッドもサポートされています。 詳細については、[便利なメソッド](https://github.com/request/request#convenience-methods)に関するセクションを参照してください。

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
node detect.js
```

## <a name="sample-response"></a>応答のサンプル

サンプルを実行すると、次のようにターミナルに出力されます。

> [!NOTE]
> 国/地域の省略形は、こちらの[言語一覧](https://docs.microsoft.com/azure/cognitive-services/translator/language-support)で確認してください。

```json
[
    {
        "alternatives": [
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "pt",
                "score": 1.0
            },
            {
                "isTranslationSupported": true,
                "isTransliterationSupported": false,
                "language": "en",
                "score": 1.0
            }
        ],
        "isTranslationSupported": true,
        "isTransliterationSupported": false,
        "language": "it",
        "score": 1.0
    }
]
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

サブスクリプション キーをプログラムにハードコーディングしている場合は、このクイック スタートを終了するときにサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次のステップ

API のリファレンスを見て、Translator でできるすべてのことを理解してください。

> [!div class="nextstepaction"]
> [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
