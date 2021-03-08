---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "67181207"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>StorSimple 用 Windows PowerShell を使用して通常の修正プログラムをインストールするには
1. デバイスのシリアル コンソールに接続します。 詳細については、「[手順 1: シリアル コンソールに接続する](../articles/storsimple/storsimple-update-device.md#step1)」を参照してください。
2. シリアル コンソール メニューで、オプション 1 の **[フル アクセスによるログイン]** を選択します。 パスワードを入力します。 既定のパスワードは **Password1** です。
3. コマンド プロンプトに、次のコマンドを入力します。
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > このコマンドは通常の修正プログラムのみに適用されます。 このコマンドは 1 つのコントローラーでのみ実行しますが、両方のコントローラーが更新されます。
    > 更新プロセス中にコントローラーのフェールオーバーが行われても、システムの可用性または処理には影響しません。

4. メッセージが表示されたら、修正プログラムのファイルが含まれているネットワーク共有フォルダーへのパスを入力します。
5. 確認を求められます。 「 **Y** 」と入力して修正プログラムのインストールを続行します。

