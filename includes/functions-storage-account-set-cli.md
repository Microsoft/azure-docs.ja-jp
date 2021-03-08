---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "78262066"
---
### <a name="set-the-storage-account-connection"></a>ストレージ アカウントの接続を設定する

local.settings.json ファイルを開き、ストレージ アカウントの接続文字列である `AzureWebJobsStorage` の値をコピーします。 次の Bash コマンドを使用して、`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定します。

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

`AZURE_STORAGE_CONNECTION_STRING` 環境変数に接続文字列を設定すると、毎回認証情報を指定しなくても、ストレージ アカウントにアクセスできます。