---
title: Azure Portal で Azure Data Box Edge デバイスを接続、構成、およびアクティブ化する | Microsoft Docs
description: Data Box Edge をデプロイする 3 番目のチュートリアルでは、ご利用の物理デバイスを接続、設定、およびアクティブ化する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/08/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Data Box Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: f4ca513e3c2e2345dc0005b95ddb7927c0164ffe
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383019"
---
# <a name="tutorial-connect-set-up-and-activate-azure-data-box-edge-preview"></a>チュートリアル: Azure Data Box Edge (プレビュー) を接続、設定、およびアクティブ化する 

このチュートリアルでは、ローカル Web UI を使用して Azure Data Box Edge デバイスを接続、設定、およびアクティブ化する方法について説明します。 

設定とアクティブ化のプロセスは、完了するまでに約 20 分かかることがあります。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 物理デバイスに接続する
> * 物理デバイスの設定とアクティブ化

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションを注文して展開する前に、[Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="prerequisites"></a>前提条件

Data Box Edge デバイスを構成および設定する前に、次のことを確認してください。

* [Data Box Edge のインストール](data-box-edge-deploy-install.md)に関するページで詳細に説明されているように、物理デバイスがインストールされていること。
* Data Box Edge デバイスを管理するために作成した Data Box Edge サービスからのアクティブ化キーを持っていること。 詳細については、[Azure Data Box Edge のデプロイ準備](data-box-edge-deploy-prep.md)に関する記事をご覧ください。

## <a name="connect-to-the-local-web-ui-setup"></a>ローカル Web UI 設定に接続する 

1. Edge デバイスに接続するためのコンピューター上のイーサネット アダプターに 192.168.100.5 の静的 IP アドレスとサブネット 255.255.255.0 を構成します。

1. デバイスでコンピューターをポート 1 に接続します。 

1. ブラウザー ウィンドウを開き、 https://192.168.100.10 からデバイスのローカル Web UI にアクセスします。  
    このアクションは、そのデバイスに電源を入れてから数分かかることがあります。 

    Web サイトのセキュリティ証明書に問題があることを示すエラーまたは警告が表示されます。 
   
    ![Web サイトのセキュリティ証明書のエラー メッセージ](./media/data-box-edge-deploy-connect-setup-activate/image2.png)

1. **[この Web ページの閲覧を続ける]** を選択します。  
    これらの手順は、使用しているブラウザーによって異なることがあります。

1. ご利用のデバイスの Web UI にサインインします。 既定のパスワードは *Password1* です。 
   
    ![Data Box Edge デバイスのサインイン ページ](./media/data-box-edge-deploy-connect-setup-activate/image3.png)

1. プロンプトで、デバイス管理者のパスワードを変更します。  
    新しいパスワードには 8 ～ 16 文字を含める必要があります。 さらに、大文字、小文字、数字、および特殊文字のうちの 3 種類の文字を含める必要があります。

これで、デバイスのダッシュボードが表示されます。

## <a name="set-up-and-activate-the-physical-device"></a>物理デバイスの設定とアクティブ化
 
ダッシュボードには、物理デバイスを構成して Data Box Edge サービスに登録するために必要なさまざまな設定が表示されます。 **デバイス名**、**ネットワーク設定**、**Web プロキシの設定**、**時刻の設定**はオプションです。 必須の設定は、**[クラウド設定]** のみです。
   
![Data Box Edge デバイスのダッシュボード](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. 左側のウィンドウで **[デバイス名]** を選択し、デバイスのフレンドリ名を入力します。  
    フレンドリ名には 1 ～ 15 文字を含める必要があり、文字、数字、およびハイフンを含める必要があります。

    ![[デバイス名] ページ](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-2.png)

1. (省略可能) 左側のウィンドウで **[ネットワーク設定]** を選択し、設定を構成します。  
    物理デバイスには 6 つのネットワーク インターフェイスがあります。 ポート 1 とポート 2 は、1 Gbps ネットワーク インターフェイスです。 ポート 3、ポート 4、ポート 5、およびポート 6 はすべて、25 Gbps ネットワーク インターフェイスです。 ポート 1 は管理専用ポートとして自動的に構成され、ポート 2 からポート 6 はすべてデータ ポートです。 **[ネットワークの設定]** ページは次のとおりです。
    
    ![[ネットワーク設定] ページ](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    ネットワーク設定を構成するときは、次のことに注意してください。

    - ご利用の環境内で DHCP が有効になっている場合は、ネットワーク インターフェイスが自動的に構成されます。 IP アドレス、サブネット、ゲートウェイ、DNS は自動的に割り当てられます。
    - DHCP が有効になっていない場合は、必要に応じて、静的 IP アドレスを割り当てることができます。
    - 使用するネットワーク インターフェイスは、IPv4 として構成できます。
   
1. (省略可能) 左側のウィンドウで **[Web proxy settings] (Web プロキシ設定)** を選択し、Web プロキシ サーバーを構成します。 Web プロキシの構成は省略可能ですが、Web プロキシを使用する場合は、このページでのみ構成できます。
   
   ![[Web proxy settings] (Web プロキシ設定) ページ](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   **[Web proxy settings] (Web プロキシ設定)** ページで、次の操作を行います。
   
   a. **[Web プロキシ URL]**  ボックスに、`http://host-IP address or FDQN:Port number` という形式の URL を入力します。 HTTPS URL はサポートされていません。

   b. **[認証]** で、**[なし]** または **[NTLM]** を選択します。

   c. 認証を使用している場合は、ユーザー名とパスワードを入力します。

   d. 構成された Web プロキシ設定を検証して適用するには、**[Apply settings] (設定を適用)** を選択します。

1. (省略可能) 左側のウィンドウで **[時刻の設定]** を選択し、タイム ゾーンとデバイスのプライマリおよびセカンダリ NTP サーバーを構成します。  
    デバイスは時刻を同期してクラウド サービス プロバイダーに対して認証できるようにする必要があるため、NTP サーバーが必要になります。
    
    ![[時刻の設定] ページ](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-5.png)
    
    **[時刻の設定]** ページで、次の操作を行います。
    
    a. **[タイム ゾーン]** ドロップダウン リストで、デバイスがデプロイされている地理的な場所に対応するタイム ゾーンを選択します。  
        デバイスの既定のタイム ゾーンは太平洋標準時です。 デバイスは、スケジュールされたすべての操作でこのタイム ゾーンを使用します。

    b. **[プライマリ NTP サーバー]** ボックスで、デバイスのプライマリ サーバーを入力するか、または time.windows.com の既定値をそのまま使用します。  
        ネットワークでデータセンターからインターネットへの NTP トラフィックの流れが許可されていることを確認してください。

    c. オプションで、**[セカンダリ NTP サーバー]** ボックスにデバイスのセカンダリ サーバーを入力します。

    d. 構成された時刻の設定を検証および適用するには、**[適用]** を選択します。

6. 左側のウィンドウで **[クラウド設定]** を選択し、Azure Portal の Data Box Edge サービスでデバイスをアクティブ化します。
    
    a. **[Activation key] (アクティブ化キー)** ボックスに、Data Box Edge の [[アクティブ化キーの取得]](data-box-edge-deploy-prep.md#get-the-activation-key) で取得したアクティブ化キーを入力します。

    b. **[適用]** を選択します。 
       
    ![[クラウド設定] ページ](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-6.png)
    
    デバイスが正常にアクティブ化されると、接続モードのオプションが表示されます。 これらの設定は、部分的に切断された状態、または切断モードでデバイスを使用する必要がある場合に構成されます。 

    ![[クラウド設定] のアクティブ化の確認](./media/data-box-edge-deploy-connect-setup-activate/set-up-activate-7.png)    

デバイスの設定が完了しました。 これで、デバイスで共有を追加できます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 物理デバイスに接続する
> * 物理デバイスの設定とアクティブ化


Data Box Edge デバイスでデータを転送する方法を学習するには、次を参照してください。

> [!div class="nextstepaction"]
> [Data Box Edge を使用してデータを転送する](./data-box-edge-deploy-add-shares.md)