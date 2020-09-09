---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "68229220"
---
Azure portal を使用して IoT ハブを作成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[リソースの作成]**  >  **[モノのインターネット]**  >  **[IoT Hub]** を選択します。

    ![IoT Hub をインストールすることを選択する](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Free レベルの IoT ハブを作成するには、次の表の値を使用します。

    | 設定 | 値 |
    | ------- | ----- |
    | サブスクリプション | ドロップダウンから Azure サブスクリプションを選択します。 |
    | Resource group | 新規作成。 このチュートリアルでは、**tutorials-iot-hub-rg** という名前を使います。 |
    | リージョン | このチュートリアルでは、 **[米国西部]** を使います。 最も近いリージョンを選択できます。 |
    | 名前 | 次のスクリーンショットでは、**tutorials-iot-hub** という名前を使っています。 ハブを作成するときは、独自の一意名を選ぶ必要があります。 |

    ![ハブの設定 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | 設定 | 値 |
    | ------- | ----- |
    | 価格とスケール ティア | F1 Free。 サブスクリプションに作成できる Free レベルのハブは 1 つだけです。 |
    | IoT Hub ユニット | 1 |

    ![ハブの設定 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. **Create** をクリックしてください。 ハブが作成されるまで数分かかる場合があります。

    ![ハブの設定 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. 選択した IoT ハブの名前を書き留めておきます。 この値は、後ほどチュートリアルの中で使用します。
