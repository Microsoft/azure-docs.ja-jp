---
title: 言語サポート - Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: Bing Custom Search API でサポートされる言語と地域の一覧。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 004bd973651d5903db4254a8883be2c8a83d9b38
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310582"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Bing Custom Search API の言語と地域のサポート

Bing Custom Search API では 30 を超える国/地域がサポートされ、その多くで複数の言語が使用されています。

必須ではありませんが、要求では [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) クエリ パラメーターを指定するべきです。このパラメーターは、結果取得元の市場を示します。 省略可能なクエリ パラメーターの一覧については、「[クエリ パラメーター](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)」をご覧ください。

`cc` クエリ パラメーターを使用して国/地域を指定できます。 国/地域を指定する場合は、`Accept-Language` ヘッダーを使用して 1 つ以上の言語コードも指定する必要があります。 サポートされる言語は国/地域によって異なります。これらは、「**市場**」の表に国/地域ごとに示されています。

`Accept-Language` ヘッダーと `setLang` クエリ パラメーターは相互に排他的なので、両方指定することはできません。 詳細については、「[Accept-language](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)」を参照してください。

## <a name="countriesregions"></a>国/リージョン

|国/リージョン|コード|
|-------|----|
|アルゼンチン|AR|
|オーストラリア|AU|
|オーストリア|AT|
|ベルギー|BE|
|ブラジル|BR|
|Canada|CA|
|チリ|CL|
|デンマーク|DK|
|フィンランド|FI|
|フランス|FR|
|ドイツ|DE|
|香港特別行政区|HK|
|インド|IN|
|インドネシア|id|
|イタリア|IT|
|日本|JP|
|韓国|KR|
|マレーシア|MY|
|メキシコ|MX|
|オランダ|NL|
|ニュージーランド|NZ|
|ノルウェー|NO|
|中国|CN|
|ポーランド|PL|
|ポルトガル|PT|
|フィリピン|PH|
|ロシア|RU|
|サウジアラビア|SA|
|南アフリカ|ZA|
|スペイン|ES|
|スウェーデン|SE|
|スイス|CH|
|台湾|TW|
|トルコ|TR|
|イギリス|GB|
|United States|US|


## <a name="markets"></a>市場

|国/リージョン|Language|市場コード|
|-------|--------|-----------|
|アルゼンチン|スペイン語|es-AR|
|オーストラリア|英語|en-AU|
|オーストリア|ドイツ語|de-AT|
|ベルギー|オランダ語|nl-BE|
|ベルギー|フランス語|fr-BE|
|ブラジル|Portuguese|pt-BR|
|Canada|英語|en-CA|
|Canada|フランス語|fr-CA|
|チリ|スペイン語|es-CL|
|デンマーク|デンマーク語|da-DK|
|フィンランド|フィンランド語|fi-FI|
|フランス|フランス語|fr-FR|
|ドイツ|ドイツ語|de-DE|
|香港特別行政区|繁体字中国語|zh-HK|
|インド|英語|en-IN|
|インドネシア|英語|en-ID|
|イタリア|イタリア語|it-IT|
|日本|日本語|ja-JP|
|韓国|韓国語|ko-KR|
|マレーシア|英語|en-MY|
|メキシコ|スペイン語|es-MX|
|オランダ|オランダ語|nl-NL|
|ニュージーランド|英語|en-NZ|
|ノルウェー|ノルウェー語|no-NO|
|中国|Chinese|zh-CN|
|ポーランド|ポーランド語|pl-PL|
|ポルトガル|Portuguese|pt-PT|
|フィリピン|英語|en-PH|
|ロシア|ロシア語|ru-RU|
|サウジアラビア|アラビア語|ar-SA|
|南アフリカ|英語|en-ZA|
|スペイン|スペイン語|es-ES|
|スウェーデン|スウェーデン語|sv-SE|
|スイス|フランス語|fr-CH|
|スイス|ドイツ語|de-CH|
|台湾|繁体字中国語|zh-TW|
|トルコ|トルコ語|tr-TR|
|イギリス|英語|en-GB|
|United States|英語|ja-JP|
|United States|スペイン語|es-US|
