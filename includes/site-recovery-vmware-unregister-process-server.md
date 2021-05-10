---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019194"
---
特定の状況に応じた手順に従います。

### <a name="unregister-a-connected-process-server"></a>接続されているプロセス サーバーの登録を解除する

1. 管理者としてプロセス サーバーにリモート接続を確立します。
2. **[コントロール パネル]** で、 **[プログラム]、[プログラムのアンインストール]** の順に開きます。
3. プログラム **Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー** をアンインストールします。
4. プログラム **Microsoft Azure Site Recovery Configuration/Process Server (Microsoft Azure Site Recovery 構成/プロセス サーバー)** をアンインストールします。
5. 手順 3 と 4 のプログラムがアンインストールされたら、**Microsoft Azure Site Recovery Configuration/Process Server Dependencies (Microsoft Azure Site Recovery 構成/プロセス サーバーの依存関係)** をアンインストールします。

### <a name="unregister-a-disconnected-process-server"></a>接続されていないプロセス サーバーの登録を解除する

プロセス サーバーがインストールされているマシンを回復する方法がない場合にのみ、以下の手順を使用してください。

1. 管理者として構成サーバーにサインインします。
2. 管理コマンド プロンプトを開き、`%ProgramData%\ASR\home\svsystems\bin` を参照します。
3. 以下のコマンドを実行して、1 つ以上のプロセス サーバーの一覧を取得します。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. No: プロセス サーバーのシリアル番号。
    - IP/Name:プロセス サーバーを実行しているマシンの IP アドレスと名前。
    - Heartbeat:プロセス サーバー マシンからの最後のハートビート。
    ![プロセス サーバーに関する情報のプレーンテキストによる表示と、上のサーバーから登録解除するものを 1 つ選択してくださいというテキストを示すスクリーンショット。(media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. 登録を解除するプロセス サーバーのシリアル番号を指定します。
5. プロセス サーバーの登録解除により、その詳細のすべてがシステムから削除され、次のメッセージが表示されます。 **<サーバー名 (サーバーの IP アドレス) の登録が正常に解除されました**

