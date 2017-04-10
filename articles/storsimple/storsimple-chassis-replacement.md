---
title: "StorSimple 8000 シリーズ デバイスのシャーシを交換する | Microsoft Docs"
description: "StorSimple プライマリ エンクロージャまたは EBOD エンクロージャのシャーシを取り外して交換する方法について説明します。"
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 537659ed-4c46-49c1-b1e4-186262f2542d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: eb56cae77722268f42e5126c45ad2878af7db94a
ms.openlocfilehash: 5295c5dd039b1d4746ebaaf90372932e4c3e7c26


---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>StorSimple デバイスのシャーシを交換する
## <a name="overview"></a>概要
このチュートリアルでは、StorSimple 8000 シリーズ デバイスのシャーシを取り外して交換する方法について説明します。 StorSimple 8100 モデルはシングル エンクロージャ デバイス (1 つのシャーシ) で、8600 はデュアル エンクロージャ デバイス (2 つのシャーシ) です。 8600 モデルでは、デバイスで故障する可能性があるシャーシが 2 つあります。主エンクロージャのシャーシと EBOD エンクロージャのシャーシです。

どちらの場合も、Microsoft から出荷される交換用のシャーシは空です。 電源および冷却モジュール (PCM)、コントローラー モジュール、ソリッド ステート ディスク ドライブ (SSD)、ハード ディスク ドライブ (HDD)、または EBOD モジュールは付属していません。

> [!IMPORTANT]
> シャーシを取り外して交換する前に、「 [StorSimple ハードウェア コンポーネントの交換](storsimple-hardware-component-replacement.md)」の安全に関する情報を再確認してください。
> 
> 

## <a name="remove-the-chassis"></a>シャーシを取り外す
次の手順を実行して、StorSimple デバイスのシャーシを取り外します。

#### <a name="to-remove-a-chassis"></a>シャーシを取り外すには
1. StorSimple デバイスがシャットダウンされていて、すべての電源から切断されていることを確認します。
2. 該当する場合は、すべてのネットワーク ケーブルと SAS ケーブルを取り外します。
3. ラックからユニットを取り外します。
4. 各ドライブを取り外し、取り外したスロットを記録しておきます。 詳細については、「 [ディスク ドライブを取り外す](storsimple-disk-drive-replacement.md#remove-the-disk-drive)」を参照してください。
5. EBOD エンクロージャで (EBOD エンクロージャのシャーシが故障した場合)、EBOD コントローラー モジュールを取り外します。 詳細については、「 [EBOD コントローラーを取り外す](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller)」を参照してください。 
   
    主エンクロージャで (主エンクロージャのシャーシが故障した場合)、コントローラーを取り外し、取り外したスロットを記録しておきます。 詳細については、「 [コントローラーを取り外す](storsimple-controller-replacement.md#remove-a-controller)」を参照してください。

## <a name="install-the-chassis"></a>シャーシを取り付ける
次の手順を実行して、StorSimple デバイスのシャーシを取り付けます。

#### <a name="to-install-a-chassis"></a>シャーシを取り付けるには
1. シャーシをラックに取り付けます。 詳細については、「[StorSimple 8100 デバイスをラックに取り付ける](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device)」または「[StorSimple 8600 デバイスをラックに取り付ける](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device)」を参照してください。
2. シャーシをラックに取り付けた後、コントローラー モジュールを前に取り付けられていたのと同じ位置に取り付けます。
3. ドライブを以前と同じ位置およびスロットに取り付けます。
   
   > [!NOTE]
   > 最初に SSD をスロットに取り付けて、その後で HDD を取り付けることをお勧めします。
   > 
   > 
4. デバイスをラックに取り付け、コンポーネントを取り付けた状態で、デバイスを適切な電源に接続し、デバイスの電源を入れます。 詳細については、「[StorSimple 8100 デバイスにケーブルを接続する](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)」または「[StorSimple 8600 デバイスにケーブルを接続する](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)」を参照してください。

## <a name="next-steps"></a>次のステップ
「 [StorSimple ハードウェア コンポーネントの交換](storsimple-hardware-component-replacement.md)」の説明を参照してください。




<!--HONumber=Jan17_HO4-->


