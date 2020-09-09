---
title: Linux 仮想マシンにおける SAP の使用 | Microsoft Docs
description: Microsoft Azure での Linux 仮想マシン (VM) における SAP の使用について説明します
services: virtual-machines-linux,virtual-network,storage
documentationcenter: saponazure
author: MSSedusch
manager: gwallace
editor: ''
tags: azure-service-management
keywords: ''
ms.assetid: f9cd93dc-71ad-48a4-8778-4e48aec484a6
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: na
ms.date: 10/04/2016
ms.author: sedusch
ms.openlocfilehash: 3ceeb20b138307b40fcd6f8c03d614fc3e811473
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87035776"
---
# <a name="using-sap-on-linux-virtual-machines-in-azure"></a>Azure での Linux 仮想マシンにおける SAP の使用
クラウド コンピューティングという言葉は、今や多くの人々に認知され、小規模な企業から大企業、多国籍企業にいたるまで、IT 業界における存在感を日増しに高めています。 Microsoft Azure はマイクロソフトが提供する Cloud Services プラットフォームで、多方面にわたって新たな可能性を生み出しています。 アプリケーションを Cloud Services として迅速にプロビジョニングしたり、プロビジョニング解除したりすることができるようになった今、技術や予算の制約に縛られることはありません。 企業は、ハードウェア インフラストラクチャに貴重な時間と予算を費やすことなく、アプリケーションやビジネス プロセス、そして顧客とユーザーの利益にのみ目を向けることができます。

Microsoft Azure Virtual Machines を通じて、マイクロソフトは包括的な IaaS (Infrastructure as a Service) プラットフォームを提供しています。 SAP NetWeaver ベースのアプリケーションは、Azure Virtual Machines (IaaS) でサポートされます。 以下のホワイトペーパーでは、SAP NetWeaver ベースのアプリケーションを計画し、Azure の Windows 仮想マシンに実装する方法が説明されています。 [Windows 仮想マシン](./get-started.md?toc=/azure/virtual-machines/windows/classic/toc.json)上に SAP NetWeaver ベースのアプリケーションを実装することもします。

[!INCLUDE [virtual-machines-common-classic-sap-get-started](../../../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure-suse-linux-virtual-machines"></a>Azure SUSE Linux Virtual Machines における SAP NetWeaver
タイトル:Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト

概要: 現時点では、SAP は Azure Linux VM 上での SAP NetWeaver の実行を正式にはサポートしていません。 それでも、SAP サポートに連絡する必要がない限り、顧客はテストを実施する、または Azure Linux VMs で SAP のデモやトレーニング システムを実行することを検討する場合があります。 この記事では、SAP を実行するために必要な Azure SUSE Linux VMs の設定方法と、潜在的な落とし穴を回避するための基本的なヒントをいくつか示します。

更新:2015 年 12 月

[記事はこちら](./sap-deployment-checklist.md?toc=/azure/virtual-machines/linux/toc.json)
