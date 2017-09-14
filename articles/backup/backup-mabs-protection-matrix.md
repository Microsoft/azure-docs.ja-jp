---
title: "Azure Backup Server でバックアップ可能な内容 | Microsoft Docs"
description: "この記事では、Azure Backup Server v2 によって保護されるすべてのワークロード、データ型、およびインストールを一覧表示したサポート マトリックスを示します。"
services: backup
documentation center: 
author: markgalioto
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
keywords: 
ms.date: 05/15/2017
ms.topic: article
ms.author: markgal,masaran
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 3608c310f43ff9f97563d252437d50d962162f47
ms.contentlocale: ja-jp
ms.lasthandoff: 08/05/2017

---

# <a name="azure-backup-server-protection-matrix"></a>Azure Backup Server の保護マトリックス

この記事では、Azure Backup Server で保護できるさまざまなサーバーおよびワークロードを一覧表示します。 次のマトリックスは、Azure Backup Server v1 および v2 で保護できる内容を一覧表示しています。

## <a name="protection-support-matrix"></a>保護のサポート マトリックス

|ワークロード|バージョン|Azure Backup Server</br> インストール|Azure Backup</br> Server v2|Azure Backup</br> Server v1 |保護と回復|
|------------|-----------|--------------------|--------------------------------------------|--------------------------------|---------------------------|
|System Center VMM|VMM 2016、<br/>VMM 2012、SP1、R2|物理サーバー<br /><br />Hyper-V 仮想マシン|Y|Y|すべてのデプロイ シナリオ: データベース|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 10|物理サーバー<br /><br />Hyper-V 仮想マシン<br /><br />VMware 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 DPM はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8.1|物理サーバー<br /><br />Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS である必要があります。 FAT および FAT32 はサポートされていません。<br /><br />ボリュームは少なくとも 1 GB である必要があります。 DPM はボリューム シャドウ コピー サービス (VSS) を使用してデータ スナップショットを取得し、そのスナップショットはボリュームが少なくとも 1 GB である場合にのみ機能します。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8.1|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 8|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 7|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows 7|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows Vista SP2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows Vista SP1|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows Vista|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ファイル<br /><br />保護されるボリュームは NTFS であり、かつ少なくとも 1 GB である必要があります。|
|クライアント コンピューター (64 ビットおよび 32 ビット)|Windows Vista|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)、重複除去されたボリューム|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2016|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /><br />VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)<br /><br />物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y<br /><br />Not Nano サーバー|N|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)、重複除去されたボリューム|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012 R2 - Datacenter および Standard|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y |ボリューム、共有、フォルダー、ファイル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、DPM が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012 R2 - Datacenter および Standard|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル)<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、DPM が Windows Server 2012 または 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、DPM が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y|ボリューム、共有、フォルダー、ファイル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、DPM が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2012/2012 SP1 - Datacenter および Standard|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル<br /><br />Windows Server 2012 の重複除去されたボリュームを保護するには、DPM が少なくとも Windows Server 2012 R2 上で実行されている必要があります。|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y<br /><br />SP1 を実行しており、かつ [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855) をインストールする必要があります|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y<br /><br />SP1 を実行しており、かつ [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855) をインストールする必要があります|Y |ボリューム、共有、フォルダー、ファイル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2 SP1 - Standard および Enterprise|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y<br /><br />SP1 を実行しており、かつ [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855) をインストールする必要があります|Y |ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|N|Y|ボリューム、共有、フォルダー、ファイル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008 R2|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|N|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|N|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Server 2008|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|サーバー (32 ビットおよび 64 ビット)|Windows Storage Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|ボリューム、共有、フォルダー、ファイル、システム状態/ベア メタル|
|SQL Server|SQL Server 2016|物理サーバー <br /><br /> オンプレミスの Hyper-V 仮想マシン <br /> <br /> Azure 仮想マシン <br /><br /> VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y |N|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2014|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y |すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2014|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y |すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012 SP2|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008 R2|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y|すべてのデプロイ シナリオ: データベース|
|SQL Server|SQL Server 2008|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|すべてのデプロイ シナリオ: データベース|
|Exchange|Exchange 2016|物理サーバー<br/><br/> オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2016|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2013|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2013|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2010|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2010|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |保護 (すべてのデプロイ シナリオ): スタンドアロンの Exchange サーバー、データベース可用性グループ (DAG) の下のデータベース<br /><br />回復 (すべてのデプロイ シナリオ): メールボックス、DAG の下のメールボックス データベース|
|Exchange|Exchange 2007|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): ストレージ グループ<br /><br />回復 (すべてのデプロイ シナリオ): ストレージ グループ、データベース、メールボックス|
|Exchange|Exchange 2007|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |保護 (すべてのデプロイ シナリオ): ストレージ グループ<br /><br />回復 (すべてのデプロイ シナリオ): ストレージ グループ、データベース、メールボックス|
|SharePoint|SharePoint 2016|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン<br /><br />Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)<br /><br />VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y |N|保護 (すべてのデプロイ シナリオ): ファーム、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合) - DPM 2012 R2 更新プログラム ロールアップ 3 以降|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2013|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y |保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー<br /><br />コンテンツ データベースに対する SQL Server 2012 の AlwaysOn 機能を使用している SharePoint ファームの保護はサポートされていないことに注意してください。|
|SharePoint|SharePoint 2010|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2010|Azure 仮想マシン (ワークロードが Azure 仮想マシンとして実行されている場合)|Y|Y |保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2010|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2007|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|SharePoint|SharePoint 2007|VMWare 内の Windows 仮想マシン (VMWare 内の Windows 仮想マシンで実行されているワークロードを保護します)|Y|Y|保護 (すべてのデプロイ シナリオ): ファーム、SharePoint 検索、フロントエンド Web サーバーのコンテンツ<br /><br />回復 (すべてのデプロイ シナリオ): ファーム、データベース、Web アプリケーション、ファイルまたはリスト項目、SharePoint 検索、フロントエンド Web サーバー|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の DPM 保護エージェント|Windows Server 2016|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|N|保護: Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダー、ボリューム、仮想ハード ドライブの項目レベルの回復|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の DPM 保護エージェント|Windows Server 2012 R2 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護: Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダー、ボリューム、仮想ハード ドライブの項目レベルの回復|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の DPM 保護エージェント|Windows Server 2012 - Datacenter および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護: Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダー、ボリューム、仮想ハード ドライブの項目レベルの回復|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の DPM 保護エージェント|Windows Server 2008 R2 SP1 - Enterprise および Standard|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|Y|Y|保護: Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダー、ボリューム、仮想ハード ドライブの項目レベルの回復|
|Hyper-V ホスト - Hyper-V ホスト サーバー、クラスター、または VM 上の DPM 保護エージェント|Windows Server 2008|物理サーバー<br /><br />オンプレミスの Hyper-V 仮想マシン|N|N|保護: Hyper-V コンピューター、クラスター共有ボリューム (CSV)<br /><br />回復: 仮想マシン、ファイルとフォルダー、ボリューム、仮想ハード ドライブの項目レベルの回復|
|VMware VM|VMware server 5.5、6.0、または 6.5 |オンプレミスの Hyper-V 仮想マシン|Y|Y (UR1 による)|VMware VMs on クラスターの共有ボリューム (CSV) 上の VMware VM、NFS、および SAN ストレージ。<br /> 項目レベルのファイルとフォルダーの回復は Windows でのみ使用できます。<br /> VMware vApps はサポートされません。|
|Linux|Hyper-V または VMware ゲストとして実行されている Linux|オンプレミスの Hyper-V 仮想マシン|Y|Y|Hyper-V が Windows Server 2012 R2 または Windows Server 2016 上で実行されている必要があります。 保護: 仮想マシン全体<br /><br />回復: 仮想マシン全体|

## <a name="cluster-support"></a>クラスターのサポート
Azure Backup Server は、次のクラスタ化されたアプリケーション内のデータを保護できます。

-   ファイル サーバー

-   SQL Server

-   Hyper-V - スケールアウトされた DPM 保護を使用して Hyper-V クラスターを保護する場合、保護された Hyper-V ワークロードに二次的な保護を追加することはできません。

    Windows Server 2008 R2 上で Hyper-V を実行する場合は、KB [975354](https://support.microsoft.com/en-us/kb/975354) で説明されている更新プログラムをインストールするようにしてください。
    クラスター構成内の Windows Server 2008 R2 上で Hyper-V を実行する場合は、SP2 および KB [971394](https://support.microsoft.com/en-us/kb/971394) をインストールするようにしてください。

-   Exchange Server - Azure Backup Server は、サポートされている Exchange Server バージョンの非共有ディスク クラスター (クラスター連続レプリケーション) を保護することができるほか、ローカル連続レプリケーションのために構成された Exchange Server も保護できます。

-   SQL Server - Azure Backup Server は、クラスター共有ボリューム (CSV) 上にホストされた SQL Server データベースのバックアップをサポートしていません。

Azure Backup Server は、DPM サーバーと同じドメイン内、および子または信頼できるドメイン内に配置されたクラスター ワークロードを保護できます。 信頼されていないドメインまたはワークグループ内のデータ ソースを保護する場合は、単一サーバーに対しては NTLM または証明書認証、クラスターに対しては証明書認証のみを使用します。

