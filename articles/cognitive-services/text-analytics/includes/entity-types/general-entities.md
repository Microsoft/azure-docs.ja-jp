---
title: 名前付き一般エンティティ
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: ba516a548fc8385ca86526a7f2dd082b27e53208
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211448"
---
## <a name="general-entity-types"></a>一般エンティティの種類:

### <a name="person"></a>Person

テキスト内の人の名前を認識します。

言語:
* パブリックプレビュー: `Arabic`、`Czech`、`Chinese-Simplified`、`Danish`、`Dutch`、`English`、`Finnish`、`French`、`German`、`Hungarian`、`Italian`、`Japanese`、`Korean`、`Norwegian (Bokmål)`、`Polish`、`Portuguese (Portugal)`、`Portuguese (Brazil)`、`Russian`、`Spanish`、`Swedish`、 および `Turkish`

| サブタイプ名 | 説明                                                      | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------------------------------|---------------------------------------|
| 該当なし          | 認識された人名 (例: `Bill Gates`、`Marie Curie`) | `2019-10-01`                          | 

### <a name="persontype"></a>PersonType
人物の職種または職務。

言語:
* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                                                | 次のモデル バージョン以降で使用できます |
|--------------|--------------------------------------------------------------------------------------------|----------------------------------------|
| 該当なし          | 職種 (例: `civil engineer`、`salesperson`、`chef`、`librarian`、`nursing aide`) | `2020-02-01`                           |

### <a name="location"></a>Location

自然および人口のランドマーク、構造、地物、地政学的実体。

言語:

* パブリックプレビュー: `Arabic`、`Czech`、`Chinese-Simplified`、`Danish`、`Dutch`、`English`、`Finnish`、`French`、`German`、`Hungarian`、`Italian`、`Japanese`、`Korean`、`Norwegian (Bokmål)`、`Polish`、`Portuguese (Portugal)`、`Portuguese (Brazil)`、`Russian`、`Spanish`、`Swedish`、 および `Turkish`

| サブタイプ名              | 説明                                                                              | 次のモデル バージョン以降で使用できます |
|---------------------------|------------------------------------------------------------------------------------------|----------------------------------------|
| 該当なし                       | 場所 (例、`Atlantic Ocean`、`library`、`Eiffel Tower`、`Statue of Liberty`)  | `2019-10-01`                           |
| 地政学的実体 (GPE) - 英語のみ| 市区町村、国、都道府県 (例: `Seattle`、`Pennsylvania`、`South Africa`、`Tokyo`) | `2020-02-01`                           |

### <a name="organization"></a>Organization  

認識された組織、企業、機関、およびその他の集団。 たとえば、企業、政治団体、音楽バンド、スポーツ クラブ、政府機関、および公的機関などです。 国籍と宗教は、このエンティティ型には含まれていません。 

言語: 

* パブリックプレビュー: `Arabic`、`Czech`、`Chinese-Simplified`、`Danish`、`Dutch`、`English`、`Finnish`、`French`、`German`、`Hungarian`、`Italian`、`Japanese`、`Korean`、`Norwegian (Bokmål)`、`Polish`、`Portuguese (Portugal)`、`Portuguese (Brazil)`、`Russian`、`Spanish`、`Swedish`、 および `Turkish`

| サブタイプ名 | 説明                                                                                             | 次のモデル バージョン以降で使用できます |
|--------------|---------------------------------------------------------------------------------------------------------|----------------------------------------|
| 該当なし          | 組織 (例: `Microsoft`、`NASA`、`National Oceanic and Atmospheric Administration`、`VOA`) | `2019-10-01`                           |

### <a name="event"></a>Event  

歴史上の出来事、社会的事件、自然発生的な事象。  

言語: 

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                            | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------------------------------------|----------------------------------------|
| 該当なし          | 出来事 (`wedding`、`hurricane`、`car accident`、`solar eclipse`、`American Revolution` など) | `2020-02-01`                           |

### <a name="product"></a>Product  

さまざまなカテゴリの物。  

言語: 

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                                        | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------------------------------------------------|----------------------------------------|
| 該当なし          | 例: `Microsoft Surface laptop`、`sunglasses`、`motorcycle`、`bag`、`Xbox` | `2020-02-01`                           |
| コンピューティング    | `Azure Cosmos DB`, `Azure Kubernetes Service`                                     | `2020-02-01`                           |

### <a name="skill"></a>スキル  

能力や得意分野を表すもの。  

言語: 

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                                                 | 次のモデル バージョン以降で使用できます |
|--------------|-----------------------------------------------------------------------------|----------------------------------------|
| 該当なし          | `nursing`、`data mining`、`linguistics`、`critical thinking`、`photography` | `2020-02-01`                           |

### <a name="phone-number"></a>電話番号

電話番号 (米国の電話番号のみ)。 

言語:

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                    | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------------|----------------------------------------|
| 該当なし          | 米国の電話番号 (例: `(312) 555-0176`) | `2019-10-01`                           |

### <a name="email"></a>Email

電子メール アドレス。 

言語:

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                      | 次のモデル バージョン以降で使用できます |
|--------------|--------------------------------------------------|----------------------------------------|
| 該当なし          | メール アドレス (例: `support@contoso.com`) | `2019-10-01`                           |

### <a name="url"></a>URL

インターネット URL です。

言語:

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                                          | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------------------|----------------------------------------|
| 該当なし          | Web サイトへの URL (例: `https://www.bing.com`) | `2019-10-01`                           |

### <a name="ip-address"></a>IP アドレス

インターネット プロトコル アドレス

言語:

* パブリック プレビュー: `English`

| サブタイプ名 | 説明                              | 次のモデル バージョン以降で使用できます |
|--------------|------------------------------------------|----------------------------------------|
| 該当なし          | ネットワーク アドレス (例: `10.0.0.101`) | `2019-10-01`                           |

###  <a name="datetime"></a>DateTime

日時エンティティ。 

* 次のモデル バージョン以降で使用できます: `2019-10-01`

言語:

* パブリック プレビュー: `Chinese-Simplified`、`English`、`French`、`German`、および `Spanish`

| サブタイプ名    | 例                     |
|-------------|------------------------------|
| 該当なし         | `6:30PM February 4, 2012`, `4/1/2011 2:45`                   |
| Date  | `May 2nd, 2017`, `05/02/2017`       |
| Time     | `8:15`, `6AM`              |
| DateRange    | `August 2nd to August 5th`         |
| TimeRange   | `4-6PM`, `10:00AM to Noon`          |
| Duration | `2.5 minutes`, `one and a half hours`         |
| オン | `every Saturday`         |

###  <a name="quantity"></a>Quantity

数値と数量です。 

* 次のモデル バージョン以降で使用できます: `2019-10-01`

言語:

* パブリック プレビュー: `Chinese-Simplified`、`English`、`French`、`German`、および `Spanish`

| サブタイプ名    | 例                     |
|-------------|------------------------------|
| Number         | `6`, `six`                   |
| [パーセント]  | `50%`, `fifty percent`       |
| Ordinal     | `2nd`, `second`              |
| Age         | `90 day old`, `30 years old` |
| Currency    | `$10.99`, `€30.00`           |
| Dimension   | `10 miles`, `40 cm`          |
| 気温 | `32 degrees`, `10°C`         |
