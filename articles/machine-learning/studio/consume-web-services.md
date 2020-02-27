---
title: Web サービスを使用する
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (クラシック) から機械学習サービスがデプロイされると、リアルタイムの要求応答サービスまたはバッチ実行サービスのいずれかとして、RESTFul Web サービスを使用できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: 6ecf5ba01b03ab899fec96b8ecd282a29e5ce3e0
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2020
ms.locfileid: "77168903"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (クラシック) Web サービスを使用する方法

Azure Machine Learning Studio (クラシック) の予測モデルを Web サービスとしてデプロイすると、REST API を使用してデータを送信し、予測を取得することができます。 リアルタイムまたはバッチ モードでデータを送信できます。

Machine Learning Studio (クラシック) を使用して Machine Learning Web サービスを作成してデプロイする方法の詳細については、次を参照してください。

* Machine Learning Studio (クラシック) で実験を作成する方法については、[初めての実験の作成](create-experiment.md)に関する記事をご覧ください。
* Web サービスをデプロイする方法の詳細については、「 [Azure Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)」をご覧ください。
* Machine Learning 全般の詳細については、 [Machine Learning ドキュメント センター](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。



## <a name="overview"></a>概要
Azure Machine Learning Web サービスを使用して、外部のアプリケーションが Machine Learning のワークフローのスコア付けモデルとリアルタイムで通信します。 Machine Learning Web サービスの呼び出しは、予測結果を外部のアプリケーションに返します。 Machine Learning Web サービスの呼び出しを実行するために、予測のデプロイ時に作成される API キーを渡します。 Machine Learning Web サービスは、Web プログラミング プロジェクトでよく選択されるアーキテクチャの REST に基づいています。

Azure Machine Learning Studio (クラシック) には、2 種類のサービスがあります。

* 要求応答サービス (RRS) – 待ち時間が短く拡張性の高い、Machine Learning Studio (クラシック) から作成およびデプロイされるステートレスなモデルへのインターフェイスを提供するサービス。
* バッチ実行サービス (BES) – データ レコードのバッチをスコア付けする非同期のサービス。

Machine Learning Web サービスの詳細については、「[Azure Machine Learning Web サービスをデプロイする](deploy-a-machine-learning-web-service.md)」をご覧ください。

## <a name="get-an-authorization-key"></a>承認キーを取得する
実験をデプロイすると、Web サービスの API キーが生成されます。 複数の場所からキーを取得できます。

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Microsoft Azure Machine Learning Web サービス ポータルを使用する
[Microsoft Azure Machine Learning Web サービス](https://services.azureml.net) ポータルにサインインする

新しい Machine Learning Web サービスの API キーを取得するには:

1. Azure Machine Learning Web サービス ポータルで、上部メニューの **[Web サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。
3. 上部のメニューで **[Consume(使用)]** をクリックします。
4. **主キー**をコピーして保存します。

従来の Machine Learning Web サービスの API キーを取得するには:

1. Azure Machine Learning Web サービス ポータルで、上部メニューの **[Classic Web Services (クラシック Web サービス)]** をクリックします。
2. 使用する Web サービスをクリックします。
3. キーを取得するエンドポイントをクリックします。
4. 上部のメニューで **[Consume(使用)]** をクリックします。
5. **主キー**をコピーして保存します。

### <a name="classic-web-service"></a>従来の Web サービス
 Machine Learning Studio (クラシック) からクラシック Web サービスのキーを取得することもできます。

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (クラシック)
1. Machine Learning Studio (クラシック) で、左側の **[Web サービス]** をクリックします。
2. Web サービスをクリックします。 **[ダッシュボード]** タブに **[API キー]** があります。

## <a id="connect"></a>Machine Learning Web サービスに接続する
HTTP 要求と応答をサポートする任意のプログラミング言語を使用して、Machine Learning Web サービスに接続することができます。 Machine Learning Web サービス ヘルプ ページから、C#、Python、および R の例を表示できます。

**Machine Learning API ヘルプ** Machine Learning API ヘルプは、Web サービスをデプロイするときに作成されます。 [チュートリアル 3: 信用リスク モデルのデプロイ](tutorial-part3-credit-risk-deploy.md)を参照してください。
Machine Learning API ヘルプには、予測 Web サービスの詳細が含まれます。

1. 使用する Web サービスをクリックします。
2. API のヘルプ ページを表示するエンドポイントをクリックします。
3. 上部のメニューで **[Consume(使用)]** をクリックします。
4. [要求/応答] または [バッチの実行] の **[API ヘルプ ページ]** をクリックします。

**新しい Web サービスの Machine Learning API ヘルプを表示するには**

[Azure Machine Learning Web Services ポータル](https://services.azureml.net/)で、次の手順を実行します。

1. 上部のメニューで **[Web サービス]** をクリックします。
2. キーを取得する Web サービスをクリックします。

**[Use Web Service]\(Web サービスの使用\)** をクリックして、[要求/応答] および [バッチの実行] サービスの URI と、C#、R、および Python のサンプル コードを取得します。

**Swagger API** をクリックして、指定した URI から呼び出される API の Swagger ベースのドキュメントを取得します。

### <a name="c-sample"></a>C# のサンプル
Machine Learning Web サービスに接続するには、ScoreData を渡す **HttpClient** を使用します。 ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。 Machine Learning サービスの認証には、API キーを使用します。

Machine Learning Web サービスに接続するには、**Microsoft.AspNet.WebApi.Client** NuGet パッケージをインストールする必要があります。

**Microsoft.AspNet.WebApi.Client NuGet in Visual Studio をインストールする**

1. Web Service "UCI からデータセットをダウンロード: Adult 2 class dataset" を公開します。
2. **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順にクリックします。
3. **[Install-Package Microsoft.AspNet.WebApi.Client]** を選択します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる "サンプル 1: UCI からデータセットをダウンロード: Adult 2 class dataset" 実験を公開します。
2. Web サービスからのキーを持つ apiKey を割り当てます。 上記の「**承認キーを取得する**」を参照してください。
3. 要求の URI を含む serviceUri を割り当てます。

**完成した要求は次のようになります。**
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python サンプル
Machine Learning Web サービスに接続するには、Python 2.X の場合は **urllib2** ライブラリ、Python 3.X の場合は **urllib.request** ライブラリを使用します。 ScoreData を渡します。この ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。 Machine Learning サービスの認証には、API キーを使用します。

**サンプル コードを実行するには**

1. Machine Learning サンプル コレクションに含まれる "Sample 1: UCI からデータセットをダウンロード: Adult 2 class dataset" 実験を公開します。
2. Web サービスからのキーを持つ apiKey を割り当てます。 この記事の始めのほうにある「**承認キーを取得する**」セクションをご覧ください。
3. 要求の URI を含む serviceUri を割り当てます。

**完成した要求は次のようになります。**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R サンプル

Machine Learning Web サービスに接続するには、**RCurl** および **rjson** ライブラリを使用して要求を作成し、返された JSON 応答を処理します。 ScoreData を渡します。この ScoreData には、ScoreData を表す数値機能の n 次元ベクトルである FeatureVector が含まれています。 Machine Learning サービスの認証には、API キーを使用します。

**完成した要求は次のようになります。**
```r
library("RCurl")
library("rjson")

# Accept SSL certificates issued by public Certificate Authorities
options(RCurlOptions = list(cainfo = system.file("CurlSSL", "cacert.pem", package = "RCurl")))

h = basicTextGatherer()
hdr = basicHeaderGatherer()

req = list(
    Inputs = list(
            "input1" = list(
                list(
                        'column1' = "value1",
                        'column2' = "value2",
                        'column3' = "value3"
                    )
            )
        ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "<your-api-key>" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

h$reset()
curlPerform(url = "<your-api-uri>",
httpheader=c('Content-Type' = "application/json", 'Authorization' = authz_hdr),
postfields=body,
writefunction = h$update,
headerfunction = hdr$update,
verbose = TRUE
)

headers = hdr$value()
httpStatus = headers["status"]
if (httpStatus >= 400)
{
print(paste("The request failed with status code:", httpStatus, sep=" "))

# Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
print(headers)
}

print("Result:")
result = h$value()
print(fromJSON(result))
```

### <a name="javascript-sample"></a>JavaScript のサンプル

Machine Learning Web サービスに接続するには、プロジェクトで **request** npm パッケージを使用します。 また、`JSON` オブジェクトを使用して入力の書式を設定し、結果を解析します。 `npm install request --save` を使用してインストールするか、`"request": "*"` を `dependencies` 以下の package.json に追加し、`npm install` を実行します。

**完成した要求は次のようになります。**
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```
