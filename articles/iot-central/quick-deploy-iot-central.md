---
title: Azure IoT Central アプリケーションを作成する | Microsoft Docs
description: 新しい Azure IoT Central アプリケーションを作成します。 アプリケーション テンプレートを使用して、試用版や従量課金制のアプリケーションを作成します。
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 23ef0afbf3a3fd3e0d0db6e3b4130b45530916be
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001258"
---
# <a name="create-an-azure-iot-central-application"></a>Azure IoT Central アプリケーションの作成

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

"_ビルダー_" は、Azure IoT Central の UI を使用して、Microsoft Azure IoT Central アプリケーションを定義します。 このクイックスタートでは、サンプル "_デバイス テンプレート_" を含む Azure IoT Central アプリケーションを作成する方法について説明します。

## <a name="create-an-application"></a>アプリケーションの作成

[Azure IoT Central のアプリケーション マネージャー](https://aka.ms/iotcentral) Web サイトに移動します。 次に、Microsoft 個人アカウントか、職場または学校アカウントを使用してサインインします。

新しい Azure IoT Central アプリケーションの作成を開始するには、 **[New Application]\(新しいアプリケーション\)** を選択します。 このリンクをクリックすると、 **[アプリケーションの作成]** ページが表示されます。

![Azure IoT Central の [アプリケーションの作成] ページ](media/quick-deploy-iot-central/iotcentralcreate.png)

新しい Azure IoT Central アプリケーションを作成するには:

1. 以下の支払プランを選択します。
   - **試用版**アプリケーションは、有効期限が切れるまでの 7 日間は無料となります。 有効期限内で、いつでも**従量課金制**に変更することができます。 **試用版**アプリケーションを作成する場合は、連絡先情報を入力し、Microsoft から情報やヒントを受信するかどうかを選択します。
   - **従量課金制**アプリケーションは、デバイスごとに課金され、最初の 5 個のデバイスは無料です。 **従量課金制**アプリケーションを作成する場合は、"*ディレクトリ*"、"*Azure サブスクリプション*"、および "*リージョン*" を選択する必要があります。
        - "*ディレクトリ*" は、アプリケーションを作成する Azure Active Directory (AD) です。 ユーザー ID、資格情報、およびその他の組織情報が含まれています。 Azure AD を持っていない場合は、Azure サブスクリプションを作成するときに自動的に作成されます。
        - "*Azure サブスクリプション*" を使用すると、Azure サービスのインスタンスを作成できます。 IoT Central では、リソースが自分のサブスクリプション内にプロビジョニングされます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。 Azure サブスクリプションを作成したら、 **[アプリケーションの作成]** ページに戻ります。 新しいサブスクリプションが **[Azure サブスクリプション]** ドロップダウンに表示されます。
        - "*リージョン*" は、アプリケーションを作成する物理的な場所または[地域](https://azure.microsoft.com/global-infrastructure/geographies/)です。 通常、最適なパフォーマンスを得るには、ご利用のデバイスに物理的に最も近いリージョンを選択する必要があります。 Azure IoT Central を使用できるリージョンは、[[リージョン別の利用可能な製品]](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) ページで確認できます。 いったんリージョンを選択すると、後でアプリケーションを別のリージョンに移動することはできません。

        価格の詳細については、[Azure IoT Central の価格に関するページ](https://azure.microsoft.com/pricing/details/iot-central/)を参照してください。

1. アプリケーション テンプレートを選択します。 アプリケーション テンプレートには、使用開始に役立つデバイス テンプレートやダッシュボードなどの事前に定義された項目を含めることができます。

    | アプリケーション テンプレート | 説明 |
    | -------------------- | ----------- |
    | サンプル Contoso       | Refrigerated Vending Machine 用に既に作成したデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、Azure IoT Central の調査を開始するために使用します。 |
    | サンプル Devkit       | MXChip または Raspberry Pi デバイスを接続するための準備ができたデバイス テンプレートを含むアプリケーションを作成します。 このテンプレートは、これらのいずれかのデバイスで実験しているデバイス開発者が使用します。 |
    | カスタム アプリケーション   | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。 |

1. Azure IoT Central によって、選択したアプリケーション テンプレートに基づいてアプリケーション名が自動的に提案されます。 この名前をそのまま使用することも、独自のわかりやすいアプリケーション名を入力することもできます (**Contoso IoT** など)。 Azure IoT Central によって、アプリケーション名に基づいて、一意の URL プレフィックスも生成されます。 この URL プレフィックスは、もっと覚えやすいものに変更することもできます。

1. 前に手順 1. で選択した支払いプランのために必要な追加情報を指定します。

1. ページの下部にある **[作成]** を選択します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、IoT Central アプリケーションを作成しました。 推奨される次の手順は、以下のとおりです。

> [!div class="nextstepaction"]
> [Azure IoT Central アプリケーションで新しいデバイスの種類を定義する](./tutorial-define-device-type.md)
