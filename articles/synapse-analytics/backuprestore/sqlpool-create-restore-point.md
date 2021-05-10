---
title: 専用 SQL プール用にユーザー定義の復元ポイントを作成する
description: Azure portal を使用して、Azure Synapse Analytics で専用 SQL プール用に新しいユーザー定義の復元ポイントを作成する方法について説明します。
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b695f6c7aabc21541fcc48efed54bbecd022f65a
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567929"
---
# <a name="user-defined-restore-points"></a>ユーザー定義の復元ポイント

この記事では、Azure portal を使用して、Azure Synapse Analytics で専用 SQL プール用に新しいユーザー定義の復元ポイントを作成する方法について説明します。

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Azure portal を使用してユーザー定義の復元ポイントを作成する

ユーザー定義の復元ポイントは、Azure portal を使用して作成することもできます。

1. [Azure portal](https://portal.azure.com/) アカウントにサインインします。

2. 復元ポイントを作成する専用 SQL プールに移動します。

3. 左側のウィンドウで **[概要]** を選択し、 **[+ 新しい復元ポイント]** を選択します。 [新しい復元ポイント] ボタンが有効でない場合は、専用 SQL プールが一時停止されていないことを確認します。

    ![新しい復元ポイント](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. ユーザー定義の復元ポイントの名前を指定して **[適用]** をクリックします。 ユーザー定義の復元ポイントには、7 日間の既定の保有期間があります。

    ![復元ポイントの名前](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>次のステップ

- [既存の専用 SQL プールを復元する](restore-sql-pool.md)

