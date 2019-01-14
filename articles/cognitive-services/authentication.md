---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: Azure Cognitive Services リソースへの要求を認証する方法には、サブスクリプション キー、ベアラー トークン、またはマルチサービス サブスクリプションの 3 つがあります。 この記事では、それぞれの方法と、要求を実行する方法について学習します。
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.openlocfilehash: 11259b99ea9d2486c8c0afde21398710ccc6ccd8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53726292"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Azure Cognitive Services に対する要求の認証

Azure Cognitive Service に対する各要求には、認証ヘッダーが含まれている必要があります。 このヘッダーを使用して、サブスクリプション キーまたはアクセス トークンを渡します。これは、サービスまたはサービスのグループについてお客様のサブスクリプションを検証するために使用されます。 この記事では、要求を認証するための 3 つの方法と、そのそれぞれの要件について学習します。

* [単一サービスのサブスクリプション キーによる認証](#authenticate-with-a-single-service-subscription-key)
* [マルチサービスのサブスクリプション キーによる認証](#authenticate-with-a-multi-service-subscription-key)
* [トークンによる認証](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>前提条件

要求を実行する前に、Azure アカウントと Azure Cognitive Services サブスクリプションが必要です。 既にアカウントをお持ちの場合は、次のセクションまでスキップして進んでください。 アカウントをお持ちでない場合は、数分で設定を行えるガイドをご覧ください。[Azure の Cognitive Services アカウントの作成](cognitive-services-apis-create-account.md)に関するページを参照してください。

## <a name="authentication-headers"></a>認証ヘッダー

Azure Cognitive Services で使用できる認証ヘッダーについて簡単に確認しましょう。

| ヘッダー | 説明 |
|--------|-------------|
| Ocp-Apim-Subscription-Key | 特定のサービスのサブスクリプション キーまたはマルチサービスのサブスクリプション キーを使用して認証するには、このヘッダーを使用します。 |
| Ocp-Apim-Subscription-Region | このヘッダーは、[Translator Text API](./Translator/reference/v3-0-reference.md) と共にマルチサービスのサブスクリプション キーを使用する場合にのみ必要です。 このヘッダーを使用して、サブスクリプション リージョンを指定します。 |
| Authorization | お客様が認証トークンを使用している場合は、このヘッダーを使用します。 トークンの交換を実行する手順については、以降のセクションで詳しく説明されています。 値は `Bearer <TOKEN>` 形式で指定します。 |

## <a name="authenticate-with-a-single-service-subscription-key"></a>単一サービスのサブスクリプション キーによる認証

1 つ目の方法では、Translator Text などの特定のサービスに対してサブスクリプション キーを使用して要求を認証します。 お客様が作成したそれぞれのリソースについて、Azure portal でキーを取得できます。 サブスクリプション キーを使用して要求を認証するには、それを `Ocp-Apim-Subscription-Key` ヘッダーとして渡す必要があります。

以下のサンプル要求は、`Ocp-Apim-Subscription-Key` ヘッダーの使用方法を示しています。 このサンプルを使用する際は有効なサブスクリプション キーを含める必要があることに注意してください。

これは、Bing Web Search API 呼び出しのサンプルです。
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

これは、Translator Text API 呼び出しのサンプルです。
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-a-multi-service-subscription-key"></a>マルチサービスのサブスクリプション キーによる認証

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて**いません**。QnA Maker、Speech Services、および Custom Vision。

この方法も、サブスクリプション キーを使用して要求を認証します。 主な違いは、サブスクリプション キーが特定のサービスに関連付けられておらず、単一のキーを使用して複数の Cognitive Services に対する要求を認証できることです。 リージョン別の提供状況、サポートされている機能、および価格については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

サブスクリプション キーは、各要求内で `Ocp-Apim-Subscription-Key` ヘッダーとして指定されます。

### <a name="supported-regions"></a>サポートされているリージョン

マルチサービスのサブスクリプション キーを使用して `api.cognitive.microsoft.com` に対する要求を実行するときは、URL にリージョンを含める必要があります。 (例: `westus.api.cognitive.microsoft.com`)。

Translator Text API と共にマルチサービスのサブスクリプション キーを使用する場合は、`Ocp-Apim-Subscription-Region` ヘッダーを使用してサブスクリプション リージョンを指定する必要があります。

マルチサービス認証は、以下のリージョンでサポートされています。

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>サンプルの要求

これは、Bing Web Search API 呼び出しのサンプルです。

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

これは、Translator Text API 呼び出しのサンプルです。

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>認証トークンによる認証

Azure Cognitive Services の中には、認証トークンを受け入れるものや、場合によっては認証トークンを必要とするものがあります。 現在、以下のサービスで認証トークンがサポートされています。

* Text Translation API
* Speech Services: Speech to Text REST API
* Speech Services: Text to Speech REST API

>[!NOTE]
> QnA Maker でも Authorization ヘッダーが使用されますが、エンドポイント キーが必要です。 詳細については、[QnA Maker でナレッジ ベースから回答を取得する](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md)方法に関するページを参照してください。

>[!WARNING]
> 認証トークンをサポートするサービスは時間の経過と共に変わる可能性があります。この認証方法を使用する前には、サービスの API リファレンスを確認してください。

単一サービスのサブスクリプション キーとマルチサービスのサブスクリプション キーは、どちらも認証トークンと交換できます。 認証トークンは 10 分間有効です。

認証トークンは、`Authorization` ヘッダーとして要求に含まれます。 指定されるトークンの値の前には `Bearer` が付いている必要があります (例: `Bearer YOUR_AUTH_TOKEN`)。

### <a name="sample-requests"></a>サンプルの要求

単一サービスのサブスクリプション キーを認証トークンと交換するには、`https://api.cognitive.microsoft.com/sts/v1.0/issueToken` という URL を使用します。

```cURL
curl -v -X POST \
"https://api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

マルチサービスのサブスクリプション キーを使用している場合は、リージョンに固有のエンドポイントをトークンの交換に使用する必要があります。 マルチサービスのサブスクリプション キーを認証トークンと交換するには、`https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` という URL を使用します。

以下のマルチサービスのリージョンで、トークンの交換がサポートされています。

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

認証トークンを取得したら、各要求内でそれを `Authorization` ヘッダーとして渡す必要があります。 これは、Translator Text API 呼び出しのサンプルです。

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>関連項目

* [Cognitive Services とは](welcome.md)
* [Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [アカウントの作成](cognitive-services-apis-create-account.md)
