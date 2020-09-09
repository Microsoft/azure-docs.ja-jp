---
title: StorSimple Snapshot Manager のバックアップ カタログ | Microsoft Docs
description: StorSimple Snapshot Manager MMC スナップインを使用して、バックアップ カタログを表示および管理する方法について説明します。
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 6abdbfd2-22ce-45a5-aa15-38fae4c8f4ec
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 94dccdabfa4cf83077bca3629dee8a796d27182a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515469"
---
# <a name="use-storsimple-snapshot-manager-to-manage-the-backup-catalog"></a>StorSimple Snapshot Manager を使用したバックアップ カタログの管理

## <a name="overview"></a>概要
StorSimple Snapshot Manager の主な機能は、スナップショットの形式で StorSimple ボリュームのアプリケーション整合性バックアップ コピーを作成できるようにすることです。 スナップショットは、 *バックアップ カタログ*と呼ばれる XML ファイルに一覧表示されます。 バックアップ カタログでは、ボリューム グループごとにスナップショットが編成され、その後、ローカル スナップショットまたはクラウド スナップショットごとに編成されます。

このチュートリアルでは、 **バックアップ カタログ** ノードを使用して、次のタスクを実行する方法を説明します。

* ボリュームの復元
* ボリュームまたはボリューム グループの複製
* バックアップの削除
* ファイルの復元
* Storsimple Snapshot Manager データベースの復元

**[スコープ]** ウィンドウで **[バックアップ カタログ]** ノードを展開し、次にボリューム グループを展開して、バックアップ カタログを表示できます。

* ボリューム グループ名をクリックすると、 **[結果]** ウィンドウにそのボリューム グループに使用可能なローカル スナップショットとクラウド スナップショットの数が表示されます。 
* **[ローカル スナップショット]** または **[クラウド スナップショット]** をクリックすると、 **[結果]** ウィンドウに各バックアップ スナップショットに関する次の情報が表示されます (表示される情報は、 **[表示]** の設定によって異なります)。
  
  * **[名前]** – スナップショットが取られた時刻。
  * **[種類]** – ローカル スナップショットまたはクラウド スナップショットのどちらであるか。
  * **[所有者]** – コンテンツの所有者。 
  * **[使用可能]** – スナップショットを現在使用できるかどうか。 **True** の場合、スナップショットは使用可能で復元可能です。**False** の場合、スナップショットは使用できません。 
  * **[インポート済み]** – バックアップがインポートされたかどうか。 **True** の場合、デバイスが StorSimple Snapshot Manager で構成されたときに、StorSimple デバイス マネージャー サービスからバックアップがインポートされたことを示します。**False** の場合、バックアップはインポートされませんでしたが、StorSimple Snapshot Manager によって作成されたことを示します (ボリューム グループのインポート元となるデバイスを識別するサフィックスが追加されるため、インポートされたボリューム グループは簡単に特定できます)。
    
    ![バックアップ カタログ](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Backup_catalog.png)
* **[ローカル スナップショット]** または **[クラウド スナップショット]** を展開し、個々のスナップショット名をクリックすると、選択したスナップショットに関する次の情報が **[結果]** ウィンドウに表示されます。
  
  * **[名前]** – ドライブ文字で識別されるボリューム。 
  * **[ローカル名]** – ドライブのローカル名 (使用可能な場合)。 
  * **[デバイス]** – ボリュームが存在するデバイスの名前。 
  * **[使用可能]** – スナップショットを現在使用できるかどうか。 **True** の場合、スナップショットは使用可能で復元可能です。**False** の場合、スナップショットは使用できません。 

## <a name="restore-a-volume"></a>ボリュームの復元
バックアップからボリュームを復元するには、以下の手順を実行します。

#### <a name="prerequisites"></a>前提条件
まだ実行していない場合は、ボリュームとボリューム グループを作成した後、そのボリュームを削除します。 既定では、StorSimple Snapshot Manager でボリュームを削除するには、ボリュームをバックアップしておく必要があります。 この予防措置により、ボリュームが誤って削除された場合、または何かの理由でデータを復旧する必要がある場合に、データが失われるのを防ぐことができます。 

StorSimple Snapshot Manager で予防措置のバックアップを作成しているときに、次のメッセージが表示されます。

![自動スナップショット メッセージ](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Automatic_snap.png) 

> [!IMPORTANT]
> ボリューム グループに属するボリュームは削除できません。 [削除] オプションは使用できません。 <br>
> 
> 

#### <a name="to-restore-a-volume"></a>ボリュームを復元するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。 
2. **[スコープ]** ウィンドウで **[バックアップ カタログ]** ノードを展開し、ボリューム グループを展開して、 **[ローカル スナップショット]** または **[クラウド スナップショット]** をクリックします。 **[結果]** ウィンドウに、バックアップ スナップショットのリストが表示されます。
3. 復元するバックアップを探して右クリックし、 **[復元]** をクリックします。
   
    ![バックアップ カタログの復元](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_BU_catalog.png) 
4. 確認のページで詳細を確認し、「**Confirm**」と入力して、 **[OK]** をクリックします。 StorSimple Snapshot Manager がバックアップを使用してボリュームを復元します。
   
    ![復元の確認メッセージ](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Restore_volume_msg.png) 
5. 実行中に復元操作を監視できます。 **[スコープ]** ウィンドウで、 **[ジョブ]** ノードを展開し、 **[実行中]** をクリックします。 **[結果]** ウィンドウにジョブの詳細が表示されます。 復元ジョブが終了すると、ジョブ詳細が **[過去 24 時間以内]** リストに転送されます。

## <a name="clone-a-volume-or-volume-group"></a>ボリュームまたはボリューム グループの複製
ボリュームまたはボリューム グループの複製を作成するには、以下の手順を実行します。

#### <a name="to-clone-a-volume-or-volume-group"></a>ボリュームまたはボリューム グループを複製するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで **[バックアップ カタログ]** ノードを展開し、ボリューム グループを展開して、 **[クラウド スナップショット]** をクリックします。 **[結果]** ウィンドウに、バックアップのリストが表示されます。
3. 複製するボリュームまたはボリューム グループを探し、ボリュームまたはボリューム グループの名前を右クリックして、 **[複製]** をクリックします。 **[クラウド スナップショットの複製]** ダイアログ ボックスが表示されます。
   
    ![クラウド スナップショットの複製](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. 次のようにして、 **[クラウド スナップショットの複製]** ダイアログ ボックスを完了します。 
   
   1. **[名前]** ボックスに、複製するボリュームの名前を入力します。 この名前は **[ボリューム]** ノードに表示されます。 
   2. (省略可能) **[ドライブ]** を選択し、ドロップダウン リストからドライブ文字を選択します。
   3. (省略可能) **[フォルダー (NTFS)]** を選択し、フォルダー パスを入力するか [参照] をクリックし、フォルダーの場所を選択します。 
   4. **Create** をクリックしてください。
5. 複製の処理が完了したら、複製されたボリュームを初期化する必要があります。 Server Manager を起動し、ディスクの管理を開始します。 詳細については、「 [ボリュームのマウント](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)」をご覧ください。 初期化が終わったら、 **[スコープ]** ウィンドウの **[ボリューム]** ノードにボリュームが一覧表示されます。 ボリュームが一覧表示されない場合は、ボリュームのリストを更新します ( **[ボリューム]** ノードを右クリックし、 **[更新]** をクリックします)。

## <a name="delete-a-backup"></a>バックアップの削除
バックアップ カタログからスナップショットを削除するには、以下の手順を実行します。 

> [!NOTE]
> スナップショットを削除すると、そのスナップショットに関連付けられているバックアップ データも削除されます。 ただし、クラウドからデータをクリーンアップする処理には、時間がかかる場合があります。<br>


#### <a name="to-delete-a-backup"></a>バックアップを削除するには
1. デスクトップ アイコンをクリックして、StorSimple Snapshot Manager を起動します。
2. **[スコープ]** ウィンドウで **[バックアップ カタログ]** ノードを展開し、ボリューム グループを展開して、 **[ローカル スナップショット]** または **[クラウド スナップショット]** をクリックします。 **[結果]** ウィンドウに、スナップショットの一覧が表示されます。
3. 削除するスナップショットを右クリックし、 **[削除]** をクリックします。
4. 確認メッセージが表示されたら、 **[OK]** をクリックします。

## <a name="recover-a-file"></a>ファイルの復元
ファイルが誤ってボリュームから削除された場合、削除した時点よりも前の日付のスナップショットを取得し、そのスナップショットを使用してボリュームの複製を作成して、複製されたボリュームから元のボリュームにファイルをコピーすることで、ファイルを復元できます。

#### <a name="prerequisites"></a>前提条件
開始する前に、ボリューム グループの現在のバックアップがあることを確認します。 次に、そのボリューム グループ内のいずれかのボリュームに保存されているファイルを削除します。 最後に、以下の手順に従って、バックアップから、削除されたファイルを復元します。 

#### <a name="to-recover-a-deleted-file"></a>削除されたファイルを回復するには
1. デスクトップの [StorSimple Snapshot Manager] アイコンをクリックします。 StorSimple Snapshot Manager のコンソール ウィンドウが表示されます。 
2. **[スコープ]** ウィンドウで **[バックアップ カタログ]** ノードを展開し、削除されたファイルを含むスナップショットを参照します。 通常、削除の直前に作成されたスナップショットを選択する必要があります。
3. 複製するボリュームを探して右クリックし、 **[複製]** をクリックします。 **[クラウド スナップショットの複製]** ダイアログ ボックスが表示されます。
   
    ![クラウド スナップショットの複製](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_Clone.png) 
4. 次のようにして、 **[クラウド スナップショットの複製]** ダイアログ ボックスを完了します。 
   
   1. **[名前]** ボックスに、複製するボリュームの名前を入力します。 この名前は **[ボリューム]** ノードに表示されます。 
   2. (省略可能) **[ドライブ]** を選択し、ドロップダウン リストからドライブ文字を選択します。 
   3. (省略可能) **[フォルダー (NTFS)]** を選択し、フォルダー パスを入力するか **[参照]** をクリックし、フォルダーの場所を選択します。 
   4. **Create** をクリックしてください。 
5. 複製の処理が完了したら、複製されたボリュームを初期化する必要があります。 Server Manager を起動し、ディスクの管理を開始します。 詳細については、「 [ボリュームのマウント](storsimple-snapshot-manager-manage-volumes.md#mount-volumes)」をご覧ください。 初期化が終わったら、 **[スコープ]** ウィンドウの **[ボリューム]** ノードにボリュームが一覧表示されます。 
   
    ボリュームが一覧表示されない場合は、ボリュームのリストを更新します ( **[ボリューム]** ノードを右クリックし、 **[更新]** をクリックします)。
6. 複製されたボリュームを含む NTFS フォルダーを開き、 **[ボリューム]** ノードを展開して、複製されたボリュームを開きます。 復元するファイルを探し、それをプライマリ ボリュームにコピーします。
7. ファイルを復元した後は、複製されたボリュームを含む NTFS フォルダーを削除することができます。

## <a name="restore-the-storsimple-snapshot-manager-database"></a>Storsimple Snapshot Manager データベースの復元
定期的に、ホスト コンピューターに StorSimple Snapshot Manager データベースをバックアップする必要があります。 障害が発生した場合や、ホスト コンピューターが何かの理由で機能しなくなった場合は、バックアップから復元できます。 データベースのバックアップは手動で行ってください。

#### <a name="to-back-up-and-restore-the-database"></a>データベースをバックアップおよび復元するには
1. Microsoft StorSimple Management Service を停止します。
   
   1. Server Manager を起動します。
   2. Server Manager ダッシュボードの **[ツール]** メニューで、 **[サービス]** を選択します。
   3. **[サービス]** ウィンドウで、 **[Microsoft StorSimple Management Service]** を選択します。
   4. 右側のウィンドウで、 **[Microsoft StorSimple Management Service]** の下にある **[サービスの停止]** をクリックします。
2. ホスト コンピューターで、C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。 
   
   > [!NOTE]
   > ProgramData は隠しフォルダーです。
   > 
   > 
3. カタログ XML ファイルを検索し、ファイルをコピーして、安全な場所、またはクラウドにコピーを保存します。 ホストが失敗した場合は、このバックアップ ファイルを使用して、StorSimple Snapshot Manager で作成したバックアップ ポリシーを復元できます。
   
    ![Azure StorSimple バックアップ カタログ ファイル](./media/storsimple-snapshot-manager-manage-backup-catalog/HCS_SSM_bacatalog.png)
4. Microsoft StorSimple Management Service を再起動します。 
   
   1. Server Manager ダッシュボードの **[ツール]** メニューで、 **[サービス]** を選択します。
   2. **[サービス]** ウィンドウで、 **[Microsoft StorSimple Management Service]** を選択します。
   3. 右側のウィンドウで、 **[Microsoft StorSimple Management Service]** の下にある **[サービスを再起動する]** をクリックします。
5. ホスト コンピューターで、C:\ProgramData\Microsoft\StorSimple\BACatalog を参照します。 
6. カタログ XML ファイルを削除し、作成したバックアップ バージョンで置き換えます。 
7. デスクトップの [StorSimple Snapshot Manager] アイコンをクリックして、StorSimple Snapshot Manager を起動します。 

## <a name="next-steps"></a>次のステップ
* [StorSimple Snapshot Manager を使用した StorSimple ソリューションの管理の詳細](storsimple-snapshot-manager-admin.md)
* [StorSimple Snapshot Manager のタスクとワークフローの詳細](storsimple-snapshot-manager-admin.md#storsimple-snapshot-manager-tasks-and-workflows)

