---
title: 'クイック スタート: サポートされている言語の一覧を取得する (Python) - Translator Text API'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、翻訳、表記変換、辞書検索がサポートされている言語の一覧を取得する方法について、Python で Translator Text API を使った例を紹介しています。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 10/29/2018
ms.author: erhopf
ms.openlocfilehash: 8b78ec4e358f654342140df1491e19d3fc62fee7
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52992394"
---
# <a name="quickstart-use-the-translator-text-api-to-get-a-list-of-supported-languages-using-python"></a>クイック スタート: Translator Text API と Python を使用してサポートされている言語の一覧を取得する

このクイック スタートでは、Python と Translator Text REST API を使用し、GET 要求を行うことによって、サポートされている言語の一覧を取得する方法を説明します。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Translator Text リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Python 2.7.x または 3.x
* Translator Text の Azure サブスクリプション キー

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットをプロジェクトの `get-languages.py` という名前のファイルにコピーします。

```python
# -*- coding: utf-8 -*-
import os, requests, uuid, json
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests uuid` を実行します。

最初のコメントでは、UTF-8 エンコードを使用するように Python インタープリターに指示しています。 次に、必要なモジュールをインポートして、環境変数からのサブスクリプション キーの読み取り、HTTP 要求の作成、一意識別子の作成、Translator Text API から返された JSON 応答の処理を行っています。

## <a name="set-the-subscription-key-base-url-and-path"></a>サブスクリプション キー、ベース URL、パスの設定

このサンプルでは、環境変数 `TRANSLATOR_TEXT_KEY` から Translator Text のサブスクリプション キーが読み取られるよう試行されます。 環境変数を使い慣れていない場合は、`subscriptionKey` を文字列として設定し、条件ステートメントをコメント アウトすることができます。

このコードをプロジェクトにコピーします。

```python
# Checks to see if the Translator Text subscription key is available
# as an environment variable. If you are setting your subscription key as a
# string, then comment these lines out.
if 'TRANSLATOR_TEXT_KEY' in os.environ:
    subscriptionKey = os.environ['TRANSLATOR_TEXT_KEY']
else:
    print('Environment variable for TRANSLATOR_TEXT_KEY is not set.')
    exit()
# If you want to set your subscription key as a string, uncomment the line
# below and add your subscription key.
#subscriptionKey = 'put_your_key_here'
```

現在、1 つのエンドポイントが Translator Text 用に使用可能で、`base_url` として設定されています。 `path` によって、`languages` ルートが設定され、API のバージョン 3 を使用することが識別されます。

>[!NOTE]
> エンドポイント、ルート、および要求パラメーターの詳細については、「[Translator Text API 3.0: Languages](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)」を参照してください。

```python
base_url = 'https://api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'
constructed_url = base_url + path
```

## <a name="add-headers"></a>ヘッダーの追加

要求を認証する最も簡単な方法は、このサンプルで使用している `Ocp-Apim-Subscription-Key` ヘッダーとしてサブスクリプション キーを渡すことです。 または、アクセス トークンのサブスクリプション キーを交換し、アクセス トークンを一緒に `Authorization` ヘッダーとして渡して要求を検証することもできます。 詳細については、[認証](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication)に関するページをご覧ください。

このコード スニペットをプロジェクトにコピーします。

```python
headers = {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}
```

## <a name="create-a-request-to-get-a-list-of-supported-languages"></a>サポートされている言語の一覧を取得するための要求を作成する

`requests` モジュールを使用して GET 要求を作成してみましょう。 これは、連結された URL と要求ヘッダーの 2 つの引数を受け取ります。

```python
request = requests.get(constructed_url, headers=headers)
response = request.json()
```

## <a name="print-the-response"></a>応答の出力

最後の手順では、結果を出力します。 このコード スニペットでは、キーを並べ替え、インデントを設定し、項目とキーの区切りを宣言することによって、結果を整形します。

```python
print(json.dumps(response, sort_keys=True, indent=4, ensure_ascii=False, separators=(',', ': ')))
```

## <a name="put-it-all-together"></a>すべてをまとめた配置

これで、Translator Text API を呼び出して JSON 応答を返す簡単なプログラムが完成しました。 ここで、プログラムを実行してみましょう。

```console
python get-languages.py
```

作成したコードをサンプル コードと比較したい場合は、完全なサンプルを [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python) から入手できます。

## <a name="sample-response"></a>応答のサンプル

このサンプルは、実行結果を抜粋したものです。

```json
{
    "translation": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr"
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl"
        },
        ...
    },
    "transliteration": {
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "scripts": [
                {
                    "code": "Arab",
                    "name": "Arabic",
                    "nativeName": "العربية",
                    "dir": "rtl",
                    "toScripts": [
                        {
                            "code": "Latn",
                            "name": "Latin",
                            "nativeName": "اللاتينية",
                            "dir": "ltr"
                        }
                    ]
                },
                {
                    "code": "Latn",
                    "name": "Latin",
                    "nativeName": "اللاتينية",
                    "dir": "ltr",
                    "toScripts": [
                        {
                            "code": "Arab",
                            "name": "Arabic",
                            "nativeName": "العربية",
                            "dir": "rtl"
                        }
                    ]
                }
            ]
        },
      ...
    },
    "dictionary": {
        "af": {
            "name": "Afrikaans",
            "nativeName": "Afrikaans",
            "dir": "ltr",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
        "ar": {
            "name": "Arabic",
            "nativeName": "العربية",
            "dir": "rtl",
            "translations": [
                {
                    "name": "English",
                    "nativeName": "English",
                    "dir": "ltr",
                    "code": "en"
                }
            ]
        },
      ...
    }
}
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーをプログラムにハードコーディングしている場合は、このクイック スタートを終了するときにサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で Python のコード例を詳しく見てみる](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Python)

## <a name="see-also"></a>関連項目

Translator Text API を使用して以下を実行する方法を確認します。

* [テキストを翻訳する](quickstart-python-translate.md)
* [テキストを表記変換する](quickstart-python-transliterate.md)
* [入力によって言語を識別する](quickstart-python-detect.md)
* [別の翻訳を取得する](quickstart-python-dictionary.md)
* [入力から文章の長さを判定する](quickstart-python-sentences.md)
