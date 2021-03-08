---
title: Face サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、Face での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 8c6f4f7db312355b719deb434bf6a46fa55eec9d
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524093"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face サービスによる保存データの暗号化

Face サービスでは、クラウドに永続化されるときにデータが自動的に暗号化されます。 Face サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Face サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Face サービスでの CMK の使用が承認されたら、新しい Face リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Face リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>次のステップ

* CMK がサポートされているサービスの完全な一覧については、[Cognitive Services 用のカスタマー マネージド キー](../encryption/cognitive-services-encryption-keys-portal.md)に関するページを参照してください
* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)