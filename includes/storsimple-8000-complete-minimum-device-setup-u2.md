---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026780"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>StorSimple の最小デバイス セットアップを完了するには

   > [!NOTE]
   > 最小デバイス セットアップが完了した後でデバイス名を変更することはできません。
   
1. **[デバイス]** ブレードの表形式のデバイス一覧で、デバイスを選択してクリックします。 デバイスの状態は "**セットアップの準備が完了しました**" になっています。 **[デバイスの構成]** ブレードが開きます。

     ![StorSimple の最小限のデバイス セットアップのネットワーク インターフェイス](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. **[デバイスの構成]** ブレードで、次の操作を行います。
   
   1. デバイスの **[表示名]** を指定します。 既定のデバイス名は、デバイスのモデルやシリアル番号などの情報を反映します。 デバイスを管理するために、最大 64 文字の表示名を割り当てることができます。
   2. デバイスをデプロイする地理的な場所に基づいて **タイム ゾーン** を設定します。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。
   3. **[データ 0 の設定]** で、次の操作を行います。

       1. セットアップ ウィザードで構成したネットワーク設定 (IP、サブネット、ゲートウェイ) で有効化された DATA 0 ネットワーク インターフェイスが表示されます。 DATA 0 は自動的に iSCSI とクラウドにも有効化されています。

       2. コントローラー 0 とコントローラー 1 の固定 IP アドレスを指定します。 **コントローラーの固定 IP アドレスは、デバイス の IP アドレスによってアクセス可能なサブネット内で空き IP となっている必要があります。** DATA 0 インターフェイスが IPv4 に対して構成されている場合、固定 IP アドレスは IPv4 形式で指定する必要があります。 IPv6 構成でプレフィックスを指定した場合、これらのフィールドには固定 IP アドレスが自動的に入力されます。

            ![StorSimple の最小限のデバイス セットアップのネットワーク インターフェイス 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            コントローラー用の固定 IP アドレスは、デバイスに更新を提供したりガベージ コレクションを実行したりするために使用されます。 このため、固定 IP はルーティング可能でありインターネットに接続できる必要があります。 [Test-HcsmConnection][Test] コマンドレットを使用して、コントローラーの固定 IP がルーティング可能であることを確認できます。 次の例に、コントローラーの固定 IP がインターネットにルーティングされ、Microsoft Update サーバーにアクセスできることを示します。

            ![Test-HcsmConnection showing routable IPs](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. **[OK]** をクリックします。 デバイス構成が開始されます。 デバイス構成が完了すると、通知が表示されます。 **[デバイス]** ブレードで、デバイスの状態が **[オンライン]** に変わります。

    ![StorSimple の最小限のデバイス セットアップのネットワーク インターフェイス 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)