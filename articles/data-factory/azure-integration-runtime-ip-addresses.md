---
title: Azure Integration Runtime の IP アドレス
description: データ ストアへのネットワーク アクセスをセキュリティで保護するためにファイアウォールを適切に構成するには、どの IP アドレスからの受信トラフィックを許可する必要があるかについて説明します。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 8b1fd7b1962a4d46d38287e5faf8f1ba4cbb40bd
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423633"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Azure Integration Runtime の IP アドレス

Azure Integration Runtime によって使用される IP アドレスは、Azure Integration Runtime が配置されているリージョンによって異なります。 同じリージョン内の "*すべての*" Azure Integration Runtime は、同じ IP アドレス範囲を使用します。

> [!IMPORTANT]  
> データ フローでは、これらの IP は現在使用されていません。 
>
> これらの IP 範囲は、データ移動、パイプライン、および外部アクティビティの実行に使用できます。 これらの IP 範囲は、Azure Integration Runtime からの受信アクセスについて、データストア/ネットワーク セキュリティ グループ (NSG)/ファイアウォールでホワイトリストに登録するために使用できます。 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime の IP アドレス: 特定のリージョン

リソースが配置されている特定の Azure リージョンの Azure Integration Runtime 用として一覧に示されている IP アドレスからのトラフィックを許可します。

|                | リージョン              | IP アドレス                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Asia           | 東アジア           | 20.189.104.128/25、 </br>20.189.106.0/26、 </br>13.75.39.112/28 |
| &nbsp;         | 東南アジア      | 20.43.128.128/25、 </br>20.43.130.0/26、 </br>40.78.236.176/28 |
| オーストラリア      | オーストラリア東部      | 20.37.193.0/25、</br>20.37.193.128/26、</br>13.70.74.144/28    |
| &nbsp;         | オーストラリア南東部 | 20.42.225.0/25、</br>20.42.225.128/26、</br>13.77.53.160/28    |
| ブラジル         | ブラジル南部        | 191.235.224.128/25、</br>191.235.225.0/26、</br>191.233.205.160/28 |
| Canada         | カナダ中部      | 52.228.80.128/25、</br>52.228.81.0/26、</br>13.71.175.80/28    |
| ヨーロッパ         | 北ヨーロッパ        | 20.38.82.0/23、</br>20.38.80.192/26、</br>13.69.230.96/28      |
| &nbsp;         | 西ヨーロッパ         | 40.74.26.0/23、</br>40.74.24.192/26、</br>13.69.67.192/28      |
| フランス         | フランス中部      | 20.43.40.128/25、</br>20.43.41.0/26、</br>40.79.132.112/28     |
| インド          | インド中部       | 52.140.104.128/25、</br>52.140.105.0/26、</br>20.43.121.48/28  |
| 日本          | 東日本          | 20.43.64.128/25、</br>20.43.65.0/26、</br>13.78.109.192/28     |
| 韓国          | 韓国中部       | 20.41.64.128/25、</br>20.41.65.0/26、</br>52.231.20.64/28      |
| 南アフリカ   | 南アフリカ北部  | 102.133.124.104/29、</br>102.133.216.128/25、</br>102.133.217.0/26 |
| イギリス | 英国南部            | 51.104.24.128/25、</br>51.104.25.0/26、</br>51.104.9.32/28     |
| United States  | 米国中部          | 20.37.154.0/23、</br>20.37.156.0/26、</br>20.44.10.64/28       |
|                | 米国東部             | 20.42.2.0/23、</br>20.42.4.0/26、</br>40.71.14.32/28           |
|                | 米国東部 2            | 20.41.2.0/23、</br>20.41.4.0/26、</br>20.44.17.80/28           |
|                | 米国東部 2 EUAP      | 20.39.8.128/26、</br>20.39.8.96/27、</br>40.75.35.144/28       |
|                | 米国中北部    | 40.80.185.0/25、</br>40.80.185.128/26、</br>52.162.111.48/28   |
|                | 米国中南部    | 40.119.9.0/25、</br>40.119.9.128/26、</br>13.73.244.32/28      |
|                | 米国中西部     | 52.150.137.128/25、</br>52.150.136.192/26、</br>13.71.199.0/28 |
|                | 米国西部             | 40.82.250.0/23、</br>40.82.249.64/26、</br>13.86.219.208/28    |
|                | 米国西部 2            | 20.42.132.0/23、</br>20.42.129.64/26、</br>13.66.143.128/28    |
|                | US Gov バージニア州     | 52.127.45.96/28、</br>52.127.48.128/25、</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Azure Storage に関する既知の問題

* Azure Storage アカウントに接続しているとき、IP ネットワーク ルールは、ストレージ アカウントと同じリージョン内の Azure Integration Runtime から送信された要求に対して効果がありません。 詳細については、[こちらの記事を参照](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)してください。 

  代わりに、[Azure Storage に接続しているときは、信頼できるサービス](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)を使用することをお勧めします。 

## <a name="next-steps"></a>次のステップ

* [Azure Data Factory におけるデータ移動のセキュリティに関する考慮事項](data-movement-security-considerations.md)
