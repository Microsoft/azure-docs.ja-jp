---
title: Azure IoT Hub のエラー 409001 DeviceAlreadyExists のトラブルシューティング
description: エラー 409001 Deviceexists を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960292"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

この記事では、**409001 DeviceAlreadyExists** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub にデバイスを登録しようとすると、要求がエラー **409001 Deviceexists** で失敗します。

## <a name="cause"></a>原因

同じデバイス ID を持つデバイスが IoT ハブに既に存在します。 

## <a name="solution"></a>解決策

別のデバイス ID を使用して、もう一度やり直してください。