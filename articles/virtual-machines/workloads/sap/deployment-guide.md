---
title: SAP NetWeaver のための Azure Virtual Machines のデプロイ | Microsoft Docs
description: Azure の Linux 仮想マシンに SAP ソフトウェアをデプロイする方法について説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/16/2020
ms.author: sedusch
ms.openlocfilehash: 6b66db639e0b22a7e0c45486465a1fa395aba33d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653513"
---
# <a name="azure-virtual-machines-deployment-for-sap-netweaver"></a>SAP NetWeaver のための Azure Virtual Machines のデプロイ

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../../../cli-install-nodejs.md
[azure-cli-2]:https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md (SAP のための Azure Virtual Machines DBMS のデプロイ)
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM と VHD のキャッシング)
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (ソフトウェア RAID)
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure Storage)
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS デプロイの構造)
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM の高可用性とディザスター リカバリー)
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 以降)
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 以前のリリース)
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (Azure Marketplace の SQL Server イメージを使用する)
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (Azure での一般的な SAP 用 SQL Server の概要)
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS の詳細)
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (ストレージの構成)
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (バックアップと復元)
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (バックアップと復元のパフォーマンスに関する考慮事項)
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (その他)
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md (SAP のための Azure Virtual Machines のデプロイ)
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP リソース)
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (カスタム イメージを使用して VM をデプロイする)
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (シナリオ 1: SAP 用 Azure Marketplace から VM をデプロイする)
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする)
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (シナリオ 3: SAP を含む汎用化されていない Azure VHD を使用してオンプレミスから VM を移動する)
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure 上の SAP 用 VM のデプロイ シナリオ)
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (Azure PowerShell コマンドレットのデプロイ)
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (SAP 関連 PowerShell コマンドレットのダウンロードとインポート)
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (オンプレミス ドメインへの VM の参加 [Windows のみ])
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux)
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (Azure VM エージェントのダウンロード、インストール、有効化)
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell)
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI)
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (Azure Extension for SAP を構成する)
[deployment-guide-configure-new-extension-ps]:deployment-guide.md#2ad55a0d-9937-4943-9dd2-69bc2b5d3de0 (Azure PowerShell を使用して新しい Azure Extension for SAP を構成する)
[deployment-guide-configure-new-extension-cli]:deployment-guide.md#c8749c24-fada-42ad-b114-f9aae2dc37da (Azure CLI を使用して新しい Azure Extension for SAP を構成する)
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Azure Extension for SAP の適合性チェック)
[deployment-guide-5.1-new]:deployment-guide.md#7bf24f59-7347-4c7a-b094-4693e4687ee5 (新しい Azure Extension for SAP の適合性チェック)
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Azure Extension for SAP の構成の正常性チェック)
[deployment-guide-5.2-new]:deployment-guide.md#464ac96d-7d3c-435d-a5ae-3faf3bfef4b3 (新しい Azure Extension for SAP の構成の正常性チェック)
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (Azure Extension for SAP のトラブルシューティング)
[deployment-guide-5.3-new]:deployment-guide.md#b7afb8ef-a64c-495d-bb37-2af96688c530 (新しい Azure Extension for SAP のトラブルシューティング)
[deployment-guide-contact-support]:deployment-guide.md#3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2 (Azure Extension for SAP のトラブルシューティング - サポートに問い合わせる)
[deployment-guide-run-the-script]:deployment-guide.md#0d2847ad-865d-4a4c-a405-f9b7baaa00c7 (Azure Extension for SAP のトラブルシューティング - セットアップ スクリプトを実行する)
[deployment-guide-redeploy-after-sysprep]:deployment-guide.md#2cd61f22-187d-42ed-bb8c-def0c983d756 (Azure Extension for SAP のトラブルシューティング - sysprep 後に再デプロイする)
[deployment-guide-fix-internet-connection]:deployment-guide.md#e92bc57d-80d9-4a2b-a2f4-16713a22ad89 (Azure Extension for SAP のトラブルシューティング - インターネット接続を修正する)


[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (VM 拡張機能を構成する)
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (プロキシの構成)
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (確認とトラブルシューティング)

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-Azvmaemextension]:https://docs.microsoft.com/powershell/module/az.compute/set-azvmaemextension

[planning-guide]:planning-guide.md (SAP のための Azure Virtual Machines の計画と実装)
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (リソース)
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure Virtual Machines で実行する SAP NetWeaver の高可用性とディザスター リカバリー)
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP アプリケーション サーバーの高可用性)
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (SAP インスタンスでの Autostart の使用)
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (クラウドのみ - オンプレミスの顧客ネットワークとの依存関係を持たない仮想マシンの Azure へのデプロイ)
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (クロスプレミス - オンプレミスのネットワークに完全に統合された 1 つまたは複数の SAP VM の Azure へのデプロイ)
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure リージョン)
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (障害ドメイン)
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (アップグレード ドメイン)
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure の可用性セット)
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure の仮想マシンの概念)
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (汎用化されていないディスクを使用してオンプレミスから Microsoft Azure に VM を移動する)
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (顧客固有のイメージを使用する VM のデプロイ)
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (オンプレミスから汎用でないディスクを使用する Azure に VM を移動する準備)
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (SAP 用の顧客固有のイメージを使用する VM のデプロイの準備)
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (Azure 用の VM と SAP の準備)
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure ディスクと Azure イメージの違い)
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (オンプレミスから Azure への VHD のアップロード)
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (Azure Storage アカウント間でのディスクのコピー)
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP デプロイ用の VM/VHD 構造)
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (アタッチされたディスクに対する自動マウントの設定)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure Monitoring Solution for SAP)
[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure のネットワーク)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (ストレージ: Microsoft Azure Storage とデータ ディスク)

[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/network-overview.md
[sap-pam]:https://support.sap.com/pam (SAP 製品の可用性マトリックス)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-os-disk-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk-md%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-2-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image-md%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-md%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image-md%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-Az-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md (Azure Resource Manager テンプレートと Azure CLI を使用した仮想マシンのデプロイと管理)
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md (Azure Resource Manager と PowerShell を使用した仮想マシンの管理)
[virtual-machines-windows-agent-user-guide]:../../extensions/agent-windows.md
[virtual-machines-linux-agent-user-guide]:../../extensions/agent-linux.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../extensions/agent-linux.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability]:../../linux/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:../../windows/quick-create-powershell.md
[virtual-machines-sizes]:../../linux/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../windows/upload-image.md
[virtual-machines-windows-tutorial]:../../windows/quick-create-portal.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md
[qs-configure-powershell-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md
[qs-configure-cli-windows-vm]:../../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md
[howto-assign-access-powershell]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md
[howto-assign-access-cli]:../../../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Virtual Machines は、時間のかかる調達サイクルなしに、最短時間でコンピューティング リソースとストレージ リソースを必要とする組織向けのソリューションです。 Azure Virtual Machines を使用して、SAP NetWeaver ベースのアプリケーションなど、従来のアプリケーションを Azure にデプロイできます。 オンプレミスのリソースを追加せずに、アプリケーションの信頼性と可用性を高めます。 Azure Virtual Machines はクロスプレミス接続をサポートしているので、Azure Virtual Machines を組織のオンプレミスのドメイン、プライベート クラウド、SAP システム ランドスケープに統合できます。

この記事では、代替のデプロイ方法やトラブルシューティングも含め、Azure の仮想マシン (VM) に SAP アプリケーションをデプロイする手順について説明します。 この記事は、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の情報に基づいています。 また、この記事は、SAP ソフトウェアをインストールしてデプロイするときの主要リソースである SAP インストール ドキュメントと SAP Note を補完するものです。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

SAP ソフトウェアのデプロイ用の Azure 仮想マシンをセットアップするには、複数の手順とリソースが必要となります。 作業を開始する前に、Azure の仮想マシンにSAP ソフトウェアをインストールするための前提条件を満たしていることを確認してください。

### <a name="local-computer"></a>ローカル コンピューター

Windows VM または Linux VM を管理するには、PowerShell スクリプトと Azure Portal を使用します。 どちらのツールも、Windows 7 以降のバージョンの Windows を実行しているローカル コンピューターが必要です。 Linux VM だけを管理し、この作業に Linux コンピューターを使用する場合は、Azure CLI を使用できます。

### <a name="internet-connection"></a>インターネット接続

SAP ソフトウェアのデプロイに必要なツールとスクリプトをダウンロードして実行するには、インターネットに接続している必要があります。 また、Azure Extension for SAP が実行されている Azure VM でも、インターネットへのアクセスが必要です。 Azure VM が Azure 仮想ネットワークまたはオンプレミス ドメインに属している場合は、「[プロキシの構成][deployment-guide-configure-proxy]」の説明に従って関連するプロキシ設定が構成されていることを確認してください。

### <a name="microsoft-azure-subscription"></a>Microsoft Azure サブスクリプション

アクティブな Azure アカウントが必要です。

### <a name="topology-and-networking"></a>トポロジとネットワーク

Azure における SAP デプロイのトポロジとアーキテクチャを定義する必要があります。

* 使用する Azure ストレージ アカウント
* SAP システムをデプロイする仮想ネットワーク
* SAP システムをデプロイするリソース グループ
* SAP システムをデプロイする Azure リージョン
* SAP 構成 (2 層または 3 層)
* VM サイズと、VM にマウントする追加データ ディスクの数
* SAP 移送/修正システム (CTS) 構成

SAP ソフトウェア デプロイ プロセスを開始する前に、Azure ストレージ アカウントまたは Azure 仮想ネットワークを作成し、構成します (必要な場合)。 これらのリソースを作成し、構成する方法については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」をご覧ください。

### <a name="sap-sizing"></a>SAP のサイズ設定

SAP のサイズ設定を行うために、次の情報を把握しておきます。

* 予想される SAP ワークロード (たとえば、SAP Quick Sizer ツールを使用して予想) と SAPS (SAP Application Performance Standard) 番号
* SAP システムの必要な CPU リソースとメモリ使用量
* 1 秒あたりの必要な入出力 (I/O) 操作
* Azure の VM 間での最終的な通信に必要なネットワーク帯域幅
* オンプレミス資産と Azure にデプロイされた SAP システム間に必要なネットワーク帯域幅

### <a name="resource-groups"></a>リソース グループ

Azure Resource Manager では、リソース グループを使用して Azure サブスクリプション内のすべてのアプリケーション リソースを管理できます。 詳細については、「[Azure Resource Manager の概要][resource-group-overview]」を参照してください。

## <a name="resources"></a>リソース

### <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP リソース

SAP ソフトウェア デプロイをセットアップするときには、次の SAP リソースが必要です。

* SAP Note [1928533]: 次の情報が含まれています。
  * SAP ソフトウェアのデプロイでサポートされる Azure VM サイズの一覧
  * Azure VM サイズの容量に関する重要な情報
  * サポートされる SAP ソフトウェア、およびオペレーティング システム (OS) とデータベースの組み合わせ
  * Microsoft Azure 上の Windows と Linux に必要な SAP カーネル バージョン

* SAP Note [2015553]: SAP でサポートされる Azure 上の SAP ソフトウェア デプロイの前提条件が記載されています。
* SAP Note [2178632]: Azure 上の SAP について報告されるすべての監視メトリックに関する詳細情報が記載されています。
* SAP Note [1409604]: Azure 上の Windows に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2191498]: Azure 上の Linux に必要な SAP Host Agent のバージョンが記載されています。
* SAP Note [2243692]: Azure 上の Linux で動作する SAP のライセンスに関する情報が記載されています。
* SAP Note [1984787]: SUSE Linux Enterprise Server 12 に関する一般情報が記載されています。
* SAP Note [2002167]: Red Hat Enterprise Linux 7.x に関する一般情報が記載されています。
* SAP Note [2069760]: Oracle Linux 7.x に関する一般情報が記載されています。
* SAP Note [1999351]: Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。
* SAP Note [1597355]: Linux のスワップ領域に関する一般情報が記載されています。
* [SAP on Azure SCN ページ](https://wiki.scn.sap.com/wiki/x/Pia7Gg): ニュースと便利なリソースのコレクションです。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux に必要なすべての SAP Note を参照できます。
* [Azure PowerShell][azure-ps] の一部である SAP 固有の PowerShell コマンドレット。
* [Azure CLI][azure-cli] の一部である SAP 固有の Azure CLI コマンド。

### <a name="windows-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Windows リソース

Microsoft の次の記事では、Azure 上の SAP デプロイについて説明しています。

* [SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP NetWeaver のための Azure Virtual Machines のデプロイ (この記事)][deployment-guide]
* [SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]

## <a name="deployment-scenarios-for-sap-software-on-azure-vms"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Azure VM での SAP ソフトウェアのデプロイ シナリオ

VM および関連付けられているディスクを Azure にデプロイする方法が複数用意されています。 選択したデプロイの種類によって、デプロイする VM を準備する手順が異なる場合があるため、デプロイ方法の違いを理解しておくことが重要です。

### <a name="scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>シナリオ 1:SAP 用 Azure Marketplace から VM をデプロイする

Azure Marketplace で Microsoft またはサード パーティが提供するイメージを使用して VM をデプロイできます。 Marketplace には、Windows Server とさまざまな Linux ディストリビューションの標準 OS イメージが用意されています。 Microsoft SQL Server などのデータベース管理システム (DBMS) SKU を含むイメージをデプロイすることもできます。 DBMS SKU を含むイメージの使用方法の詳細については、「[Azure Virtual Machines 上の SAP NetWeaver – DBMS デプロイ ガイド][dbms-guide]」をご覧ください。

次のフローチャートは、Azure Marketplace から VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Azure Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-100]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの作成

Azure Marketplace のイメージを使用して新しい仮想マシンを作成する最も簡単な方法は、Azure Portal を使用することです。

1.  <https://portal.azure.com/#create/hub> にアクセスします。  または、Azure Portal のメニューの **[+ 新規]** をクリックします。
1.  **[コンピューティング]** を選択し、デプロイするオペレーティング システムの種類 (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2)、Oracle Linux 7.2 など) を選びます。 既定のリスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 完全な一覧を表示するには、 **[すべて表示]** を選択します。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
1.  次のページで使用条件を確認します。
1.  **[デプロイ モデルの選択]** で **[Resource Manager]** を選択します。
1.  **［作成］** を選択します

ウィザードの指示に従って、必要なすべてのリソース (ネットワーク インターフェイスやストレージ アカウントなど) だけでなく、仮想マシンを作成するために必要なパラメーターも設定します。 次のようなパラメーターがあります。

1. **[基本]** :
   * **Name**:リソースの名前 (仮想マシン名)。
   * **VM ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
   * **[ユーザー名とパスワード]** または **[SSH 公開キー]** : プロビジョニング中に作成されるユーザーのユーザー名とパスワードを入力します。 Linux 仮想マシンの場合、マシンへのサインインに使用する Secure Shell (SSH) 公開キーを入力できます。
   * **サブスクリプション**:新しい仮想マシンをプロビジョニングするために使用するサブスクリプションを選択します。
   * **[リソース グループ]** :VM のリソース グループの名前。 新しいリソース グループの名前を入力することも、既存のリソース グループの名前を入力することもできます。
   * **[場所]** :新しい仮想マシンをデプロイする場所。 仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を必ず選択します。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[Microsoft Azure のネットワーク][planning-guide-microsoft-azure-networking]」をご覧ください。
1. **Size**:

     サポートされている VM の種類の一覧については、SAP Note [1928533] を参照してください。 Azure Premium Storage を使用する場合は、適切な VM の種類を選択してください。 Premium Storage ではすべての VM の種類はサポートしていません。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」と [SAP ワークロード用の Azure Storage](./planning-guide-storage.md) に関する記事をご覧ください。

1. **設定**:
   * **Storage**
     * **ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
     * **[マネージド ディスクの使用]** : Managed Disks を使用する場合は、[はい] を選択します。 Managed Disks について詳しくは、計画ガイドの [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) の章をご覧ください。
     * **ストレージ アカウント**: 既存のストレージ アカウントを選択するか、または新しいアカウントを作成します。 すべてのストレージの種類が SAP アプリケーションの実行に対応しているわけではありません。 ストレージの種類の詳細については、「[RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)」を参照してください。
   * **Network**
     * **[仮想ネットワーク]** と **[サブネット]** : 仮想マシンをイントラネットと統合するには、オンプレミスのネットワークに接続されている仮想ネットワークを選択します。
     * **[パブリック IP アドレス]** : 使用するパブリック IP アドレスを選択するか、または新しいパブリック IP アドレスを作成するためのパラメーターを入力します。 パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスできます。 仮想マシンへのアクセスをセキュリティで保護するために、ネットワーク セキュリティ グループも作成してください。
     * **[ネットワーク セキュリティ グループ]** : 詳細については、[ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御][virtual-networks-nsg]に関するページを参照してください。
   * **[拡張機能]** : デプロイに追加することによって、仮想マシン拡張機能をインストールできます。 この手順では、拡張機能を追加する必要はありません。 SAP サポートに必要な拡張機能は、後でインストールします。 このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章をご覧ください。
   * **高可用性**: 可用性セットを選択するか、または新しい可用性セットを作成するためのパラメーターを入力します。 詳細については、「[Azure の可用性セット][planning-guide-3.2.3]」をご覧ください。
   * **Monitoring**
     * **[ブート診断]** : ブート診断には **[無効]** を選択できます。
     * **[ゲスト OS の診断]** : 監視診断には **[無効]** を選択できます。

1. **概要**:

   選択内容を確認し、 **[OK]** をクリックします。

選択したリソース グループに仮想マシンがデプロイされます。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>テンプレートを使用した仮想マシンの作成

[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートのいずれかを使用して仮想マシンを作成できます。 また、[Azure portal][virtual-machines-windows-tutorial]、[PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]、または [Azure CLI][virtual-machines-linux-tutorial] を使用して、仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - Managed Disks** (sap-2-tier-marketplace-image-md)][sap-templates-2-tier-marketplace-image-md]

  1 台だけの仮想マシンと Managed Disks を使って 2 層システムを作成する場合は、このテンプレートを使います。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]

  複数の仮想マシンを使用して 3 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート - Managed Disks** (sap-3-tier-marketplace-image-md)][sap-templates-3-tier-marketplace-image-md]

  複数の仮想マシンと Managed Disks を使って 3 層システムを作成する場合は、このテンプレートを使います。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。

1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID (SID)。
   * **[OS の種類]** : デプロイするオペレーティング システム (Windows Server 2012 R2、SUSE Linux Enterprise Server 12 (SLES 12)、Red Hat Enterprise Linux 7.2 (RHEL 7.2)、Oracle Linux 7.2 など)。

     リスト ビューには、サポートされているオペレーティング システムがすべて表示されるわけではありません。 SAP ソフトウェア デプロイのサポートされているオペレーティング システムの詳細については、SAP Note [1928533] を参照してください。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[System availability] (システムの可用性)** (3 層テンプレートのみ): システムの可用性。

     高可用性インストールに適した構成用の **HA** を選択します。 ABAP SAP Central Services (ASCS) 用の 2 つのデータベース サーバーと 2 つのサーバーが作成されます。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[管理ユーザー名]** と **[管理パスワード]** : ユーザー名とパスワード。
     仮想マシンへのサインインに使用する新しいユーザーが作成されます。
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

Azure Marketplace のイメージを使用すると、Azure VM エージェントが既定でデプロイされます。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。 詳細については、「[プロキシの構成][deployment-guide-configure-proxy]」をご覧ください。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-vm-extension"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>VM 拡張機能を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 「[SAP リソース][deployment-guide-2.2]」で示されているリソースの一覧で、SAP の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="vm-extension-for-sap-check"></a>SAP 用 VM 拡張機能のチェック

「[確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、SAP 用 VM 拡張機能が動作しているかどうかをチェックします。

#### <a name="post-deployment-steps"></a>デプロイ後の手順

VM を作成してデプロイしたら、必要なソフトウェア コンポーネントを VM にインストールする必要があります。 VM のこの種のデプロイでは、デプロイ後にソフトウェアをインストールすることになるため、インストールするソフトウェアが Azure 内または別の VM 上で既に使用可能であるか、接続可能なディスクとして提供できる必要があります。 または、オンプレミスの資産 (インストール共有) への接続を提供するクロスプレミス シナリオを使用することを検討します。

Azure に VM をデプロイしたら、オンプレミス環境の場合と同じガイドラインとツールに従って VM に SAP ソフトウェアをインストールします。 Azure VM に SAP ソフトウェアをインストールする場合、SAP と Microsoft では、SAP インストール メディアを Azure VHD または Managed Disks にアップロードして保存するか、必要なすべての SAP インストール メディアを格納したファイル サーバーとして機能する Azure VM を作成することを推奨しています。

### <a name="scenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>シナリオ 2:SAP のカスタム イメージを使用して VM をデプロイする

オペレーティング システムまたは DBMS のバージョンによってパッチ要件がそれぞれ異なるため、Azure Marketplace で提供されるイメージがニーズに合わない場合があります。 このような場合、後で再度デプロイできるように、独自の OS/DBMS の VM イメージを使用して VM を作成できます。
プライベート イメージを作成する手順は、Windows と Linux で異なります。

---
> ![Windows][Logo_Windows] Windows
>
> 複数の仮想マシンをデプロイするために使用できる Windows イメージを準備するには、オンプレミス VM で Windows 設定 (Windows SID やホスト名など) を抽象化または汎用化する必要があります。 これを行うには、[sysprep](/previous-versions/windows/it-pro/windows-8.1-and-8/hh825084(v=win.10)) を使用します。
>
> ![Linux][Logo_Linux] Linux
>
> 複数の仮想マシンをデプロイするために使用できる Linux イメージを準備するには、オンプレミス VM で一部の Linux 設定を抽象化または汎用化する必要があります。 これを行うには、`waagent -deprovision` を使用します。 詳細については、[Azure で実行されている Linux 仮想マシンのキャプチャ][virtual-machines-linux-capture-image]に関する記事、および「[Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide-command-line-options]」をご覧ください。
>
>

---
カスタム イメージを作成し、そのイメージを使用して複数の新しい VM を作成できます。 方法については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」をご覧ください。 SAP Software Provisioning Manager を使って新しい SAP システムをインストールし、仮想マシンに接続されているディスクからデータベース バックアップを復元するか、Azure Storage からデータベース バックアップを直接復元して (DBMS でサポートされている場合)、データベース コンテンツを設定します。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]」を参照してください。 オンプレミス VM (特に 2 層システム) に SAP システムが既にインストールされている場合は、SAP Software Provisioning Manager でサポートされているシステムの名前変更手順に従って、Azure VM のデプロイ後に SAP システム設定を調整できます (SAP Note [1619720])。 それ以外の場合は、Azure VM のデプロイ後に SAP ソフトウェアをインストールできます。

次のフローチャートは、カスタム イメージから VM をデプロイする際の SAP 固有の一連の手順を示しています。

![Private Marketplace の VM イメージを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-300]

#### <a name="create-a-virtual-machine-by-using-the-azure-portal"></a>Azure Portal を使用した仮想マシンの作成

管理ディスクのイメージから新しい仮想マシンを作成する最も簡単な方法は、Azure Portal を使うことです。 管理ディスクイメージを作成する方法について詳しくは、「[Azure で一般化された VM の管理対象イメージをキャプチャする](../../windows/capture-image-resource.md)」をご覧ください

1.  <https://ms.portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Compute%2Fimages> にアクセスします。 または、Azure Portal のメニューで **[イメージ]** を選びます。
1.  デプロイする管理ディスク イメージを選び、 **[VM の作成]** をクリックします

ウィザードの指示に従って、必要なすべてのリソース (ネットワーク インターフェイスやストレージ アカウントなど) だけでなく、仮想マシンを作成するために必要なパラメーターも設定します。 次のようなパラメーターがあります。

1. **[基本]** :
   * **Name**:リソースの名前 (仮想マシン名)。
   * **VM ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
   * **[ユーザー名とパスワード]** または **[SSH 公開キー]** : プロビジョニング中に作成されるユーザーのユーザー名とパスワードを入力します。 Linux 仮想マシンの場合、マシンへのサインインに使用する Secure Shell (SSH) 公開キーを入力できます。
   * **サブスクリプション**:新しい仮想マシンをプロビジョニングするために使用するサブスクリプションを選択します。
   * **[リソース グループ]** :VM のリソース グループの名前。 新しいリソース グループの名前を入力することも、既存のリソース グループの名前を入力することもできます。
   * **[場所]** :新しい仮想マシンをデプロイする場所。 仮想マシンをオンプレミス ネットワークに接続する場合は、Azure をオンプレミス ネットワークに接続する仮想ネットワークの場所を必ず選択します。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[Microsoft Azure のネットワーク][planning-guide-microsoft-azure-networking]」をご覧ください。
1. **Size**:

     サポートされている VM の種類の一覧については、SAP Note [1928533] を参照してください。 Azure Premium Storage を使用する場合は、適切な VM の種類を選択してください。 Premium Storage ではすべての VM の種類はサポートしていません。 詳細については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」の「[ストレージ: Microsoft Azure Storage とデータ ディスク][planning-guide-storage-microsoft-azure-storage-and-data-disks]」と [SAP ワークロード用の Azure Storage](./planning-guide-storage.md) に関する記事をご覧ください。

1. **設定**:
   * **Storage**
     * **ディスクの種類**: OS ディスクのディスクの種類を選択します。 データ ディスクに Premium Storage を使いたい場合は、OS ディスクにも Premium Storage を使うことをお勧めします。
     * **[マネージド ディスクの使用]** : Managed Disks を使用する場合は、[はい] を選択します。 Managed Disks について詳しくは、計画ガイドの [Managed Disks](./planning-guide-storage.md#microsoft-azure-storage-resiliency) の章をご覧ください。
   * **Network**
     * **[仮想ネットワーク]** と **[サブネット]** : 仮想マシンをイントラネットと統合するには、オンプレミスのネットワークに接続されている仮想ネットワークを選択します。
     * **[パブリック IP アドレス]** : 使用するパブリック IP アドレスを選択するか、または新しいパブリック IP アドレスを作成するためのパラメーターを入力します。 パブリック IP アドレスを使用して、インターネット経由で仮想マシンにアクセスできます。 仮想マシンへのアクセスをセキュリティで保護するために、ネットワーク セキュリティ グループも作成してください。
     * **[ネットワーク セキュリティ グループ]** : 詳細については、[ネットワーク セキュリティ グループを使用したネットワーク トラフィック フローの制御][virtual-networks-nsg]に関するページを参照してください。
   * **[拡張機能]** : デプロイに追加することによって、仮想マシン拡張機能をインストールできます。 この手順では、拡張機能を追加する必要はありません。 SAP サポートに必要な拡張機能は、後でインストールします。 このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章をご覧ください。
   * **高可用性**: 可用性セットを選択するか、または新しい可用性セットを作成するためのパラメーターを入力します。 詳細については、「[Azure の可用性セット][planning-guide-3.2.3]」をご覧ください。
   * **Monitoring**
     * **[ブート診断]** : ブート診断には **[無効]** を選択できます。
     * **[ゲスト OS の診断]** : 監視診断には **[無効]** を選択できます。

1. **概要**:

   選択内容を確認し、 **[OK]** をクリックします。

選択したリソース グループに仮想マシンがデプロイされます。

#### <a name="create-a-virtual-machine-by-using-a-template"></a>テンプレートを使用した仮想マシンの作成

Azure Portal からプライベート OS イメージを使用してデプロイを作成するには、次のいずれかの SAP テンプレートを使用します。 これらのテンプレートは、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されています。 また、[PowerShell][virtual-machines-upload-image-windows-resource-manager] を使用して仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-user-image)][sap-templates-2-tier-user-image]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - マネージド ディスク イメージ** (sap-2-tier-user-image-md)][sap-templates-2-tier-user-image-md]

  1 台だけの仮想マシンと管理ディスク イメージを使って 2 層システムを作成する場合は、このテンプレートを使います。
* [**3 層構成 (複数の仮想マシン) テンプレート** (sap-3-tier-user-image)][sap-templates-3-tier-user-image]

  複数の仮想マシンまたは独自の OS イメージを使用して 3 層システムを作成する場合は、このテンプレートを使用します。
* [**3 層構成 (複数の仮想マシン) テンプレート - マネージド ディスク イメージ** (sap-3-tier-user-image-md)][sap-templates-3-tier-user-image-md]

  複数の仮想マシンまたは独自の OS イメージと管理ディスク イメージを使って 3 層システムを作成する場合は、このテンプレートを使用します。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID。
   * **[OS の種類]** : デプロイするオペレーティング システムの種類 (Windows または Linux)。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[System availability] (システムの可用性)** (3 層テンプレートのみ): システムの可用性。

     高可用性インストールに適した構成用の **HA** を選択します。 ASCS 用の 2 つのデータベース サーバーと 2 つのサーバーが作成されます。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[User image VHD URI] (ユーザー イメージ VHD の URI)** (非管理ディスク イメージ テンプレートのみ): プライベート OS イメージ VHD の URI (https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd など)。
   * **[User image storage account] (ユーザー イメージのストレージ アカウント)** (非管理ディスク イメージ テンプレートのみ): プライベート OS イメージが格納されるストレージ アカウントの名前 (https://&lt;アカウント名>.blob.core.windows.net/vhds/userimage.vhd の &lt;アカウント名> など)。
   * **[userImageId] (ユーザー イメージ ID)** (マネージド ディスク イメージ テンプレートのみ): 使用するマネージド ディスク イメージの ID
   * **[管理ユーザー名]** と **[管理パスワード]** : ユーザー名とパスワード。

     仮想マシンへのサインインに使用する新しいユーザーが作成されます。
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

#### <a name="install-the-vm-agent-linux-only"></a>VM エージェントのインストール (Linux のみ)

前のセクションで説明したテンプレートを使用するには、ユーザー イメージに Linux エージェントが既にインストールされている必要があります。インストールされていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、ユーザー イメージにインストールします。 テンプレートを使用しない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または Azure ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この手順に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。「[プロキシの構成][deployment-guide-configure-proxy]」を参照してください。

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM Extension for SAP を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 「[SAP リソース][deployment-guide-2.2]」で示されているリソースの一覧で、SAP の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="sap-vm-extension-check"></a>SAP VM 拡張機能のチェック

「[確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、SAP 用 VM 拡張機能が動作しているかどうかをチェックします。


### <a name="scenario-3-moving-an-on-premises-vm-by-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>シナリオ 3:SAP を含む汎用化されていない Azure VHD を使用してオンプレミス VM を移動する

このシナリオでは、特定の SAP システムをオンプレミス環境から Azure に移行します。 これを行うには、OS、SAP バイナリ、最終的な DBMS バイナリを格納している VHD と、DBMS のデータ ファイルとログ ファイルを格納している VHD を Azure にアップロードします。 「[シナリオ 2: SAP のカスタム イメージを使用して VM をデプロイする][deployment-guide-3.3]」で説明されたシナリオとは異なり、この場合は、ホスト名、SAP SID、および SAP ユーザー アカウントがオンプレミスの環境で構成されたため、これらを Azure VM に保持します。 OS を汎用化する必要はありません。 ほとんどの場合、このシナリオは、SAP ランドスケープの一部がオンプレミスで実行され、一部が Azure で実行されるクロスプレミス シナリオに適用されます。

このシナリオでは、VM エージェントはデプロイ時に自動的にインストール**されません**。 Azure で SAP NetWeaver を実行するには VM エージェントと Azure Extension for SAP が必要であるため、仮想マシンの作成後に両方のコンポーネントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure VM エージェントの詳細については、次のリソースを参照してください。

---
> ![Windows][Logo_Windows] Windows
>
> [Azure 仮想マシン エージェントの概要][virtual-machines-windows-agent-user-guide]
>
> ![Linux][Logo_Linux] Linux
>
> [Azure Linux エージェント ユーザー ガイド][virtual-machines-linux-agent-user-guide]
>
>

---

次のフローチャートは、汎用化されていない Azure VHD を使用してオンプレミス VM を移行する際の一連の手順を示しています。

![VM ディスクを使用した SAP システム用 VM のデプロイのフローチャート][deployment-guide-figure-400]

ディスクが既にアップロードされ、Azure で定義されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」を参照)、以下のセクションで説明する作業を行います。

#### <a name="create-a-virtual-machine"></a>仮想マシンの作成

Azure portal からプライベート OS ディスクを使用してデプロイを作成する場合、[azure-quickstart-templates GitHub リポジトリ][azure-quickstart-templates-github]で公開されている SAP テンプレートを使用します。 また、PowerShell を使用して仮想マシンを手動で作成することもできます。

* [**2 層構成 (仮想マシン 1 台のみ) テンプレート** (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]

  仮想マシンを 1 台だけ使用して 2 層システムを作成する場合は、このテンプレートを使用します。
* [**2 層構成 (仮想マシン 1 台のみ) テンプレート - マネージド ディスク** (sap-2-tier-user-disk-md)][sap-templates-2-tier-os-disk-md]

  1 台だけの仮想マシンと管理ディスクを使って 2 層システムを作成する場合は、このテンプレートを使います。

Azure Portal で、テンプレートの次のパラメーターを入力します。

1. **[基本]** :
   * **サブスクリプション**:テンプレートをデプロイするために使用するサブスクリプション。
   * **[リソース グループ]** :テンプレートをデプロイするために使用するリソース グループ。 新しいリソース グループを作成することも、サブスクリプション内の既存のリソース グループを選択することもできます。
   * **[場所]** :テンプレートをデプロイする場所。 既存のリソース グループを選択した場合は、そのリソース グループの場所が使用されます。
1. **設定**:
   * **[SAP System ID] (SAP システム ID)** : SAP システム ID。
   * **[OS の種類]** : デプロイするオペレーティング システムの種類 (Windows または Linux)。
   * **[SAP system size] (SAP システムのサイズ)** : SAP システムのサイズ。

     新しいシステムで提供する SAPS の数です。 システムに必要な SAPS の数がわからない場合は、SAP のテクノロジ パートナーまたはシステム インテグレーターにお問い合わせください。
   * **[ストレージの種類]** (2 層テンプレートのみ): 使用するストレージの種類。

     大規模なシステムの場合、Azure Premium Storage を使用することを強くお勧めします。 ストレージの種類の詳細については、次のリソースを参照してください。
      * [Use of Azure Premium SSD Storage for SAP DBMS Instance (SAP DBMS インスタンスでの Azure Premium SSD Storage の使用)][2367194]
      * [RDBMS デプロイの VM のストレージの構造](./dbms_guide_general.md#65fa79d6-a85f-47ee-890b-22e794f51a64)
      * [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ][storage-premium-storage-preview-portal]
      * [Microsoft Azure Storage の概要][storage-introduction]
   * **[OS disk VHD URI] (OS ディスク VHD の URI)** (非管理ディスク テンプレートのみ): プライベート OS ディスクの URI (https://&lt; アカウント名>.blob.core.windows.net/vhds/osdisk.vhd など)。
   * **[OS disk Managed Disk ID]\(OS ディスクのマネージド ディスク ID\)** (マネージド ディスク テンプレートのみ):マネージド ディスクの OS ディスクの ID (/subscriptions/92d102f7-81a5-4df7-9877-54987ba97dd9/resourceGroups/group/providers/Microsoft.Compute/disks/WIN)
   * **[New or existing subnet] (新規または既存のサブネット)** : 新しい仮想ネットワークとサブネットを作成するか、または既存のサブネットを使用するかを決定します。 オンプレミス ネットワークに接続されている仮想ネットワークが既にある場合は、 **[Existing (既存)]** を選択します。
   * **[Subnet ID]\(サブネット ID\)** :VM を既存の VNet にデプロイする場合、その VNet で VM の割り当て先サブネットが定義されているときは、その特定のサブネットの ID を指定します。 通常、ID は /subscriptions/&lt;サブスクリプション ID>/resourceGroups/&lt;リソース グループ名>/providers/Microsoft.Network/virtualNetworks/&lt;仮想ネットワーク名>/subnets/&lt;サブネット名> のようになります。

1. **使用条件**:  
    法律条項を確認し、同意します。

1. **[購入]** を選択します。

#### <a name="install-the-vm-agent"></a>VM エージェントのインストール

前のセクションで説明したテンプレートを使用するには、VM エージェントを OS ディスクにインストールする必要があります。インストールしていない場合、デプロイは失敗します。 「[Azure VM エージェントのダウンロード、インストール、有効化][deployment-guide-4.4]」の説明に従って、VM エージェントをダウンロードし、VM にインストールします。

前のセクションで説明したテンプレートを使わない場合は、VM エージェントを後でインストールすることもできます。

#### <a name="join-a-domain-windows-only"></a>ドメインへの参加 (Windows のみ)

Azure デプロイが Azure サイト間 VPN 接続または ExpressRoute 経由でオンプレミスの Active Directory または DNS インスタンスに接続されている場合 (「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide]」では、これを "*クロスプレミス*" と呼んでいます)、VM がオンプレミス ドメインに参加している必要があります。 この作業に関する考慮事項の詳細については、「[オンプレミス ドメインへの VM の参加 (Windows のみ)][deployment-guide-4.3]」をご覧ください。

#### <a name="configure-proxy-settings"></a>プロキシ設定の構成

オンプレミス ネットワークの構成方法によっては、VM でプロキシを設定することが必要な場合があります。 VM が VPN または ExpressRoute 経由でオンプレミス ネットワークに接続されている場合、インターネットにアクセスできないことがあります。この場合、VM では、必要な VM 拡張機能をダウンロードしたり、Azure 用 SAP 拡張機能を介して SAP Host Agent 用の Azure インフラストラクチャ情報を収集したりすることができません。「[プロキシの構成][deployment-guide-configure-proxy]」を参照してください。

#### <a name="configure-azure-vm-extension-for-sap"></a>Azure VM Extension for SAP を構成する

SAP で現在の環境がサポートされるように、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、Azure Extension for SAP を設定します。 「[SAP リソース][deployment-guide-2.2]」で示されているリソースの一覧で、SAP の前提条件と、SAP カーネルおよび SAP Host Agent の必要最小バージョンを確認してください。

#### <a name="sap-vm-check"></a>SAP VM のチェック

「[確認とトラブルシューティング][deployment-guide-troubleshooting-chapter]」の説明に従って、SAP 用 VM 拡張機能が動作しているかどうかをチェックします。

## <a name="update-the-configuration-of-azure-extension-for-sap"></a>Azure Extension for SAP の構成を更新する

次のいずれかのシナリオで、Azure Extension for SAP の構成を更新します。
* Microsoft と SAP の共同チームによって VM 拡張機能が拡張され、カウンターの増減が要求されている場合。
* Microsoft によって、データを提供する基になる Azure インフラストラクチャの新しいバージョンが導入され、Azure Extension for SAP を変更に適応させる必要がある場合。
* Azure VM に追加のデータ ディスクをマウントするか、データ ディスクを削除します。 このシナリオでは、ストレージ関連のデータのコレクションを更新します。 エンドポイントを追加または削除することで、または VM に IP アドレスを割り当てることで構成を変更しても、拡張機能の構成には影響しません。
* A5 から他の VM サイズへの変更など、Azure VM のサイズを変更した場合。
* Azure VM に新しいネットワーク インターフェイスを追加した場合。

設定を更新するには、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の手順に従って、Azure Extension for SAP の構成を更新します。

## <a name="detailed-tasks-for-sap-software-deployment"></a>SAP ソフトウェアをデプロイするための作業の詳細

このセクションでは、構成およびデプロイ プロセスで個々の作業を実行する手順について詳しく説明します。

### <a name="deploy-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Azure PowerShell コマンドレットのデプロイ

[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関する記事で説明されている手順に従ってください

PowerShell コマンドレットの更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。 [こちら](/powershell/azure/install-az-ps#update-the-azure-powershell-module)の記事で説明されている手順に従ってください。 SAP Note [1928533] または [2015553] に特に記載されていない限り、最新バージョンの Azure PowerShell コマンドレットを使用することをお勧めします。

コンピューターにインストールされている Azure PowerShell コマンドレットのバージョンを確認するには、次の PowerShell コマンドを実行します。

```powershell
(Get-Module Az.Compute).Version
```

### <a name="deploy-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Azure CLI のデプロイ

記事「[Azure CLI のインストール](/cli/azure/install-azure-cli)」で説明されている手順に従ってください

Azure CLI の更新プログラムがあるかどうかをこまめに確認してください。通常は毎月更新されます。

コンピューターにインストールされている Azure CLI のバージョンを確認するには、次のコマンドを実行します。

```console
az --version
```

### <a name="join-a-vm-to-an-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>オンプレミス ドメインへの VM の参加 (Windows のみ)

オンプレミスの Active Directory と DNS が Azure に拡張されるクロスプレミス シナリオで SAP VM をデプロイする場合、VM がオンプレミス ドメインに参加している必要があります。 オンプレミス ドメインに VM を参加させるための詳細な手順と、オンプレミス ドメインのメンバーになるために必要な追加ソフトウェアは、お客様によって異なります。 通常、オンプレミス ドメインに VM を参加させるには、マルウェア対策ソフトウェアや、バックアップまたは監視ソフトウェアなどの追加ソフトウェアをインストールする必要があります。

このシナリオでは、VM が環境内のドメインに参加したときにインターネット プロキシ設定が適用されている場合に、ゲスト VM の Windows ローカル システム アカウント (S-1-5-18) にも同じプロキシ設定が適用されていることを確認する必要があります。 最も簡単な方法は、ドメイン内のシステムに適用されるドメイン グループ ポリシーを使用してプロキシを適用することです。

### <a name="download-install-and-enable-the-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Azure VM エージェントのダウンロード、インストール、有効化

汎用化されていない OS イメージ (Windows System Preparation (sysprep ) ツールで作成されていないイメージなど) からデプロイされた仮想マシンの場合、Azure VM エージェントを手動でダウンロードしてインストールし、有効にする必要があります。

Azure Marketplace から VM をデプロイした場合には、この手順は不要です。 Azure Marketplace のイメージには、Azure VM エージェントが既に含まれています。

#### <a name="windows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

1. Azure VM エージェントのダウンロード:
   1.  [Azure VM エージェント インストーラー パッケージ](https://go.microsoft.com/fwlink/?LinkId=394789)をダウンロードします。
   1.  パーソナル コンピューターまたはサーバーで、VM エージェント MSI パッケージをローカルに保存します。
1. Azure VM エージェントのインストール:
   1.  リモート デスクトップ プロトコル (RDP) を使用して、デプロイした Azure VM に接続します。
   1.  VM で Windows エクスプローラー ウィンドウを開き、VM エージェントの MSI ファイルのターゲット ディレクトリを選択します。
   1.  Azure VM エージェント インストーラー MSI ファイルを、ローカル コンピューター/サーバーから VM 上の VM エージェントのターゲット ディレクトリにドラッグします。
   1.  VM 上の MSI ファイルをダブルクリックします。
1. オンプレミス ドメインに参加している VM の場合、「[プロキシの構成][deployment-guide-configure-proxy]」で説明するように、最終的なインターネット プロキシ設定が VM の Windows ローカル システム アカウント (S-1-5-18) にも適用されていることを確認します。 VM エージェントはこのコンテキストで実行されるので、Azure に接続できる必要があります。

Azure VM エージェントを更新する際にユーザーの操作は不要です。 VM エージェントは自動的に更新され、VM の再起動を必要としません。

#### <a name="linux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux

次のコマンドを使用して、Linux の VM エージェントをインストールします。

* **SUSE Linux Enterprise Server (SLES)**

  ```console
  sudo zypper install WALinuxAgent
  ```

* **Red Hat Enterprise Linux (RHEL) または Oracle Linux**

  ```console
  sudo yum install WALinuxAgent
  ```

エージェントが既にインストールされている場合、Azure Linux エージェントを更新するには、[VM 上の Azure Linux エージェントを GitHub で最新バージョンに更新する方法][virtual-machines-linux-update-agent]に関する記事に記載されている手順を実行します。

### <a name="configure-the-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>プロキシの構成

プロキシを構成する手順は、Windows と Linux で異なります。

#### <a name="windows"></a>Windows

インターネットにアクセスするには、ローカル システム アカウントのプロキシ設定を適切に設定する必要があります。 プロキシ設定がグループ ポリシーで設定されていない場合は、ローカル システム アカウントのプロキシ設定を構成できます。

1. **[スタート]** メニューで「**gpedit.msc**」と入力し、**Enter** キーを押します。
1. **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]**  >  **[Internet Explorer]** を選択します。 **[コンピューター別にプロキシを設定する (ユーザー別ではなく)]** が無効になっているか、構成されていないことを確認します。
1. **コントロール パネル**で、 **[ネットワークと共有センター]**  >  **[インターネット オプション]** に移動します。
1. **[接続]** タブの **[LAN の設定]** をクリックします。
1. **[設定を自動的に検出する]** チェック ボックスをオフにします。
1. **[LAN にプロキシ サーバーを使用する]** チェック ボックスをオンにし、プロキシのアドレストとポートを入力します。
1. **[Advanced] \(詳細設定)**  ボタンを選択します。
1. **[例外]** ボックスに、IP アドレスとして「**168.63.129.16**」と入力します。 **[OK]** を選択します。

#### <a name="linux"></a>Linux

Microsoft Azure ゲスト エージェントの構成ファイル (\\etc\\waagent.conf) で適切なプロキシを構成します。

次のパラメーターを設定します。

1. **HTTP プロキシ ホスト**。 たとえば、**proxy.corp.local** に設定します。

   ```console
   HttpProxy.Host=<proxy host>

   ```

1. **HTTP プロキシ ポート**。 たとえば、**80** に設定します。

   ```console
   HttpProxy.Port=<port of the proxy host>

   ```

1. エージェントを再起動します。

   ```console
   sudo service waagent restart
   ```

\\etc\\waagent.conf のプロキシ設定は、必要な VM 拡張機能にも適用されます。 Azure リポジトリを使用する場合は、これらのリポジトリへのトラフィックがオンプレミスのイントラネットを経由しないようにしてください。 強制トンネリングを有効にするためにユーザー定義ルートを作成した場合は、サイト間 VPN 接続を介さずに、リポジトリへのトラフィックをインターネットに直接ルーティングするルートを追加する必要があります。

* **SLES**

  \\etc\\regionserverclnt.cfg に示されている IP アドレスのルートも追加する必要があります。 次の図は例を示しています。

  ![強制トンネリング][deployment-guide-figure-50]


* **RHEL**

  \\etc\\yum.repos.d\\rhui-load-balancers に示されているホストの IP アドレスのルートも追加する必要があります。 例については、前の図を参照してください。

* **Oracle Linux**

  Azure には Oracle Linux 用のリポジトリはありません。 Oracle Linux 用に独自のリポジトリを構成するか、パブリック リポジトリを使う必要があります。

ユーザー定義ルートの詳細については、[ユーザー定義ルートと IP 転送][virtual-networks-udr-overview]に関する記事をご覧ください。

### <a name="configure-the-azure-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Azure Extension for SAP を構成する

> [!NOTE]
> 一般的なサポートに関する声明:Azure Extension for SAP に関するサポートが必要な場合は、常にコンポーネント BC-OP-NT-AZR (Windows の場合) または BC-OP-LNX-AZR で SAP に関するインシデントを開始してください。
> 共同のお客様を支援するために、SAP サポート システムで作業している専門の Microsoft サポート エンジニアが存在します。

[Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの説明に従って VM を準備すると、Azure VM エージェントが仮想マシンにインストールされます。 次に、Azure グローバル データセンターの Azure 拡張リポジトリで入手できる Azure Extension for SAP をデプロイします。 詳しくは、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装][planning-guide-9.1]」をご覧ください。

私たちは、新しいバージョンの Azure Extension for SAP のリリースに向けて作業を行っています。 新しい拡張機能では、仮想マシンのシステム割り当て ID を使用して、接続されているディスク、ネットワーク インターフェイス、および仮想マシン自体に関する情報が取得されます。 これらのリソースにアクセスできるようにするには、仮想マシンのシステム ID に、仮想マシン、OS ディスク、データ ディスク、およびネットワーク インターフェイスに対する閲覧者アクセス許可が必要です。 現在、次のシナリオでは新しい拡張機能のみをインストールすることをお勧めします。

1. Terraform、Azure Resource Manager テンプレート、または Azure CLI、Azure PowerShell 以外のその他の方法を使用して拡張機能をインストールする場合
1. SUSE SLES 15 以降に拡張機能をインストールする場合。
1. Microsoft または SAP サポートから新しい拡張機能をインストールするように求められた場合
1. Azure Ultra Disk または Standard Managed Disks を使用する場合

これらのシナリオについては、章「[Azure PowerShell を使用して新しい Azure Extension for SAP を構成する][deployment-guide-configure-new-extension-ps]」(Azure PowerShell の場合) または「[Azure CLI を使用して新しい Azure Extension for SAP を構成する][deployment-guide-configure-new-extension-cli]」(Azure CLI の場合) に記載されている手順に従ってください。

標準バージョンの Azure Extension for SAP をインストールして構成する場合は、[Azure PowerShell][deployment-guide-4.5.1] または [Azure CLI][deployment-guide-4.5.2] の章に従ってください。

#### <a name="azure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Linux VM および Windows VM の Azure PowerShell

PowerShell を使用して Azure Extension for SAP をインストールするには:

1. 最新バージョンの Azure PowerShell コマンドレットがインストールされていることを確認します。 詳細については、「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」をご覧ください。  
1. 次の PowerShell コマンドレットを実行します。
    使用可能な環境の一覧を表示するには、コマンドレット `Get-AzEnvironment` を実行します。 グローバル Azure を使う場合の環境は **AzureCloud** です。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
    ```

アカウント データを入力すると、スクリプトによって必要な拡張機能がデプロイされ、必要な機能が有効化されます。 これには数分かかる場合があります。
`Set-AzVMAEMExtension` の詳細については、「[Set-AzVMAEMExtension][msdn-set-Azvmaemextension]」を参照してください。

![SAP 固有の Azure コマンドレット Set-AzVMAEMExtension の正常な実行][deployment-guide-figure-900]

`Set-AzVMAEMExtension` の構成では、SAP 用のホスト データ収集を構成するためのすべての手順が実行されます。

スクリプトの出力には次の情報が含まれています。

* OS ディスクとすべての追加データ ディスクのデータ収集が構成されていることの確認。
* 次の 2 つのメッセージで、特定のストレージ アカウントのストレージ メトリックの構成が確認されます。
* 出力の 1 つの行に、VM Extension for SAP の構成に関する実際の更新の状態が示されます。
* 出力の別の行で、構成がデプロイまたは更新されたことが確認されます。
* 出力の最後の行は情報提供です。 この行には、VM Extension for SAP の構成をテストするためのオプションが示されます。
* Azure VM Extension for SAP のすべての手順が正常に実行されたこと、および Azure インフラストラクチャから必要なデータが提供されていることを確認するには、「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1]」の説明に従って、Azure Extension for SAP の適合性チェックを実行します。
* Azure Diagnostics が関連データを収集するまで 15 ～ 30 分待ちます。

#### <a name="azure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Linux VM 向け Azure CLI

Azure CLI を使用して Azure Extension for SAP をインストールするには:

   1. 「[Azure クラシック CLI のインストール][azure-cli]」の説明に従って、Azure クラシック CLI をインストールします。
   1. Azure アカウントでサインインします。

      ```console
      azure login
      ```

   1. Azure Resource Manager モードに切り替えます。

      ```console
      azure config mode arm
      ```

   1. Azure Extension for SAP を有効にします。

      ```console
      azure vm enable-aem <resource-group-name> <vm-name>
      ```

1. Azure CLI 2.0 を使用してインストールする

   1. 「[Azure CLI 2.0 のインストール][azure-cli-2]」の説明に従って、Azure CLI 2.0 をインストールします。
   1. Azure アカウントでサインインします。

      ```azurecli
      az login
      ```

   1. Azure CLI AEM Extension をインストールします。
  
      ```azurecli
      az extension add --name aem
      ```
  
   1. 次のように、拡張機能をインストールします。
  
      ```azurecli
      az vm aem set -g <resource-group-name> -n <vm name>
      ```

1. Azure Extension for SAP が Azure Linux VM 上で有効になっていることを確認します。 \\var\\lib\\AzureEnhancedMonitor\\PerfCounters ファイルが存在するかどうかを確認します。 存在する場合は、コマンド プロンプトで次のコマンドを実行して、Azure Extension for SAP によって収集された情報を表示します。

   ```console
   cat /var/lib/AzureEnhancedMonitor/PerfCounters
   ```

   出力は次のようになります。

   ```output
   ...
   2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
   2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
   ...
   ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-powershell"></a><a name="2ad55a0d-9937-4943-9dd2-69bc2b5d3de0"></a>Azure PowerShell を使用して新しい Azure Extension for SAP を構成する

SAP 用の新しい VM 拡張機能では、VM の監視データと構成データにアクセスするために、VM に割り当てられたマネージド ID が使用されます。 PowerShell を使用して新しい Azure Extension for SAP をインストールするには、まずそのような ID を VM に割り当ててから、その VM で使用されているすべてのリソース (ディスクやネットワーク インターフェイスなど) に対するアクセス権をその ID に付与する必要があります。

> [!NOTE]
> 次の手順では、リソース グループまたは個々のリソース (仮想マシン、データ ディスクなど) に対する所有者特権が必要です。

1. 必ず SAP Host Agent 7.21 PL 47 以上を使用してください。
1. 必ず現在のバージョンの SAP 用 VM 拡張機能をアンインストールしてください。 同じ仮想マシンに両方のバージョンの SAP 用 VM 拡張機能をインストールすることはサポートされていません。
1. 最新バージョンの Azure PowerShell コマンドレット (4.3.0 以上) がインストールされていることを確認します。 詳細については、「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」をご覧ください。
1. 次の PowerShell コマンドレットを実行します。
    使用可能な環境の一覧を表示するには、コマンドレット `Get-AzEnvironment` を実行します。 グローバル Azure を使う場合の環境は **AzureCloud** です。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

    ```powershell
    $env = Get-AzEnvironment -Name <name of the environment>
    Connect-AzAccount -Environment $env
    Set-AzContext -SubscriptionName <subscription name>

    Set-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name> -InstallNewExtension
    ```

#### <a name="configure-the-new-azure-extension-for-sap-with-azure-cli"></a><a name="c8749c24-fada-42ad-b114-f9aae2dc37da"></a>Azure CLI を使用して新しい Azure Extension for SAP を構成する

SAP 用の新しい VM 拡張機能では、VM の監視データと構成データにアクセスするために、VM に割り当てられたマネージド ID が使用されます。 Azure CLI を使用して新しい Azure Extension for SAP をインストールするには、まずそのような ID を VM に割り当ててから、その VM で使用されているすべてのリソース (ディスクやネットワーク インターフェイスなど) に対するアクセス権をその ID に付与する必要があります。

> [!NOTE]
> 次の手順では、リソース グループまたは個々のリソース (仮想マシン、データ ディスクなど) に対する所有者特権が必要です。

1. 必ず SAP Host Agent 7.21 PL 47 以上を使用してください。
1. 必ず現在のバージョンの SAP 用 VM 拡張機能をアンインストールしてください。 同じ仮想マシンに両方のバージョンの SAP 用 VM 拡張機能をインストールすることはサポートされていません。
1. 「[Azure CLI 2.0 のインストール][azure-cli-2]」の説明に従って、Azure CLI 2.0 をインストールします。

1. Azure アカウントでサインインします。

   ```azurecli
   az login
   ```

1. 記事「[Azure CLI を使用して Azure VM 上に Azure リソースのマネージド ID を構成する][qs-configure-cli-windows-vm]」に記載されている手順に従って、VM に対してシステム割り当てマネージド ID を有効にします。 ユーザー割り当てマネージド ID は、SAP 用 VM 拡張機能ではサポートされていません。 ただし、システム割り当て ID とユーザー割り当て ID の両方を有効にすることができます。

   例:
   ```azurecli
   az vm identity assign -g <resource-group-name> -n <vm name>
   ```

1. 「[Azure CLI を使用して、リソースにマネージド ID アクセスを割り当てる][howto-assign-access-cli]」の説明に従って、VM のリソース グループ、またはすべてのネットワーク インターフェイス、マネージド ディスク、および VM 自体に、マネージド ID アクセスを割り当てます

    例:

    ```azurecli
    spID=$(az resource show -g <resource-group-name> -n <vm name> --query identity.principalId --out tsv --resource-type Microsoft.Compute/virtualMachines)
    rgId=$(az group show -g <resource-group-name> --query id --out tsv)
    az role assignment create --assignee $spID --role 'Reader' --scope $rgId
    ```

1. 次の Azure CLI コマンドを実行して、Azure Extension for SAP をインストールします。
    現時点では、この拡張機能は AzureCloud でのみサポートされています。 Azure China 21Vianet、Azure Government、またはその他の特殊な環境はまだサポートされていません。

    ```azurecli
    # For Linux machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Linux --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'

    #For Windows machines
    az vm extension set --publisher Microsoft.AzureCAT.AzureEnhancedMonitoring --name MonitorX64Windows --version 1.0 -g <resource-group-name> --vm-name <vm name> --settings '{"system":"SAP"}'
    ```

## <a name="checks-and-troubleshooting"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>確認とトラブルシューティング

Azure VM をデプロイし、関連する Azure Extension for SAP を設定した後は、拡張機能のすべてのコンポーネントが意図したとおりに機能していることを確認します。

「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1]」の説明に従って、Azure Extension for SAP の適合性チェックを実行します。 適合性チェックのすべての結果が良好であり、関連するすべてのパフォーマンス カウンターで OK と示されていれば、Azure Extension for SAP は正常に設定されています。 この場合、「[SAP リソース][deployment-guide-2.2]」に示されている SAP Note に記載された SAP Host Agent のインストールに進むことができます。 適合性チェックの結果に、カウンターが見つからないことが示されている場合は、「[Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2]」の説明に従って、Azure Extension for SAP の正常性チェックを実行します。 トラブルシューティングのオプションについて詳しくは、「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」をご覧ください。

### <a name="readiness-check-for-the-azure-extension-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Azure Extension for SAP の適合性チェック

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、既定の VM 拡張機能について説明します。 新しい VM 拡張機能がインストールされている場合は、「[新しい Azure Extension for SAP の適合性チェック][deployment-guide-5.1-new]」の章を参照してください。

このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる Azure Extension for SAP によって提供されていることを確認します。

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM での適合性チェックの実行

1. Azure 仮想マシンにサインインします (管理者アカウントを使用する必要はありません)。
1. コマンド プロンプト ウィンドウを開きます。
1. コマンド プロンプトで、Azure Extension for SAP のインストール フォルダー (C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;バージョン>\\drop) にディレクトリを変更します。

   拡張機能のパスに含まれる "*バージョン*" は異なる場合があります。 インストール フォルダーに拡張機能の複数のバージョンのフォルダーがある場合は、AzureEnhancedMonitoring Windows サービスの構成を確認し、"*実行可能ファイルへのパス*" として示されているフォルダーに切り替えます。

   ![Azure Extension for SAP が実行されているサービスのプロパティ][deployment-guide-figure-1000]

1. コマンド プロンプトで、パラメーターを指定せずに **azperflib.exe** を実行します。

   > [!NOTE]
   > azperflib.exe はループで実行され、収集したカウンターを 60 秒ごとに更新します。 ループを終了するには、コマンド プロンプト ウィンドウを閉じます。
   >
   >

Azure Extension for SAP がインストールされていない場合、または AzureEnhancedMonitoring サービスが実行されていない場合は、拡張機能が正しく構成されていません。 拡張機能をデプロイする方法の詳細については、「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」をご覧ください。

> [!NOTE]
> Azperflib.exe は、独自の目的には使用できないコンポーネントです。 そのコンポーネントでは、SAP Host Agent 用の VM だけに関連する Azure インフラストラクチャ データが提供されます。
> 

##### <a name="check-the-output-of-azperflibexe"></a>azperflib.exe の出力の確認

azperflib.exe の出力には、SAP 用に設定されたすべての Azure パフォーマンス カウンターが表示されます。 収集されたカウンターのリストの下部に表示される概要と正常性インジケーターでは、Azure Extension for SAP の状態が示されています。

![azperflib.exe の実行による正常性チェックの、問題がないことを示す出力][deployment-guide-figure-1100]
<a name="figure-11"></a>

上記の図に示されている **Counters total** の返された結果 ("empty" と報告されているもの) と、**Health status** の返された結果を確認します。

結果の値は次のように解釈します。

| azperflib.exe の結果の値 | Azure Extension for SAP の正常性状態 |
| --- | --- |
| **API Calls - not available (API 呼び出し － 使用不可)** | "not available" であるカウンターは、仮想マシン構成に適用できないか、エラーであると考えられます。 「**Health status**」を参照してください。 |
| **Counters total - empty** |次の 2 つの Azure Storage カウンターは空の場合があります。 <ul><li>Storage Read Op Latency Server msec</li><li>Storage Read Op Latency E2E msec</li></ul>他のすべてのカウンターには値が含まれている必要があります。 |
| **Health status** |リターン状態が **OK** の場合にのみ、"OK" と表示されます。 |
| **診断** |正常性状態に関する詳細情報。 |

**Health status** の値が **OK** でない場合は、「[Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2]」の手順に従います。

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM での適合性チェックの実行

1. SSH を使用して Azure 仮想マシンに接続します。

1. Azure Extension for SAP の出力を確認します。

   a.  `more /var/lib/AzureEnhancedMonitor/PerfCounters` を実行します。

   **予測される結果**: パフォーマンス カウンターの一覧を返します。 ファイルを空にすることはできません。

   b. `cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error` を実行します。

   **予測される結果**: エラーが **none** である 1 行 (**3;config;Error;;0;0;none;0;1456416792;tst-servercs;** など) を返します。

   c. `more /var/lib/AzureEnhancedMonitor/LatestErrorRecord` を実行します。

   **予測される結果**: 空または存在しないとして返します。

前のチェックが失敗した場合は、次の追加チェックを実行します。

1. waagent がインストールされ、有効になっていることを確認します。

   a.  `sudo ls -al /var/lib/waagent/` を実行します。

     **予測される結果**: waagent ディレクトリの内容を一覧表示します。

   b.  `ps -ax | grep waagent` を実行します。

   **予測される結果**: `python /usr/sbin/waagent -daemon` のような 1 つのエントリを表示します。

1. Azure Extension for SAP がインストールされ、実行されていることを確認します。

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'` を実行します。

   **予測される結果**: Azure Extension for SAP ディレクトリの内容が一覧表示されます。

   b. `ps -ax | grep AzureEnhanced` を実行します。

   **予測される結果**: `python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon` のような 1 つのエントリを表示します。

1. SAP Note [1031096] の説明に従って SAP Host Agent をインストールし、`saposcol` の出力を確認します。

   a.  `/usr/sap/hostctrl/exe/saposcol -d` を実行します。

   b.  `dump ccm` を実行します。

   c.  **Virtualization_Configuration\Enhanced Monitoring Access** メトリックが **true** であるかどうかを確認します。

既に SAP NetWeaver ABAP アプリケーション サーバーをインストール済みである場合は、トランザクション ST06 を開き、監視が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合、拡張機能を再デプロイする方法の詳細については、「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」をご覧ください。

### <a name="readiness-check-for-the-new-azure-extension-for-sap"></a><a name="7bf24f59-7347-4c7a-b094-4693e4687ee5"></a>新しい Azure Extension for SAP の適合性チェック

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、新しい VM 拡張機能について説明します。 既定の VM 拡張機能がインストールされている場合は、「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1]」の章を参照してください。

このチェックでは、SAP アプリケーション内で表示されるすべてのパフォーマンス メトリックが、基になる Azure Extension for SAP によって提供されていることを確認します。

#### <a name="run-the-readiness-check-on-a-windows-vm"></a>Windows VM での適合性チェックの実行

1. Azure 仮想マシンにサインインします (管理者アカウントを使用する必要はありません)。
1. Web ブラウザーを開き、 http://127.0.0.1:11812/azure4sap/metrics にアクセスします
1. ブラウザーによって、仮想マシンの監視データを含む XML ファイルが表示またはダウンロードされるはずです。 そうでない場合は、Azure Extension for SAP がインストールされていることを確認してください。

##### <a name="check-the-content-of-the-xml-file"></a>XML ファイルの内容を確認する

http://127.0.0.1:11812/azure4sap/metrics でアクセスできる XML ファイルには、SAP 用に設定されたすべての Azure パフォーマンス カウンターが含まれています。 また、Azure Extension for SAP の状態の概要と正常性インジケーターも含まれています。

**Provider Health Description** 要素の値を確認します。 その値が **OK** でない場合は、「[新しい Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2-new]」の手順に従ってください。

#### <a name="run-the-readiness-check-on-a-linux-vm"></a>Linux VM での適合性チェックの実行

1. SSH を使用して Azure 仮想マシンに接続します。

1. 次のコマンドの出力を確認します

    ```console
    curl http://127.0.0.1:11812/azure4sap/metrics
    ```
    
   **予測される結果**: 仮想マシン、そのディスク、およびネットワーク インターフェイスの監視情報を含む XML ドキュメントが返されます。

前のチェックが失敗した場合は、次の追加チェックを実行します。

1. waagent がインストールされ、有効になっていることを確認します。

   a.  `sudo ls -al /var/lib/waagent/` を実行します。

     **予測される結果**: waagent ディレクトリの内容を一覧表示します。

   b.  `ps -ax | grep waagent` を実行します。

   **予測される結果**: `python /usr/sbin/waagent -daemon` のような 1 つのエントリを表示します。

1. Azure Extension for SAP がインストールされ、実行されていることを確認します。

   a.  `sudo sh -c 'ls -al /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-*/'` を実行します。

   **予測される結果**: Azure Extension for SAP ディレクトリの内容が一覧表示されます。

   b. `ps -ax | grep AzureEnhanced` を実行します。

   **予測される結果**: `/var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-1.0.0.82/AzureEnhancedMonitoring -monitor` のような 1 つのエントリを表示します。

1. SAP Note [1031096] の説明に従って SAP Host Agent をインストールし、`saposcol` の出力を確認します。

   a.  `/usr/sap/hostctrl/exe/saposcol -d` を実行します。

   b.  `dump ccm` を実行します。

   c.  **Virtualization_Configuration\Enhanced Monitoring Access** メトリックが **true** であるかどうかを確認します。

既に SAP NetWeaver ABAP アプリケーション サーバーをインストール済みである場合は、トランザクション ST06 を開き、監視が有効になっているかどうかを確認します。

上記のチェックのいずれかが失敗した場合、また拡張機能を再デプロイする方法の詳細については、「[新しい Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3-new]」をご覧ください。

### <a name="health-check-for-the-azure-extension-for-sap-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Azure Extension for SAP の構成の正常性チェック

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、既定の VM 拡張機能について説明します。 新しい VM 拡張機能がインストールされている場合は、「[新しい Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2-new]」の章を参照してください。

一部のインフラストラクチャ データが、「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1]」で説明したテストで示されているように正しく提供されない場合は、`Test-AzVMAEMExtension` コマンドレットを実行して、Azure インフラストラクチャと Azure Extension for SAP が正しく構成されているかどうかを確認します。

1. 「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」で説明したように、Azure PowerShell コマンドレットの最新バージョンがインストールされていることを確認します。
1. 次の PowerShell コマンドレットを実行します。 使用可能な環境のリストを表示するには、`Get-AzEnvironment` コマンドレットを実行します。 グローバル Azure を使う場合は、**AzureCloud** 環境を選びます。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. 選択した仮想マシンの構成がスクリプトによってテストされます。

   ![Azure Extension for SAP のテストが成功した場合の出力][deployment-guide-figure-1300]

正常性チェックのすべての結果が **OK** であることを確認します。 一部のチェックで **OK** と表示されない場合は、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」で説明した更新コマンドレットを実行します。 15 分待ってから、「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1]」と「[Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2]」で説明されているチェックをもう一度実行します。 チェックで一部またはすべてのカウンターの問題が引き続き見つかった場合は、「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」をご覧ください。

> [!Note]
> Azure Standard Managed Disks を使用している場合、警告が発生する可能性があります。 テストから "OK" が返る代わりに、警告が表示されます。 これは正常な動作であり、このディスク タイプの場合は意図されたものです。 「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」も参照してください
> 

### <a name="health-check-for-the-new-azure-extension-for-sap-configuration"></a><a name="464ac96d-7d3c-435d-a5ae-3faf3bfef4b3"></a>新しい Azure Extension for SAP の構成の正常性チェック

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、新しい VM 拡張機能について説明します。 既定の VM 拡張機能がインストールされている場合は、「[Azure Extension for SAP の構成の正常性チェック][deployment-guide-5.2]」の章を参照してください。

一部のインフラストラクチャ データが、「[Azure Extension for SAP の適合性チェック][deployment-guide-5.1-new]」で説明したテストで示されているように正しく提供されない場合は、`Get-AzVMExtension` コマンドレットを実行して、Azure Extension for SAP がインストールされているかどうかを確認します。 `Test-AzVMAEMExtension` では、新しい拡張機能はまだサポートされていません。 コマンドレットで新しい拡張機能がサポートされたら、この記事は更新されます。

1. 「[Azure PowerShell コマンドレットのデプロイ][deployment-guide-4.1]」で説明したように、Azure PowerShell コマンドレットの最新バージョンがインストールされていることを確認します。
1. 次の PowerShell コマンドレットを実行します。 使用可能な環境のリストを表示するには、`Get-AzEnvironment` コマンドレットを実行します。 グローバル Azure を使う場合は、**AzureCloud** 環境を選びます。 Azure China 21Vianet の場合は、**AzureChinaCloud** を選択します。

   ```powershell
   $env = Get-AzEnvironment -Name <name of the environment>
   Connect-AzAccount -Environment $env
   Set-AzContext -SubscriptionName <subscription name>
   Test-AzVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
   ```

1. 選択した仮想マシン上の、SAP 用 VM 拡張機能の構成がコマンドレットによってテストされます。

### <a name="troubleshooting-azure-extension-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Azure Extension for SAP のトラブルシューティング

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、既定の VM 拡張機能について説明します。 新しい VM 拡張機能がインストールされている場合は、「[新しい Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3-new]」の章を参照してください。

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure パフォーマンス カウンターまったく表示されない

Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 サービスが正しくインストールされていない場合、または VM で実行されていない場合は、パフォーマンス メトリックを収集できません。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である

###### <a name="issue"></a>問題

インストール ディレクトリ C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\&lt;バージョン>\\drop が空です。

###### <a name="solution"></a>解決策

拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

##### <a name="service-for-azure-extension-for-sap-does-not-exist"></a>Azure Extension for SAP のサービスが存在しない

###### <a name="issue"></a>問題

AzureEnhancedMonitoring Windows サービスが存在しません。

azperflib.exe の出力で次のエラーがスローされます。

![Azure Extension for SAP のサービスが実行されていないことを示す azperflib.exe の実行][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>解決策

サービスが存在しない場合、Azure Extension for SAP が正しくインストールされていません。 [Azure 上の SAP 用 VM のデプロイ シナリオ][deployment-guide-3]に関するセクションの該当するデプロイ シナリオの手順に従って、拡張機能を再デプロイします。

拡張機能をデプロイしたら、Azure VM 内で Azure パフォーマンス カウンターが提供されているかどうかを 1 時間後にもう一度確認します。

##### <a name="service-for-azure-extension-for-sap-exists-but-fails-to-start"></a>Azure Extension for SAP のサービスは存在するが、起動に失敗する

###### <a name="issue"></a>問題

AzureEnhancedMonitoring Windows サービスが存在し、有効になっていますが、起動に失敗します。 詳細については、アプリケーション イベント ログを確認してください。

###### <a name="solution"></a>解決策

構成が正しくありません。 「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、VM で Azure Extension for SAP を再起動します。

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows サービスによって収集されます。 このサービスは、複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Diagnostics から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログ記録から使用されます。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、`Set-AzVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure パフォーマンス カウンターまったく表示されない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。 デーモンが実行されていない場合、パフォーマンス メトリックを収集できません。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である

###### <a name="issue"></a>問題

\\var\\lib\\waagent\\ ディレクトリに、Azure Extension for SAP のサブディレクトリがありません。

###### <a name="solution"></a>解決策

拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 コンピューターを再起動するか、`Set-AzVMAEMExtension` 構成スクリプトを再実行することが必要な場合があります。

##### <a name="the-execution-of-set-azvmaemextension-and-test-azvmaemextension-show-warning-messages-stating-that-standard-managed-disks-are-not-supported"></a>Set-AzVMAEMExtension および Test-AzVMAEMExtension を実行すると、Standard Managed Disks はサポートされていないことを示す警告メッセージが表示されます。

###### <a name="issue"></a>問題

Set-AzVMAEMExtension または Test-AzVMAEMExtension を実行すると、次のようなメッセージが表示されます。

<pre><code>
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
WARNING: [WARN] Standard Managed Disks are not supported. Extension will be installed but no disk metrics will be available.
</code></pre>

前に説明したように、azperfli.exe を実行すると、非正常状態を示す結果が返る場合があります。 

###### <a name="solution"></a>解決策

このメッセージの原因は、Standard Azure ストレージ アカウントの統計情報を確認するために Azure Extension for SAP によって使用される API が、Standard Managed Disks で提供されていないためです。 これは、重要な問題ではありません。 Standard Disk Storage アカウントにデータ収集が導入されたのは、頻繁に発生する入出力を調整するためでした。 Managed Disks では、ストレージ アカウント内のディスクの数を制限することによってこのような調整は回避されます。 そのため、その種のデータがなくても重要ではありません。


#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。デーモンは複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Diagnostics から読み取られます。 ストレージ カウンターは、ストレージ サブスクリプション レベルでログから使用されます。

既知の問題の最新のリストについては、SAP Note [1999351] を参照してください。この SAP Note には、Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。

SAP Note [1999351] を使用したトラブルシューティングで問題が解決しない場合は、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、`Set-AzVMAEMExtension` 構成スクリプトを再実行します。 ストレージ分析または診断カウンターは、有効になった直後には作成されないことがあるため、1 時間待たなければならない場合があります。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。

### <a name="troubleshooting-the-new-azure-extension-for-sap"></a><a name="b7afb8ef-a64c-495d-bb37-2af96688c530"></a>新しい Azure Extension for SAP のトラブルシューティング

> [!NOTE]
> VM 拡張機能には 2 つのバージョンがあります。 この章では、新しい VM 拡張機能について説明します。 既定の VM 拡張機能がインストールされている場合は、「[Azure Extension for SAP のトラブルシューティング][deployment-guide-5.3]」の章を参照してください。

#### <a name="windowslogo_windows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Azure パフォーマンス カウンターまったく表示されない

Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring プロセスによって収集されます。 VM でこのプロセスが実行されていない場合、パフォーマンス メトリックを収集することはできません。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である

###### <a name="issue"></a>問題

インストール ディレクトリ C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;バージョン> が空です。

###### <a name="solution"></a>解決策

拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 場合によっては、マシンを再起動するか、VM 拡張機能をもう一度インストールする必要があります。

#### <a name="windowslogo_windows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、AzureEnhancedMonitoring Windows プロセスによって収集されます。 このプロセスでは、複数のソースからデータが取得されます。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Monitor から読み取られます。

SAP Note [1999351] を使用してトラブルシューティングを行っても問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。 ログ ファイル C:\\Packages\\Plugins\\Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Windows\\&lt;バージョン>\\logapp.txt をインシデントに添付してください。

#### <a name="linuxlogo_linux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Azure パフォーマンス カウンターまったく表示されない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。 デーモンが実行されていない場合、パフォーマンス メトリックを収集できません。

##### <a name="the-installation-directory-of-the-azure-extension-for-sap-is-empty"></a>Azure Extension for SAP のインストール ディレクトリが空である

###### <a name="issue"></a>問題

\\var\\lib\\waagent\\ ディレクトリに、Azure Extension for SAP のサブディレクトリがありません。

###### <a name="solution"></a>解決策

拡張機能がインストールされていません。 これがプロキシの問題 (前述) かどうかを確認します。 場合によっては、マシンを再起動するか、VM 拡張機能をもう一度インストールする必要があります。

#### <a name="linuxlogo_linux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 一部の Azure パフォーマンス カウンターが見つからない

Azure のパフォーマンス メトリックは、デーモンによって収集されます。デーモンは複数のソースからデータを取得します。 一部の構成データはローカルで収集され、一部のパフォーマンス メトリックは Azure Monitor から読み取られます。

既知の問題の最新のリストについては、SAP Note [1999351] を参照してください。この SAP Note には、Azure Extension for SAP に関するその他のトラブルシューティング情報が記載されています。

SAP Note [1999351] を使用してトラブルシューティングを行っても問題が解決しない場合は、「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の説明に従って、拡張機能をもう一度インストールしてください。 問題が解決しない場合は、Windows では BC-OP-NT-AZR コンポーネント、Linux 仮想マシンでは BC-OP-LNX-AZR コンポーネントで、SAP カスタマー サポート メッセージを開きます。 ログ ファイル /var/lib/waagent/Microsoft.AzureCAT.AzureEnhancedMonitoring.MonitorX64Linux-&lt;バージョン>/logapp.txt をインシデントに添付してください。

## <a name="azure-extension-error-codes"></a>Azure 拡張機能のエラー コード

| エラー ID | エラーの説明 | 解決策 |
|---|---|---|
| <a name="cfg_018"></a>cfg/018 | アプリの構成が見つかりません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_019"></a>cfg/019 | アプリの構成にデプロイ ID がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_020"></a>cfg/020 | アプリの構成に RoleInstanceId がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_022"></a>cfg/022 | アプリの構成に RoleInstanceId がありません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_031"></a>cfg/031 | Azure の構成を読み取ることができません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_021"></a>cfg/021 | アプリの構成ファイルが見つかりません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_015"></a>cfg/015 | アプリの構成に VM サイズがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_016"></a>cfg/016 | GlobalMemoryStatusEx カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_023"></a>cfg/023 | MaxHwFrequency カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_024"></a>cfg/024 | NIC カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_025"></a>cfg/025 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_026"></a>cfg/026 | プロセッサ名カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_027"></a>cfg/027 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_038"></a>cfg/038 | 拡張機能の構成ファイル config.xml にメトリック 'Disk type' がありません。 'Disk type' は、他のいくつかのカウンターと共に、v2.2.0.68 2015/12/16 で導入されました。 2015 年 12 月 16 日より前に拡張機能をデプロイした場合は、古い構成ファイルが使用されます。 Azure 拡張機能フレームワークによって拡張機能は自動的に新しいバージョンにアップグレードされますが、config.xml は変更されません。 構成を更新するには、最新の PowerShell セットアップ スクリプトをダウンロードして実行します。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_039"></a>cfg/039 | ディスク キャッシュがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_036"></a>cfg/036 | ディスクの SLA スループットがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_037"></a>cfg/037 | ディスクの SLA IOPS がありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_028"></a>cfg/028 | ディスク マッピング カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_029"></a>cfg/029 | 最後のハードウェア変更カウンターが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_030"></a>cfg/030 | NIC カウンターが失敗しました | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_017"></a>cfg/017 | VM の sysprep により、Windows SID が変更されました。 | [sysprep 後に再デプロイする][deployment-guide-redeploy-after-sysprep] |
| <a name="str_007"></a>str/007 | Storage Analytics へのアクセスに失敗しました。 <br /><br />新しく作成された VM 上での Storage Analytics データの作成には、最大で 30 分かかる場合があるため、しばらくするとエラーが表示されなくなる可能性があります。 エラーがまだ表示されている場合は、セットアップ スクリプトを再実行してください。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_010"></a>str/010 | Storage Analytics カウンターがありません。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_009"></a>str/009 | Storage Analytics が失敗しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_004"></a>wad/004 | WAD 構成が不適切です。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_002"></a>wad/002 | 予期しない WAD 形式です。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="wad_001"></a>wad/001 | WAD カウンターが見つかりませんでした。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="wad_040"></a>wad/040 | 古い WAD カウンターが見つかりました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="wad_003"></a>wad/003 | WAD テーブルを読み取ることができません。 WAD テーブルへの接続がありません。 これにはいくつかの原因が考えられます。<br /><br /> 1) 期限切れの構成 <br />2) Azure へのネットワーク接続がない <br />3) WAD セットアップに関する問題 | [セットアップ スクリプトの実行][deployment-guide-run-the-script]<br />[インターネット接続の修正][deployment-guide-fix-internet-connection]<br />[サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_011"></a>prf/011 | Perfmon NIC メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_012"></a>prf/012 | Perfmon ディスク メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_013"></a>prf/013 | 一部の prefmon メトリックが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="prf_014"></a>prf/014 | Perfmon でカウンターを作成できませんでした。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="cfg_035"></a>cfg/035 | メトリック プロバイダーが構成されていません。 | [サポートに問い合わせる][deployment-guide-contact-support] |
| <a name="str_006"></a>str/006 | Storage Analytics 構成が不適切です。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_032"></a>str/032 | Storage Analytics のメトリックが失敗しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="cfg_033"></a>cfg/033 | メトリック プロバイダーの 1 つでエラーが発生しました。 | [セットアップ スクリプトの実行][deployment-guide-run-the-script] |
| <a name="str_034"></a>str/034 | プロバイダー スレッドが失敗しました。 | [サポートに問い合わせる][deployment-guide-contact-support] |

### <a name="detailed-guidelines-on-solutions-provided"></a>指定されたソリューションに関する詳細なガイドライン

#### <a name="run-the-setup-script"></a><a name="0d2847ad-865d-4a4c-a405-f9b7baaa00c7"></a>セットアップ スクリプトを実行する

このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章の手順に従って、拡張機能をもう一度インストールします。 カウンターによっては、プロビジョニングに最大 30 分かかる場合があることに注意してください。

エラーが解消されない場合は、[サポートにお問い合わせください][deployment-guide-contact-support]。

#### <a name="contact-support"></a><a name="3ba34cfc-c9bb-4648-9c3c-88e8b9130ca2"></a>サポートに問い合わせる

予期しないエラーが発生したか、既知の解決策がありません。 フォルダー C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\drop (Windows) または /var/log/azure/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux (Linux) にある AzureEnhancedMonitoring_service.log ファイルを収集し、SAP サポートに連絡してさらに支援を求めてください。

#### <a name="redeploy-after-sysprep"></a><a name="2cd61f22-187d-42ed-bb8c-def0c983d756"></a>sysprep 後に再デプロイする

一般化された sysprep 済み OS イメージ (SAP ソフトウェアを含めることが可能です) をビルドする予定の場合は、このイメージに Azure Extension for SAP を含めないことをお勧めします。 一般化された OS イメージの新しいインスタンスがデプロイされた後に、Azure Extension for SAP をインストールする必要があります。

ただし、一般化および sysprep された OS イメージに既に Azure Extension for SAP が含まれている場合は、新しくデプロイされた VM インスタンス上で、次の回避策を適用して拡張機能を再構成することができます。

* 新しくデプロイされた VM インスタンス上で、次のフォルダーの内容を削除します。  
  C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\RuntimeSettings C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\\<version\>\Status

* このガイドの「[Azure Extension for SAP を構成する][deployment-guide-4.5]」の章の手順に従って、拡張機能をもう一度インストールします。

#### <a name="fix-internet-connection"></a><a name="e92bc57d-80d9-4a2b-a2f4-16713a22ad89"></a>インターネット接続の修正

Azure Extension for SAP を実行している Microsoft Azure 仮想マシンには、インターネットへのアクセスが必要です。 Azure VM が Azure 仮想ネットワークに属しているか、オンプレミス ドメインにある場合は、関連するプロキシ設定が構成されていることを確認してください。 インターネットにアクセスするには、これらの設定がローカル システム アカウントでも有効である必要があります。 このガイドの「[プロキシの構成][deployment-guide-configure-proxy]」の章に従ってください。

また、Azure VM の静的 IP アドレスを設定する必要がある場合は、Azure VM 内で手動で設定せず、[Azure PowerShell](../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md)、[Azure CLI](../../../virtual-network/virtual-networks-static-private-ip-arm-cli.md)、[Azure portal](../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md) を使用して設定してください。 静的 IP は、Azure DHCP サービスを介して伝達されます。

Azure VM 内での静的 IP アドレスの手動による設定はサポートされておらず、Azure Extension for SAP に関する問題が発生する可能性があります。
