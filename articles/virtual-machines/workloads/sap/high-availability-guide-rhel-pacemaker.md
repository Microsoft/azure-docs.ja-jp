---
title: Azure での RHEL に対する Pacemaker の設定 | Microsoft Docs
description: Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/04/2020
ms.author: radeltch
ms.openlocfilehash: a1e097692eade956446b46782bca5ecf3a17de75
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800264"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Azure の Red Hat Enterprise Linux に Pacemaker をセットアップする

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


はじめに、次の SAP Note およびガイドを確認してください

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧。
  * Azure VM サイズの容量に関する重要な情報。
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ。
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン。
* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2002167] では、Red Hat Enterprise Linux 用の OS 設定が推奨されています
* SAP Note [2009879] には、Red Hat Enterprise Linux 用の SAP HANA ガイドラインが記載されています
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1999351]: Azure Enhanced Monitoring Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Linux 上の SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [Linux 上の SAP のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [Linux 上の SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* [Pacemaker クラスターでの SAP HANA システム レプリケーション](https://access.redhat.com/articles/3004101)
* 一般的な RHEL ドキュメント
  * [高可用性アドオンの概要](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [高可用性アドオンの管理](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [高可用性アドオンの参照](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [RHEL 高可用性クラスターのサポートポリシー - sbd と fence_sbd](https://access.redhat.com/articles/2800691)
* Azure 固有の RHEL ドキュメント:
  * [RHEL 高可用性クラスターに関するポリシーをサポート - クラスター メンバーとしての Microsoft Azure Virtual Machines](https://access.redhat.com/articles/3131341)
  * [Microsoft Azure 上で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)
  * [RHEL 7.6 上の Pacemaker で SAP S/4HANA ASCS/ERS と Standalone Enqueue Server 2 (ENSA2) を構成する](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>クラスターのインストール

![RHEL における Pacemaker の概要](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

> [!NOTE]
> Red Hat では、ソフトウェアでエミュレートするウォッチドッグはサポートしていません。 Red Hat では、クラウド プラットフォームの SBD はサポートしていません。 詳細については、「[Support Policies for RHEL High Availability Clusters - sbd and fence_sbd](https://access.redhat.com/articles/2800691)」(RHEL 高可用性クラスターのサポートポリシー - sbd と fence_sbd) に関するページを参照してください。
> Azure 上の Pacemaker が使用された Red Hat Enterprise Linux クラスターでサポートされるフェンス メカニズムは、Azure フェンス エージェントのみです。  

次の各手順の先頭には、 **[A]** - 全ノードが該当、 **[1]** - ノード 1 のみ該当、 **[2]** - ノード 2 のみ該当、のいずれかが付いています。

1. **[A]** 登録します

   使用する仮想マシンを登録し、それを、RHEL 7 リポジトリを含むプールに追加します。

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Azure Marketplace PAYG RHEL イメージにプールをアタッチすると、RHEL の使用に対して二重に請求されることになります。PAYG イメージに対して 1 回、アタッチするプールの RHEL エンタイトルメントに対して 1 回請求されます。 これを防ぐ目的で、Azure では BYOS RHEL イメージが提供されるようになりました。 詳細については[こちら](../redhat/byos.md)を参照してください。

1. **[A]** SAP のリポジトリ用に RHEL を有効にします

   必要なパッケージをインストールするには、次のリポジトリを有効にします。

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** RHEL HA アドオンをインストールします

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > リソースの停止に失敗した場合や、クラスター ノードが互いに通信できなくなった場合に、お客様がフェールオーバー時間の高速化の恩恵を受けられるよう、次のバージョン (またはこれら以降) の Azure Fence Agent をお勧めします。  
   > RHEL 7.6: fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5: fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4: fence-agents-4.0.11-66.el7_4.12  
   > 詳細については、「[Azure VM running as a RHEL High Availability cluster member take a very long time to be fenced, or fencing fails / times-out before the VM shuts down](https://access.redhat.com/solutions/3408711)」 (RHEL 高可用性クラスターのメンバーを実行している Azure VM のフェンシングに非常に長い時間がかかる、フェンシングが失敗する、VM がシャットダウンする前にタイムアウトする) をご覧ください。

   Azure Fence Agent のバージョンを確認します。 必要に応じて、前述したものと同じかそれ以降のバージョンに更新します。

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Azure Fence Agent を更新する必要があり、カスタム ロールを使用している場合は、カスタム ロールを更新してアクション **powerOff** を含めるようにします。 詳細については、「[フェンス エージェントのカスタム ロールを作成する](#1-create-a-custom-role-for-the-fence-agent)」を参照してください。  

1. **[A]** ホスト名解決を設定します

   DNS サーバーを使用するか、すべてのノードの /etc/hosts を変更します。 この例では、/etc/hosts ファイルを使用する方法を示しています。
   次のコマンドの IP アドレスとホスト名を置き換えます。  

   >[!IMPORTANT]
   > クラスター構成でホスト名を使用する場合は、信頼できるホスト名解決が不可欠です。 名前が使用できず、クラスターのフェールオーバーの遅延につながる可能性がある場合、クラスター通信は失敗します。
   > /etc/hosts を使用する利点は、単一障害点になる可能性もある DNS からクラスターを独立させられることです。  

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   次の行を /etc/hosts に挿入します。 お使いの環境に合わせて IP アドレスとホスト名を変更します

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** hacluster のパスワードを同じパスワードに変更します

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Pacemaker のファイアウォール規則を追加します

   クラスター ノード間のすべてのクラスターの通信用に、次のファイアウォール規則を追加します。

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** 基本的なクラスター サービスを有効にします

   次のコマンドを実行し、Pacemaker サービスを有効にして、それを開始します。

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Pacemaker クラスターを作成します

   次のコマンドを実行し、ノードを認証してクラスターを作成します。 トークンを 30000 に設定してメモリ保持メンテナンスを可能にします。 詳細については、[Linux のこの記事][virtual-machines-linux-maintenance]を参照してください。

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** 予想される票を設定します

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

1. **[1]** 同時フェンス アクションを許可します

   <pre><code>sudo pcs property set concurrent-fencing=true
   </code></pre>

## <a name="create-stonith-device"></a>STONITH デバイスの作成

STONITH デバイスは、サービス プリンシパルを使用して Microsoft Azure を承認します。 サービス プリンシパルを作成するには、次に手順に従います。

1. [https://resources.azure.com](<https://portal.azure.com>) に移動します
1. [Azure Active Directory] ブレードを開きます  
   [プロパティ] に移動し、ディレクトリ ID をメモします。 これは、**テナント ID** です。
1. [アプリの登録] を選択します
1. [New Registration]\(新規登録\) をクリックします
1. 名前を入力し、[Accounts in this organization directory only]\(この組織ディレクトリ内のアカウントのみ\) を選択します 
2. [アプリケーションの種類] は "Web" を選択し、サインオン URL (たとえば http:\//localhost) を入力して、[追加] をクリックします  
   サインオン URL は使用されず、任意の有効な URL を指定することができます
1. [Certificates and Secrets]\(証明書とシークレット\) を選択し、[New client secret]\(新しいクライアント シークレット\) をクリックします
1. 新しいキーの説明を入力し、[Never expires]\(有効期限なし\) を選択して [追加] をクリックします
1. 値をメモします。 この値は、サービス プリンシパルの**パスワード**として使用します
1. [概要] を選択します。 アプリケーション ID をメモします。 これは、サービス プリンシパルのユーザー名 (下記の手順の**ログイン ID**) として使用します

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** フェンス エージェントのカスタム ロールを作成する

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 クラスターのすべての仮想マシンを開始および停止 (電源オフ) する権限を、サービス プリンシパルに付与する必要があります。 まだカスタム ロールを作成していない場合は、[PowerShell](../../../role-based-access-control/role-assignments-powershell.md) または [Azure CLI](../../../role-based-access-control/role-assignments-cli.md) を使って作成してください

入力ファイルには次の内容を使用します。 実際のサブスクリプションに合わせて内容を調整する必要があります。つまり、c276fc76-9cd4-44c9-99a7-4fd71546436e and e91d47c4-76f3-4271-a796-21b4ecfe3624 は、ご利用のサブスクリプションの ID に置き換えます。 ご利用のサブスクリプションが 1 つしかない場合は、AssignableScopes の 2 つ目のエントリは削除してください。

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** サービス プリンシパルにカスタム ロールを割り当てる

最後の章で作成したカスタム ロール "Linux Fence Agent Role" をサービス プリンシパルに割り当てます。 所有者ロールは今後使わないでください。

1. [https://resources.azure.com](https://portal.azure.com ) に移動します
1. [All resources] \(すべてのリソース) ブレードを開きます
1. 1 つ目のクラスター ノードの仮想マシンを選択します
1. [アクセス制御 (IAM)] を選択します
1. [ロールの割り当ての追加] をクリックします
1. "Linux Fence Agent Role" ロールを選択します
1. 上記で作成したアプリケーションの名前を入力します
1. [保存] をクリックします。

2 つ目のクラスター ノードについても上記の手順を繰り返します。

### <a name="1-create-the-stonith-devices"></a>**[1]** STONITH デバイスを作成します

仮想マシンのアクセス許可を編集したあとで、クラスターの STONITH デバイスを構成できます。

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

次のコマンドを使用してフェンス デバイスを構成します。

> [!NOTE]
> 'pcmk_host_map' のオプションは、RHEL ホスト名と Azure ノード名が同一でない場合のみ、このコマンドで必要です。 このコマンドの太字のセクションを参照してください。

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> \
power_timeout=240 pcmk_reboot_timeout=900 pcmk_monitor_timeout=120 pcmk_monitor_retries=4 pcmk_action_limit=3 \
op monitor interval=3600
</code></pre>

> [!IMPORTANT]
> 監視およびフェンス操作は逆シリアル化されます。 その結果、長時間実行されている監視操作と同時フェンス イベントがある場合、既に実行されている監視操作により、クラスターのフェールオーバーに遅延は発生しません。  

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** STONITH デバイスの使用を有効にします

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

> [!TIP]
>Azure Fence Agent では、[標準 ILB を使用した VM 用のパブリック エンドポイント接続](./high-availability-guide-standard-load-balancer-outbound-connections.md)に関する記事で説明されているように、使用可能なソリューションと共に、パブリック エンドポイントへの送信接続が必要です。  

## <a name="next-steps"></a>次のステップ

* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
* Azure VM 上の SAP HANA の高可用性を確保し、ディザスター リカバリーを計画する方法を確認するには、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性][sap-hana-ha]」を参照してください。
