---
title: Azure Data Box の設定 | Microsoft Docs
description: ケーブルを配線して Azure Data Box を接続する方法を学習する
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/26/2018
ms.author: alkohli
ms.openlocfilehash: fb2ec4802ad2ecf25a8fca031553c8209dc4267d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791321"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box"></a>チュートリアル:ケーブルを配線して Azure Data Box に接続する

このチュートリアルでは、Azure Data Box のケーブル配線、接続、起動の方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box にケーブルを配線する
> * Data Box に接続する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [チュートリアル:Azure Data Box を注文する](data-box-deploy-ordered.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータルで注文の状態が **[配信済み]** に更新されていること。 
    - 現在のラベルの下にデバイスに貼付されているクリア ポーチに、配送先住所ラベルがあります。 このラベルは返品用に使用するため、安全に保管してください。
    - ヨーロッパの一部のリージョンについては、デバイスが箱に梱包されて発送されます。 デバイスを開梱し、箱は返送用に必ず保管しておいてください。
3. [Data Box の安全性に関するガイドライン](data-box-safety.md)を確認していること。
4. 100 TB ストレージ デバイスで使用するためのアース端子付き電源ケーブルを 1 本受け取っていること。
5. Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用できますが、コピーの速度が影響を受けます。 
6. Data Box を設置できる平らな場所があること。 デバイスを標準的なラック棚に設置しようとする場合は、データ センター ラックに 7U のスロットが必要です。 デバイスは平面に置くことも、ラックに立てて置くこともできます。
7. Data Box をホスト コンピューターに接続するために以下のケーブルを用意していること。
    - 10 GbE SFP+ Twinax 銅線ケーブル 2 本 (DATA 1、DATA 2 ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6 ネットワーク ケーブル 1 本 (MGMT ネットワーク インターフェイスで使用)
    - RJ-45 CAT 6A OR ネットワーク ケーブル 1 本、RJ-45 CAT 6 ネットワーク ケーブル 1 本 (それぞれ 10 Gbps または 1 Gbps として構成されている DATA 3 ネットワーク インターフェイスで使用)

## <a name="cable-your-device"></a>デバイスにケーブルを配線する

デバイスにケーブルを配線するには、次のステップを実行します。

1. デバイスを調べて、開梱された形跡や、その他明らかな損傷がないか確認します。 開梱されている場合またはひどい損傷がある場合には、続行しないでください。 すぐに Microsoft サポートに問い合わせて、デバイスが正常に使用できる状態にあるかどうか、また交換ディスクを発送してもらう必要があるかどうかを評価してください。
2. 電源と接続する場所にデバイスを運びます。 平らな場所にデバイスを置きます。 デバイスは標準のラック棚に設置することもできます。
3. 電源ケーブルとネットワーク ケーブルを接続します。 一般的な構成用に接続したデバイスのバックプレーンを以下に示します。 
    
    ![ケーブル配線した Data Box デバイスのバックプレーン](media/data-box-deploy-set-up/data-box-cabled-dhcp.png)

    1. ラベルで表示された電源入力端子に電源ケーブルを接続します。 電源ケーブルのもう一方の端は、電力配分装置に接続してください。
    2. RJ-45 CAT 6 ケーブルを使用して、一方の端を MGMT ポート、もう一方の端をノート PC に接続します。            
    3. RJ-45 CAT 6A ケーブルを使用し、一方の端を DATA 3 ポートに接続します。 DATA 3 は、RJ-45 CAT 6A ケーブルで接続すると 10 GbE として、RJ-45 CAT 6 ケーブルで接続すると 1 GbE として構成されます。
    4. 10 GbE SFP+ Twinax 銅線ケーブルを使用して、DATA 1 ポートと DATA 2 ポートをそれぞれ接続します。 
    5. データ ポートからのケーブルのもう一方の端を、10 GbE スイッチ経由でホスト コンピューターに接続します。

4. デバイス前面の操作パネルの電源ボタンを確認します。 デバイスの電源を入れます。

    ![Data Box の電源ボタン](media/data-box-deploy-set-up/data-box-powered-door-open.png)

## <a name="connect-to-your-device"></a>デバイスに接続する

次のステップを実行して、ローカル Web UI とポータル UI を使用してデバイスを設定します。

1. デバイスに接続するために使用するノート PC のイーサネット アダプターを、静的 IP アドレス 192.168.100.5、サブネット 255.255.255.0 で構成します。 
2. デバイスの MGMT ポートに接続し、https\://192.168.100.10 のローカル Web UI にアクセスします。 デバイスを起動してから最大 5 分かかることがあります。
3. **[詳細]** をクリックしてから **[Web ページへ移動]** をクリックします。

   ![ローカル Web UI への接続](media/data-box-deploy-set-up/data-box-connect-local-web-ui.png) 

4. ローカル Web UI の **[サインイン]** ページが表示されます。 デバイスのシリアル番号が、ポータル UI とローカル Web UI で一致することを確認します。 デバイスはこの時点でロックされます。
5. [Azure Portal](https://portal.azure.com) にサインインします。
6. ポータルからデバイスの資格情報をダウンロードします。 **[全般] > [デバイスの詳細]** に移動します。 **[デバイスのパスワード]** をコピーします。 デバイスのパスワードは、ポータルで特定の注文に関連付けられています。 

    ![デバイスの資格情報の取得](media/data-box-deploy-set-up/data-box-device-credentials.png)
    
    
7. 前の手順で Azure portal から取得したデバイスのパスワードを指定して、デバイスのローカル Web UI にサインインします。 **[サインイン]** をクリックします。
8. **[ダッシュボード]** で、ネットワーク インターフェイスが構成されていることを確認します。 
    - 環境で DHCP が有効になっている場合、ネットワーク インターフェイスは自動的に構成されます。 
    - DHCP が有効ではない場合、必要であれば **[ネットワーク インターフェイスの設定]** に移動し静的な IP を割り当てます。

    ![デバイス ダッシュボード](media/data-box-deploy-set-up/data-box-dashboard-1.png)

データ ネットワーク インターフェイスが構成されると、DATA 1 から DATA 3 までのインターフェイスのいずれかの IP アドレスを使用して、ローカルの Web UI (`https://<IP address of a data network interface>`) にアクセスすることもできます。 

デバイスの設定が完了したら、デバイス共有に接続し、コンピューターからデバイスにデータをコピーすることができます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box にケーブルを配線する
> * Data Box に接続する

次のチュートリアルに進み、Data Box にデータをコピーする方法を学習してください。

> [!div class="nextstepaction"]
> [データを Data Box にコピーする](./data-box-deploy-copy-data.md)

