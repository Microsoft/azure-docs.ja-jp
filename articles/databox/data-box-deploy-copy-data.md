---
title: Microsoft Azure Data Box に SMB 経由でデータをコピーする | Microsoft Docs
description: Azure Data Box に SMB 経由でデータをコピーする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: alkohli
ms.openlocfilehash: 6349ced07385ede42b21c9a8401dd3e0a23bcfbe
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790302"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>チュートリアル:Azure Data Box に SMB 経由でデータをコピーする

このチュートリアルでは、ローカル Web UI を使用してホスト コンピューターに接続し、そこからデータをコピーして、Data Box を発送する準備をする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Data Box に接続する
> * Data Box にデータをコピーする
> * Data Box の発送を準備する

## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. [Azure Data Box の設定に関するチュートリアル](data-box-deploy-set-up.md)を完了していること。
2. Data Box の受け取りが済んでいて、ポータル内での注文の状態が **[配信済み]** であること。
3. Data Box にコピーするデータが格納されているホスト コンピューターがあること。 このホスト コンピューターは次の条件を満たしている必要があります。
    - [サポート対象のオペレーティング システム](data-box-system-requirements.md)が実行されていること。
    - 高速ネットワークに接続していること。 10 GbE 接続を少なくとも 1 つ利用することを強くお勧めします。 10 GbE 接続を利用できない場合は、1 GbE データ リンクを使用します。ただし、コピー速度は低下します。 

## <a name="connect-to-data-box"></a>Data Box に接続する

選択したストレージ アカウントに基づいて、Data Box では最大で次のものが作成されます。
- GPv1 および GPv2 に対して関連付けられているストレージ アカウントごとに 3 つの共有。
- Premium または BLOB ストレージ アカウントに対して 1 つの共有。

ブロック BLOB とページ BLOB の共有では、第 1 レベルのエンティティはコンテナーであり、第 2 レベルのエンティティは BLOB です。 Azure Files の共有では、第 1 レベルのエンティティは共有であり、第 2 レベルのエンティティはファイルです。

次の表は、Data Box 上の共有への UNC パスと、データのアップロード先である Azure Storage のパスの URL を示しています。 Azure Storage の最終的なパスの URL は、UNC 共有パスから導き出すことができます。
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Azure ブロック BLOB | <li>共有への UNC パス: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure ページ BLOB  | <li>共有への UNC パス: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>共有への UNC パス: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage の URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Windows Server ホスト コンピューターを使用している場合は、次の手順に従って Data Box に接続します。

1. 最初に、認証を行ってセッションを開始します。 **[接続とコピー]** に移動します。 **[資格情報の取得]** をクリックして、ストレージ アカウントに関連付けられている共有用のアクセス資格情報を取得します。 

    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. [共有にアクセスしてデータをコピーする] ダイアログ ボックスで、共有に対応する **[ユーザー名]** と **[パスワード]** をコピーします。 Click **OK**.
    
    ![共有の資格情報を取得する 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. ストレージ アカウントに関連付けられている共有 (以下の例の場合は、*devicemanagertest1*) にホスト コンピューターからアクセスするため、コマンド ウィンドウを開きます。 コマンド プロンプトに、次のコマンドを入力します。

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    データ形式に応じて、共有パスは次のようになっています。
    - Azure ブロック BLOB - `\\10.126.76.172\devicemanagertest1_BlockBlob`
    - Azure ページ BLOB - `\\10.126.76.172\devicemanagertest1_PageBlob`
    - Azure Files - `\\10.126.76.172\devicemanagertest1_AzFile`
    
4. メッセージに従って共有のパスワードを入力します。 上記のコマンドを使用して共有に接続する例を次に示します。

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Windows キーを押しながら R キーを押します。**[ファイル名指定して実行]** ウィンドウで、「`\\<device IP address>`」と入力します。 **[OK]** をクリックして、エクスプローラーを開きます。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    共有がフォルダーとして表示されます。
    
    **常にコピーしようとするファイル用のフォルダーを共有下に作成してから、ファイルをそのフォルダーにコピーします**。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *$root* フォルダーに直接ファイルをコピーすることはできません。
    
    ![エクスプローラーで共有に接続する 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

## <a name="copy-data-to-data-box"></a>Data Box にデータをコピーする

Data Box 共有に接続したら、次にデータをコピーします。 データのコピーを開始する前に、次の考慮事項を確認してください。

- 適切なデータ形式に対応する共有にデータをコピーする必要があります。 たとえば、ブロック BLOB データは、ブロック BLOB 用の共有にコピーしてください。 データ形式が適切な共有の種類と一致しない場合は、後続の手順で、Azure へのデータのアップロードに失敗します。
-  データをコピーするときは、データのサイズが、「[Azure Storage および Data Box の制限に関する記事](data-box-limits.md)」に記載されているサイズ制限に従っていること確認してください。
- Data Box によってアップロードされているデータが、Data Box の外部で別のアプリケーションによって同時にアップロードされた場合、アップロード ジョブ エラーやデータの破損が生じる可能性があります。
- SMB と NFS の両方を同時に使用しないこと、また、同じデータを Azure 上の同じ最終コピー先にコピーしないことをお勧めします。 このようにした場合は、最終的な結果が不確定になります。
- 必ず、コピーするファイル用のフォルダーを共有の下に作成してから、ファイルをそのフォルダーにコピーしてください。 ブロック BLOB およびページ BLOB の共有の下に作成したフォルダーは、データが BLOB としてアップロードされるコンテナーになります。 ストレージ アカウント内の *$root* フォルダーに直接ファイルをコピーすることはできません。

SMB 共有に接続した後、データのコピーを開始します。 Robocopy などの SMB 互換ファイル コピー ツールを使用して、データをコピーできます。 Robocopy を使用して、複数のコピー ジョブを開始できます。 次のコマンドを使用します。
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 属性については次の表で説明します。
    
|Attribute  |説明  |
|---------|---------|
|/e     |サブディレクトリをコピーします (空のディレクトリを含む)。         |
|/r:     |失敗したコピーの再試行回数を指定します。         |
|/w:     |再試行間の待ち時間 (秒) を指定します。         |
|/is     |同じファイルを含めます。         |
|/nfl     |ファイル名をログに記録しないことを指定します。         |
|/ndl    |ディレクトリ名をログに記録しないことを指定します。        |
|/np     |コピー操作の進行状況 (それまでにコピーされたファイルまたはディレクトリの数) が表示されないように指定します。 進行状況を表示すると、パフォーマンスが大幅に低下します。         |
|/MT     | マルチスレッドを使用します (推奨は 32 または 64 スレッド)。 このオプションは、暗号化されたファイルでは使用されません。 暗号化されたファイルと暗号化されていないファイルを分離することが必要な場合があります。 ただし、シングルスレッドのコピーでは、パフォーマンスが大幅に低下します。           |
|/fft     | 任意のファイル システムのタイムスタンプの細分性を下げるために使用します。        |
|/b     | バックアップ モードでファイルをコピーします。        |
|/z    | 再起動モードでファイルをコピーします。環境が不安定な場合はこれを使用します。 このオプションでは、追加ログによりスループットが低下します。      |
| /zb     | 再起動モードを使用します。 アクセスが拒否された場合、このオプションはバックアップ モードを使用します。 このオプションでは、チェックポイント処理のためにスループットが低下します。         |
|/efsraw     | すべての暗号化されたファイルを EFS raw モードでコピーします。 暗号化されたファイルでのみ使用します。         |
|log+:<LogFile>| 既存のログ ファイルに出力を追加します。|    
 
次のサンプルは、Data Box にファイルをコピーする robocopy コマンドの出力を示しています。
    
    C:\Users>robocopy
        -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:53 PM
            Simple Usage :: ROBOCOPY source destination /MIR
    
                    source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                    /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
       

パフォーマンスを最適化するには、データをコピーするときに、次の robocopy パラメーターを使用します。

|    プラットフォーム    |    ほとんどが小さいファイル (< 512 KB)                           |    ほとんどが中規模のファイル (512 KB ～ 1 MB)                      |    ほとんどが大きいファイル (> 1 MB)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy セッション <br> セッションあたり 16 スレッド    |    3 Robocopy セッション <br> セッションあたり 16 スレッド    |    2 Robocopy セッション <br> セッションあたり 24 スレッド    |  |


Robocopy コマンドについて詳しくは、「[Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)」(Robocopy といくつかの例) をご覧ください。

コピー先フォルダーを開いて、コピー済みのファイルを表示し、確認します。 コピー処理中にエラーが発生した場合は、トラブルシューティングのためにエラー ファイルをダウンロードします。

データの整合性を保証するため、データがコピーされるときにインラインでチェックサムが計算されます。 コピーが完了したら、デバイスで使用済み領域と空き領域を確認します。
    
   ![ダッシュボードで空き領域と使用済み領域を確認する](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="prepare-to-ship"></a>発送の準備をする

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Data Box に関する次のようなトピックについて説明しました。

> [!div class="checklist"]
> * Data Box に接続する
> * Data Box にデータをコピーする
> * Data Box の発送を準備する

次のチュートリアルに進み、Data Box を Microsoft に返送する方法を学習してください。

> [!div class="nextstepaction"]
> [Azure Data Box を Microsoft に発送する](./data-box-deploy-picked-up.md)

