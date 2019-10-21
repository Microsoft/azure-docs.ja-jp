---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 6591fd6eb232bf5fb242c9e08830324f864dac2f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837554"
---
[!INCLUDE [Prerequisites](prerequisites-python.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `detect.py` という名前のファイルにコピーします。

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests uuid` を実行します。

最初のコメントでは、UTF-8 エンコードを使用するように Python インタープリターに指示しています。 次に、必要なモジュールをインポートして、環境変数からのサブスクリプション キーの読み取り、HTTP 要求の作成、一意識別子の作成、Translator Text API から返された JSON 応答の処理を行っています。

## <a name="set-the-subscription-key-endpoint-and-path"></a>サブスクリプション キー、エンドポイント、パスの設定

このサンプルでは、Translator Text のサブスクリプション キーとエンドポイントを環境変数 `TRANSLATOR_TEXT_KEY` および `TRANSLATOR_TEXT_ENDPOINT` から読み取ることを試みます。 環境変数を使い慣れていない場合は、`subscription_key` および `endpoint` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```python
key_var_name = 'TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
if not key_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(key_var_name))
subscription_key = os.environ[key_var_name]

endpoint_var_name = 'TRANSLATOR_TEXT_ENDPOINT'
if not endpoint_var_name in os.environ:
    raise Exception('Please set/export the environment variable: {}'.format(endpoint_var_name))
endpoint = os.environ[endpoint_var_name]
```

Translator Text のグローバル エンドポイントは、`endpoint` として設定されます。 `path` によって、`detect` ルートが設定され、API のバージョン 3 を使用することが識別されます。

>[!NOTE]
> エンドポイント、ルート、および要求パラメーターの詳細については、「[Translator Text API 3.0: Detect](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect)」をご覧ください。

```python
path = '/detect?api-version=3.0'
constructed_url = endpoint + path
```

## <a name="add-headers"></a>ヘッダーの追加

要求を認証する最も簡単な方法は、このサンプルで使用している `Ocp-Apim-Subscription-Key` ヘッダーとしてサブスクリプション キーを渡すことです。 または、アクセス トークンのサブスクリプション キーを交換し、アクセス トークンを一緒に `Authorization` ヘッダーとして渡して要求を検証することもできます。 詳細については、[認証](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)に関するページをご覧ください。

このコード スニペットをプロジェクトにコピーします。

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

Cognitive Services のマルチサービス サブスクリプションを使用している場合は、要求のパラメーターに `Ocp-Apim-Subscription-Region` も含める必要があります。 [マルチサービス サブスクリプションを使用した認証の詳細を参照してください](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)。

## <a name="create-a-request-to-detect-text-language"></a>テキストの言語を検出する要求の作成

言語の検出対象となる 1 つまたは複数の文字列を定義します。

```python
# You can pass more than one object in body.
body = [{
    'text': 'Salve, mondo!'
}]
```

次に、`requests` モジュールを使用して POST 要求を作成します。 これは、連結された URL、要求ヘッダー、および要求本文の 3 つの引数を受け取ります。

```python
request = requests.post(constructed_url, headers=headers, json=body)
response = request.json()
```

## <a name="print-the-response"></a>応答の出力

最後の手順では、結果を出力します。 このコード スニペットでは、キーを並べ替え、インデントを設定し、項目とキーの区切りを宣言することによって、結果を整形します。

```python
print(json.dumps(response, sort_keys=True, indent=4,
                 ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator Text API を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
python detect.py
```

作成したコードをサンプル コードと比較したい場合は、完全なサンプルを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) から入手できます。

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

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーをプログラムにハードコーディングしている場合は、このクイック スタートを終了するときにサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

API のリファレンスを見て、Translator Text API でできるすべてのことを理解してください。

> [!div class="nextstepaction"]
> [API リファレンス](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
