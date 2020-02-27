---
title: Azure Lab Services でラボの使用状況を追跡する | Microsoft Docs
description: このチュートリアルでは、ラボの使用状況をその作成者/所有者として追跡します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 55c7e2f30324fe7878e38f1129c63c6e0c51a75f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134809"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>チュートリアル:Azure Lab Services でラボの使用状況を追跡する
このチュートリアルでは、ラボの作成者/所有者がラボの使用状況を追跡する方法について説明します。

このチュートリアルでは、次のアクションを実行します。

> [!div class="checklist"]
> * ラボに登録されているユーザーを確認する
> * ラボの VM の使用状況を確認する
> * 学生の VM を管理する 


## <a name="view-registered-users"></a>登録されているユーザーを表示する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 
2. **[サインイン]** を選択して、資格情報を入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。
3. **[My labs]\(自分のラボ\)** ページで、使用状況を追跡するラボを選択します。 
4. 左側のメニューまたは **[ユーザー]** タイルの **[ユーザー]** を選択します。 ラボに登録されている学生が表示されます。  

    ![登録されているユーザー](../media/tutorial-track-usage/registered-users.png)

    ラボのユーザーの追加と管理の詳細については、「[ラボ ユーザーを追加および管理する](how-to-configure-student-usage.md)」を参照してください。

## <a name="view-the-usage-of-vms"></a>VM の使用状況を表示する

1. 左側のメニューから **[仮想マシン]** を選択します。 
2. VMの状態と VM が実行されている時間数が表示されていることを確認します。 最後の列に表示される使用時間には、ラボの所有者が学生の VM に関して費やした時間はカウントされません。 

    ![VM の使用状況](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>学生の VM を管理する 
このページでは、 **[状態]** 列またはツール バーのコントロールを使用して、学生の VM を起動、停止、またはリセットできます。

![VM のコントロール](../media/tutorial-track-usage/vm-controls.png)

ラボの仮想マシン プールの管理の詳細については、「[仮想マシン プールを設定および管理する](how-to-set-virtual-machine-passwords.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
クラスルーム ラボの詳細については、[攻略ガイド](how-to-manage-lab-accounts.md)の各記事を参照してください。
