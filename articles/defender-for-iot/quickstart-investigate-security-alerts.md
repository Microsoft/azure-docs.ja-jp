---
title: クイック スタート:セキュリティ アラートを調べる
description: お使いの IoT デバイスに関する Defender for IoT セキュリティ アラートの理解を深め、ドリルダウンと調査を行います。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "90945174"
---
# <a name="quickstart-investigate-security-alerts"></a>クイック スタート:セキュリティ アラートを調べる

お使いの IoT ソリューション全体にわたってコンプライアンスと保護を確保するための最善の方法は、Defender for IoT から発行されたアラートを定期的に調べ、修復を行うことです。

このクイックスタートでは、各 IoT セキュリティ アラートから得られる情報について確認すると共に、各アラートおよび関連デバイスの詳細情報にドリルダウンし、それを使用して対応と修復を行う方法について説明します。 

では、始めましょう。 


## <a name="investigate-new-security-alerts"></a>新規のセキュリティ アラートを調べる

IoT ハブのセキュリティ アラートの一覧には、IoT ハブについての集計済みセキュリティ アラートがすべて表示されます。 

1. Azure portal で、新規のアラートを調べる **IoT ハブ**を開きます。
1. **[セキュリティ]** メニューの **[アラート]** を選択します。 IoT ハブのすべてのセキュリティ アラートが表示されます。 **[新規]** フラグが付いたアラートは、過去 24 時間に発行されたアラートであることを示しています。
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="新規フラグを使用して、新規の IoT セキュリティ アラートを調べます":::
1. 一覧からアラートを選択してアラートの詳細を開き、アラートの各細目にドリルダウンします。 

## <a name="security-alert-details"></a>Security alert details

各集計済みアラートを開くと、アラートについての詳しい説明、修復手順、アラートをトリガーした各デバイスのデバイス ID、アラートの重要度、および Log Analytics を使用した調査用の直接アクセス リンクが表示されます。 

1. セキュリティ アラートを選択して開くには、 **[IoT Hub]**  >  **[セキュリティ]**  >  **[アラート]** の順に選択して一覧を表示します。 
1. 集計期間内にこのアラートを発行したすべてのデバイスについて、アラートの**説明**、**重要度**、**検出元**、**デバイスの詳細**を確認します。
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="新規フラグを使用して、新規の IoT セキュリティ アラートを調べます"::: 
1. アラートの細目を確認した後、 **[Manual remediation steps]\(手動による修復手順\)** に示されている手順を使用して、アラートの原因となった問題を修復したり解決したりすることができます。 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="新規フラグを使用して、新規の IoT セキュリティ アラートを調べます":::

1. さらに調査が必要な場合は、リンクを使用して **Log Analytics でアラートを調査します**。 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="新規フラグを使用して、新規の IoT セキュリティ アラートを調べます":::

## <a name="next-steps"></a>次のステップ

Defender のアラートの種類と利用可能なカスタマイズの詳細については、次の記事に進んでください。

> [!div class="nextstepaction"]
> [IoT セキュリティ アラートについて](concept-security-alerts.md)
