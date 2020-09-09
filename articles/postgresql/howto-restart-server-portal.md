---
title: サーバーを再起動する - Azure portal - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure portal を使用して Azure Database for PostgreSQL - 単一サーバーを再起動する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4bd5b2d3715376aaca689c4589c3aab41a78f514
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120910"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure portal を使用して Azure Database for PostgreSQL - 単一サーバーを再起動する
このトピックでは、Azure Database for PostgreSQL サーバーを再起動する方法について説明します。 メンテナンス上の理由でサーバーの再起動が必要な場合があります。これを行うと、サーバーが操作を実行しているときに短時間の停止が発生します。

サービスがビジー状態の場合、サーバーの再起動はブロックされます。 たとえば、仮想コアのスケーリングなどの前に要求した操作がサービスで処理中である場合があります。
 
再起動を完了するために必要な時間は、PostgreSQL の回復プロセスに依存しています。 再起動の時間を短縮するために、再起動の実行前にサーバー上で発生しているアクティビティの量を最小限に抑えることをお勧めします。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドを完了するには、次が必要です。
- [Azure Database for PostgreSQL サーバー](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>サーバーの再起動を実行する

次の手順で PostgreSQL サーバーを再起動します。

1. [Azure portal](https://portal.azure.com/) で、ご利用の Azure Database for PostgreSQL サーバーを選択します。

2. サーバーの **[概要]** ページのツール バーで、 **[再起動]** をクリックします。

   ![Azure Database for PostgreSQL - 概要 - [再起動] ボタン](./media/howto-restart-server-portal/2-server.png)

3. **[はい]** をクリックして、サーバーを再起動することを確認します。

   ![Azure Database for PostgreSQL - 再起動の確認](./media/howto-restart-server-portal/3-restart-confirm.png)

4. サーバーの状態が [再起動中] に変わることを確認します。

   ![Azure Database for PostgreSQL - 再起動状態](./media/howto-restart-server-portal/4-restarting-status.png)

5. サーバーの再起動が成功したことを確認します。

   ![Azure Database for PostgreSQL - 再起動成功](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>次のステップ

[Azure Database for PostgreSQL でパラメーターを設定する方法](howto-configure-server-parameters-using-portal.md)について確認する