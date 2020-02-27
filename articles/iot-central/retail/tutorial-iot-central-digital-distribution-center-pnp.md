---
title: IoT のデジタル配布センターのチュートリアル | Microsoft Docs
description: IoT Central 用のデジタル配布センター アプリケーション テンプレートのチュートリアル
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 93a77d73b5cc249c39609f98f055a7b1927dd6ed
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025488"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>チュートリアル:デジタル配布センター アプリケーション テンプレートを展開して調べる



このチュートリアルでは、IoT Central の**デジタル配布センター** アプリケーション テンプレートをデプロイする方法について説明します。 テンプレートをデプロイする方法、既定で含まれるもの、次にできることについて説明します。

このチュートリアルで学習する内容は次のとおりです。 
* デジタル配布センター アプリケーションを作成する 
* アプリケーションを調べる 

## <a name="prerequisites"></a>前提条件
* このアプリをデプロイするために必要な特定の前提条件はありません
* Azure サブスクリプションを用意することをお勧めしますが、なくても試してみることはできます

## <a name="create-digital-distribution-center-application-template"></a>デジタル配布センター アプリケーション テンプレートを作成する

以下の手順でアプリケーションを作成できます

1. Azure IoT Central のアプリケーション マネージャー Web サイトに移動します。 左側のナビゲーションバーから **[ビルド]** を選択し、 **[Retail]\(小売り\)** タブをクリックします。

    > [!div class="mx-imgBorder"]
    > ![デジタル配布センター](./media/tutorial-iot-central-ddc/iotc-retail-homepage.png)

2. **[小売業]** タブを選択し、 **[デジタル流通センター]** の下にある **[アプリの作成]** を選択します

3. **[アプリの作成]** を選択すると、[新しいアプリケーション] フォームが開き、次のように要求された詳細が設定されます。
   **[アプリケーション名]** : 既定の推奨名を使用するか、わかりやすいアプリケーション名を入力できます。
   **[URL]** : 既定の推奨 URL を使用するか、わかりやすい一意の URL を入力できます。 次に、Azure サブスクリプションが既にある場合は、既定の設定をお勧めします。 7 日間の無料試用版料金プランから始め、無料試用版が期限切れになる前に、いつでも標準の料金プランに変換することもできます。
   **課金情報**:リソースをプロビジョニングするには、ディレクトリ、Azure サブスクリプション、リージョンの詳細が必要です。
   **作成**:ページの下部にある [作成] を選択して、アプリケーションをデプロイします。

    > [!div class="mx-imgBorder"]
    > ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-create.png)

    > [!div class="mx-imgBorder"]
    > ![デジタル流通センターの課金情報](./media/tutorial-iot-central-ddc/ddc-create-billinginfo.png)

## <a name="walk-through-the-application-dashboard"></a>アプリケーション ダッシュボードを調べる 

アプリ テンプレートが正常にデプロイされると、既定のダッシュボードが配布センターのオペレーターを対象とするポータルになります。 Northwind Traders 社は、コンベヤ システムを管理する架空の配布センター ソリューション プロバイダーです。 

このダッシュボードには、1 つのゲートウェイと、IoT デバイスとして機能している 1 つのカメラが表示されます。 ゲートウェイでは、有効、無効、不明、サイズなどのパッケージに関するテレメトリと共に、関連付けられたデバイス ツイン プロパティが提供されています。 すべてのダウンストリーム コマンドは、カメラなどの IoT デバイスで実行されます。 このダッシュボードは、配布センターの重要なデバイス操作アクティビティが表示されるように事前に構成されています。

ダッシュボードは、Azure IoT ゲートウェイと IoT デバイスのデバイス管理機能が表示されるように、論理的に編成されています。  
   * ゲートウェイ コマンドと制御タスクを実行できます
   * ソリューションの一部であるすべてのカメラを管理します。 

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>デバイス テンプレート

[デバイス テンプレート] タブをクリックすると、ゲートウェイ機能モデルが表示されます。 機能モデルは、 **[Camera]\(カメラ\)** と **[Digital Distribution Gateway]\(デジタル配布ゲートウェイ\)** という 2 つの異なるインターフェイスを中心に構成されています

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**[Camera]\(カメラ\)** - このインターフェイスには、すべてのカメラ固有のコマンド機能が整理されています 

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-camera.png)

**[Digital Distribution Gateway]\(デジタル配布ゲートウェイ\)** - このインターフェイスは、カメラから送られてくるすべてのテレメトリ、クラウドで定義されているデバイス ツインのプロパティ、ゲートウェイ情報を表します。

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>ゲートウェイ コマンド
このインターフェイスには、すべてのゲートウェイ コマンド機能が整理されています

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>ルール
[Rules]\(規則\) タブを選択して、このアプリケーション テンプレートに存在する 2 つの異なる規則を表示します。 これらの規則は、詳細な調査のためオペレーターにメールで通知するように構成されています。

 **[Too many invalid packages alert]\(多すぎる無効パッケージ アラート\)** - この規則は、コンベヤ システムを通過する無効なパッケージの数が多いことがカメラによって検出されるとトリガーされます。
 
**[Large package]\(大きいパッケージ\)** - この規則は、品質を検査できない大きなパッケージをカメラが検出するとトリガーされます。 

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>ジョブ
[ジョブ] タブを選択すると、このアプリケーション テンプレートの一部として存在する 5 つの異なるジョブが表示されます。ジョブ機能を利用して、ソリューション全体の操作を実行できます。 このデジタル配布センター ジョブでは、デバイス コマンドとツイン機能を使用して、次のようなタスクが実行されます。
   * パッケージ検出を開始する前にカメラを調整します 
   * カメラのファームウェアを定期的に更新します
   * テレメトリの間隔を変更して、データのアップロードを管理します

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする
このアプリケーションをもう使わない場合は、 **[管理]**  >  **[アプリケーションの設定]** に移動し、 **[削除]** をクリックすることによって、アプリケーション テンプレートを削除します。

> [!div class="mx-imgBorder"]
> ![デジタル配布センター](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>次のステップ
* デジタル配布センター ソリューション アーキテクチャの[デジタル配布センターの概念](./architecture-digital-distribution-center-pnp.md)についてさらに詳しく学習します
* 他の [IoT Central 小売りテンプレート](./overview-iot-central-retail-pnp.md)についてさらに詳しく学習します
* [IoT Central の概要](../core/overview-iot-central.md)に関する記事を参照し、IoT Central についてさらに詳しく学習します
