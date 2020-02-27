---
title: Azure Lab Services でクラスルーム ラボを管理する | Microsoft Docs
description: クラスルーム ラボを作成して構成したり、すべてのクラスルーム ラボを表示したり、登録リンクをラボ ユーザーと共有したり、ラボを削除したりする方法について説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: ad7fd664f0dce08e4482b4fb2cba2831208396fc
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264833"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Azure Lab Services でクラスルーム ラボを管理する 
この記事では、クラスルーム ラボの作成および削除方法について説明します。 また、ラボ アカウントのすべてのクラスルーム ラボを表示する方法についても説明します。 

## <a name="prerequisites"></a>前提条件
ラボ アカウントでクラスルーム ラボを設定するには、ラボ アカウントにおける**ラボの作成者**ロールのメンバーであることが必要です。 ラボ アカウントを作成するために使用したアカウントは、このロールに自動的に追加されます。 ラボ所有者は、「[ユーザーをラボの作成者ロールに追加する](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)」の記事の手順に従って、他のユーザーをラボの作成者ロールに追加できます。

## <a name="create-a-classroom-lab"></a>クラスルーム ラボを作成する

1. [Azure Lab Services Web サイト](https://labs.azure.com)に移動します。 Internet Explorer 11 はまだサポートされていません。 
2. **[サインイン]** を選択して、資格情報を入力します。 ラボ アカウントに**ラボの作成者**のメンバーである**ユーザー ID** を入力または作成し、パスワードを入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. **[New lab]\(新しいラボ\)** を選択します。 
    
    ![クラスルーム ラボを作成する](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. **[New Lab]\(新しいラボ\)** ウィンドウで、次のようにします。 
    1. ラボの**名前**を指定します。 
    2. クラスに必要な**仮想マシンのサイズ**を選択します。 使用可能なサイズの一覧については、「[VM サイズ](#vm-sizes)」のセクションを参照してください。 
    3. クラスルーム ラボで使用する**仮想マシン イメージ**を選択します。 Linux イメージを選択した場合は、リモート デスクトップ接続を有効にするためのオプションが表示されます。 詳細については、[Linux のリモート デスクトップ接続の有効化](how-to-enable-remote-desktop-linux.md)に関するページを参照してください。
    4. ページに表示される **1 時間あたりの合計料金**を確認します。 
    6. **[保存]** を選択します。

        ![新しいラボのウィンドウ](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. **[Virtual machine credentials]\(仮想マシンの資格情報\)** ページで、ラボ内のすべての VM 用の既定の資格情報を指定します。
    1. ラボ内のすべての VM に使う**ユーザーの名前**を指定します。
    2. ユーザーの**パスワード**を指定します。 

        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。
    3. 学生に自分自身のパスワードを設定させる場合は、 **[Use same password for all virtual machines]\(すべての仮想マシンに同じパスワードを使用する\)** オプションを無効にします。 この手順は**省略可能**です。 

        教師は、ラボ内のすべての VM に同じパスワードを使用するか、学生に自分の VM のパスワードを設定させるかを選択できます。 既定では、この設定は、Ubuntu を除き、すべての Windows と Linux のイメージで有効になっています。 **Ubuntu** VM を選択すると、この設定は無効になり、初めてサインインしたときに、パスワードを設定するよう求めるプロンプトが学生に表示されます。  

        ![新しいラボのウィンドウ](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > ユーザー名とパスワードはメモしておいてください。 これらは再表示されません。    
    4. 次に、 **[Virtual machine credentials]\(仮想マシンの資格情報\)** ページで **[次へ]** を選択します。 
5. **[Lab policies]\(ラボのポリシー\)** ページで、ラボに対してスケジュールされた時間以外に各ユーザーに割り当てられる時間数を入力し ( **[Quota for each user]\(各ユーザーのクォータ\)** )、 **[完了]** を選択します。 

    ![[Quota for each user]\(各ユーザーのクォータ\)](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. テンプレート VM の作成の状態を示す次の画面が表示されます。 ラボ内のテンプレートの作成には、最大 20 分がかかります。 

    ![テンプレート VM の作成の状態](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. **[テンプレート]** ページで、次の手順を実行します。これらの手順は、チュートリアルでは**省略可能**です。

    2. **[接続]** を選択してテンプレート VM に接続します。 Linux のテンプレート VM の場合は、SSH と RDP のどちらを使用して接続するかを選択します (RDP が有効な場合)。
    1. **[パスワードのリセット]** を選択して、VM のパスワードをリセットします。 
    1. テンプレート VM にソフトウェアをインストールして構成します。 
    1. VM を**停止**します。  
    1. テンプレートの**説明**を入力します。
10. **[テンプレート]** ページで、ツール バーの **[発行]** を選択します。 

    ![テンプレートの発行ボタン](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 一度発行すると、再発行することはできません。 
8. **[Publish template]\(テンプレートの発行\)** ページで、ラボに作成する仮想マシンの数を入力し、 **[発行]** を選択します。 

    ![テンプレートの発行 - VM の数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. ページには、テンプレートの**発行の状態**が表示されます。 このプロセスには、最大で 1 時間かかることがあります。 

    ![テンプレートの発行 - 進行状況](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 左側のメニューで [仮想マシン] を選択するか、[仮想マシン] タイルを選択して、 **[仮想マシン プール]** ページに切り替えます。 **[未割り当て]** 状態の仮想マシンが表示されていることを確認します。 これらの VM は、まだ学生に割り当てられていません。 その状態が **[停止]** になっている必要があります。 このページで、学生の VM の起動、VM への接続、VM の停止、VM の削除を実行できます。 VM は、このページから自分で起動できるほか、学生に起動してもらうこともできます。 

    ![仮想マシンが停止済み状態](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    次のタスクはこのページで実行します (このチュートリアルでは、これらの手順を実行しないでください。 これらの手順は情報提供のみを目的としています)。 
    
    1. ラボの容量 (ラボ内の VM の数) を変更するには、ツール バーの **[Lab capacity]\(ラボの容量\)** を選択します。
    2. すべての VM を一度に起動するには、ツール バーの **[Start all]\(すべて起動\)** を選択します。 
    3. 特定の VM を起動するには、 **[状態]** の下向き矢印を選択し、 **[開始]** を選択します。 また、最初の列で VM を選択した後にツール バーの **[Start]\(起動\)** を選択して、1 台の VM を起動することもできます。                

### <a name="vm-sizes"></a>VM サイズ  

| Size | コア | RAM | 説明 | 
| ---- | ----- | --- | ----------- | 
| Small | 2 | 3.5 GB | このサイズは、コマンド ライン、Web ブラウザーの起動、トラフィックが少ない Web サーバー、中小規模のデータベースに最適です。 |
| Medium | 4 | 7 GB | このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。 | 
| 中 (入れ子になった仮想化) | 4 | 16 GB | このサイズは、リレーショナル データベース、メモリ内 Caching、および分析に最適です。 また、このサイズは入れ子になった仮想化もサポートしています。 <p>このサイズは、それぞれの学生が複数の VM を必要とするシナリオで使用できます。 教師は、入れ子になった仮想化を使用して、仮想マシン内に、小さいサイズの入れ子になった仮想マシンをいくつか設定することができます。 </p> |
| Large | 8 | 32 GB | このサイズは、高速の CPU、ローカル ディスクのよりすぐれたパフォーマンス、大規模なデータベース、大きなメモリ キャッシュを必要とするアプリケーションに最適です。 このサイズは、入れ子になった仮想化もサポートします。 |  
| Small GPU (視覚化) | 6 | 56 GB | このサイズは、リモートの視覚化、ストリーミング、ゲーム、OpenGL や DirectX などのフレームワークを使用したエンコードに最適です。 | 
| Small GPU (Compute) | 6 | 56 GB | このサイズは、人工知能やディープ ラーニング アプリケーションのような、コンピューティング集中型およびネットワーク集中型のアプリケーションに最適です。 | 
| Medium GPU (視覚化) | 12 | 112 GB | このサイズは、リモートの視覚化、ストリーミング、ゲーム、OpenGL や DirectX などのフレームワークを使用したエンコードに最適です。 | 

> [!NOTE]
> GPU イメージを含むラボを作成すると、Azure Lab Services によって必要な GPU ドライバーが自動的にインストールされ、構成されます。  

## <a name="view-all-classroom-labs"></a>すべてのクラスルーム ラボを表示する
1. [Azure Lab Services ポータル](https://labs.azure.com)に移動します。
2. **[サインイン]** をクリックします。 ラボ アカウントに**ラボの作成者**のメンバーである**ユーザー ID** を入力または作成し、パスワードを入力します。 Azure Lab Services では、組織アカウントと Microsoft アカウントがサポートされています。 
3. 選択したラボ アカウント内にすべてのラボが表示されることを確認します。 ラボのタイルには、ラボ内の仮想マシンの数と各ユーザーのクォータ (スケジュールされた時間外) が表示されます。

    ![すべてのラボ](../media/how-to-manage-classroom-labs/all-labs.png)
3. 上部にあるドロップダウン リストを使用して、別のラボ アカウントを選択できます。 選択したラボ アカウントのラボが表示されます。 

## <a name="delete-a-classroom-lab"></a>クラスルーム ラボを削除する
1. ラボ用のタイルで、隅にある 3 つのドット (...) を選択し、 **[削除]** を選択します。 

    ![[削除] ボタン](../media/how-to-manage-classroom-labs/delete-button.png)
3. **[Delete lab]\(ラボの削除\)** ダイアログ ボックスで、 **[削除]** を選択して削除を続行します。 

## <a name="switch-to-another-classroom-lab"></a>別のクラスルーム ラボに切り替える
現在のクラスルーム ラボから別のクラスルーム ラボに切り替えるには、一番上のラボ アカウントにあるラボのドロップダウン リストを選択します。

![一番上のドロップダウン リストからラボを選択する](../media/how-to-manage-classroom-labs/switch-lab.png)

このドロップダウン リストで **[New lab]\(新しいラボ\)** を使用して新しいラボを作成することもできます。 

> [!NOTE]
> Az.LabServices PowerShell モジュール (プレビュー) を使用して、ラボを管理することもできます。 詳しくは、[GitHub の Az.LabServices ホーム ページ](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)をご覧ください。

別のラボ アカウントに切り替えるには、ラボ アカウントの横にあるドロップダウンを選択し、他の ラボアカウントを選択します。 

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボ所有者としてテンプレートを設定および発行する](how-to-create-manage-template.md)
- [ラボ所有者としてラボの使用を構成および制御する](how-to-configure-student-usage.md)
- [ラボ ユーザーとしてクラスルーム ラボにアクセスする](how-to-use-classroom-lab.md)

