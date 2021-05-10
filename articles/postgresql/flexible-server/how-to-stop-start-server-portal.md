---
title: 停止または起動する - Azure portal - Azure Database for PostgreSQL フレキシブル サーバー
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL で操作を停止または起動する方法について説明します。
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e28243f5919c355e6ad0d4998ff8388a56e3d0ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607370"
---
# <a name="stopstart-an-azure-database-for-postgresql---flexible-server-preview"></a>Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) を停止または起動する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーは現在プレビュー段階です。

この記事では、フレキシブル サーバーの停止と起動の手順が示されています。

## <a name="pre-requisites"></a>前提条件

このハウツー ガイドを完了するには、次が必要です。

-   Azure Database for PostgreSQL フレキシブル サーバーを所有している必要があります。

## <a name="stop-a-running-server"></a>実行中のサーバーを停止する

1.  [Azure portal](https://portal.azure.com/) で、停止するフレキシブル サーバーを選択します。

2.  **[概要]** ページのツール バーで **[停止]** ボタンをクリックします。

> [!NOTE]
> サーバーが停止した後、その他の管理操作はフレキシブル サーバーでは使用できなくなります。

停止したサーバーは 7 日後に自動的に起動されることに注意してください。 保留中のメンテナンスの更新は、サーバーが次回起動されるときに適用されます。

## <a name="start-a-stopped-server"></a>停止したサーバーを起動する

1.  [Azure portal](https://portal.azure.com/) で、起動するフレキシブル サーバーを選択します。

2.  **[概要]** ページのツール バーで **[起動]** ボタンをクリックします。

> [!NOTE]
> サーバーが起動した後、すべての管理操作をフレキシブル サーバーで使用できるようになります。

## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL フレキシブル サーバーのコンピューティングとストレージのオプション](./concepts-compute-storage.md)の詳細を確認します。
