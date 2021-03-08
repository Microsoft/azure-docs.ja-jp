---
title: 'チュートリアル: Azure Stack Edge Mini R デバイスを配置するために Azure portal、データセンター環境を準備する | Microsoft Docs'
description: Azure Stack Edge Mini R デバイスの配置に関する最初のチュートリアルでは、Azure portal の準備を行います。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: b6745ed879f02a341027417b54eb459b5bfed705
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762946"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>チュートリアル:Azure Stack Edge Mini R の配置を準備する

このチュートリアルは、Azure Stack Edge Mini R デバイスを完全に配置するために必要な配置チュートリアル シリーズの 1 つ目です。 このチュートリアルでは、Azure Stack Edge リソースを配置するために Azure portal を準備する方法を説明します。

セットアップと構成のプロセスを完了するには、管理者特権が必要です。 ポータルの準備は 10 分ほどで完了します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

### <a name="get-started"></a>はじめに

Azure Stack Edge Mini R デバイスを配置するには、次の順序どおりにチュートリアルを参照してください。

| 手順 | 説明 |
| --- | --- |
| **準備** |後で行う配置のための準備として、これらの手順を完了する必要があります。 |
| **[配置の構成のチェック リスト](#deployment-configuration-checklist)** |このチェック リストを使用して、デプロイ前およびデプロイ中に情報を収集し、記録します。 |
| **[デプロイの前提条件](#prerequisites)** |これらの前提条件を使用して、配置に対する環境の準備が完了していることを確認します。 |
|  | |
|**配置のチュートリアル** |これらのチュートリアルは、ご利用の Azure Stack Edge Mini R デバイスを運用環境に配置するために必要です。 |
|**[1. デバイスのために Azure portal を準備する](azure-stack-edge-mini-r-deploy-prep.md)** |物理デバイスを設置する前に、ご利用の Azure Stack Edge リソースを作成して構成します。 |
|**[2.デバイスを設置する](azure-stack-edge-mini-r-deploy-install.md)**|物理デバイスの検査とケーブル接続を行います。  |
|**[3. デバイスに接続する](azure-stack-edge-mini-r-deploy-connect.md)** |デバイスを設置したら、デバイスのローカル Web UI に接続します。  |
|**[4.ネットワーク設定を構成する](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |デバイスに対するコンピューティング ネットワークや Web プロキシの設定など、ネットワークを構成します。   |
|**[5.デバイス設定を構成する](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |デバイス名と DNS ドメインを割り当てて、更新サーバーとデバイスの時刻を構成します。 |
|**[6.セキュリティ設定を構成する](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |独自の証明書を使用して証明書を構成し、VPN を設定し、ご利用のデバイスの保存時の暗号化を構成します。   |
|**[7.デバイスをアクティブにする](azure-stack-edge-mini-r-deploy-activate.md)** |サービスからのアクティブ化キーを使用して、デバイスをアクティブ化します。 デバイスは、SMB または NFS 共有を設定するか、REST 経由で接続できる状態になります。 |
|**[8.コンピューティングを構成する](azure-stack-edge-gpu-deploy-configure-compute.md)** |デバイスでコンピューティング ロールを構成します。 Kubernetes クラスターも作成されます。 |

これで、Azure Portal をセットアップする準備が整いました。

## <a name="deployment-configuration-checklist"></a>デプロイの構成チェック リスト

ご利用のデバイスを配置する前に、ご利用の Azure Stack Edge Mini R デバイスにソフトウェアを構成するための情報を収集する必要があります。 事前にこの情報の一部を準備することで、環境内にデバイスを配置するプロセスを効率化できます。 [Azure Stack Edge Mini R 配置構成チェックリスト](azure-stack-edge-mini-r-deploy-checklist.md)を使用して、ご利用のデバイスを配置するときの構成の詳細をメモします。

## <a name="prerequisites"></a>前提条件

次に示すのは、Azure Stack Edge リソース、Azure Stack Edge デバイス、およびデータセンター ネットワークの構成に関する前提条件です。

### <a name="for-the-azure-stack-edge-resource"></a>Azure Stack Edge リソースの前提条件

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Azure Stack Edge デバイスの前提条件

物理デバイスを配置する前に次の点を確認します。

- [Azure Stack Edge デバイスの安全性に関するガイドライン](azure-stack-edge-mini-r-safety.md)に関するページで、このデバイスの安全性に関する情報を確認したこと。
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>データセンター ネットワークの前提条件

開始する前に次の点を確認します。

- Azure Stack Edge デバイスのネットワーク要件に従ってデータセンターのネットワークが構成されていること。 詳細については、「[Azure Stack Edge Mini R のシステム要件](azure-stack-edge-mini-r-system-requirements.md)」を参照してください。

- Azure Stack Edge の通常の動作条件下における帯域幅は次のとおりです。

    - デバイスが常に最新の状態に保たれるように最低 10 Mbps のダウンロード帯域幅。
    - ファイル転送用に最低 20 Mbps の専用のアップロード/ダウンロード帯域幅。

## <a name="create-a-new-resource"></a>新しいリソースを作成

物理デバイスを管理する既存の Azure Stack Edge リソースがある場合は、この手順をスキップして、「[アクティブ化キーの取得](#get-the-activation-key)」に進みます。

Azure Stack Edge リソースを作成するには、Azure portal で次の手順を実行します。

1. Microsoft Azure 資格情報を使用し、Azure portal にサインインします。URL は [https://portal.azure.com](https://portal.azure.com) です。


2. 左側のウィンドウで、**[+ リソースの作成]** を選択します。 **[Azure Stack Edge/Data Box Gateway]** を検索して選択します。 **［作成］** を選択します 

3. Azure Stack Edge Pro デバイスに使用するサブスクリプションを選択します。 この物理デバイスを発送する先の国を選択します。 **[デバイスの表示]** を選択します。

    ![リソースを作成する 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. デバイスの種類を選択します。 **[Azure Stack Edge]** で、 **[Azure Stack Edge Mini R]** を選択し、 **[選択]** を選択します。 問題が発生した場合、またはデバイスの種類を選択できない場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

    [![リソースを作成する 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


5. **[基本]** タブで、次の **プロジェクト情報** を入力または選択します。
    
    |設定  |値  |
    |---------|---------|
    |サブスクリプション    |サブスクリプションは、前の選択に基づいて自動的に設定されます。 サブスクリプションは、課金アカウントにリンクされます。 |
    |Resource group  |既存のグループを選択するか、新しいグループを作成します。<br>Azure リソース グループの詳細については[こちら](../azure-resource-manager/management/overview.md)をご覧ください。     |


6. 次の **インスタンス情報** を入力または選択します。

    |設定  |値  |
    |---------|---------|
    |名前   | リソースを識別するわかりやすい名前を入力します。<br>名前は 2 から 50 文字で、英字、数字、ハイフンを含みます。<br> 名前の最初と最後には、英字か数字を使用します。        |
    |リージョン     |Azure Stack Edge リソースを使用できるすべてのリージョンの一覧については、[リージョン別の利用可能な Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)に関するページを参照してください。 Azure Government を使用している場合は、「[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)」に記載されているすべての政府機関向けリージョンを選択できます。<br> デバイスをデプロイする地理的リージョンに最も近い場所を選択します。|

    ![リソースを作成する 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


7. **配送先住所** を選択します。

   - 既にデバイスがある場合は、 **[I already have a device]\(既にデバイスを持っています\)** のコンボ ボックスを選択します。

     ![リソースを作成する 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

   - 新しいデバイスを注文する場合は、連絡先名、会社、デバイスの配送先住所、連絡先情報を入力します。

     ![リソースを作成する 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

8. **タグ** を選択します。 必要に応じて、リソースを分類し、請求を統合するためのタグを指定します。 **確認と作成** をクリックします。

9. **[確認と作成]** タブで、**価格の詳細**、**使用条件**、リソースの詳細を確認します。 **[I have reviewed the privacy terms]\(プライバシー条件を確認しました\)** のコンボ ボックスを選択します。

    ![リソースを作成する 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png)

    また、リソースの作成時に、クラウド サービスに対して認証できるようにする管理サービス ID (MSI) が有効になっていることも通知されます。 この ID は、リソースが存在する限り存在します。

10. **［作成］** を選択します

    リソースの作成には数分かかります。 また、Azure Stack Edge デバイスが Azure のリソースプロバイダーと通信できるようにする MSI も作成されます。
    
    リソースが正常に作成されてデプロイされると通知が表示されます。 **[リソースに移動]** を選択します。
    
    ![Azure Stack Edge Pro リソースに移動する](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
Microsoft は受け取った注文を確認し、発送の詳細と共にお客様に (メールで) 連絡します。

> [!NOTE]
> 一度に複数の注文を作成したい場合や既存の注文を複製したい場合は、[「Azure サンプル」のスクリプト](https://github.com/Azure-Samples/azure-stack-edge-order)をご利用ください。 詳細については、Readme ファイルを参照してください。

注文処理の間に問題が発生した場合は、[注文の問題のトラブルシューティング](azure-stack-edge-troubleshoot-ordering.md)に関する記事を参照してください。

## <a name="get-the-activation-key"></a>アクティブ化キーの取得

Azure Stack Edge リソースが起動して実行中になったら、アクティブ化キーを取得する必要があります。 このキーを使用して、ご利用の Azure Stack Edge Mini R デバイスのアクティブ化とリソースへの接続を行います。 このキーは Azure portal ですぐに入手できます。

1. 自分が作成したリソースを選択し、 **[概要]** を選択します。

   ![[デバイスの設定] を選択する](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. **[アクティブ化]** タイルで、Azure キー コンテナーの名前を指定するか、既定の名前をそのまま使用します。 キー コンテナーの名前は 3 から 24 文字で指定できます。 

    デバイスでアクティブ化される Azure Stack Edge リソースごとに、キー コンテナーが作成されます。 キー コンテナーを使用すると、シークレットを格納してアクセスできます。 たとえば、サービスのチャネル整合性キー (CIK) をキー コンテナーに格納できます。

    キー コンテナーの名前を指定したら、 **[Generate activation key]\(アクティブ化キーの生成\)** を選択して、アクティブ化キーを作成します。

    [![アクティブ化キーの取得](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    キー コンテナーとアクティブ化キーが作成されるまで数分待ちます。 コピー アイコンを選択してキーをコピーし、後で使用できるように保存します。

> [!IMPORTANT]
> - アクティブ化キーは生成後 3 日間で有効期限が切れます。
> - キーの有効期限が切れた場合は、新しいキーを生成してください。 古いキーは無効です。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Stack Edge に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * 新しいリソースを作成
> * アクティブ化キーの取得

次のチュートリアルに進み、Azure Stack Edge を設置する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Stack Edge を設置する](./azure-stack-edge-mini-r-deploy-install.md)
