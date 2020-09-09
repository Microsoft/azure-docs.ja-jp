---
title: Azure Lab Services でラボに他の所有者を追加する方法
description: この記事では、Azure Lab Services で管理者がユーザーをラボに所有者として追加する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6671a3070dae672769eecf59d614d3b75455ef5a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445867"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure Lab Services で既存のラボに所有者を追加する方法
この記事では、管理者が既存のラボに所有者を追加する方法について説明します。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>ラボ アカウントの閲覧者ロールにユーザーを追加する
既存のラボに追加の所有者としてユーザーを追加するには、まず、ラボ アカウントに対する**読み取り** アクセス許可をユーザーに付与する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインする
2. 左側のメニューから、 **[すべてのサービス]** を選択します。 **[Lab Services]** を探して選択します。
3. 一覧からお使いの**ラボ アカウント**を選択します。 
2. **[ラボ アカウント]** ページで、左側のメニューから **[アクセス制御 (IAM)]** を選択します。 
2. **[アクセス制御 (IAM)]** ページで、ツール バーの **[追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。

    ![ラボ アカウントのロールの割り当て ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. **[ロールの割り当ての追加]** ページで、次の手順のようにします。 
    1. **[ロール]** で **[閲覧者]** を選択します。 
    2. ユーザーを選択します。 
    3. **[保存]** を選択します。 

        ![ラボ アカウントの閲覧者ロールにユーザーを追加する ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>ラボの所有者ロールにユーザーを追加する

1. **[ラボ アカウント]** ページに戻り、左側のメニューの **[すべてのラボ]** を選択します。
2. 所有者としてユーザーを追加する**ラボ**を選択します。 
    
    ![ラボを選ぶ ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. **[ラボ]** ページで、左側のメニューから **[アクセス制御 (IAM)]** を選択します。
4. **[アクセス制御 (IAM)]** ページで、ツール バーの **[追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。
5. **[ロールの割り当ての追加]** ページで、次の手順のようにします。 
    1. **ロール**として **[所有者]** を選択します。 
    2. ユーザーを選択します。 
    3. **[保存]** を選択します。 

## <a name="next-steps"></a>次のステップ
[Lab Services ポータル](https://labs.azure.com)にログインするときに、ユーザーにラボが表示されることを確認します。