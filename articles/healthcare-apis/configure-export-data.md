---
title: Azure API for FHIR でエクスポート設定を構成する
description: この記事では、Azure API for FHIR でエクスポート設定を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: e4adceea5c2cd2a36d7a867ca9b9d2ad7c33c155
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91529985"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>エクスポート設定を構成してストレージ アカウントを設定する

Azure API for FHIR では、Azure API for FHIR アカウントからストレージ アカウントにデータをエクスポートできるようにする $export コマンドがサポートされています。

Azure API for FHIR でエクスポートを構成するには、次の 3 つの手順があります。

1. Azure API for FHIR サービスのマネージド ID を有効にする
2. Azure ストレージ アカウントを作成し (まだ実行していない場合)、Azure API for FHIR へのアクセス許可をストレージ アカウントに割り当てる
3. Azure API for FHIR でストレージ アカウントをエクスポート ストレージ アカウントとして選択する

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR のマネージド ID の有効化

Azure API for FHIR のエクスポートを構成する最初の手順は、サービスに対するシステム全体のマネージド ID を有効にすることです。 Azure のマネージド ID の詳細については、[こちら](../active-directory/managed-identities-azure-resources/overview.md)で確認できます。

これを行うには、Azure API for FHIR サービスに移動し、[ID] ブレードを選択します。 状態を [オン] に変更すると、Azure API for FHIR サービスでマネージド ID が有効になります。

![マネージド ID の有効化](media/export-data/fhir-mi-enabled.png)

これで、次の手順に進んで、ストレージ アカウントの作成とサービスへのアクセス許可の割り当てを実行できます。

## <a name="adding-permission-to-storage-account"></a>ストレージ アカウントへのアクセス許可の追加

エクスポートの次の手順は、ストレージ アカウントに書き込むためのアクセス許可を Azure API for FHIR サービスに割り当てることです。

ストレージ アカウントを作成したら、ストレージ アカウントの [アクセス制御 (IAM)] ブレードに移動し、[ロールの割り当ての追加] を選択します。

![ロールの割り当てのエクスポート](media/export-data/fhir-export-role-assignment.png)

ここでは、ストレージ BLOB データ共同作成者というロールをサービス名に追加します。

![ロールの追加](media/export-data/fhir-export-role-add.png)

これで、次の手順で、$export の既定のストレージ アカウントとして Azure API for FHIR のストレージ アカウントを選択できるようになりました。

## <a name="selecting-the-storage-account-for-export"></a>$export 用のストレージ アカウントの選択

最後の手順は、Azure API for FHIR によるデータのエクスポート先として使用する Azure ストレージ アカウントを割り当てることです。 これを行うには、Azure portal で Azure API for FHIR サービスの [統合] ブレードに移動し、ストレージ アカウントを選択します。

![FHIR エクスポート ストレージ](media/export-data/fhir-export-storage.png)

その後、$export コマンドを使用してデータをエクスポートできます。

>[!div class="nextstepaction"]
>[[追加設定]](azure-api-for-fhir-additional-settings.md)
