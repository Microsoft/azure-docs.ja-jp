---
author: cynthn
ms.service: azure
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: c2ed33aea77b5478e8d17f6bd0213ef3e778b806
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "67181512"
---
## <a name="transfer-local-files-to-cloud-shell"></a>Cloud Shell にローカル ファイルを転送する
`clouddrive` ディレクトリは、Azure Portal ストレージ ブレードと同期します。 このブレードを使用して、ファイル共有との間でローカル ファイルを移動できます。 Cloud Shell 内からファイルを更新すると、ブレードを更新するときにファイル ストレージ GUI に反映されます。

### <a name="download-files"></a>ファイルのダウンロード

![ローカル ファイルの一覧](../articles/cloud-shell/media/persisting-shell-storage/download.png)
1. Azure Portal で、マウントされているファイル共有に移動します。
2. 対象のファイルを選択します。
3. **[ダウンロード]** ボタンを選択します。

### <a name="upload-files"></a>ファイルをアップロードする

![更新されるローカル ファイル](../articles/cloud-shell/media/persisting-shell-storage/upload.png)
1. マウントされているファイル共有に移動します。
2. **[アップロード]** ボタンを選択します。
3. アップロードする 1 つまたは複数のファイルを選択します。
4. アップロードを確認します。

Cloud Shell の `clouddrive` ディレクトリでファイルにアクセスできるようになったことがわかります。