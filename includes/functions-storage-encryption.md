---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 0bc5977a581006dc760c0435f9d68371ced7e4cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "83648784"
---
Azure Storage は、保存されているストレージ アカウント内のすべてのデータを暗号化します。 詳細については、「[保存データ向け Azure ストレージの暗号化](../articles/storage/common/storage-service-encryption.md)」をご覧ください。

規定では、データは Microsoft のマネージド キーで暗号化されます。 暗号化キーをさらに制御するために、BLOB とファイル データの暗号化に使用する目的で、顧客が管理するキーを提供できます。 Functions からストレージ アカウントにアクセスできるように、これらのキーは Azure Key Vault 内に置かれている必要があります。 詳細については、「[カスタマー マネージド キーを使用した保存時の暗号化](../articles/azure-functions/configure-encrypt-at-rest-using-cmk.md)」をご覧ください。  