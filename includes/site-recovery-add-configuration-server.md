---
title: インクルード ファイル
description: インクルード ファイル
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: f699ffe6d5a91e8ce3ae90c7e12249bbad0fff3e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500430"
---
1. 統合セットアップ インストール ファイルを実行します。
2. **[開始する前に]** で **[Install the configuration server and process server]\(構成サーバーとプロセス サーバーをインストールする\)** を選択します。

    ![統合セットアップの [開始する前に] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、 **[同意する]** をクリックして MySQL をダウンロードし、インストールします。

    ![統合セットアップの [Third Party Software License]\(サードパーティ製ソフトウェア ライセンス\) 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. **[登録]** で、コンテナーからダウンロードした登録キーを選択します。

    ![統合セットアップの [登録] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。 必要な URL へのアクセスが許可されていることを確認してください。

    - マシンで現在セットアップされているプロキシを使用して接続する場合は、 **[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。
    - プロバイダーから直接接続するように指定する場合は、 **[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
    - 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、 **[Connect with custom proxy setting]\(カスタム プロキシ設定を使用して接続する\)** を選択して、アドレス、ポート、資格情報を指定します。
     ![統合セットアップの [インターネット設定] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。 **グローバル時刻の同期チェック**に関する警告が表示された場合は、システム クロックの時刻 ( **[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

    ![統合セットアップの [前提条件の確認] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスにログオンするための資格情報を作成します。

    ![統合セットアップの [MySQL Configuration]\(MySQL の構成\) 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. Azure Stack VM または物理サーバーをレプリケートする場合は、 **[環境の詳細]** で [いいえ] を選択します。 
9. **[インストール場所]** で、バイナリをインストールしキャッシュを格納する場所を選択します。 選択するドライブには使用可能なディスク領域が 5 GB 以上必要ですが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

    ![統合セットアップの [インストール場所] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. **[ネットワークの選択]** で、最初に、モビリティ サービスの検出とソース マシンへのプッシュ インストールのために組み込みプロセス サーバーによって使用される NIC を選択し、次に、構成サーバーによって Azure との接続に使用される NIC を選択します。 既定では、ポート 9443 がレプリケーション トラフィックの送受信用に使用されます。このポート番号は、実際の環境の要件に合わせて変更できます。 ポート 9443 に加え、ポート 443 も開きます。このポートは、Web サーバーがレプリケーション操作を調整するために使用されます。 ポート 443 はレプリケーション トラフィックの送受信用に使用しないでください。

    ![統合セットアップの [ネットワークの選択] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. **[概要]** で情報を確認し、 **[インストール]** をクリックします。 インストールが完了すると、パスフレーズが生成されます。 このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。

    ![統合セットアップの [概要] 画面のスクリーンショット。](./media/site-recovery-add-configuration-server/combined-wiz10.png)

登録が完了すると、コンテナーの **[設定]**  >  **[サーバー]** ブレードに、サーバーが表示されます。
