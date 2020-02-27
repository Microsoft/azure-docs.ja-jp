---
title: Azure IoT Central アプリケーションを Dynamics 365 フィールド サービスに接続する |Microsoft Docs
description: Azure IoT Central と Dynamics 365 フィールド サービスを使用してエンドツーエンド ソリューションを構築する方法について説明します
author: miriambrus
ms.author: miriamb
ms.date: 10/23/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ae266495db1d6b94a43aa962a3e9b63a8115c526
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77017669"
---
# <a name="build-end-to-end-solution-with-azure-iot-central-and-dynamics-365-field-service"></a>Azure IoT Central と Dynamics 365 フィールド サービスを使用してエンドツーエンド ソリューションを構築する 



ビルダーとして、他のビジネス システムへの Azure IoT Central アプリケーションの統合を有効にすることができます。 


たとえば、接続された廃棄物管理ソリューションで、ごみ回収車の配車を最適化できます。 最適化は、接続されたごみ箱からの IoT センサー データに基づいて行うことができます。[接続された IoT Central 廃棄物管理アプリケーション](./tutorial-connected-waste-management.md)で、ルールとアクションを構成し、Dynamics フィールド サービスでアラートの作成をトリガーするように設定できます。 このシナリオは Microsoft Flow を使用して実現されます。これは、アプリケーションやサービスにまたがるワークフローを自動化するために IoT Central で直接構成されます。 また、フィールド サービスのサービス アクティビティに基づいて、情報を Azure IoT Central に送り返すことができます。 

## <a name="how-to-connect-your-azure-iot-central-application-with-dynamics-365-field-services"></a>Azure IoT Central アプリケーションを Dynamics 365 フィールド サービスに接続する方法 

次の統合プロセスは、純粋な構成エクスペリエンスに基づいて簡単に実装することができます。
* Azure IoT Central では、Connected Field Service に、デバイスの異常に関する情報を (IoT アラートとして) 診断のために送信することができます。
* Connected Field Service では、デバイスの異常によってトリガーされるケースまたは作業指示を作成することができます。
* Connected Field Service では、ダウンタイム インシデントを防止するために、技術者による検査をスケジュールできます。
* Azure IoT Central デバイス ダッシュボードは、関連するサービスおよびスケジュール情報で更新することができます。


## <a name="next-steps"></a>次のステップ
* [IoT Central 政府機関テンプレート](./overview-iot-central-government.md)の詳細を学習する
* [IoT Central](https://docs.microsoft.com/azure/iot-central/core/overview-iot-central) についての詳細情報
* [Dynamics 365 フィールド サービス](https://docs.microsoft.com/dynamics365/field-service/cfs-iot-overview)の詳細情報
