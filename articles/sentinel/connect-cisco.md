---
title: Azure Sentinel に Cisco データを接続する | Microsoft Docs
description: Azure Sentinel に Cisco データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 264dab4fabc890ffe03d911f8ee3c08ef51375d8
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588401"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Cisco ASA を Azure Sentinel に接続する



この記事では、Cisco ASA アプライアンスを Azure Sentinel に接続する方法について説明します。 Cisco ASA データ コネクタを使用すると、Azure Sentinel に Cisco ASA のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、および調査の改善も行うことができます。 Azure Sentinel で Cisco ASA を使用すると、組織のインターネット使用状況に関するより多くの洞察が得られ、セキュリティ操作機能が強化されます。 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA のログを Syslog エージェントに転送する

Cisco ASA は CEF をサポートしていないため、ログは Syslog として送信され、Azure Sentinel エージェントはそれらを CEF ログのように解析する方法を認識しています。 Syslog のエージェントを介して Azure ワークスペースに Syslog メッセージを転送するように Cisco ASA を構成します。

1. [外部 Syslog サーバーに Syslog メッセージを送信する](https://aka.ms/asi-syslog-cisco-forwarding)方法に関するページに進み、指示に従って接続を設定します。 入力を求められたら、次のパラメーターを使用します。
    - **port** を 514 またはエージェントで設定したポートに設定します。
    - **syslog_ip** をエージェントの IP アドレスに設定します。

1. Log Analytics で Cisco イベントに関連するスキーマを使用するために、`CommonSecurityLog` を検索します。

1. 次に、「[手順 3:接続の検証](connect-cef-verify.md)」に進みます。




## <a name="next-steps"></a>次のステップ
このドキュメントでは、Cisco ASA アプライアンスを Azure Sentinel に接続する方法について学びました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


