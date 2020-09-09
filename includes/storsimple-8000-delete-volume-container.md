---
title: インクルード ファイル
description: インクルード ファイル
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 30bbd06e36ed1e03caa391165a8abc275f1899a7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102788"
---
ボリューム コンテナーを削除するには、次の操作が必要です。
 - ボリューム コンテナー内のボリュームを削除します。 ボリューム コンテナーにボリュームが関連付けられている場合は、先にそれらのボリュームをオフラインにします。 「 [ボリュームをオフラインにする](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)」の手順に従ってください。 ボリュームがオフラインになったら、それらを削除できます。 
 - 関連付けられているバックアップ ポリシーおよびクラウド スナップショットを削除します。 ボリューム コンテナーに、関連付けられているバックアップ ポリシーおよびクラウド スナップショットが存在するかどうかを確認します。 存在する場合は、[バックアップ ポリシーを削除](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy)します。 これにより、クラウド スナップショットも削除されます。 
 
ボリューム コンテナー内に関連付けられているボリューム、バックアップ ポリシー、およびクラウド スナップショットが存在していなければ、そのボリューム コンテナーは削除することができます。 ボリューム コンテナーを削除するには、次の手順を実行します。

#### <a name="to-delete-a-volume-container"></a>ボリューム コンテナーを削除するには
1. StorSimple デバイス マネージャー サービスに移動し、 **[デバイス]** をクリックします。 デバイスを選択してクリックし、 **[設定]、[管理]、[ボリューム コンテナー]** の順にクリックします。

    ![[ボリューム コンテナー] ブレード](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. ボリューム コンテナーの表形式の一覧で、削除するボリューム コンテナーを選択し、 **[...]** を右クリックして、 **[削除]** を選択します。

    ![ボリューム コンテナーの削除](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. ボリューム コンテナーに関連付けられているボリューム、バックアップ ポリシー、クラウド スナップショットがない場合、ボリューム コンテナーを削除できます。 ボリューム コンテナーの削除による影響を示すメッセージが表示されたら、内容を確認し、チェック ボックスをオンにします。 **[削除]** をクリックすると、ボリューム コンテナーが削除されます。

    ![削除の確定](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

削除されたボリューム コンテナーを反映するようにボリューム コンテナーの一覧が更新されます。

![ボリューム コンテナー ページのスクリーンショット。 ボリューム コンテナーの表形式の一覧に、削除されたコンテナーが含まれなくなりました。](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


