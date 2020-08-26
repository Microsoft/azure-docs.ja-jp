---
title: Azure NetApp Files を使用したソリューション アーキテクチャ | Microsoft Docs
description: Azure NetApp Files を使用したソリューション アーキテクチャのベスト プラクティスへの参照を提供します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 79ad95179f4ad6d332a848e59ca341b8a9f90b1f
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258224"
---
# <a name="solution-architectures-using-azure-netapp-files"></a>Azure NetApp Files を使用したソリューション アーキテクチャ
この記事では、Azure NetApp Files を使用するためのソリューション アーキテクチャを理解するうえで役立つベスト プラクティスへの参照を提供します。  

次の図は、Azure NetApp Files が提供するソリューション アーキテクチャのカテゴリをまとめたものです。

![ソリューション アーキテクチャのカテゴリ](../media/azure-netapp-files/solution-architecture-categories.png)

## <a name="linux-oss-apps-and-database-solutions"></a>Linux OSS アプリとデータベース ソリューション

このセクションでは、Linux OSS アプリケーションとデータベースのソリューションに関するリファレンスを提供します。 

### <a name="oracle"></a>Oracle

* [Azure NetApp Files を使用した Azure への Oracle デプロイのベスト プラクティス ガイド](https://www.netapp.com/us/media/tr-4780.pdf)
* [Microsoft Azure での Oracle VM イメージとそのデプロイ:共有ストレージの構成オプション](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-vm-solutions#shared-storage-configuration-options)
* [Oracle Database での Azure NetApp Files 利用のメリット](solutions-benefits-azure-netapp-files-oracle-database.md)

## <a name="windows-apps-and-sql-server-solutions"></a>Windows アプリと SQL Server ソリューション

このセクションでは、Windows アプリケーションと SQL Server ソリューションに関するリファレンスを提供します。

### <a name="file-sharing-and-global-file-caching"></a>ファイル共有とグローバル ファイル キャッシュ

* [Build Your Own Azure NFS?Wrestling Linux File Shares into Cloud](https://cloud.netapp.com/blog/ma-anf-blg-build-your-own-linux-nfs-file-shares) (独自の Azure NFS の構築: Linux ファイル共有をクラウドに移行する)
* [グローバル ファイル キャッシュ / Azure NetApp Files のデプロイ](https://youtu.be/91LKb1qsLIM)

### <a name="sql-server"></a>SQL Server

* [Azure NetApp Files を使用して SMB 経由で SQL Server をデプロイする](https://www.youtube.com/watch?v=x7udfcYbibs)
* [Azure NetApp Files を使用して SMB 経由で SQL Server Always On フェールオーバー クラスターをデプロイする](https://www.youtube.com/watch?v=zuNJ5E07e8Q)
* [Azure NetApp Files を使用して Always On 可用性グループをデプロイする](https://www.youtube.com/watch?v=y3VQmzzeyvc)

## <a name="sap-on-azure-solutions"></a>SAP on Azure ソリューション

このセクションでは、SAP on Azure ソリューションのリファレンスを提供します。 

### <a name="generic-sap-and-sap-netweaver"></a>汎用 SAP と SAP Netweaver 

* [Azure NetApp Files を使用した Microsoft Azure 上の SAP アプリケーション](https://www.netapp.com/us/media/tr-4746.pdf)
* [SAP アプリケーション用の Azure NetApp Files を使用した SUSE Linux Enterprise Server 上の Azure VM 上の SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
* [SAP アプリケーション用の Azure NetApp Files を使用した Red Hat Enterprise Linux 上の SAP NetWeaver 用の Azure Virtual Machines の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* [SAP アプリケーション用の Azure NetApp Files (SMB) を使用した Windows 上の Azure VM における SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
* [Red Hat Enterprise Linux for SAP Applications マルチ SID 上の Azure VM での SAP NetWeaver の高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)

### <a name="sap-hana"></a>SAP HANA 

* [SAP HANA Azure 仮想マシンのストレージ構成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
* [SUSE Linux Enterprise Server 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
* [Red Hat Enterprise Linux 上で Azure NetApp Files を使用した Azure VM のスタンバイ ノードを使用して SAP HANA をスケールアウトする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)

### <a name="sap-tech-community-and-blog-posts"></a>SAP 技術コミュニティとブログ記事 

* [Azure NetApp Files – SAP HANA のバックアップ (秒単位)](https://blog.netapp.com/azure-netapp-files-sap-hana-backup-in-seconds/)
* [Azure NetApp Files – スナップショット バックアップから HANA データベースを復元する](https://blog.netapp.com/azure-netapp-files-backup-sap-hana)
* [Azure NetApp Files – クラウド同期を使用した SAP HANA のオフロード バックアップ](https://blog.netapp.com/azure-netapp-files-sap-hana)
* [Azure NetApp Files を使用して SAP HANA システムのコピーを高速化する](https://blog.netapp.com/sap-hana-faster-using-azure-netapp-files/)
* [Cloud Volumes ONTAP と Azure NetApp Files:SAP HANA システムの移行が簡単に](https://blog.netapp.com/cloud-volumes-ontap-and-azure-netapp-files-sap-hana-system-migration-made-easy/)

## <a name="virtual-desktop-infrastructure-solutions"></a>仮想デスクトップ インフラストラクチャ ソリューション

このセクションでは、仮想デスクトップ インフラストラクチャ ソリューションに関するリファレンスを提供します。

### <a name="windows-virtual-desktop"></a>Windows Virtual Desktop

* [Windows Virtual Desktop での Azure NetApp Files 利用のメリット](solutions-windows-virtual-desktop.md)
* [Windows Virtual Desktop の FSLogix プロファイル コンテナーのストレージ オプション](https://docs.microsoft.com/azure/virtual-desktop/store-fslogix-profile#azure-platform-details)
* [Azure NetApp Files を使用してホスト プール用の FSLogix プロファイル コンテナーを作成する](https://docs.microsoft.com/azure/virtual-desktop/create-fslogix-profile-container)
* [エンタープライズ規模の Windows Virtual Desktop](https://docs.microsoft.com/azure/architecture/example-scenario/wvd/windows-virtual-desktop)

## <a name="hpc-solutions"></a>HPC ソリューション

このセクションでは、ハイ パフォーマンス コンピューティング (HPC) ソリューションに関するリファレンスを提供します。 

### <a name="generic-hpc"></a>汎用 HPC

* [Azure NetApp Files: クラウド ストレージを最大限に活用する](https://cloud.netapp.com/hubfs/Resources/ANF%20PERFORMANCE%20TESTING%20IN%20TEMPLATE.pdf)
* [Azure Batch と Azure NetApp Files で MPI ワークロードを実行する](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
* [Azure CycleCloud:Azure NetApp Files 上の CycleCloud HPC 環境](https://docs.microsoft.com/azure/cyclecloud/overview)

### <a name="oil-and-gas"></a>石油、ガス

* [ハイ パフォーマンス コンピューティング (HPC):Azure での石油およびガス](https://techcommunity.microsoft.com/t5/azure-global/high-performance-computing-hpc-oil-and-gas-in-azure/ba-p/824926)
* [Azure 上での貯留層シミュレーション ソフトウェアの実行](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/reservoir-simulation)

### <a name="electronic-design-automation-eda"></a>電子設計自動化 (EDA)

* [電子設計自動化に Azure NetApp Files を使用するメリット](solutions-benefits-azure-netapp-files-electronic-design-automation.md)
* [Azure CycleCloud:Azure NetApp Files を使用した EDA HPC ラボ](https://github.com/Azure/cyclecloud-hands-on-labs/blob/master/EDA/README.md)

### <a name="analytics"></a>Analytics

* [Azure NetApp Files: Microsoft Azure 上の SAS グリッドで使用する新しい共有ファイル システム](https://communities.sas.com/t5/Architecture/Azure-NetApp-Files-A-new-shared-file-system-to-use-with-SAS-Grid/m-p/606978)

## <a name="azure-platform-services-solutions"></a>Azure プラットフォーム サービス ソリューション

このセクションでは、Azure プラットフォーム サービスのソリューションについて説明します。 

### <a name="azure-kubernetes-services-and-kubernetes"></a>Azure Kubernetes Services と Kubernetes

* [Azure NetApp Files と Azure Kubernetes Service を統合する](https://docs.microsoft.com/azure/aks/azure-netapp-files)
* [Azure NetApp Files を使用した Azure での現実離れした Kubernetes のパフォーマンス](https://cloud.netapp.com/blog/ma-anf-blg-configure-kubernetes-openshift)
* [Trident - コンテナー用のストレージ オーケストレーター](https://netapp-trident.readthedocs.io/en/stable-v20.04/kubernetes/operations/tasks/backends/anf.html)

### <a name="azure-batch"></a>Azure Batch

* [Azure Batch と Azure NetApp Files で MPI ワークロードを実行する](https://azure.microsoft.com/resources/run-mpi-workloads-with-azure-batch-and-azure-netapp-files/)
 
