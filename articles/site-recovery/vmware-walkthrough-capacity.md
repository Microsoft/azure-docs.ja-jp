---
title: "Azure Site Recovery を使用した Azure への VMware レプリケーションの容量とスケーリングを計画する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して Azure に VMware VM をレプリケートする場合の容量とスケーリングを計画します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: rayne
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: f5b334e594e3d002e1862b25c4faba7163efa7d4
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="step-3-plan-capacity-and-scaling-for-vmware-to-azure-replication"></a>手順 3: VMware から Azure へのレプリケーションの容量とスケーリングを計画する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してオンプレミスの VMware VM および物理サーバーを Azure にレプリケートする場合に容量とスケーリングを計画する方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="how-do-i-start-capacity-planning"></a>容量計画はどのように開始すればよいか

レプリケーション環境に関する情報を収集し、この記事で強調されている考慮事項と共にこの情報を使用して容量を計画します。


## <a name="gather-information"></a>情報を収集する

1. VMware レプリケーションのための [Deployment Planner ツール](https://aka.ms/asr-deployment-planner)をダウンロードします。
2. [この記事を読み](site-recovery-deployment-planner.md)、ツールの実行方法を理解します。
3. このツールを使用して、互換および非互換の VM、VM あたりのディスク数、ディスクあたりのデータ チャーンに関する情報を収集します。 また、このツールでは、ネットワーク帯域幅の要件に加えて、レプリケーションとテスト フェールオーバーを正常に実行するために必要な Azure インフラストラクチャに関する情報も収集されます。

## <a name="replication-considerations"></a>レプリケーションに関する考慮事項

デプロイを開始する前に、次の考慮事項に注意してください。

**コンポーネント** | **詳細** |
--- | --- | ---
**レプリケーション** | **日次変更率の上限:** 保護されたマシンが使用できるプロセス サーバーは 1 つだけです。また、1 つのプロセス サーバーが対応できる日次変更率は最大 2 TB (テラバイト) です。 そのため、保護されたマシンでサポートされる 1 日のデータ変化率の上限は 2 TB です。<br/><br/> **最大スループット:** レプリケートされたマシンは、Azure の 1 つのストレージ アカウントに属することができます。 Standard ストレージ アカウントは、1 秒間に最大 20,000 要求を処理できます。ソース マシン全体の1 秒あたりの必要な入出力操作 (IOPS) 数を 20,000 以下に抑えることをお勧めします。 たとえば、5 個のディスクが搭載されたソース マシンで、各ディスクから 120 IOPS (8K サイズ) が生成される場合、Azure 内のディスクごとの IOPS 制限は 500 になります。 (必要なストレージ アカウントの数は、合計ソース マシン IOPS/20000 に等しいです)

## <a name="configuration-server-capacity"></a>構成サーバーの容量

構成サーバーは、保護されたマシンで実行されているすべてのワークロードの日次変更率容量を処理できる必要があります。また、Azure Storage にデータを継続的にレプリケートできる十分な帯域幅が必要です。

ベスト プラクティスとして、保護対象のマシンと同じネットワークおよび LAN セグメントに構成サーバーを配置します。 管理サーバーを別のネットワークに配置することもできますが、保護対象のマシンは管理サーバーに対して、レイヤー 3 のネットワーク可視性が必要です。

## <a name="sizing-recommendations"></a>サイズ設定に関する推奨事項

CPU に基づくサイズ設定に関する推奨事項を次の表に示します。

**CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5 ギガヘルツ [GHz]) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシンをレプリケートします。
12 vCPU (2 ソケット * 6 コア @ 2.5 GHz) | 18 GB | 600 GB | 500 GB ～ 1 TB | 100 ～ 150 台のマシンをレプリケートします。
16 vCPU (2 ソケット * 8 コア @ 2.5 GHz) | 32 GB | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 200 台のマシンをレプリケートします。
別のプロセス サーバーをデプロイします | | | > 2 TB | 200 台を超えるマシンをレプリケートする場合、または 1 日のデータ変化率が 2 TB を超える場合、追加のプロセス サーバーをデプロイします。

各値の説明:

* 各ソース マシンは、それぞれ 100 GB の 3 個のディスクで構成されています。
* キャッシュ ディスクの測定には、RAID 10 で 10,000 RPM の SAS ドライブが 8 個というベンチマーク用ストレージを使用しました。

## <a name="process-server-capacity"></a>プロセス サーバーの容量


プロセス サーバーは、保護されたマシンからレプリケーション データを受信し、キャッシュ、圧縮、および暗号化を使用して最適化します。 次に、データを Azure に送信します。

- プロセス サーバー マシンには、これらのタスクを実行できる十分なリソースが必要です。
- 最初のプロセス サーバーは、構成サーバーに既定でインストールされます。 追加のプロセス サーバーをデプロイして環境を拡張できます。
- プロセス サーバーは、ディスクベースのキャッシュを使用します。 ネットワークのボトルネックや障害が発生した場合に、格納されているデータの変更を処理できるように、600 GB 以上のキャッシュ ディスクを別に使用します。
- 200 台を超えるマシンを保護する必要がある場合や、日次変更率が 2 TB を超える場合は、レプリケーションの負荷を処理するプロセス サーバーを追加できます。 スケールアウトするには、次の方法があります。
    - 構成サーバーの数を増やします。 たとえば、2 つの構成サーバーを使用すると最大 400 台のマシンを保護できます。
    - プロセス サーバーを追加し、構成サーバーの代わりに (または構成サーバーに加えて) トラフィックの処理に使用します。


### <a name="example-process-server-scaling"></a>プロセス サーバーのスケーリングの例

以下の表は、次のようなシナリオの場合を示します。

* 構成サーバーをプロセス サーバーとして使用しない場合。
* 追加のプロセス サーバーをセットアップした場合。
* 追加のプロセス サーバーを使用するように保護対象の仮想マシンを構成した場合。
* 保護された各ソース マシンには、それぞれ 100 GB の 3 つのディスクが構成されています。

**構成サーバー** | **追加のプロセス サーバー** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン**
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア @ 2.5 GHz)、16 GB メモリ | 4 vCPU (2 ソケット * 2 コア @ 2.5 GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 85 台以下のマシンをレプリケートします。
8 vCPU (2 ソケット * 4 コア @ 2.5 GHz)、16 GB メモリ | 8 vCPU (2 ソケット * 4 コア @ 2.5 GHz)、12 GB メモリ | 600 GB | 250 GB ～ 1 TB | 85 ～ 150 台のマシンをレプリケートします。
12 vCPU (2 ソケット * 6 コア @ 2.5 GHz)、18 GB メモリ | 12 vCPU (2 ソケット * 6 コア @ 2.5 GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 TB ～ 2 TB | 150 ～ 225 台のマシンをレプリケートします。

サーバーのスケールの調整方法は、スケールアップ モデルまたはスケールアウト モデルのどちらを選択するかによって変わります。  少数のハイエンドの構成サーバーとプロセス サーバーをデプロイしてスケールアップするか、リソースが少ないサーバーを追加してスケールアウトします。 たとえば、220 台のマシンを保護する必要がある場合、次のいずれかを実行できます。

* 12 vCPU、18 GB のメモリが搭載された構成サーバーと、12 vCPU、24 GB のメモリが搭載された追加のプロセス サーバーをセットアップします。 追加のプロセス サーバーだけを使用するように保護されたマシンを構成します。
* 2 つの構成サーバー (2 x 8 vCPU、16 GB RAM) と 2 つの追加のプロセス サーバー (1 x 8 vCPU + 1 x 4 vCPU で 135 + 85 [220] 台のマシンに対応) を設定します。 追加のプロセス サーバーだけを使用するように保護されたマシンを構成します。

## <a name="deploy-additional-process-servers"></a>追加のプロセス サーバーをデプロイする

追加のプロセス サーバーをセットアップするには、次の手順に従います。 サーバーをセットアップしたら、サーバーを使用するソース マシンを移行します。

1. **[Site Recovery servers]\(Site Recovery サーバー\)** で、構成サーバー、**[+ プロセス サーバー]** の順にクリックします。
2. **[サーバーの種類]** で、**[Process server (on-premises)] \(プロセス サーバー (オンプレミス))** をクリックします。

    ![プロセス サーバー](./media/vmware-walkthrough-capacity/migrate-ps2.png)
3. Site Recovery 統合セットアップ ファイルをダウンロードします。
4. セットアップを実行してプロセス サーバーをインストールし、コンテナーに登録します。
5. **[開始する前に]** で **[Add additional process servers to scale out deployment] \(デプロイをスケールアウトするためにプロセス サーバーを追加する)** を選択します。
6. **[Configuration Server Details] \(構成サーバーの詳細)** で、構成サーバーの IP アドレスとパスフレーズを指定します。 パスフレーズを取得していない場合は、構成サーバーで **[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe –n** を実行して取得します。

    ![構成サーバー](./media/vmware-walkthrough-capacity/add-ps2.png)
7. 構成サーバーをセットアップしたときと同様に、セットアップの残りの手順を完了します。

### <a name="migrate-machines-to-use-the-process-server"></a>マシンを移行してプロセス サーバーを使用する

1. **[設定]** > **[Site Recovery servers]\(Site Recovery サーバー\)** で、構成サーバー、**[プロセス サーバー]** の順にクリックします。
2. 現在使用中のプロセス サーバーを右クリックし、**[切り替え]** をクリックします。

    ![プロセス サーバーの切り替え](./media/vmware-walkthrough-capacity/migrate-ps3.png)
3. **[ターゲット プロセス サーバーの選択]** で、使用するプロセス サーバーを選択し、そのサーバーで処理する VM を選択します。
4. 情報アイコンをクリックします。 負荷の決定に役立つように、選択した各 VM を新しいプロセス サーバーにレプリケートするために必要な平均容量が表示されます。
5. チェック マークをクリックして、新しいプロセス サーバーへのレプリケーションを開始します。

## <a name="control-network-bandwidth"></a>ネットワーク帯域幅の管理

[Deployment Planner ツール](site-recovery-deployment-planner.md)を実行して、レプリケーションに必要な帯域幅 (初期レプリケーションと差分) を計算した後、次の 2 つのオプションを使用してレプリケーションに使用する帯域幅の量を制御できます。

* **帯域幅を調整する**: Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。 プロセス サーバーとして実行されているマシンの帯域幅を調整できます。
* **帯域幅に影響を与える**: レジストリ キーをいくつか使用して、レプリケーションに使用される帯域幅に影響を与えることができます。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM** レジストリ値は、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を指定します。 値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。
  * **Hkey_local_machine \software\microsoft\windows Azure Backup\DownloadThreadsPerVM** は、フェールバック時にデータ転送に使用されるスレッドの数を指定します。

### <a name="throttle-bandwidth"></a>帯域幅を調整する

1. プロセス サーバーとして機能しているマシンで、Azure Backup MMC スナップインを開きます。 既定では、Backup のショートカットが、デスクトップかフォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]** をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** をオンにします。
4. 勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

    ![スロットル](./media/vmware-walkthrough-capacity/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。 サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

### <a name="influence-network-bandwidth-for-a-vm"></a>VM のネットワーク帯域幅に影響を与える

1. VM のレジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。
   * レプリケートするディスクで帯域幅のトラフィックに影響を与えるには、**UploadThreadsPerVM** の値を変更するか、このキーが存在しない場合は作成します。
   * Azure からのフェールバックのトラフィックの帯域幅に影響を与えるには、**DownloadThreadsPerVM** の値を変更します。
2. 既定値は 4 ですが、 プロビジョニング超過状態のネットワークの場合は、これらのレジストリ キーを変更する必要があります。 最大値は 32 です。 トラフィックを監視して値を最適化できます。




## <a name="next-steps"></a>次のステップ

[手順 4: ネットワークを計画する](vmware-walkthrough-network.md)方法に関するページに進む。

